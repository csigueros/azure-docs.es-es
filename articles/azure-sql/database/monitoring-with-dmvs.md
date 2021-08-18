---
title: Supervisión del rendimiento mediante DMV
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Obtenga información sobre cómo detectar y diagnosticar problemas comunes de rendimiento con vistas de administración dinámica para supervisar Microsoft Azure SQL Database y la Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 03/15/2021
ms.openlocfilehash: 68daeddb958a3d009cfb2632c30780d7dfe6ce6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739655"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Supervisión del rendimiento de Microsoft Azure SQL Database e Instancia administrada de Azure SQL mediante vistas de administración dinámica
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Microsoft Azure SQL Database e Instancia administrada de Azure SQL habilitan un subconjunto de vistas de administración dinámica para diagnosticar problemas de rendimiento, que pueden deberse a consultas bloqueadas o de ejecución prolongada, cuellos de botella de recursos, planes de consulta deficientes, etc. En este artículo se ofrece información sobre cómo encontrar problemas comunes de rendimiento con vistas de administración dinámica.

Microsoft Azure SQL Database e Instancia administrada de Azure SQL admiten parcialmente tres categorías de vistas de administración dinámica:

- Vistas de administración dinámica relacionadas con bases de datos.
- Vistas de administración dinámica relacionadas con ejecuciones.
- Vistas de administración dinámica relacionadas con transacciones.

Para obtener información detallada sobre las vistas de administración dinámica, consulte [Funciones y vistas de administración dinámica (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

## <a name="monitor-with-sql-insights"></a>Supervisión con SQL Insights

[Azure Monitor SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) es una herramienta para supervisar las instancias administradas de Azure SQL, las bases de datos de Azure SQL y las instancias de SQL Server en VM de Azure SQL. Este servicio usa un agente remoto para capturar los datos de vistas de administración dinámica (DMV) y enrutarlos a Azure Log Analytics, donde se pueden supervisar y analizar. Puede ver estos datos de [Azure Monitor](../../azure-monitor/overview.md) en las vistas proporcionadas, o puede acceder directamente a los datos de registro para ejecutar consultas y analizar tendencias. Para empezar a usar Azure Monitor SQL Insights, consulte [Habilitación de SQL Insights](../../azure-monitor/insights/sql-insights-enable.md).

## <a name="permissions"></a>Permisos

En Azure SQL Database, para realizar consultas en una vista de administración dinámica se requieren los permisos **VIEW DATABASE STATE**. El permiso **VIEW DATABASE STATE** devuelve información sobre todos los objetos de la base de datos actual.
Para conceder el permiso **VIEW DATABASE STATE** a un usuario de base de datos en concreto, ejecute la consulta siguiente:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

En Instancia administrada de Azure SQL, para realizar consultas en una vista de administración dinámica se requieren los permisos **VIEW SERVER STATE**. Para más información, consulte [Vistas de administración dinámica del sistema](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions).

En una instancia de SQL Server y en Instancia administrada de Azure SQL, las vistas de administración dinámica devuelven la información de estado del servidor. En Azure SQL Database, devuelven información relativa únicamente a la base de datos lógica actual.

Este artículo contiene una colección de consultas de DMV que puede ejecutar mediante SQL Server Management Studio o Azure Data Studio para detectar los siguientes tipos de problemas en el rendimiento de las consultas:

- [Identificación de consultas relacionadas con el consumo excesivo de CPU](#identify-cpu-performance-issues)
- [Esperas de PAGELATCH_* y WRITE_LOG relacionadas con cuellos de botella de E/S](#identify-io-performance-issues)
- [Esperas de PAGELATCH_ * provocadas por la contención de bytTempDB](#identify-tempdb-performance-issues)
- [Esperas de RESOURCE_SEMAHPORE debidas a problemas de espera en la concesión de memoria](#identify-memory-grant-wait-performance-issues)
- [Identificación de tamaños de objetos y de bases de datos](#calculating-database-and-objects-sizes)
- [Recuperación de información de sesiones activas](#monitoring-connections)
- [Recuperación de información sobre el uso de recursos tanto de la base de datos como de todo el sistema](#monitor-resource-use)
- [Recuperación de información sobre el rendimiento de consultas](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identificar problemas de rendimiento de CPU

Si el consumo de CPU es superior al 80 % durante largos períodos de tiempo, tenga en cuenta los siguientes pasos de solución de problemas:

### <a name="the-cpu-issue-is-occurring-now"></a>El problema de CPU se produce en este momento

Si el problema se produce ahora mismo, hay dos escenarios posibles:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Muchas consultas individuales que consumen una gran cantidad de CPU de forma acumulativa

Utilice la siguiente consulta para identificar los principales valores hash de la consulta:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Consultas de larga ejecución que consumen CPU siguen en ejecución

Utilice la siguiente consulta para identificar estas consultas:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>El problema de CPU se produjo en el pasado

Si el problema se produjo en el pasado y quiere realizar el análisis de la causa principal, utilice [Almacén de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Los usuarios con acceso a la base de datos pueden utilizar T-SQL para consultar los datos del Almacén de consultas. Las configuraciones predeterminadas del Almacén de consultas utilizan una granularidad de una hora. Utilice la siguiente consulta para observar la actividad de las consultas que consumen mucha CPU. Esta consulta devuelve las 15 consultas que más consumen CPU. No se olvide de cambiar `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Una vez identificadas las consultas problemáticas, es hora de optimizar las consultas para reducir el uso de CPU.  Si no tiene tiempo para optimizar las consultas, también puede actualizar el SLO de la base de datos para solucionar el problema.

## <a name="identify-io-performance-issues"></a>Identificar problemas de rendimiento de E/S

Cuando se identifican problemas de rendimiento de E/S, los principales tipos de espera asociados a los problemas de E/S son:

- `PAGEIOLATCH_*`

  En el caso de problemas de E/S de archivos de datos (incluidos `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Si en el nombre de tipo de espera se incluye **IO**, indica un problema de E/S. Si no hay ningún **IO** en el nombre de tiempo de espera de bloqueo temporal de la página, indica otro tipo de problema (por ejemplo, la contención de tempdb).

- `WRITE_LOG`

  En el caso de problemas de E/S del registro de transacciones.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Si el problema de E/S se produce en este momento

Utilice la vista [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) o [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para ver `wait_type` y `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identificar el uso de E/S de registros y datos

Utilice la siguiente consulta para identificar el uso de E/S de registros y datos: Si la E/S de registros o datos es superior al 80 %, implica que los usuarios han usado la E/S disponible para el nivel de servicio de SQL Database.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Si se ha alcanzado el límite de E/S, tiene dos opciones:

- Opción 1: Actualizar el tamaño de proceso o nivel de servicio
- Opción 2: Identificar y optimizar las consultas que utilizan la mayoría de E/S

#### <a name="view-buffer-related-io-using-the-query-store"></a>Ver E/S relacionadas con el búfer mediante el Almacén de consultas

En la opción 2, puede usar la consulta siguiente en el Almacén de consultas sobre E/S relacionadas con el búfer para ver las dos últimas horas de actividad con seguimiento:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Ver el total de E/S de registros para esperas WRITELOG

Si el tipo de espera es `WRITELOG`, utilice la siguiente consulta para ver el total de E/S de registros por instrucción:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identificar problemas de rendimiento de `tempdb`

Cuando se identifican problemas de rendimiento de E/S, los principales tipos de espera asociados a problemas de `tempdb` son `PAGELATCH_*` (no `PAGEIOLATCH_*`). Pero las esperas `PAGELATCH_*` no siempre significan que tenga contención `tempdb`.  Esta espera también puede significar que tiene contención de páginas de datos de objeto de usuario debido a solicitudes simultáneas que se destinan a la misma página de datos. Para confirmar aún más el argumento `tempdb`, utilice [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para confirmar que el valor wait_resource comienza con `2:x:y` donde 2 indica que `tempdb` es el id. de la base de datos, `x` es el id. del archivo y `y` es el id. de la página.  

En el caso de la contención de tempdb, un método común consiste en reducir o reescribir el código de la aplicación que necesita de `tempdb`.  Las áreas de uso de `tempdb` comunes incluyen:

- Tablas temporales
- Variables de tabla
- Parámetros con valores de tabla
- Uso del almacén de versiones (asociado a transacciones de larga duración)
- Consultas que tienen planes de consultas que usan ordenaciones, combinaciones hash y colas de impresión

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Principales consultas que utilizan tablas temporales y variables de tabla

Utilice la siguiente consulta para identificar las principales consultas que usan tablas temporales y variables de tabla:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identificar transacciones de larga ejecución

Utilice la siguiente consulta para identificar transacciones de larga ejecución. Las transacciones de larga ejecución impiden la limpieza del almacén de versiones.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identificar problemas de rendimiento de espera de concesión de memoria

Si su principal tipo de espera es `RESOURCE_SEMAHPORE` y no tiene el problema del uso elevado de la CPU, es posible que lo tenga con la espera de la concesión de memoria.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Determinar si una espera `RESOURCE_SEMAHPORE` es una espera principal

Utilice la siguiente consulta para determinar si una espera `RESOURCE_SEMAHPORE` es una espera principal.

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Identificar instrucciones con un alto consumo de memoria

Utilice la siguiente consulta para identificar las instrucciones con un alto consumo de memoria:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identificar las 10 concesiones principales de memoria activa

Utilice la siguiente consulta para identificar las 10 concesiones principales de memoria activa:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Calcular los tamaños de base de datos y objetos

La siguiente consulta devuelve el tamaño de la base de datos en megabytes:

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

La consulta siguiente devuelve el tamaño de objetos individuales (en megabytes) de la base de datos:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Supervisión de conexiones

Puede usar la vista [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) para recuperar información sobre las conexiones establecidas con un servidor y una instancia administrada concretos, así como los detalles de cada conexión. Además, la vista [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) resulta útil para recuperar información sobre todas las conexiones de usuario activas y las tareas internas.

La siguiente consulta recupera información sobre la conexión actual:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Al ejecutar las vistas **sys.dm_exec_requests** y **sys.dm_exec_sessions**, si el usuario tiene permiso **VIEW DATABASE STATE** en la base de datos, verá todas las sesiones en ejecución en la base de datos; en caso contrario, el usuario solo verá la sesión actual.

## <a name="monitor-resource-use"></a>Supervisión del uso de recursos

Puede supervisar el uso de recursos de Azure SQL Database mediante [Información de rendimiento de consultas de SQL Database](query-performance-insight-use.md). En el caso de Azure SQL Database e Instancia administrada de Azure SQL, puede realizar la supervisión con el [Almacén de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

También puede supervisar el uso con estas vistas:

- Azure SQL Database: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Instancia administrada de Azure SQL: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Azure SQL Database e Instancia administrada de Azure SQL: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Puede usar la vista [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en todas las bases de datos. La vista **sys.dm_db_resource_stats** muestra los datos de uso reciente de recursos en relación con el nivel de servicio. Porcentajes medios para CPU, E/S de datos, escritura de registros y memoria, se registran cada 15 segundos y se mantienen durante una hora.

Dado que esta vista proporciona una panorámica más granular del uso de recursos, use primero **sys.dm_db_resource_stats** para cualquier análisis o solución de problemas de estado actual. Por ejemplo, esta consulta muestra el uso medio y máximo de recursos para la base de datos actual durante la última hora:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Para otras consultas, consulte los ejemplos de [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

Puede usar [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) para devolver datos de uso de CPU, E/S y almacenamiento de datos para una Instancia administrada de Azure SQL. Los datos se recopilan y se agregan en intervalos de cinco minutos. Hay una fila por cada 15 segundos de informes. Los datos devueltos incluyen el uso de CPU, el tamaño de almacenamiento, el uso de E/S y la SKU de instancia administrada. Los datos históricos se conservan durante 14 días aproximadamente.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys.resource_stats

La vista [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) de la base de datos **maestra** proporciona información adicional que puede ayudar a supervisar el rendimiento de la base de datos en su nivel de servicio y tamaño de proceso específicos. Los datos se recopilan cada cinco minutos y se mantienen durante aproximadamente 14 días. Esta vista es útil para realizar análisis históricos a largo plazo de cómo la base de datos usa los recursos.

En el siguiente gráfico se muestra el uso de recursos de CPU para una base de datos Premium con el tamaño de proceso P2 durante cada hora de una semana. Este gráfico en concreto empieza el lunes, muestra cinco días laborables y, a continuación, un fin de semana cuando hay mucha menos actividad en la aplicación.

![Uso de recursos de la base de datos](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

Según los datos, esta base de datos tiene actualmente una carga máxima de CPU superior al 50 % de uso de la CPU respecto al tamaño de proceso P2 (mediodía del martes). Si la CPU es el factor dominante en el perfil de recursos de la aplicación, puede decidir que P2 es el tamaño de proceso adecuado para garantizar que la carga de trabajo siempre sea la correcta. Si espera que una aplicación crezca con el tiempo, es una buena idea tener un búfer de recursos adicionales para que la aplicación no alcance el límite del nivel de rendimiento. Si aumenta el tamaño de proceso, puede ayudar a evitar errores visibles para el cliente que pueden producirse cuando una base de datos no tiene suficiente capacidad para procesar las solicitudes de manera eficaz, especialmente en entornos sensibles a la latencia. Un ejemplo es una base de datos que admite una aplicación que dibuja páginas web basadas en los resultados de las llamadas de base de datos.

Otros tipos de aplicaciones pueden interpretar el mismo gráfico de manera diferente. Por ejemplo, si una aplicación intenta procesar datos de nóminas todos los días y tiene el mismo gráfico, este tipo de modelo de "trabajo por lotes" podría funcionar bien en un tamaño de proceso P1. El tamaño de proceso P1 tiene 100 DTU en comparación con las 200 DTU en el tamaño de proceso P2. El tamaño de proceso P1 proporciona la mitad de rendimiento que el tamaño de proceso P2. Por lo tanto, el 50 por ciento de uso de CPU en P2 es igual al 100 por cien de uso de CPU en P1. Si la aplicación no tiene tiempos de espera, puede dar igual que un trabajo tarde 2 o 2,5 horas en completarse, siempre que se termine hoy. Una aplicación de esta categoría probablemente pueda usar un tamaño de proceso P1. Puede aprovechar el hecho de que hay períodos de tiempo durante el día en que el uso de recursos es menor, lo que significa que las "cargas elevadas" podrían retrasarse a uno de esos momentos más tarde ese día. El tamaño de proceso P1 podría ser conveniente para ese tipo de aplicación (y ahorrar dinero), siempre y cuando los trabajos se puedan finalizar a tiempo cada día.

El motor de base de datos muestra información sobre los recursos consumidos para cada base de datos activa en la vista **sys.resource_stats** de la base de datos **maestra** de cada servidor. Los datos de la tabla se agregan en intervalos de 5 minutos. Con los niveles de servicio Básico, Estándar y Premium, los datos pueden tardar más de cinco minutos en aparecer en la tabla, así que estos datos son mejores para el análisis histórico que para el análisis casi en tiempo real. Consulte la vista **sys.resource_stats** para ver el historial reciente de una base de datos y para validar si la reserva elegida proporciona el rendimiento que quiere cuando lo necesita.

> [!NOTE]
> En Azure SQL Database, debe conectarse a la base de datos **maestra** para poder consultar **sys.resource_stats** en los ejemplos siguientes.

En este ejemplo se muestra cómo se exponen los datos en esta vista:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![La vista de catálogo sys.resource_stats](./media/monitoring-with-dmvs/sys_resource_stats.png)

En el siguiente ejemplo se muestran distintas maneras en que puede usar la vista de catálogo **sys.resource_stats** para obtener información sobre cómo la base de datos usa los recursos:

1. Para ver el uso de recursos de la semana anterior en la base de datos userdb1, puede ejecutar esta consulta:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Para evaluar si la carga de trabajo se ajusta bien al tamaño de proceso, tiene que explorar en profundidad cada aspecto de las métricas de recursos: CPU, lecturas, escrituras, número de trabajadores y número de sesiones. Esta es una consulta revisada mediante **sys.resource_stats** para notificar los valores medio y máximo de estas métricas de recursos:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Con la información anterior sobre los valores promedio y máximo de cada métrica de recursos, puede evaluar si la carga de trabajo se ajusta bien al tamaño de proceso que eligió. Por lo general, los valores medios de **sys.resource_stats** son una buena referencia para el tamaño de destino. Debería ser su vara de medida principal. Por ejemplo, podría estar usando el nivel de servicio Estándar con el tamaño de proceso S2. Los porcentajes de uso medio de CPU y de lecturas y escrituras de E/S están por debajo del 40 por ciento, el número medio de trabajadores está por debajo de 50 y el número medio de sesiones está por debajo de 200. La carga de trabajo podría ajustarse al tamaño de proceso S1. Es fácil ver si la base de datos se ajusta a los límites de trabajadores y de sesión. Para ver si una base de datos se ajusta a un tamaño de proceso inferior con respecto a CPU, lecturas y escrituras, divida el número de DTU del tamaño de proceso inferior por el número de DTU de su tamaño de proceso actual y multiplique el resultado por 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    El resultado es la diferencia porcentual de rendimiento relativa entre los dos tamaños de proceso. Si el uso de recursos no supera esta cantidad, la carga de trabajo podría ajustarse al tamaño de proceso inferior. Sin embargo, debe examinar todos los intervalos de valores de uso de recursos y determinar, según el porcentaje, con qué frecuencia se ajustaría la carga de trabajo de la base de datos al tamaño de proceso inferior. La siguiente consulta proporciona el porcentaje de ajuste por dimensión de recursos según el umbral del 40 % que hemos calculado en este ejemplo.

   ```sql
    SELECT
        100*((COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name)) AS 'CPU Fit Percent',
        100*((COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name)) AS 'Log Write Fit Percent',
        100*((COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name)) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'sample' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    En función de su objetivo de nivel de servicio de la base de datos, puede decidir si la carga de trabajo se ajusta al tamaño de proceso inferior. Si el objetivo de la carga de trabajo de la base de datos es del 99,9 % y la consulta anterior devuelve valores mayores que el 99,9 % para las tres dimensiones de recursos, es probable que la carga de trabajo se ajuste al tamaño de proceso inferior.

    El porcentaje de ajuste también ofrece información detallada sobre si debería pasar al siguiente tamaño de proceso superior para cumplir su objetivo. Por ejemplo, userdb1 muestra el siguiente uso de CPU durante la semana pasada:

   | Porcentaje medio de CPU | Porcentaje máximo de CPU |
   | --- | --- |
   | 24,5 |100,00 |

    El promedio de CPU es aproximadamente un cuarto del límite del tamaño de proceso, que se ajustaría bien al tamaño de proceso de la base de datos. Sin embargo, el valor máximo muestra que la base de datos alcanza el límite del tamaño de proceso. ¿Necesita pasar al siguiente tamaño de proceso superior? Observe cuántas veces la carga de trabajo alcanza el 100 % y compárelo con el objetivo de la carga de trabajo de la base de datos.

    ```sql
     SELECT
         100*((COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name)) AS 'CPU Fit Percent',
         100*((COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name)) AS 'Log Write Fit Percent',
         100*((COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name)) AS 'Physical Data IO Fit Percent'
     FROM sys.resource_stats
     WHERE database_name = 'sample' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Si esta consulta devuelve un valor inferior al 99,9 por ciento para cualquiera de las tres dimensiones de recursos, plantéese subir al siguiente tamaño de proceso o usar técnicas de optimización de aplicaciones para reducir la carga en la base de datos.

4. Este ejercicio también tiene en cuenta el aumento de la carga de trabajo proyectada en el futuro.

En los grupos elásticos, puede supervisar bases de datos individuales del grupo con las técnicas descritas en esta sección. Sin embargo, también puede supervisar el grupo en conjunto. Para más información, consulte el artículo sobre la [supervisión y administración de un grupo de bases de datos elásticas](elastic-pool-overview.md).

### <a name="maximum-concurrent-requests"></a>Número máximo de solicitudes simultáneas

Para ver el número de solicitudes simultáneas, ejecute esta consulta Transact-SQL en la base de datos:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Para analizar la carga de trabajo de una base de datos SQL Server, modifique esta consulta para filtrar por la base de datos específica que quiere analizar. Por ejemplo, si tiene una base de datos local denominada MyDatabase, esta consulta Transact-SQL devuelve el número de solicitudes simultáneas en esa base de datos.

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Se trata simplemente de una instantánea en un solo momento dado. Para comprender mejor la carga de trabajo y los requisitos de solicitudes simultáneas, debe recopilar muchas muestras durante un período de tiempo.

### <a name="maximum-concurrent-logins"></a>Número máximo de inicios de sesión simultáneos

Puede analizar los patrones de usuario y aplicación para hacerse una idea de la frecuencia de los inicios de sesión. También podría ejecutar cargas reales en un entorno de prueba para asegurarse de que no está alcanzando este u otros límites descritos en este artículo. No hay una ninguna consulta o vista de administración dinámica (DMV) individuales que puedan mostrar el número o el historial de inicios de sesión simultáneos.

Si varios clientes usan la misma cadena de conexión, el servicio autentica cada inicio de sesión. Si 10 usuarios se conectan de forma simultánea a una base de datos con el mismo nombre de usuario y contraseña, habrá 10 conexiones simultáneas. Este límite se aplica solo a la duración del inicio de sesión y la autenticación. Si los mismos 10 usuarios se conectan a la base de datos de forma secuencial, el número de inicios de sesión simultáneos nunca será superior a uno.

> [!NOTE]
> Este límite no se aplica actualmente a las bases de datos de grupos elásticos.

### <a name="maximum-sessions"></a>Número máximo de sesiones

Para ver el número de sesiones activas actuales, ejecute esta consulta de Transact-SQL en la base de datos:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

Si va a analizar una carga de trabajo de SQL Server, modifique la consulta para centrarse en una base de datos específica. Esta consulta le ayuda a determinar las posibles necesidades de sesión para esa base de datos si la mueve a Azure.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

Nuevamente, estas consultas devuelven un número puntual. Si recopila varias muestras a lo largo de un tiempo, entenderá mejor el uso de la sesión.

Puede obtener estadísticas históricas sobre las sesiones consultando la vista [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) y revisando la columna **active_session_count**.

## <a name="monitoring-query-performance"></a>Supervisión del rendimiento de las consultas

Las consultas de ejecución lenta o prolongada pueden consumir una cantidad significativa de recursos del sistema. En esta sección se muestra cómo usar vistas de administración dinámica para detectar algunos problemas comunes de rendimiento de las consultas.

### <a name="finding-top-n-queries"></a>Búsqueda de las N mejores consultas

El siguiente ejemplo devuelve información acerca de las cinco consultas principales clasificadas en función del tiempo promedio de CPU. Este ejemplo agrega las consultas conforme a sus hash de consulta, por lo que las consultas lógicamente equivalentes se agrupan por sus consumos de recursos acumulativos.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Supervisión de consultas bloqueadas

Las consultas lentas o de larga ejecución pueden contribuir al consumo excesivo de recursos y ser la consecuencia de consultas bloqueadas. La causa del bloqueo puede ser un diseño deficiente de las aplicaciones, los planes de consulta incorrectos, la falta de índices útiles, etc. Puede usar la vista sys.dm_tran_locks para obtener información sobre la actividad de bloqueo actual en su base de datos. Para obtener código de ejemplo, consulte [sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql). Para obtener más información sobre la solución de problemas de bloqueo, consulte [Descripción y resolución de problemas de bloqueo en Azure SQL](understand-resolve-blocking.md).

### <a name="monitoring-query-plans"></a>Supervisión de planes de consulta

Un plan de consulta ineficaz también puede aumentar el consumo de CPU. En el ejemplo siguiente, se usa la vista [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) para determinar qué consulta emplea la mayor cantidad acumulativa de CPU.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Consulte también

[Introducción a Azure SQL Database e Instancia administrada de Azure SQL](sql-database-paas-overview.md)

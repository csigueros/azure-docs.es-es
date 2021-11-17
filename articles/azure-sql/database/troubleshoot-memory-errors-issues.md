---
title: Solución de problemas de memoria
titleSuffix: Azure SQL Database
description: Aquí se proporcionan los pasos para solucionar problemas de memoria en Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 11/03/2021
ms.openlocfilehash: 372247d1fbb189f9662bf4d40be0c3779bf1d23c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579390"
---
# <a name="troubleshoot-out-of-memory-errors-with-azure-sql-database"></a>Solución de problemas de errores de memoria con Azure SQL Database  
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Es posible que vea mensajes de error cuando el motor de base de datos SQL no ha podido asignar suficiente memoria para ejecutar la consulta. Esto puede deberse a varias razones, como los límites del objetivo de servicio seleccionado, las demandas de la memoria de carga de trabajo agregadas y las demandas de memoria de la consulta. Para obtener más información sobre el límite de recursos de memoria para las instancias de Azure SQL Database, consulte [Administración de recursos en Azure SQL Database](resource-limits-logical-server.md#memory).

> [!NOTE]
> **Este artículo está orientado a Azure SQL Database.** Para obtener más información sobre cómo solucionar problemas de memoria en SQL Server, consulte [MSSQLSERVER_701](/sql/relational-databases/errors-events/mssqlserver-701-database-engine-error).

Pruebe las siguientes vías de investigación en respuesta a:

- Código de error 701 con el mensaje de error "No hay suficiente memoria del sistema en el grupo de recursos '%ls' para ejecutar esta consulta".
- Código de error 802 con el mensaje de error "No hay memoria suficiente disponible en el grupo de búferes".

## <a name="investigate-memory-allocation"></a>Investigación de la asignación de memoria

Si los errores de memoria no se conservan en Azure SQL Database, considere la posibilidad de aumentar temporalmente el objetivo de nivel de servicio de la base de datos en Azure Portal. Si los errores de memoria insuficiente persisten, use las siguientes consultas para buscar concesiones de memoria de consulta inusualmente altas que puedan contribuir a una condición de memoria insuficiente. Ejecute las consultas de ejemplo siguientes en la base de datos que experimentó el error (no en la base de datos `master` del servidor lógico de Azure SQL).  

### <a name="use-dmvs-to-view-memory-clerks"></a>Uso de DMV para ver los distribuidores de memoria

Comience con una investigación amplia, si el error de memoria se produjo recientemente, consultando la asignación de memoria a los distribuidores de memoria. Los distribuidores de memoria son elementos internos del motor de base de datos para esta instancia de Azure SQL Database. Los principales distribuidores de memoria en términos de páginas asignadas pueden ser informativos para saber qué tipo de consulta o característica de SQL Server consume más memoria. 


```sql
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY pages_kb DESC;
GO
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY virtual_memory_committed_kb DESC;
```

 - Algunos distribuidores de memoria comunes, como MEMORYCLERK_SQLQERESERVATIONS, se resuelven mejor mediante la identificación de consultas con concesiones de memoria grandes, y puede mejorar su rendimiento con una mejor indexación y optimización de índices. 
 - Aunque OBJECTSTORE_LOCK_MANAGER no está relacionado con las concesiones de memoria, se espera que sea alto cuando las consultas reclamen muchos bloqueos; por ejemplo, debido a una extensión de bloqueo deshabilitada o a transacciones muy grandes. 
 - Se espera que algunos distribuidores de memoria tengan el uso más alto: MEMORYCLERK_SQLBUFFERPOOL es casi siempre el primer distribuidor, mientras que CACHESTORE_COLUMNSTOREOBJECTPOOL será alto cuando se usen índices de almacén de columnas. Se espera una mayor utilización por parte de estos distribuidores de memoria. 

 Para obtener más información sobre los distribuidores de memoria, consulte [sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql). 

### <a name="use-dmvs-to-investigate-active-queries"></a>Uso de DMV para investigar consultas activas 

En la mayoría de los casos, la transacción que no se ha realizado no es la causa de este error. 

La consulta de ejemplo siguiente para Azure SQL Database devuelve información importante sobre las transacciones que actualmente mantienen o esperan concesiones de memoria. Examine las consultas principales identificadas para el examen y el ajuste del rendimiento y evalúe si se ejecutan según lo previsto. Tenga en cuenta el tiempo de las consultas de informes que consumen mucha memoria o las operaciones de mantenimiento.

```sql
--Active requests with memory grants
SELECT
--Session data 
  s.[session_id], s.open_transaction_count
--Memory usage
, r.granted_query_memory, mg.grant_time, mg.requested_memory_kb, mg.granted_memory_kb, mg.required_memory_kb, mg.used_memory_kb, mg.max_used_memory_kb     
--Query 
, query_text = t.text, input_buffer = ib.event_info, query_plan_xml = qp.query_plan, request_row_count = r.row_count, session_row_count = s.row_count
--Session history and status
, s.last_request_start_time, s.last_request_end_time, s.reads, s.writes, s.logical_reads, session_status = s.[status], request_status = r.status
--Session connection information
, s.host_name, s.program_name, s.login_name, s.client_interface_name, s.is_user_process
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests AS r 
    ON r.[session_id] = s.[session_id]
LEFT OUTER JOIN sys.dm_exec_query_memory_grants AS mg 
    ON mg.[session_id] = s.[session_id]
OUTER APPLY sys.dm_exec_sql_text (r.[sql_handle]) AS t
OUTER APPLY sys.dm_exec_input_buffer(s.[session_id], NULL) AS ib 
OUTER APPLY sys.dm_exec_query_plan (r.[plan_handle]) AS qp 
WHERE mg.granted_memory_kb > 0
ORDER BY mg.granted_memory_kb desc, mg.requested_memory_kb desc;
```

Puede decidir usar la instrucción KILL para detener una consulta que se está ejecutando actualmente y que está manteniendo o esperando una concesión de memoria grande. Sin embargo, utilice esta instrucción con sumo cuidado, especialmente cuando se estén ejecutando procesos críticos para su negocio. Para obtener más información, consulte [KILL &#40;Transact-SQL&#41;](/sql/t-sql/language-elements/kill-transact-sql). 


### <a name="use-query-store-to-investigate-past-query-memory-usage"></a>Uso de Almacén de consultas para investigar el uso de memoria de consulta anterior

Aunque la consulta de ejemplo anterior solo informa de los resultados de la consulta en directo, la consulta siguiente usa el [Almacén de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para devolver información sobre la ejecución de consultas anteriores. Esto puede serle útil para investigar un error de memoria que se produjo en el pasado.

La consulta de ejemplo siguiente para Azure SQL Database devuelve información importante sobre las ejecuciones de consultas que haya registrado el Almacén de consultas. Examine las consultas principales identificadas para el examen y el ajuste del rendimiento y evalúe si se ejecutan según lo previsto. Tenga en cuenta el filtro de tiempo en `qsp.last_execution_time` para restringir los resultados al historial reciente. Puede ajustar la cláusula TOP para generar más o menos resultados en función de su entorno.

```sql
SELECT TOP 10 PERCENT --limit results
  a.plan_id, query_id, plan_group_id, query_sql_text
, query_plan = TRY_CAST(query_plan as XML)
, avg_query_max_used_memory
, min_query_max_used_memory
, max_query_max_used_memory
, last_query_max_used_memory 
, last_execution_time  
, query_count_executions
    FROM (
    SELECT 
      qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    , last_execution_time = MAX(qsp.last_execution_time)
    , query_count_executions = SUM(qsrs.count_executions) 
    , avg_query_max_used_memory = AVG(qsrs.avg_query_max_used_memory)
    , min_query_max_used_memory = MIN(qsrs.min_query_max_used_memory)
    , max_query_max_used_memory = MAX(qsrs.max_query_max_used_memory)
    , last_query_max_used_memory = MAX(qsrs_latest.last_query_max_used_memory) --only from latest result
    FROM sys.query_store_plan AS qsp 
    INNER JOIN sys.query_store_query AS qsq
        ON qsp.query_id = qsq.query_id
    INNER JOIN sys.query_store_query_text AS qsqt
        ON qsq.query_text_id = qsqt.query_text_id 
    INNER JOIN sys.query_store_runtime_stats AS qsrs
        ON qsp.plan_id = qsrs.plan_id 
    INNER JOIN (SELECT plan_id
            , last_query_max_used_memory 
            , rownum = ROW_NUMBER() OVER (PARTITION BY plan_id ORDER BY last_execution_time DESC)
            FROM sys.query_store_runtime_stats qsrs) AS qsrs_latest
        ON qsrs_latest.plan_id = qsp.plan_id
        AND qsrs_latest.rownum = 1 --use latest last_query_max_used_memory per plan_id
    WHERE DATEADD(hour, -24, sysdatetime()) < qsp.last_execution_time --past 24 hours only
    AND qsrs_latest.last_query_max_used_memory > 0
    GROUP BY qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    ) AS a
ORDER BY max_query_max_used_memory DESC, avg_query_max_used_memory DESC;
```

### <a name="extended-events"></a>Eventos extendidos
Además de la información anterior, puede ser útil capturar el seguimiento de las actividades en el servidor para investigar exhaustivamente un problema de memoria en Azure SQL Database. 

Hay dos maneras de capturar los seguimientos en SQL Server; eventos extendidos (XEvents) y los de Profiler. Sin embargo, [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) es una tecnología de seguimiento en desuso que no es compatible con Azure SQL Database. Los [eventos extendidos](/sql/relational-databases/extended-events/extended-events) son la nueva tecnología de seguimiento que ofrece mayor versatilidad y menor impacto en el sistema observado y su interfaz está integrada en SQL Server Management Studio (SSMS). 

Consulte el documento en el que se explica cómo usar el [Asistente para nueva sesión de eventos extendidos](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) en SSMS. No obstante, en el caso de las bases de datos de Azure SQL, SSMS proporciona una subcarpeta de eventos extendidos en cada base de datos del Explorador de objetos. Use una sesión de eventos extendidos para capturar estos eventos útiles e identificar las consultas que los generan: 

-   Errores de categoría:
    - error_reported
    - exchange_spill
    - hash_spill_details
    
-   Ejecución de categoría:
    - excessive_non_grant_memory_used

-   Memoria de categoría:
    - query_memory_grant_blocking
    - query_memory_grant_usage

La captura de bloques de concesión de memoria, desbordamientos de concesión de memoria o concesiones de memoria excesivas puede ser una pista potencial que indique que una consulta tomó repentinamente más memoria de la que tenía en el pasado y una posible explicación de un error emergente de memoria que esté fuera de la memoria en una carga de trabajo existente.

### <a name="in-memory-oltp-out-of-memory"></a>OLTP en memoria sin memoria 

Puede encontrarse `Error code 41805: There is insufficient memory in the resource pool '%ls' to run this operation` si usa OLTP en memoria. Reduzca la cantidad de datos en tablas optimizadas para memoria y parámetros con valores de tabla optimizados para memoria, o escale verticalmente la base de datos a un objetivo de servicio superior para tener más memoria. Para obtener más información sobre los problemas de memoria con el OLTP en memoria de SQL Server, consulte [Solución a problemas de memoria fuera de memoria](/sql/relational-databases/in-memory-oltp/resolve-out-of-memory-issues).

### <a name="get-azure-sql-db-support"></a>Obtener compatibilidad con Azure SQL DB 

Si los errores de falta de memoria persisten en Azure SQL Database, presente una solicitud de soporte técnico de Azure seleccionando **Obtener soporte técnico** en el sitio [Soporte técnico de Azure](https://azure.microsoft.com/support/options).

## <a name="next-steps"></a>Pasos siguientes

- [Procesamiento de consultas inteligente en bases de datos SQL](/sql/relational-databases/performance/intelligent-query-processing)
- [Guía de arquitectura de procesamiento de consultas](/sql/relational-databases/query-processing-architecture-guide)    
- [Centro de rendimiento para el motor de base de datos SQL Server y Azure SQL Database](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database.md)  
- [Solución de problemas de conectividad y otros errores con Azure SQL Database y Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
- [Solución de problemas de errores de conexión transitorios en SQL Database e Instancia administrada de SQL](troubleshoot-common-connectivity-issues.md)
- [Demostración del procesamiento de consultas inteligentes](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/intelligent-query-processing)   
- [Administración de recursos en Azure SQL Database](resource-limits-logical-server.md#memory).

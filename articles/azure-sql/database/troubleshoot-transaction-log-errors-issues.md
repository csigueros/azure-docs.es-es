---
title: Solución de problemas del registro de transacciones
titleSuffix: Azure SQL Database and Azure SQL Managed Instance
description: Proporciona los pasos para solucionar problemas del registro de transacciones de Azure SQL Database en Azure SQL Database o Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 0b9e47d23968c29a0c69a2da198bcf8d183f1bb0
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675254"
---
# <a name="troubleshooting-transaction-log-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Solución de errores del registro de transacciones con Azure SQL Database y Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Es posible que vea los errores 9002 o 40552 cuando el registro de transacciones esté lleno y no pueda aceptar nuevas transacciones. Estos errores se producen cuando el registro de transacciones de base de datos, administrado por Azure SQL Database o Azure SQL Managed Instance, supera los umbrales de espacio y no puede seguir aceptando transacciones. 

Estos errores son similares a los problemas de un registro de transacciones lleno en SQL Server, pero tienen resoluciones diferentes en Azure SQL Database o Azure SQL Managed Instance.

> [!NOTE]
> **Este artículo se centra en Azure SQL Database y Azure SQL Managed Instance.** Azure SQL Database y Azure SQL Managed Instance se basan en la versión estable más reciente del motor de base de datos de Microsoft SQL Server, por lo que gran parte del contenido es similar, aunque las herramientas y las opciones de solución de problemas pueden diferir. Para más información sobre cómo solucionar problemas de un registro de transacciones en SQL Server, vea [Solucionar problemas de un registro de transacciones lleno (Error 9002 de SQL Server)](/sql/relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002).

## <a name="automated-backups-and-the-transaction-log"></a>Copias de seguridad automatizadas y el registro de transacciones

Hay algunas diferencias clave entre Azure SQL Database y Azure SQL Managed Instance en lo que respecta a la administración del espacio de archivos de base de datos. 

- En Azure SQL Database o Azure SQL Managed Instance, la copia de seguridad del registro de transacciones se realiza automáticamente. Para más información, la frecuencia y la retención, consulte [Copias de seguridad automatizadas: Azure SQL Database y SQL Managed Instance](automated-backups-overview.md). 
- En Azure SQL Database, también se administra el espacio libre en disco, el crecimiento del archivo de base de datos y la ubicación del archivo, por lo que las causas y las soluciones típicas de los problemas del registro de transacciones son diferentes de SQL Server. 
- En Azure SQL Managed Instance, la ubicación y el nombre de los archivos de base de datos no se pueden administrar, pero los administradores pueden administrar los archivos de base de datos y la configuración del crecimiento automático de archivos. Las causas y soluciones típicas de los problemas del registro de transacciones son similares a SQL Server. 

Al igual que SQL Server, el registro de transacciones de cada base de datos se trunca siempre que se realiza una copia de seguridad de registros. El truncamiento deja espacio vacío en el archivo de registro, al que pueden acceder las nuevas transacciones. Cuando las copias de seguridad de registros no pueden truncar el archivo de registro, este crece para dar cabida a nuevas transacciones. Si el archivo de registro crece hasta sus límites máximos en Azure SQL Database o Azure SQL Managed Instance, no se pueden aceptar nuevas transacciones. Se trata de un escenario muy inusual.

## <a name="prevented-transaction-log-truncation"></a>Truncamiento del registro de transacciones impedido

Para detectar lo que impide el truncamiento del registro en un caso determinado, consulte `log_reuse_wait_desc` en `sys.databases`. La espera de reutilización de registros le informa de qué condiciones o causas impiden que el registro de transacciones se trunque mediante una copia de seguridad de registros normal. Para obtener más información, vea [sys.databases &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql). 

```sql
SELECT [name], log_reuse_wait_desc FROM sys.databases;
```

Los siguientes valores de `log_reuse_wait_desc` en `sys.databases` pueden indicar el motivo por el que se impide el truncamiento del registro de transacciones de la base de datos:

| log_reuse_wait_desc | Diagnóstico | Respuesta necesaria |
|--|--|--|
| **NOTHING** | Estado típico. No hay nada que bloquee el truncamiento del registro. | No. |
| **CHECKPOINT** | Se necesita un punto de control para el truncamiento del registro. Poco frecuente. | No se requiere ninguna respuesta a menos que sea persistente. Si persiste, abra una solicitud de soporte técnico en [Soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **LOG BACKUP** | Una copia de seguridad de registros está en curso. | No se requiere ninguna respuesta a menos que sea persistente. Si persiste, abra una solicitud de soporte técnico en [Soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **ACTIVE BACKUP OR RESTORE** | Hay una copia de seguridad de base de datos en curso. | No se requiere ninguna respuesta a menos que sea persistente. Si persiste, abra una solicitud de soporte técnico en [Soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **ACTIVE TRANSACTION** | Una transacción en curso impide el truncamiento del registro. | El archivo de registro no se puede truncar debido a transacciones activas o no confirmadas. Consulte la siguiente sección.| 
| **REPLICATION** | En Azure SQL Database, probablemente debido a la característica de [captura de datos modificados (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).<BR>En Azure SQL Managed Instance, debido a la [replicación](../managed-instance/replication-transactional-overview.md) o CDC. | En Azure SQL Database, consulte [sys.dm_cdc_errors](/sql/relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors) y resuelva los errores. Si no se puede resolver, abra una solicitud de soporte técnico en [Soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support).<BR>En Azure SQL Managed Instance, si persiste, investigue los agentes implicados en CDC o la replicación. Para solucionar problemas de CDC, consulte los trabajos de [msdb.dbo.cdc_jobs](/sql/relational-databases/system-tables/dbo-cdc-jobs-transact-sql). Si no están presentes, agréguelos mediante [sys.sp_cdc_add_job](/sql/relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql). Para la replicación, considere la [Solución de problemas de la replicación transaccional](/sql/relational-databases/replication/troubleshoot-tran-repl-errors). Si no se puede resolver, abra una solicitud de soporte técnico en [Soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **AVAILABILITY_REPLICA** | La sincronización con la réplica secundaria está en curso. | No se requiere ninguna respuesta a menos que sea persistente. Si persiste, abra una solicitud de soporte técnico en [Soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support). | 

### <a name="log-truncation-prevented-by-an-active-transaction"></a>Truncamiento del registro impedido por una transacción activa

El escenario más común para un registro de transacciones que no puede aceptar nuevas transacciones es una transacción de larga duración o bloqueada.

Ejecute esta consulta de ejemplo para buscar transacciones no confirmadas o activas y sus propiedades.

- Devuelve información sobre las propiedades de transacciones desde [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql).
- Devuelve información de conexión de sesión desde [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql).
- Devuelve información de solicitud (para solicitudes activas), desde [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql). Esta consulta también se puede usar para identificar las sesiones que se están bloqueando; busque `request_blocked_by`. Para obtener más información sobre el bloqueo, vea [Recopilación de información de bloqueo](understand-resolve-blocking.md#gather-blocking-information). 
- Devuelve el texto de la solicitud actual o el texto del búfer de entrada mediante las DMV [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) o [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql). Si los datos devueltos por el campo `text` de `sys.dm_exec_sql_text` son NULL, la solicitud no está activa, pero tiene una transacción pendiente. En ese caso, el campo `event_info` de `sys.dm_exec_input_buffer` contendrá la última cadena de comandos pasada al motor de base de datos. 

```sql
SELECT [database_name] = db_name(s.database_id)
, tat.transaction_id, tat.transaction_begin_time, tst.session_id 
, session_open_transaction_count = tst.open_transaction_count --uncommitted and unrolled back transactions open. 
, transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime())
, input_buffer = ib.event_info
, request_text = CASE  WHEN r.statement_start_offset = 0 and r.statement_end_offset= 0 THEN left(est.text, 4000)
                       ELSE    SUBSTRING ( est.[text],    r.statement_start_offset/2 + 1, 
                                           CASE WHEN r.statement_end_offset = -1 THEN LEN (CONVERT(nvarchar(max), est.[text])) 
                                                ELSE r.statement_end_offset/2 - r.statement_start_offset/2 + 1
                                           END  )  END
, request_status = r.status
, request_blocked_by = r.blocking_session_id
, transaction_state = CASE tat.transaction_state    
                     WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                     WHEN 1 THEN 'The transaction has been initialized but has not started.'
                     WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                     WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                     WHEN 4 THEN 'The commit process has been initiated on the distributed transaction. This is for distributed transactions only. The distributed transaction is still active but further processing cannot take place.'
                     WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                     WHEN 6 THEN 'The transaction has been committed.'
                     WHEN 7 THEN 'The transaction is being rolled back.'
                     WHEN 8 THEN 'The transaction has been rolled back.' END 
, transaction_name = tat.name
, azure_dtc_state    --Applies to: Azure SQL Database only
             =    CASE tat.dtc_state 
                 WHEN 1 THEN 'ACTIVE'
                 WHEN 2 THEN 'PREPARED'
                 WHEN 3 THEN 'COMMITTED'
                 WHEN 4 THEN 'ABORTED'
                 WHEN 5 THEN 'RECOVERED' END
, transaction_type = CASE tat.transaction_type    WHEN 1 THEN 'Read/write transaction'
                                             WHEN 2 THEN 'Read-only transaction'
                                             WHEN 3 THEN 'System transaction'
                                             WHEN 4 THEN 'Distributed transaction' END
, tst.is_user_transaction
, local_or_distributed = CASE tst.is_local WHEN 1 THEN 'Local transaction, not distributed' WHEN 0 THEN 'Distributed transaction or an enlisted bound session transaction.' END
, transaction_uow    --for distributed transactions. 
, s.login_time, s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
, session_cpu_time = s.cpu_time, session_logical_reads = s.logical_reads, session_reads = s.reads, session_writes = s.writes
, observed = sysdatetimeoffset()
FROM sys.dm_tran_active_transactions AS tat 
INNER JOIN sys.dm_tran_session_transactions AS tst  on tat.transaction_id = tst.transaction_id
INNER JOIN Sys.dm_exec_sessions AS s on s.session_id = tst.session_id 
LEFT OUTER JOIN sys.dm_exec_requests AS r on r.session_id = s.session_id
CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib 
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) AS est;
```


### <a name="file-management-to-free-more-space"></a>Administración de archivos para liberar más espacio

Si se impide el truncamiento del registro de transacciones, liberar más espacio en la asignación de archivos de base de datos puede formar parte de la solución. Sin embargo, es clave resolver la raíz de la condición que bloquea el truncamiento del archivo de registro de transacciones. 

En algunos casos, la creación temporal de más espacio en disco permitirá que se complete una transacción de larga ejecución, lo que elimina la condición que impide que el archivo de registro de transacciones se trunque con una copia de seguridad normal del registro de transacciones. Sin embargo, liberar espacio en la asignación puede que solo proporcione una ayuda temporal hasta que el registro de transacciones vuelva a crecer. 

Para más información sobre cómo administrar el espacio de archivos de bases de datos y grupos elásticos, consulte [Administración del espacio de archivos para bases de datos en Azure SQL Database](file-space-manage.md).


### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Error 40552: La sesión ha terminado debido al uso excesivo del espacio de registro de transacciones

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Para resolver este problema, pruebe los métodos siguientes:

1. El problema puede producirse debido a operaciones de inserción, actualización o eliminación. Revise la transacción para evitar escrituras innecesarias. Intente reducir el número de filas que en las que se trabaja inmediatamente al implementar el procesamiento por lotes o al dividir las transacciones en varias más pequeñas. Para más información, consulte [Uso del procesamiento por lotes para mejorar el rendimiento de las aplicaciones de SQL Database](../performance-improve-use-batching.md).
2. El problema puede producirse debido a operaciones de recompilación de índices. Para evitar este problema, asegúrese de que se cumple la fórmula siguiente: (número de filas afectadas en la tabla) multiplicado por (tamaño medio del campo que se actualiza en bytes + 80) < 2 gigabytes (GB). En el caso de las tablas grandes, considere la posibilidad de crear particiones y realizar el mantenimiento de índices solo en algunas particiones de la tabla. Para obtener más información, consulte [Crear tablas e índices con particiones](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=azuresqldb-current&preserve-view=true).
3. Si realiza inserciones masivas con la utilidad `bcp.exe` o la clase `System.Data.SqlClient.SqlBulkCopy`, intente usar las opciones `-b batchsize` o `BatchSize` para limitar el número de filas copiadas al servidor en cada transacción. Para obtener más información, consulte [bcp Utility](/sql/tools/bcp-utility).
4. Si está volviendo a crear un índice con la instrucción `ALTER INDEX`, use las opciones `SORT_IN_TEMPDB = ON` y `ONLINE = ON`. Para más información, vea [ALTER INDEX (Transact-SQL)](/sql/t-sql/statements/alter-index-transact-sql).

> [!NOTE]
> Para más información sobre otros errores del regulador de recursos, consulte [Errores de gobernanza de recursos](troubleshoot-common-errors-issues.md#resource-governance-errors).

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de conectividad y otros errores con Azure SQL Database y Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
- [Solución de problemas de errores de conexión transitorios en SQL Database e Instancia administrada de SQL](troubleshoot-common-connectivity-issues.md)


Para información sobre los tamaños del registro de transacciones, consulte: 
- Para conocer los límites de recursos de los núcleos virtuales de una base de datos única, consulte los [límites de recursos para bases de datos únicas con el modelo de compra del núcleo virtual](resource-limits-vcore-single-databases.md).
- Para conocer los límites de recursos de núcleos virtuales para grupos elásticos, consulte los [límites de recursos para grupos elásticos con el modelo de compra del núcleo virtual](resource-limits-vcore-elastic-pools.md).
- Para conocer los límites de recursos de DTU para una base de datos única, consulte los [límites de recursos para bases de datos únicas que usan el modelo de compra de DTU](resource-limits-dtu-single-databases.md).
- Para conocer los límites de recursos de DTU para grupos elásticos, consulte los [límites de recursos para grupos elásticos que usan el modelo de compra de DTU](resource-limits-dtu-elastic-pools.md).
- Para los límites de recursos de SQL Managed Instance, consulte los [límites de recursos para SQL Managed Instance](../managed-instance/resource-limits.md).


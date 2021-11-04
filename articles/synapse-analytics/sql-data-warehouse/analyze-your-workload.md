---
title: Análisis de la carga de trabajo del grupo de SQL dedicado
description: Técnicas para analizar la priorización del grupo de SQL dedicado en Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/03/2021
ms.author: rortloff
ms.reviewer: wiassaf
ms.custom: azure-synapse
ms.openlocfilehash: d0dc05f32ca3f2fec1e19106e93178dcc40e4d0b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501350"
---
# <a name="analyze-your-workload-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Análisis de la carga de trabajo para el grupo de SQL dedicado en Azure Synapse Analytics

Técnicas para analizar la carga de trabajo del grupo de SQL dedicado en Azure Synapse Analytics. 

## <a name="resource-classes"></a>Clases de recursos

El grupo de SQL dedicado proporciona clases de recursos para asignar los recursos del sistema a las consultas.  Para más información acerca de las clases de recursos, consulte [Clases de recursos y administración de cargas de trabajo](resource-classes-for-workload-management.md).  Las consultas esperarán si la clase de recursos asignada a una consulta necesita más recursos de los que están disponibles actualmente.

## <a name="queued-query-detection-and-other-dmvs"></a>Detección de consulta en cola y otras DMV

Puede usar la DMV `sys.dm_pdw_exec_requests` para identificar las consultas que están a la espera en una cola de simultaneidad. Las consultas que esperan un espacio de simultaneidad tendrán el estado de **suspendido**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Los roles de administración de cargas de trabajo se pueden ver con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

La consulta siguiente muestra qué rol tiene asignado cada usuario.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

El grupo de SQL dedicado ofrece los siguientes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: se refiere a las consultas que residen fuera del marco del espacio de simultaneidad. Las funciones del sistema y las consultas DMV como `SELECT @@VERSION` son ejemplos de consultas locales.
* **UserConcurrencyResourceType**: se refiere a las consultas que residen dentro del marco del espacio de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.
* **DmsConcurrencyResourceType**: se refiere a las esperas que son el resultado de operaciones de movimiento de datos.
* **BackupConcurrencyResourceType**: se refiere a una espera que indica que se está creando la copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se solicitaron al mismo tiempo, las demás se pondrán en la cola. En general, se recomienda un tiempo mínimo de 10 minutos entre instantáneas consecutivas.

La DMV `sys.dm_pdw_waits` puede utilizarse para ver por qué recursos está esperando una solicitud.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

La DMV `sys.dm_pdw_resource_waits` muestra la información de espera para una consulta determinada. El tiempo de espera del recurso mide el tiempo de espera para que se proporcionen recursos. El tiempo de espera de la señal es el tiempo que tardan los servidores SQL subyacentes en programar la consulta en la CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

También puede usar la DMW `sys.dm_pdw_resource_waits` para calcular cuántas ranuras de simultaneidad se han concedido.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id();
```

La DMV `sys.dm_pdw_wait_stats` se puede utilizar para analizar las tendencias históricas de las esperas.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo administrar los usuarios y la seguridad de la base de datos, consulte [Protección de un grupo de SQL dedicado (anteriormente SQL DW)](sql-data-warehouse-overview-manage-security.md). Para más información sobre cómo las clases de recursos mayores pueden mejorar la calidad de los índices de almacén de columnas agrupado, consulte [Regeneración de índices para mejorar la calidad de los segmentos](sql-data-warehouse-tables-index.md#rebuild-indexes-to-improve-segment-quality).

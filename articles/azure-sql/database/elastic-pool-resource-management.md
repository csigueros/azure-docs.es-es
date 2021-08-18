---
title: Administración de recursos en grupos elásticos densos
description: Administre recursos de proceso en grupos elásticos de Azure SQL Database con muchas bases de datos.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 09/16/2020
ms.openlocfilehash: 48d037e4fe18f214af0f5ecaf9eb4e9b7e3ed59e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735580"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Administración de recursos en grupos elásticos densos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Los [grupos elásticos](./elastic-pool-overview.md) de Azure SQL Database son una solución rentable para administrar muchas bases de datos con un uso de recursos variable. Todas las bases de datos de un grupo elástico comparten la misma asignación de recursos, como CPU, memoria, subprocesos de trabajo, espacio de almacenamiento o tempdb, suponiendo que **solo un subconjunto de bases de datos del grupo usa recursos de proceso en un momento dado**. Este supuesto permite que los grupos elásticos sean rentables. En lugar de pagar por todos los recursos que podría necesitar cada base de datos individual, los clientes pagan por un conjunto de recursos mucho más pequeño, que comparten todas las bases de datos del grupo.

## <a name="resource-governance"></a>Regulación de recursos

El uso compartido de recursos requiere que el sistema controle cuidadosamente el uso de recursos para minimizar el efecto de "vecino ruidoso", donde una base de datos con un consumo elevado de recursos afecta a otras bases de datos del mismo grupo elástico. Al mismo tiempo, el sistema debe proporcionar recursos suficientes para características como alta disponibilidad y recuperación ante desastres (HADR), copia de seguridad y restauración, supervisión, Almacén de consultas y ajuste automático, entre otros, para que funcione de manera confiable.

Para lograr estos objetivos, Azure SQL Database emplea varios mecanismos de regulación de recursos, como [objetos de trabajo](/windows/win32/procthread/job-objects) de Windows para la regulación de recursos de nivel de proceso, el [Administrador de recursos del servidor de archivos (FSRM)](/windows-server/storage/fsrm/fsrm-overview) de Windows para la administración de cuotas de almacenamiento y una versión modificada y ampliada de [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) de SQL Server para implementar la regulación de recursos dentro de SQL Database.

El objetivo de diseño principal de los grupos elásticos es la rentabilidad. Por esta razón, el sistema permite a los clientes de forma intencionada crear grupos _densos_, es decir, grupos con un número de bases de datos que se aproximen o alcancen el máximo permitido, pero con una asignación moderada de recursos de proceso. Por la misma razón, el sistema no reserva todos los recursos potencialmente necesarios para sus procesos internos, sino que permite que se compartan entre estos procesos y las cargas de trabajo de usuario.

Este enfoque permite que los clientes usen grupos elásticos densos para conseguir un rendimiento adecuado y mayores ahorros en los costos. Sin embargo, si la carga de trabajo en muchas bases de datos de un grupo denso es suficientemente intensa, la contención de recursos puede ser considerable. La contención de recursos reduce el rendimiento de la carga de trabajo del usuario y puede afectar de forma negativa a los procesos internos.

> [!IMPORTANT]
> En los grupos densos con muchas bases de datos activas, puede no ser factible aumentar el número de bases de datos del grupo hasta los máximos documentados para los grupos elásticos por [DTU](resource-limits-dtu-elastic-pools.md) y [núcleo virtual](resource-limits-vcore-elastic-pools.md).
>
> El número de bases de datos que se puede colocar en los grupos densos sin causar problemas de contención de recursos y rendimiento depende del número de bases de datos activas simultáneamente y de los recursos consumidos por las cargas de trabajo de usuario en cada base de datos. Este número puede cambiar con el tiempo a medida que cambian las cargas de trabajo del usuario.

Cuando se produce la contención de recursos en un grupo empaquetado de forma densa, los clientes pueden elegir una o varias de las siguientes acciones para mitigarla:

- Ajuste la carga de trabajo de consulta para reducir el consumo de recursos o divida el consumo de recursos entre varias bases de datos a lo largo del tiempo.
- Reducir la densidad del grupo trasladando algunas bases de datos a otro grupo o convirtiéndolas en bases de datos independientes.
- Escalar verticalmente el grupo para obtener más recursos.

Para obtener sugerencias sobre cómo implementar las dos últimas acciones, consulte [Recomendaciones operativas](#operational-recommendations) más adelante en este artículo. La reducción de la contención de recursos beneficia tanto a las cargas de trabajo de los usuarios como a los proceso internos, y permite que el sistema mantenga de manera confiable el nivel de servicio esperado.

## <a name="monitoring-resource-consumption"></a>Supervisión del consumo de recursos

Para evitar la degradación del rendimiento debido a la contención de recursos, los clientes que usen grupos elásticos densos deben supervisar proactivamente el consumo de recursos y realizar acciones oportunas si el aumento de esta empieza a afectar a las cargas de trabajo. La supervisión continua es importante porque el uso de recursos en un grupo cambia con el tiempo debido a los cambios en la carga de trabajo del usuario, los cambios en los volúmenes de datos y la distribución, los cambios en la densidad del grupo y los cambios en servicio Azure SQL Database.

Azure SQL Database proporciona varias métricas que son de interés para este tipo de supervisión. Superar el valor medio recomendado de cada métrica indica la contención de recursos en el grupo y se debe solucionar mediante una de las acciones mencionadas anteriormente.

|Nombre de métrica|Descripción|Valor medio recomendado|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Uso de CPU del proceso de SQL asociado a un grupo elástico, medido por el sistema operativo subyacente. Disponible en la vista [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) de cada base de datos y en la vista [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de la base de datos `master`. Esta métrica también se emite para Azure Monitor, donde se [denomina](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` y se puede ver en Azure Portal. Este valor es el mismo para todas las bases de datos del mismo grupo elástico.|Por debajo del 70 %. Pueden ser aceptables picos breves ocasionales hasta el 90 %.|
|`max_worker_percent`|Utilización de [subprocesos de trabajo](/sql/relational-databases/thread-and-task-architecture-guide). Se proporciona para cada base de datos del grupo, así como para el propio grupo. Hay diferentes límites en el número de subprocesos de trabajo en el nivel de base de datos y en el nivel de grupo; por lo tanto, se recomienda supervisar esta métrica en ambos niveles. Disponible en la vista [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) de cada base de datos y en la vista [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de la base de datos `master`. Esta métrica también se emite para Azure Monitor, donde se [denomina](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` y se puede ver en Azure Portal.|Por debajo del 80 %. Los picos hasta el 100 % provocarán errores en los intentos de conexión y las consultas.|
|`avg_data_io_percent`|Uso de IOPS para la E/S física de lectura y escritura. Se proporciona para cada base de datos del grupo, así como para el propio grupo. Hay distintos límites en el número de IOPS en el nivel de base de datos y en el nivel de grupo; por lo tanto, se recomienda supervisar esta métrica en ambos niveles. Disponible en la vista [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) de cada base de datos y en la vista [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de la base de datos `master`. Esta métrica también se emite para Azure Monitor, donde se [denomina](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` y se puede ver en Azure Portal.|Por debajo del 80 %. Pueden ser aceptables picos breves ocasionales hasta el 100 %.|
|`avg_log_write_percent`|Usos del rendimiento para la E/S de escritura del registro de transacciones. Se proporciona para cada base de datos del grupo, así como para el propio grupo. Hay diferentes límites en el rendimiento de registros en el nivel de base de datos y en el nivel de grupo; por lo tanto, se recomienda supervisar esta métrica en ambos niveles. Disponible en la vista [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) de cada base de datos y en la vista [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de la base de datos `master`. Esta métrica también se emite para Azure Monitor, donde se [denomina](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` y se puede ver en Azure Portal. Cuando esta métrica esté cerca del 100 %, todas las modificaciones de base de datos (instrucciones INSERT, UPDATE, DELETE, MERGE, SELECT ... INTO, BULK INSERT, etc.) se ralentizarán.|Por debajo del 90 %. Pueden ser aceptables picos breves ocasionales hasta el 100 %.|
|`oom_per_second`|La tasa de errores de memoria insuficiente (OOM) en un grupo elástico, que es un indicador de la presión de memoria. Disponible en la vista [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database). Consulte la sección [Ejemplos](#examples) para ver una consulta de ejemplo para calcular esta métrica.|0|
|`avg_storage_percent`|Espacio de almacenamiento total usado por los datos de todas las bases de datos de un grupo elástico. No incluye el espacio vacío en los archivos de base de datos. Disponible en la vista [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de la base de datos `master`. Esta métrica también se emite para Azure Monitor, donde se [denomina](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` y se puede ver en Azure Portal.|Por debajo del 80 %. Puede aproximarse al 100 % en grupos sin crecimiento de datos.|
|`avg_allocated_storage_percent`|Espacio de almacenamiento total que usan los archivos de base de datos en el almacenamiento en todas las bases de datos de un grupo elástico. Incluye el espacio vacío en los archivos de base de datos. Disponible en la vista [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de la base de datos `master`. Esta métrica también se emite para Azure Monitor, donde se [denomina](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` y se puede ver en Azure Portal.|Por debajo del 90 %. Puede aproximarse al 100 % en grupos sin crecimiento de datos.|
|`tempdb_log_used_percent`|Utilización del espacio de registro de transacciones en la base de datos de `tempdb`. Aunque los objetos temporales creados en una base de datos no están visibles en otras bases de datos del mismo grupo elástico, `tempdb` es un recurso compartido por todas las bases de datos del mismo grupo. Una transacción de larga duración o huérfana en `tempdb` iniciada desde una base de datos del grupo puede consumir una gran parte del registro de transacciones y producir errores en las consultas de otras bases de datos del mismo grupo. Deriva de las vistas [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) y [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql). Esta métrica también se emite para Azure Monitor, y se puede ver en Azure Portal. Consulte la sección de [Ejemplos](#examples) para ver una consulta de ejemplo que devuelve el valor actual de esta métrica.|Por debajo del 50 % Son aceptables picos ocasionales de hasta el 80 %.|
|||

Además de estas métricas, Azure SQL Database proporciona una vista que devuelve los límites reales de la regulación de recursos, así como vistas adicionales que devuelven estadísticas de uso de recursos en el nivel de grupo de recursos y en el nivel de grupo de cargas de trabajo.

|Nombre de la vista|Descripción|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Devuelve la configuración y los valores de capacidad reales que usan los mecanismos de regulación de recursos en la base de datos o el grupo elástico actuales.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Devuelve información sobre el estado actual del grupo de recursos, la configuración actual de los grupos de recursos y las estadísticas acumuladas del grupo de recursos.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Devuelve las estadísticas acumuladas del grupo de cargas de trabajo y su configuración actual. Esta vista puede combinarse con sys.dm_resource_governor_resource_pools en la columna `pool_id` para obtener información del grupo de recursos.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Devuelve las estadísticas de uso del grupo de recursos de los últimos 32 minutos. Cada fila representa un intervalo de 20 segundos. Las columnas `delta_` devuelven el cambio en cada estadística durante el intervalo.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Devuelve las estadísticas de uso del grupo de cargas de trabajo de los últimos 32 minutos. Cada fila representa un intervalo de 20 segundos. Las columnas `delta_` devuelven el cambio en cada estadística durante el intervalo.|
|||

Estas vistas se pueden usar para supervisar el uso de recursos y solucionar problemas de contención de recursos casi en tiempo real. La carga de trabajo de usuario de las réplicas primarias y secundarias legibles, incluidas las réplicas geográficas, se clasifica en el grupo de recursos `SloSharedPool1` y en el grupo de cargas de trabajo `UserPrimaryGroup.DBId[N]`, donde `N` representa el valor del identificador de base de datos.

Además de supervisar el uso de recursos actual, los clientes que usan grupos densos pueden mantener datos de uso de recursos históricos en un almacén de datos independiente. Estos datos se pueden usar en análisis predictivos para administrar de forma proactiva el uso de recursos en función de las tendencias históricas y estacionales.

## <a name="operational-recommendations"></a>Recomendaciones operativas

**Deje suficiente espacio para los recursos**. En caso de producirse contención de recursos y degradación del rendimiento, la mitigación puede implicar sacar algunas bases de datos fuera del grupo elástico afectado o escalar verticalmente el grupo, como se indicó anteriormente. Sin embargo, para realizar estas acciones se requieren recursos de proceso adicionales. En concreto, en el caso de los grupos Premium y Crítico para la empresa, estas acciones requieren la transferencia de todos los datos de las bases de datos que se van a migrar o de todas las bases de datos del grupo elástico si el grupo se escala verticalmente. La transferencia de datos es una operación de larga duración que consume muchos recursos. Si el grupo ya tiene una elevada presión de recursos, la operación de mitigación en sí reducirá el rendimiento aún más. En casos extremos, puede que no sea posible resolver la contención de recursos mediante la migración de la base de datos o el escalado horizontal del grupo, ya que los recursos necesarios no están disponibles. En este caso, puede que la única solución pase por reducir temporalmente la carga de trabajo de consultas en el grupo elástico afectado.

Los clientes que usen grupos densos deben supervisar de cerca las tendencias de utilización de recursos descritas anteriormente y llevar a cabo acciones de mitigación mientras las métricas permanezcan dentro de los intervalos recomendados y todavía haya suficientes recursos en el grupo elástico.

La utilización de recursos depende de varios factores que cambian con el tiempo en cada base de datos y cada grupo elástico. Conseguir una relación óptima de precio/rendimiento en grupos densos requiere una supervisión y un reequilibrio continuos, que suponen migrar las bases de datos de los grupos más utilizados a los menos utilizados y crear grupos según sea necesario para dar cabida a una mayor carga de trabajo.

**No mueva las bases de datos "activas"** . Si la contención de recursos en el nivel de grupo se debe principalmente a un número pequeño de bases de datos muy utilizadas, puede ser tentador migrar estas bases de datos a un grupo menos utilizado o convertirlas en bases de datos independientes. Sin embargo, no es el enfoque recomendado mientras una base de datos presente una utilización muy alta, ya que la operación de migración disminuirá aún más el rendimiento, tanto para la base de datos que se migra como para todo el grupo. En su lugar, espere a que la alta utilización disminuya o migre las bases de datos menos utilizadas en lugar de aligerar la presión de los recursos en el nivel de grupo. Sin embargo, mover las bases de datos con una utilización muy baja no proporciona ninguna ventaja en este caso, ya que no reduce sustancialmente la utilización de recursos en el nivel de grupo.

**Cree bases de datos en un grupo de "cuarentena"** . En escenarios donde se crean con frecuencia bases de datos, como las aplicaciones que usan el modelo de inquilino por base de datos, existe el riesgo de que una nueva base de datos colocada en un grupo elástico existente consuma inesperadamente una cantidad considerable de recursos y afecte a otras bases de datos y procesos internos del grupo. Para mitigar este riesgo, cree un grupo de "cuarentena" aparte con asignación amplia de recursos. Use este grupo con bases de datos nuevas en las que se desconozcan los patrones de consumo de recursos. Una vez que una base de datos se ha mantenido en este grupo durante un ciclo comercial, como una semana o un mes, y se conoce su consumo de recursos, se puede migrar a un grupo con capacidad suficiente como para dar cabida a este uso adicional de recursos.

**Supervise el espacio usado y asignado**. Cuando el espacio de grupo asignado (tamaño total de todos los archivos de base de datos en almacenamiento para todas las bases de datos de un grupo) alcanza el tamaño máximo del grupo, pueden producirse errores de espacio insuficiente. Si el espacio asignado tiende a ser alto y está en camino de alcanzar el tamaño máximo del grupo, las opciones de mitigación incluyen:
- Sacar algunas bases de datos del grupo para reducir el espacio total asignado
- [Reducir](file-space-manage.md) archivos de base de datos para limitar el espacio asignado vacío en los archivos
- Escalar verticalmente el grupo a un objetivo de servicio con un tamaño de grupo máximo mayor

Si el espacio de grupo utilizado (tamaño total de datos en todas las bases de datos de un grupo, sin incluir el espacio vacío en los archivos) tiende a ser alto y está en camino de alcanzar el tamaño máximo del grupo, las opciones de mitigación incluyen:
- Sacar algunas bases de datos del grupo para reducir el espacio total utilizado
- Sacar (archivar) datos de la base de datos o eliminar los datos que ya no sean necesarios
- Implementar la [compresión de datos](/sql/relational-databases/data-compression/data-compression)
- Escalar verticalmente el grupo a un objetivo de servicio con un tamaño de grupo máximo mayor

**Evite servidores demasiado densos**. Azure SQL Database [admite](./resource-limits-logical-server.md) hasta 5000 bases de datos por servidor. Los clientes que usan grupos elásticos con miles de bases de datos podrían colocar varios grupos elásticos en un solo servidor, con el número total de bases de datos hasta el límite admitido. Sin embargo, los servidores con muchos miles de bases de datos presentan complicaciones operativas. Las operaciones que requieren la enumeración de todas las bases de datos de un servidor, por ejemplo, la visualización de las bases de datos en el portal, serán más lentas. Los errores operativos, como la modificación incorrecta de los inicios de sesión de nivel de servidor o las reglas de firewall, afectarán a un mayor número de bases de datos. La eliminación accidental del servidor requerirá la asistencia del servicio de soporte técnico de Microsoft para recuperar las bases de datos del servidor eliminado y provocará una interrupción prolongada en todas las bases de datos afectadas.

Se recomienda limitar el número de bases de datos por servidor a un número menor que el máximo admitido. En muchos escenarios, el uso de hasta 1000 o 2000 bases de datos por servidor se considera óptimo. Para reducir la probabilidad de eliminación accidental del servidor, se recomienda colocar un [bloqueo de eliminación](../../azure-resource-manager/management/lock-resources.md) en el servidor o en su grupo de recursos.

En el pasado, algunos escenarios que implicaban migrar las bases de datos entre grupos elásticos del mismo servidor, o meterlas o sacarlas de estos, eran más rápidos que migrar las bases de datos entre servidores. Actualmente, todas las migraciones de bases de datos se ejecutan a la misma velocidad, con independencia del servidor de origen y destino.

## <a name="examples"></a>Ejemplos

### <a name="monitoring-memory-utilization"></a>Supervisión del uso de memoria

Esta consulta calcula la métrica `oom_per_second` de cada grupo de recursos durante los últimos 32 minutos. Se puede ejecutar en cualquier base de datos de un grupo elástico.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Supervisión de la utilización del espacio de registros `tempdb`

Esta consulta devuelve el valor actual de la métrica `tempdb_log_used_percent`, que muestra la utilización relativa del registro de transacciones `tempdb` con respecto al tamaño máximo permitido. Se puede ejecutar en cualquier base de datos de un grupo elástico.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Pasos siguientes

- Se puede encontrar una introducción a los grupos elásticos en [Los grupos elásticos pueden ayudarle a administrar y escalar varias bases de datos de Azure SQL Database](./elastic-pool-overview.md).
- Para información sobre cómo optimizar las cargas de trabajo de consultas para reducir la utilización de recursos, consulte [Supervisión y ajuste](monitoring-tuning-index.yml) y [Optimización de la supervisión y el rendimiento](./monitor-tune-overview.md).

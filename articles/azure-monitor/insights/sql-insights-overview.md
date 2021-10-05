---
title: Supervisión de las implementaciones de SQL mediante SQL Insights (versión preliminar)
description: Introducción a SQL Insights en Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 76f65bffb2762735424084561dc0098f128bb7a2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548682"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Supervisión de las implementaciones de SQL mediante SQL Insights (versión preliminar)
SQL Insights es una solución completa para realizar la supervisión de cualquier producto de la [familia de Azure SQL](../../azure-sql/index.yml). SQL Insights usa las [vistas de administración dinámicas](../../azure-sql/database/monitoring-with-dmvs.md) para exponer los datos que necesita para supervisar el estado, diagnosticar problemas y optimizar el rendimiento.  

Asimismo, SQL Insights realiza toda la supervisión de forma remota. Los agentes de supervisión en máquinas virtuales dedicadas se conectan a los recursos de SQL y recopilan datos de forma remota. Los datos recopilados se almacenan en los [registros de Azure Monitor](../logs/data-platform-logs.md) para pemitir la agregación, el filtrado y el análisis de tendencias de forma fácil. Puede ver los datos recopilados de la [plantilla de libro](../visualize/workbooks-overview.md) de SQL Insights o puede profundizar directamente en los datos mediante las [consultas del registro](../logs/get-started-queries.md).

## <a name="pricing"></a>Precios
No hay ningún costo directo para SQL Insights. Todos los costos se producen debido a las máquinas virtuales que recopilan los datos, las áreas de trabajo de Log Analytics que almacenan los datos y las reglas de alertas configuradas en los datos. 

### <a name="virtual-machines"></a>Máquinas virtuales

En el caso de las máquinas virtuales, se le cobrará en función del precio publicado en la [Página de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). El número de máquinas virtuales necesarias varía en función del número de cadenas de conexión que quiera supervisar. Se recomienda asignar una máquina virtual de tamaño Standard_B2s por cada 100 cadenas de conexión. Consulte los [requisitos de la máquina virtual de Azure](sql-insights-enable.md#azure-virtual-machine-requirements) para obtener más detalles.

### <a name="log-analytics-workspaces"></a>Áreas de trabajo de Log Analytics

En el caso de las áreas de trabajo de Log Analytics, se le cobrará en función de los precios publicados en la [Página de precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Las áreas de trabajo de Log Analytics que usa SQL Insights generarán costos de ingesta de datos, retención de datos y, opcionalmente, de exportación de datos. 

Los cargos exactos variarán en función de la cantidad de datos ingeridos, retenidos y exportados. La cantidad de estos datos variará en función de la actividad de la base de datos y de la configuración de la recopilación definida en los [perfiles de supervisión](sql-insights-enable.md#create-sql-monitoring-profile).

### <a name="alert-rules"></a>Las reglas de alertas

En el caso de las reglas de alertas de Azure Monitor, se le cobrará en función de los precios publicados en la [Página de precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Si decide [crear alertas con SQL Insights](sql-insights-alerts.md), se le cobrará por las reglas de alertas creadas y las notificaciones enviadas.

## <a name="supported-versions"></a>Versiones compatibles 
SQL Insights admite la actualización de las siguientes versiones de SQL Server:
- SQL Server 2012 y versiones posteriores

SQL Insights admite que SQL Server se ejecute en los siguientes entornos:
- Azure SQL Database
- Instancia administrada de Azure SQL
- SQL Server en Azure Virtual Machines (instancia de SQL Server que se ejecuta en máquinas virtuales registradas con el proveedor de [máquinas virtuales de SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md))
- VM de Azure (instancia de SQL Server que se ejecuta en máquinas virtuales que no están registradas con el proveedor de [máquinas virtuales de SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md))

SQL Insights no ofrece compatibilidad o tiene compatibilidad limitada con:
- **Instancias que no son de Azure**: la ejecución de SQL Server en máquinas virtuales fuera de Azure no se admite.
- **Grupos eslásticos de Azure SQL Database**: no se pueden recopilar métricas para grupos elásticos ni para bases de datos dentro de grupos elásticos.
- **Niveles de servicio bajos de Azure SQL Database**: no se pueden recopilar métricas para bases de datos de los [niveles de servicio](../../azure-sql/database/resource-limits-dtu-single-databases.md) Básico, S0, S1 y S2.
- **Nivel sin servidor de Azure SQL Database**: se pueden recopilar métricas para bases de datos que usan el nivel de proceso sin servidor. Sin embargo, el proceso de recopilación de métricas restablecerá el temporizador de retraso de pausa automática, lo que impide que la base de datos entre en un estado de pausa automática.
- **Réplicas secundarias**: solo se pueden recopilar métricas para una única réplica secundaria por base de datos. Si una base de datos tiene más de una réplica secundaria, solo se puede supervisar una.
- **Autenticación con Azure Active Directory**: el único método admitido de [autenticación](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) para la supervisión es la autenticación de SQL. En SQL Server en Azure Virtual Machines, no se admite la autenticación mediante Active Directory en un controlador de dominio personalizado.  

## <a name="opening-sql-insights"></a>Apertura de SQL Insights
Para abrir SQL Insights:

1. En Azure Portal, vaya al menú de **Azure Monitor**.
1. En la sección **Información**, seleccione **SQL (preview)** (SQL [versión preliminar]). 
1. Seleccione un icono para cargar la experiencia del recurso de SQL que está supervisando.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Captura de pantalla que muestra SQL Insights en Azure Portal.":::

Para más instrucciones, consulte [Habilitación de SQL Insights](sql-insights-enable.md) y [Solución de problemas de SQL Insights](sql-insights-troubleshoot.md).

## <a name="collected-data"></a>Datos recopilados
Asimismo, SQL Insights realiza toda la supervisión de forma remota. No hay ningún agente instalado en las máquinas virtuales que ejecutan SQL Server. 

SQL Insights usa máquinas virtuales de supervisión dedicadas para recopilar de forma remota datos de los recursos de SQL. Cada máquina virtual de supervisión tendrá instalados el [agente de Azure Monitor](../agents/azure-monitor-agent-overview.md) y la extensión Workload Insights (WLI). 

La extensión WLI incluye el [agente de Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) de código abierto. SQL Insights usa [reglas de recopilación de datos](../agents/data-collection-rule-overview.md) para especificar la configuración de la recopilación de datos para el [complemento de SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/) de Telegraf.

Existen diferentes conjuntos de datos disponibles para Azure SQL Database, Azure SQL Managed Instance y SQL Server. En las siguientes tablas se describen los datos disponibles. Puede personalizar qué conjuntos de datos se van a recopilar y la frecuencia de recopilación cuando se [crea un perfil de supervisión](sql-insights-enable.md#create-sql-monitoring-profile).

Las tablas siguientes tienen las siguientes columnas:
- **Nombre descriptivo**: nombre de la consulta tal como se muestra en Azure Portal al crear un perfil de supervisión.
- **Nombre de configuración**: nombre de la consulta tal como se muestra en Azure Portal al editar un perfil de supervisión.
- **Espacio de nombres**: nombre de la consulta que se encuentra en un área de trabajo de Log Analytics. Este identificador aparece en la tabla **InsighstMetrics** de la propiedad `Namespace` de la columna `Tags`.
- **DMV**: vistas administradas dinámicas que se usan para generar el conjunto de datos.
- **Habilitado de manera predeterminada**: especifica si los datos se recopilan de manera predeterminada.
- **Frecuencia de recopilación predeterminada**: especifica con qué frecuencia se recopilan los datos de manera predeterminada.

### <a name="data-for-azure-sql-database"></a>Datos para Azure SQL Database 
| Nombre descriptivo | Nombre de la configuración | Espacio de nombres | DMV | Habilitado de forma predeterminada | Frecuencia de recopilación predeterminada |
|:---|:---|:---|:---|:---|:---|
| Estadísticas de espera de DB | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | No aplicable |
| Estadísticas de espera de DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Sí | 60 segundos |
| Distribuidores de memoria | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Sí | 60 segundos |
| E/S de base de datos | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | Sí | 60 segundos |
| Propiedades del servidor | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys.[databases]<br>sys.[database_service_objectives] | Sí | 60 segundos |
| Contadores de rendimiento | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Sí | 60 segundos |
| Estadísticas de recursos | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Sí | 60 segundos |
| Regulación de recursos | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Sí | 60 segundos |
| Requests | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | No aplicable |
| Programadores| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | No aplicable  |

### <a name="data-for-azure-sql-managed-instance"></a>Datos para Azure SQL Managed Instance 

| Nombre descriptivo | Nombre de la configuración | Espacio de nombres | DMV | Habilitado de forma predeterminada | Frecuencia de recopilación predeterminada |
|:---|:---|:---|:---|:---|:---|
| Estadísticas de espera | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Sí | 60 segundos |
| Distribuidores de memoria | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Sí | 60 segundos |
| E/S de base de datos | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Sí | 60 segundos |
| Propiedades del servidor | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Sí | 60 segundos |
| Contadores de rendimiento | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Sí | 60 segundos |
| Estadísticas de recursos | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Sí | 60 segundos |
| Regulación de recursos | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Sí | 60 segundos |
| Requests | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | N/D |
| Programadores | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | No aplicable |

### <a name="data-for-sql-server"></a>Datos para SQL Server

| Nombre descriptivo | Nombre de la configuración | Espacio de nombres | DMV | Habilitado de forma predeterminada | Frecuencia de recopilación predeterminada |
|:---|:---|:---|:---|:---|:---|
| Estadísticas de espera | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Sí | 60 segundos | 
| Distribuidores de memoria | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Sí | 60 segundos |
| E/S de base de datos | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Sí | 60 segundos |
| Propiedades del servidor | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Sí | 60 segundos |
| Contadores de rendimiento | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Sí | 60 segundos |
| Espacio del volumen | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Sí | 60 segundos |
| CPU de SQL Server | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Sí | 60 segundos |
| Programadores | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | No aplicable |
| Requests | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | No aplicable |
| Estados de réplica de disponibilidad | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 segundos |
| Réplicas de base de datos de disponibilidad | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 segundos |

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Preguntas más frecuentes](/azure/azure-monitor/faq#sql-insights-preview) para ver las preguntas más frecuentes sobre SQL Insights.
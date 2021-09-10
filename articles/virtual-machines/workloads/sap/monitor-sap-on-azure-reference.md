---
title: Supervisión de SAP en la referencia de datos de Azure
description: Material de referencia importante necesario al supervisar SAP en Azure.
author: Ajayan1008
ms.topic: reference
ms.author: v-hborys
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.custom: subject-monitoring
ms.date: 08/27/2021
ms.openlocfilehash: b2e5fe3b8a6ce5afec5489bfa6c95e34641455c6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101820"
---
# <a name="monitor-sap-on-azure-preview-data-reference"></a>Supervisión de SAP en la referencia de datos de Azure (versión preliminar)

En este artículo se proporciona una referencia de datos de registro recopilados para analizar el rendimiento y la disponibilidad de Azure Monitor para soluciones de SAP. Para obtener más información sobre la recopilación y el análisis de datos de supervisión para SAP en Azure, consulte [Supervisión de SAP en Azure (vista preliminar)](monitor-sap-on-azure.md).

## <a name="metrics"></a>Métricas

Azure Monitor para soluciones de SAP no admite métricas.

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor

En esta sección se hace referencia a todas las tablas de Kusto de registros de Azure Monitor para soluciones de SAP y que están disponibles para consulta por Log Analytics. Azure Monitor para soluciones de SAP usa registros personalizados. Los esquemas de algunas tablas se definen mediante proveedores de terceros, como SAP. Estos son los registros personalizados actuales para Azure Monitor para soluciones de SAP con vínculos a orígenes para obtener más información.

### <a name="saphana_hostconfig_cl"></a>SapHana_HostConfig_CL

Para obtener más información, consulte [Vista del sistema M_LANDSCAPE_HOST_CONFIGURATION](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20b1d83875191014b028e076c874e9d7.html) en SAP HANA SQL y referencia de vistas del sistema.

### <a name="saphana_hostinformation_cl"></a>SapHana_HostInformation_CL

Para obtener más información, consulte [Vista del sistema M_HOST_INFORMATION](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20b10028751910148c1c9de602d771de.html) en SAP HANA SQL y referencia de vistas del sistema.

### <a name="saphana_systemoverview_cl"></a>SapHana_SystemOverview_CL

Para obtener más información, consulte [Vista del sistema M_SYSTEM_OVERVIEW](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20c61f0675191014a1d5f96d9668855b.html) en SAP HANA SQL y referencia de vistas del sistema.

### <a name="saphana_loadhistory_cl"></a>SapHana_LoadHistory_CL

Para obtener más información, consulte [Vista del sistema M_LOAD_HISTORY_HOST](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/3fa52abf1d854edbb7342a69364bcb0e.html) en SAP HANA SQL y referencia de vistas del sistema.

### <a name="saphana_disks_cl"></a>SapHana_Disks_CL

Para obtener más información, consulte [Vista del sistema M_DISKS](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20aef7a275191014b37acbc35b4f20a4.html) en SAP HANA SQL y referencia de vistas del sistema.

### <a name="saphana_systemavailability_cl"></a>SapHana_SystemAvailability_CL

Para obtener más información, consulte [Vista del sistema M_SYSTEM_AVAILABILITY](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/1ef9723a03214bd889c4fb8947765aa4.html) en SAP HANA SQL y referencia de vistas del sistema.

### <a name="saphana_backupcatalog_cl"></a>SapHana_BackupCatalog_CL

Para más información, consulte:
- [Vista del sistema M_BACKUP_CATALOG_FILES](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20a8437d7519101495a3fa7ad9961cf6.html?q=M_BACKUP_CATALOG)
- [Vista del sistema M_BACKUP_CATALOG](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/1.0.12/en-US/20a8100e75191014870ecf908b5d2abf.html)

### <a name="saphana_systemreplication_cl"></a>SapHana_SystemReplication_CL

Para obtener más información, consulte [Vista del sistema M_SERVICE_REPLICATION](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/2021_2_QRC/en-US/20c43fc975191014b0ece11b47a86c10.html) en SAP HANA SQL y referencia de vistas del sistema.
 
### <a name="prometheus_osexporter_cl"></a>Prometheus_OSExporter_CL

Para obtener más información, consulte [prometheus/node_exporter en GitHub](https://github.com/prometheus/node_exporter).

### <a name="prometheus_haclusterexporter_cl"></a>Prometheus_HaClusterExporter_CL

Para obtener más información, consulte [ClusterLabs/ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md).

### <a name="mssql_dbconnections_cl"></a>MSSQL_DBConnections_CL

Para más información, consulte:
- [sys.dm_exec_sessions (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 
- [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)

### <a name="mssql_systemprops_cl"></a>MSSQL_SystemProps_CL

Para más información, consulte: 
- [sys.dm_os_windows_info (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql) 
- [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)
- [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)
- [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)
- [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)
- [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [Vistas de administración dinámica relacionadas con el sistema operativo de SQL Server (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)
- [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)
- [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)
- [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)

### <a name="mssql_fileoverview_cl"></a>MSSQL_FileOverview_CL

Para obtener más información, consulte [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

### <a name="mssql_memoryoverview_cl"></a>MSSQL_MemoryOverview_CL

Para obtener más información, consulte [sys.dm_os_memory_clerks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql).

### <a name="mssql_top10statements_cl"></a>MSSQL_Top10Statements_CL

Para más información, consulte:
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)

### <a name="mssql_ioperformance_cl"></a>MSSQL_IOPerformance_CL

Para obtener más información, consulte [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql).

### <a name="mssql_tablesizes_cl"></a>MSSQL_TableSizes_CL

Para obtener más información, consulte [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql).

### <a name="mssql_batchrequests_cl"></a>MSSQL_BatchRequests_CL

Para obtener más información, consulte [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql).

### <a name="mssql_waitpercs_cl"></a>MSSQL_WaitPercs_CL

Para obtener más información, consulte [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="mssql_pagelifeexpectancy2_cl"></a>MSSQL_PageLifeExpectancy2_CL

Para obtener más información, consulte [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql).

### <a name="mssql_error_cl"></a>MSSQL_Error_CL

Para obtener más información, consulte [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql).

### <a name="mssql_cpuusage_cl"></a>MSSQL_CPUUsage_CL

Para obtener más información, consulte [Vistas de administración dinámica relacionadas con el sistema operativo de SQL Server (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql).

### <a name="mssql_aooverview_cl"></a>MSSQL_AOOverview_CL

Para obtener más información, consulte [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql).

### <a name="mssql_aowaiter_cl"></a>MSSQL_AOWaiter_CL

Para obtener más información, consulte [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql).

### <a name="mssql_aowaitstats_cl"></a>MSSQL_AOWaitstats_CL

Para obtener más información, consulte [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="mssql_aofailovers_cl"></a>MSSQL_AOFailovers_CL

Para más información, consulte:
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

### <a name="mssql_bckbackups2_cl"></a>MSSQL_BckBackups2_CL

Para obtener más información, consulte [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql).

### <a name="mssql_blockingprocesses_cl"></a>MSSQL_BlockingProcesses_CL

Para obtener más información, consulte [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el uso de Azure Monitor para soluciones de SAP, consulte [Supervisión de SAP en Azure](monitor-sap-on-azure.md).
- Para obtener más información acerca de Azure Monitor, consulte [Supervisión de recursos de Azure con Azure Monitor](../../../azure-monitor/essentials/monitor-azure-resource.md).

---
title: Administración de los registros de Azure Monitor en la CLI de Azure
description: Aprenda a usar comandos de la CLI de Azure para administrar un área de trabajo en los registros de Azure Monitor, incluida la forma en la que las áreas de trabajo interactúan con otros servicios de Azure.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 530602fdb471b86ba84aea0093d9e6c4d91d3c75
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820594"
---
# <a name="managing-azure-monitor-logs-in-azure-cli"></a>Administración de los registros de Azure Monitor en la CLI de Azure

Use los comandos de la CLI de Azure que se describen aquí para administrar el área de trabajo de Log Analytics en Azure Monitor.

> [!NOTE]
> El 31 de agosto de 2024, Microsoft retirará el agente de Log Analytics. Puede usar el agente de Azure Monitor después de ese momento. Para más información, consulte [Información general sobre los agentes de Azure Monitor](../agents/agents-overview.md).

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-workspace-for-monitor-logs"></a>Creación de un área de trabajo para los registros de Azure Monitor

Ejecute el comando [az group create](/cli/azure/group#az_group_create) para crear un grupo de recursos o use un grupo de recursos existente. Para crear un área de trabajo, use el comando [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create).

```azurecli
az group create --name ContosoRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Para más información acerca de las áreas de trabajo, consulte [Introducción a los registros de Azure Monitor](./data-platform-logs.md).

## <a name="list-tables-in-your-workspace"></a>Enumeración de las tablas del área de trabajo

Cada área de trabajo contiene tablas con columnas que tienen varias filas de datos. Cada tabla se define mediante un conjunto único de columnas de datos proporcionados por el origen de datos.

Para ver las tablas del área de trabajo, use el comando [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list):

```azurecli
az monitor log-analytics workspace table list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

El valor de salida `table` presenta los resultados en un formato más legible. Para más información, consulte [Formato de salida](/cli/azure/use-cli-effectively#output-formatting).

Para cambiar el tiempo de retención de una tabla, ejecute el comando [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update):

```azurecli
az monitor log-analytics workspace table update --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name Syslog --retention-time 45
```

El tiempo de retención está entre 30 y 730 días.

Para más información sobre las tablas, consulte [Estructura de los datos](./data-platform-logs.md#data-structure).

## <a name="export-data-from-selected-tables"></a>Exportación de datos de tablas seleccionadas

Puede exportar continuamente datos de tablas seleccionadas a una cuenta de almacenamiento de Azure o a Azure Event Hubs. Utilice el comando [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create):

```azurecli
az monitor log-analytics workspace data-export create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --table Syslog \
   --destination /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/exportaccount \
   --enable
```

Para ver las exportaciones de datos, ejecute el comando [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list).

```azurecli
az monitor log-analytics workspace data-export list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Para eliminar una exportación de datos, ejecute el comando [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete). El parámetro `--yes` omite la confirmación.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --yes
```

Para más información sobre la exportación de datos, consulte [Exportación de datos del área de trabajo de Log Analytics en Azure Monitor (versión preliminar)](./logs-data-export.md).

## <a name="manage-a-linked-service"></a>Administración de un servicio vinculado

Los servicios vinculados definen una relación entre el área de trabajo y otro recurso de Azure. Los registros de Azure Monitor y los recursos de Azure usan esta conexión en sus operaciones. El ejemplo utiliza servicios vinculados, como una cuenta de Automation y una asociación del área de trabajo a claves administradas por el cliente.

Para crear un servicio vinculado, ejecute el comando [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create):

```azurecli
az monitor log-analytics workspace linked-service create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation \
   --resource-id /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Web/sites/ContosoWebApp09

az monitor log-analytics workspace linked-service list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Para eliminar una relación de servicio vinculado, ejecute el comando [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete):

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation
```

Para más información, consulte [az monitor log-analytics workspace linked-service](/cli/azure/monitor/log-analytics/workspace/linked-service).

## <a name="manage-linked-storage"></a>Administración del almacenamiento vinculado

Si proporciona y administra su propia cuenta de almacenamiento para Log Analytics, puede administrarla con estos comandos de la CLI de Azure.

Para vincular el área de trabajo a una cuenta de almacenamiento, ejecute el comando [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create):

```azurecli
az monitor log-analytics workspace linked-storage create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace \
   --storage-accounts /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/contosostorage \
   --type Alerts

az monitor log-analytics workspace linked-storage list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

Para eliminar el vínculo a una cuenta de almacenamiento, ejecute el comando [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete):

```azurecli
az monitor log-analytics workspace linked-storage delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --type Alerts
```

Para más información, consulte [Uso de cuentas de almacenamiento administradas por el cliente en Log Analytics de Azure Monitor](./private-storage.md).

## <a name="manage-intelligence-packs"></a>Administración de módulos de inteligencia

Para ver los módulos de inteligencia disponibles, ejecute el comando [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list). El comando también indica si el paquete está habilitado.

```azurecli
az monitor log-analytics workspace pack list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Use los comandos [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable) o [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable):

```azurecli
az monitor log-analytics workspace pack enable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow

az monitor log-analytics workspace pack disable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow
```

## <a name="manage-saved-searches"></a>Administración de búsquedas guardadas

Para crear una búsqueda guardada, ejecute el comando [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create):

```azurecli
az monitor log-analytics workspace saved-search create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 \
   --category "Log Management" --display-name SavedSearch01 \
   --saved-query "AzureActivity | summarize count() by bin(TimeGenerated, 1h)" --fa Function01 --fp "a:string = value"
```

Para ver la búsqueda guardada, ejecute el comando [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show). Para ver todas las búsquedas guardadas, ejecute el comando [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list).

```azurecli
az monitor log-analytics workspace saved-search show --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01
az monitor log-analytics workspace saved-search list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

Para eliminar una búsqueda guardada, ejecute el comando [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete):

```azurecli
az monitor log-analytics workspace saved-search delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 --yes
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Si ha creado un grupo de recursos para probar estos comandos, puede quitar el grupo de recursos y todo su contenido mediante el comando [az group delete](/cli/azure/group#az-group-delete):

```azurecli
az group delete --name ContosoRG
```

Si desea quitar una nueva área de trabajo de un grupo de recursos existente, ejecute el comando [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete):

```azurecli
az monitor log-analytics workspace delete --resource-group ContosoRG 
   --workspace-name ContosoWorkspace --yes
```

Las áreas de trabajo de Log Analytics tienen una opción de eliminación temporal. Puede recuperar un área de trabajo eliminada durante dos semanas después de la eliminación. Ejecute el comando [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover):

```azurecli
az monitor log-analytics workspace recover --resource-group ContosoRG 
   --workspace-name ContosoWorkspace
```

En el comando delete, agregue el parámetro `--force` para eliminar el área de trabajo inmediatamente.

## <a name="azure-cli-commands-used-in-this-article"></a>Comandos de la CLI de Azure usados en este artículo

- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create)
- [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete)
- [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list)
- [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete)
- [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create)
- [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete)
- [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create)
- [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete)
- [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable)
- [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable)
- [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list)
- [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover)
- [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete)
- [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)
- [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show)
- [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create)
- [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list)
- [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update)

## <a name="next-steps"></a>Pasos siguientes

[Introducción a Log Analytics en Azure Monitor](log-analytics-overview.md)
---
title: Administración de componentes de Application Insights en la CLI de Azure
description: Use este código de ejemplo para administrar componentes en Application Insights. Esta característica forma parte de Azure Monitor.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/10/2012
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8c7b81847a1ac2484a1eaa0df56bde35eec6385
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709116"
---
# <a name="manage-application-insights-components-by-using-azure-cli"></a>Administración de componentes de Application Insights mediante la CLI de Azure

En Azure Monitor, los componentes son partes que se pueden implementar independientemente de su aplicación de microservicios o distribuida. Use estos comandos de la CLI de Azure para administrar componentes en Application Insights.

Los ejemplos de este artículo realizan las siguientes tareas de administración:

- Crear un componente.
- Conexión de un componente a una aplicación web.
- Vinculación de un componente a una cuenta de almacenamiento con un componente.
- Creación de una configuración de exportación continua para un componente.

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-component"></a>Crear un componente

Si aún no tiene un grupo de recursos y un área de trabajo, puede crearlos mediante [az group create](/cli/azure/group#az_group_create) y [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create):

```azurecli
az group create --name ContosoAppInsightRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoAppInsightRG \
   --workspace-name AppInWorkspace
```

Para crear un componente, ejecute el comando [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create). El comando [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show) muestra el componente.

```azurecli
az monitor app-insights component create --resource-group ContosoAppInsightRG \
   --app ContosoApp --location eastus2 --kind web --application-type web \
   --retention-time 120
az monitor app-insights component show --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="connect-a-webapp"></a>Conexión de una aplicación web

En este ejemplo se conecta el componente a una aplicación web. Puede crear una aplicación web mediante los comandos [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) y [az webapp create](/cli/azure/webapp#az_webapp_create):

```azurecli
az appservice plan create --resource-group ContosoAppInsightRG --name ContosoAppService
az webapp create --resource-group ContosoAppInsightRG --name ContosoApp \
   --plan ContosoAppService --name ContosoApp8765
```

Ejecute el comando [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp) para conectar el componente a la aplicación web:

```azurecli
az monitor app-insights component connect-webapp --resource-group ContosoAppInsightRG \
   --app ContosoApp --web-app ContosoApp8765 --enable-debugger false --enable-profiler false
```

En su lugar, puede conectarse a una función de Azure mediante el comando [az monitor app-insights component connect-function](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_function).

## <a name="link-a-component-to-storage"></a>Vinculación de un componente al almacenamiento

Puede vincular un componente a una cuenta de almacenamiento. Para crear una cuenta de almacenamiento, use el comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

```azurecli
az storage account create --resource-group ContosoAppInsightRG \
   --name contosolinkedstorage --location eastus2 --sku Standard_LRS
```

Para vincular el componente a la cuenta de almacenamiento, ejecute el comando [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link). Puede ver los vínculos existentes mediante el comando [az monitor app-insights component linked-storage show](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_show):


```azurecli
az monitor app-insights component linked-storage link  --resource-group ContosoAppInsightRG \
   --app ContosoApp --storage-account contosolinkedstorage
az monitor app-insights component linked-storage show --resource-group ContosoAppInsightRG \
   --app ContosoApp
```

Para desvincular el almacenamiento, ejecute el comando [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink):

```AzureCLI
az monitor app-insights component linked-storage unlink  \
   --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="set-up-continuous-export"></a>Configuración de la exportación continua

La exportación continua guarda eventos del portal de Application Insights en un contenedor de almacenamiento en formato JSON.

> [!NOTE]
> La exportación continua solo se admite para los recursos clásicos de Application Insights. Los [recursos de Application Insights basados en áreas de trabajo](../app/create-workspace-resource.md) deben usar la [configuración de diagnóstico](../app/create-workspace-resource.md#export-telemetry).
>

Para crear un contenedor de almacenamiento, ejecute el comando [az storage container create](/cli/azure/storage/container#az_storage_container_create). 

```azurecli
az storage container create --name contosostoragecontainer --account-name contosolinkedstorage \
   --public-access blob 
```

Necesita que el acceso del contenedor sea de solo escritura. Ejecute el cmdlet [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create):

```azurecli
az storage container policy create --container-name contosostoragecontainer \
   --account-name contosolinkedstorage --name WAccessPolicy --permissions w
```

Cree una clave SAS mediante el comando [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas). Asegúrese de usar el valor del parámetro `--output tsv` para guardar la clave sin formato no deseado como comillas. Para más información, consulte [Uso de la CLI de Azure con eficacia](/cli/azure/use-cli-effectively).

```azurecli
containersas=$(az storage container generate-sas --name contosostoragecontainer \
   --account-name contosolinkedstorage --permissions w --output tsv)
```

Para crear una exportación continua, ejecute el comando [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create).

```azurecli
az monitor app-insights component continues-export create --resource-group ContosoAppInsightRG \
   --app ContosoApp --record-types Event --dest-account contosolinkedstorage \
   --dest-container contosostoragecontainer --dest-sub-id 00000000-0000-0000-0000-000000000000 \
   --dest-sas $containersas
```

Para eliminar una exportación continua configurada, utilice el comando [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete). 

```azurecli
az monitor app-insights component continues-export list \
   --resource-group ContosoAppInsightRG --app ContosoApp
az monitor app-insights component continues-export delete \
   --resource-group ContosoAppInsightRG --app ContosoApp --id abcdefghijklmnopqrstuvwxyz=
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Si ha creado un grupo de recursos para probar estos comandos, puede quitar el grupo de recursos y todo su contenido mediante el comando [az group delete](/cli/azure/group#az_group_delete):

```azurecli
az group delete --name ContosoAppInsightRG 
```

## <a name="azure-cli-commands-used-in-this-article"></a>Comandos de la CLI de Azure usados en este artículo

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp)
- [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create)
- [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete)
- [az monitor app-insights component continues-export list](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_list)
- [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)
- [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link)
- [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink)
- [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az storage account create](/cli/azure/storage/account#az_storage_account_create)
- [az storage container create](/cli/azure/storage/container#az_storage_container_create)
- [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas)
- [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create)
- [az webapp create](/cli/azure/webapp#az_webapp_create)

## <a name="next-steps"></a>Pasos siguientes

[Ejemplos de la CLI de Azure Monitor](../cli-samples.md)

[Búsqueda y diagnóstico de problemas de rendimiento](../app/tutorial-performance.md)

[Supervisión y alertas sobre el estado de la aplicación](../app/tutorial-alert.md)

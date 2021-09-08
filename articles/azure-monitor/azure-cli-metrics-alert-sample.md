---
title: Creación de monitores de alertas de métricas en la CLI de Azure
description: Aprenda a crear alertas de métricas en Azure Monitor con los comandos de la CLI de Azure. Estos ejemplos crean alertas para una máquina virtual y un plan de App Service.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 60d2b7f706c985eaa13c634e0323927eb39446ad
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117851"
---
# <a name="create-metric-alert-monitors-in-azure-cli"></a>Creación de monitores de alertas de métricas en la CLI de Azure

Estos ejemplos crean monitores de alertas de métricas en Azure Monitor mediante los comandos de la CLI de Azure. El primer ejemplo crea una alerta para una máquina virtual. El segundo comando crea una alerta que incluye una dimensión para un plan de App Service.  

[!INCLUDE [Prepare your Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-alert"></a>Crear una alerta

Esta alerta supervisa una máquina virtual existente denominada `VM07` en el grupo de recursos denominado `ContosoVMRG`.

Puede crear un grupo de recursos con el comando [az group create](/cli/azure/group#az-group-create). Para información sobre cómo crear máquinas virtuales, consulte [Creación de una máquina virtual Windows con la CLI de Azure](../virtual-machines/windows/quick-create-cli.md), [Creación de una máquina virtual Linux con la CLI de Azure](../virtual-machines/linux/quick-create-cli.md) y el comando [az vm create](/cli/azure/vm#az-vm-create).

```azurecli
# resource group name: ContosoVMRG
# virtual machine name: VM07

# Create scope
scope=$(az vm show --resource-group ContosoVMRG --name VM07 --output tsv --query id)

# Create action
action=$(az monitor action-group create --name ContosoWebhookAction \
  --resource-group ContosoVMRG --output tsv --query id \
  --action webhook https://alerts.contoso.com usecommonalertschema)

# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
  --metric "Percentage CPU" --op GreaterThan --type static --threshold 90 --output tsv)

# Create metrics alert
az monitor metrics alert create --name alert-01 --resource-group ContosoVMRG \
  --scopes $scope --action $action --condition $condition --description "Test High CPU"
```

En este ejemplo se usa el tipo de salida `tsv`, que no incluye símbolos no deseados, como comillas. Para más información, consulte [Uso de la CLI de Azure con eficacia](/cli/azure/use-cli-effectively).

## <a name="create-an-alert-with-a-dimension"></a>Creación de una alerta con una dimensión

En este ejemplo se crea un plan de App Service y, a continuación, se crea una alerta de métricas para él. En el ejemplo se usa una dimensión para especificar que todas las instancias del plan de App Service estarán en esta métrica. En el ejemplo se crea un grupo de recursos y un plan de servicio de aplicaciones.

```azurecli
# Create resource group
az group create --name ContosoRG --location eastus2
 
# Create application service plan
az appservice plan create --resource-group ContosoRG --name ContosoAppServicePlan \
   --is-linux --number-of-workers 4 --sku S1 
 
# Create scope
scope=$(az appservice plan show --resource-group ContosoRG --name ContosoAppServicePlan \
   --output tsv --query id) 
 
# Create dimension
dim01=$(az monitor metrics alert dimension create --name Instance --value * --op Include --output tsv)
 
# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
   --metric CpuPercentage --op GreaterThan --type static --threshold 90 \
   --dimension $dim01 --output tsv)
```

Para ver una lista de las métricas posibles, ejecute el comando [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions). El parámetro `--output` muestra los valores en un formato legible.


```azurecli
az monitor metrics list-definitions --resource $scope --output table 
 
# Create metrics alert
az monitor metrics alert create --name alert-02 --resource-group ContosoRG \
   --scopes $scope --condition $condition --description "Service Plan High CPU"
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Si ha creado grupos de recursos para probar estos comandos, puede quitar un grupo de recursos y todo su contenido mediante el comando [az group delete](/cli/azure/group#az-group-delete):

```azurecli
az group delete --name ContosoVMRG

az group delete --name ContosoRG
```

Si ha usado recursos existentes que quiere conservar, use el comando [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete) para eliminar las alertas de las prácticas:

```azurecli
az monitor metrics alert delete --name alert-01

az monitor metrics alert delete --name alert-02
```

## <a name="azure-cli-commands-used-in-this-article"></a>Comandos de la CLI de Azure usados en este artículo

En este artículo se usan los siguientes comandos de la CLI de Azure:

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az appservice plan show](/cli/azure/appservice/plan#az_appservice_plan_show)
- [az group create](/cli/azure/group#az-group-create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor action-group create](/cli/azure/monitor/action-group#az_monitor_action_group_create)
- [az monitor metrics alert condition create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-condition-create)
- [az monitor metrics alert create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-create)
- [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete)
- [az monitor metrics alert dimension create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-dimension-create)
- [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>Pasos siguientes

- [Ejemplos de la CLI de Azure Monitor](cli-samples.md)
- [Descripción del funcionamiento de las alertas de métricas en Azure Monitor](alerts/alerts-metric-overview.md)

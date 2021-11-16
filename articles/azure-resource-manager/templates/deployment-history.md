---
title: Historial de implementación
description: Se describe cómo ver las operaciones de implementación de Azure Resource Manager con el portal, PowerShell, CLI de Azure y API de REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60bc06ab15e7f36a5c67ff5fc4de9da8bad28f88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310284"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visualización del historial de implementación con Azure Resource Manager

Azure Resource Manager permite ver el historial de implementación. Se pueden examinar las operaciones específicas en implementaciones pasadas y ver qué recursos se han implementado. Este historial contiene información sobre los errores.

El historial de implementaciones de un grupo de recursos está limitado a 800 implementaciones. A medida que se acerque el límite, las implementaciones se eliminarán automáticamente del historial. Para obtener más información, vea [Eliminaciones automáticas del historial de implementaciones](deployment-history-deletions.md).

Para obtener ayuda para resolver errores de implementación concretos, consulte [Solución de errores comunes de implementación de Azure](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Obtención de las implementaciones y el Id. de correlación

Se pueden ver los detalles sobre una implementación mediante Azure Portal, PowerShell, la CLI de Azure o la API REST. Cada implementación tiene un id. de correlación que se usa para realizar el seguimiento de los eventos relacionados. Si [crea una solicitud de soporte técnico de Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md), puede que el servicio de soporte técnico le pida un identificador de correlación. Este identificador de correlación lo usan para identificar las operaciones de la implementación que ha dado error.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Seleccione el grupo de recursos que quiera examinar.

1. Seleccione el vínculo situado bajo **Implementaciones**.

   :::image type="content" source="media/deployment-history/select-deployment-history.png" alt-text="Captura de pantalla de información general del grupo de recursos que muestra una implementación correcta.":::

1. Seleccione una de las implementaciones del historial de implementación.

   :::image type="content" source="media/deployment-history/select-details.png" alt-text="Captura de pantalla del vínculo resaltado para una implementación de recursos.":::

1. Se muestra un resumen de la implementación, incluido el id. de correlación.

   :::image type="content" source="media/deployment-history/show-correlation-id.png" alt-text="Captura de pantalla del historial de implementación que destaca el id. de correlación.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para enumerar todas las implementaciones de un grupo de recursos, use el comando [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment).

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Para obtener una implementación específica de un grupo de recursos, agregue el parámetro `DeploymentName`.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Para obtener el Id. de correlación, use:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para mostrar todas las implementaciones de un grupo de recursos, use [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Para obtener una implementación concreta, use [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Para obtener el Id. de correlación, use:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Para enumerar las implementaciones de un grupo de recursos, use la operación siguiente. Para obtener el número de versión de la API más reciente que se va a usar en la solicitud, vea [Implementaciones: mostrar por grupo de recursos](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Para obtener una implementación específica, use la siguiente operación. Para obtener el número de versión de la API más reciente que se va a usar en la solicitud, vea [Implementaciones: obtener](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

La respuesta incluye el id. de correlación.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "11111111-1111-1111-1111-111111111111",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Obtención de operaciones de implementación y mensajes de error

Cada implementación puede incluir varias operaciones. Para ver más detalles acerca de una implementación, vea las operaciones de implementación. Cuando se produce un error en una implementación, las operaciones de implementación incluyen un mensaje de error.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. En el Resumen de una implementación, seleccione **Detalles de la operación**.

   :::image type="content" source="media/deployment-history/get-operation-details.png" alt-text="Captura de pantalla de la implementación con errores que destaca el vínculo para los detalles de la operación.":::

1. Verá los detalles de ese paso de la implementación. Cuando se produce un error, los detalles incluyen el mensaje de error.

   :::image type="content" source="media/deployment-history/see-operation-details.png" alt-text="Captura de pantalla de los detalles de la operación de la implementación con errores.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Con el fin de ver las operaciones de implementación para la implementación en un grupo de recursos, use el comando [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Para ver las operaciones con errores, filtre las operaciones con el estado **Erróneas**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }
```

Para obtener el mensaje de estado de las operaciones erróneas, use el comando siguiente:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -Name ExampleDeployment | Where-Object { $_.ProvisioningState -eq "Failed" }).StatusMessage
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Con el fin de ver las operaciones de implementación para la implementación en un grupo de recursos, use el comando [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list). Debe tener la CLI de Azure 2.6.0 o una versión posterior.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Para ver las operaciones con errores, filtre las operaciones con el estado **Erróneas**.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed']"
```

Para obtener el mensaje de estado de las operaciones erróneas, use el comando siguiente:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Para obtener las operaciones de implementación, use la operación siguiente. Para obtener el número de versión de la API más reciente que se va a usar en la solicitud, vea [Operaciones de implementación: mostrar](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

La respuesta incluye un mensaje de error.

```json
{
  "value": [
    {
      "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeployment/operations/1234567890ABCDEF",
      "operationId": "1234567890ABCDEF",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "11111111-1111-1111-1111-111111111111",
        "serviceRequestId": "11111111-1111-1111-1111-111111111111",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storage",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storage"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Pasos siguientes

- Para obtener ayuda para resolver errores de implementación concretos, consulte [Solución de errores comunes de implementación de Azure](common-deployment-errors.md).
- Para obtener información sobre cómo se administran las implementaciones en el historial, vea [Eliminaciones automáticas del historial de implementaciones](deployment-history-deletions.md).
- Para obtener una vista previa de los cambios que aplicará una plantilla antes de realizar la implementación, consulte [Operación hipotética de implementación de plantillas de ARM](deploy-what-if.md).

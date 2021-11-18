---
title: Habilitación del registro de depuración
description: Descripción de cómo habilitar el registro de depuración para solucionar problemas de los recursos de Azure implementados con plantillas de Azure Resource Manager (plantillas de ARM) o archivos Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fcd2cbdf052f934bb797b3f1dab148d951d09167
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989656"
---
# <a name="enable-debug-logging"></a>Habilitación del registro de depuración

A la hora de solucionar un error de implementación, ayuda a recopilar más información. Use Azure PowerShell para habilitar el registro de depuración. Puede obtener datos sobre la solicitud y la respuesta de una implementación para conocer la causa del problema. El registro de depuración funciona con plantillas de Azure Resource Manager (plantillas de ARM) y archivos Bicep.

## <a name="get-debug-information"></a>Obtención de información de depuración

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) para establecer el parámetro `DeploymentDebugLogLevel` en `All`, `ResponseContent` o `RequestContent`. Cuando se habilita el registro de depuración, se muestra una advertencia que indica que se pueden registrar secretos como contraseñas o `listKeys` mediante comandos como [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json `
  -DeploymentDebugLogLevel All
```

La salida muestra el registro de depuración:

```Output
DeploymentDebugLogLevel : RequestContent, ResponseContent
```

Para ver todas las propiedades de las operaciones de implementación:

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

Puede especificar una propiedad, como `StatusMessage` o `StatusCode`, para filtrar la salida.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

No se puede habilitar el registro de depuración con la CLI de Azure, pero se pueden recuperar los datos del registro de depuración.

Obtenga las operaciones de implementación con el comando [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list):

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Obtenga el contenido de la solicitud con el comando siguiente:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

Obtenga el contenido de la respuesta con el comando siguiente:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

---

## <a name="nested-template"></a>Plantilla anidada

Para registrar la información de depuración de una plantilla de ARM [anidada](../templates/linked-templates.md#nested-template), use el elemento `debugSetting` [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments).

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep usa [módulos](../bicep/modules.md) en lugar de `Microsoft.Resources/deployments`. Con los módulos, puede reutilizar el código para implementar un archivo Bicep desde otro archivo Bicep.

## <a name="next-steps"></a>Pasos siguientes

- [Errores de implementación frecuentes](common-deployment-errors.md)
- [Búsqueda de códigos de error](find-error-code.md)
- [Creación de una plantilla de solución de problemas](create-troubleshooting-template.md)

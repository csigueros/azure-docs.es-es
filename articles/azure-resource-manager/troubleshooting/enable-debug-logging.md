---
title: Habilitación del registro de depuración
description: Descripción de cómo habilitar el registro de depuración para solucionar problemas de los recursos de Azure implementados con plantillas de Azure Resource Manager (plantillas de ARM) o archivos Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d68041953979b594c83059a28a78e3440ca297ac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478333"
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

Puede especificar una propiedad. Por ejemplo, la propiedad `StatusMessage` genera los mismos datos que la propiedad `response` de la CLI de Azure.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

Use la CLI de Azure para obtener la información de `request` y `response` de depuración. En las versiones 4.8 y posteriores del módulo Az, `Get-AzResourceGroupDeploymentOperation` no incluye esas propiedades en la salida. Para obtener una lista de las propiedades disponibles, consulte [salidas](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation#outputs).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

No se puede habilitar el registro de depuración con la CLI de Azure, pero se pueden recuperar los datos del registro de depuración.

Obtenga las operaciones de implementación con el comando siguiente:

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

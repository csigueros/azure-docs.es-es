---
title: Búsqueda de códigos de error
description: Descripción de cómo buscar códigos de error para solucionar problemas de los recursos de Azure implementados con plantillas de Azure Resource Manager (plantillas de ARM) o archivos Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dc33dec3f043332b4ce5b2e7fe53b9f16d41d54f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846318"
---
# <a name="find-error-codes"></a>Búsqueda de códigos de error

Cuando se produce un error en una implementación de recursos de Azure usando plantillas de Azure Resource Manager (plantillas de ARM) o archivos Bicep, y se recibe el código de error. En este artículo se describe cómo buscar códigos de error para que pueda solucionar el problema. Para más información sobre los códigos de error, vea [Errores de implementación comunes](common-deployment-errors.md).

## <a name="error-types"></a>Tipos de errores

Hay tres tipos de errores relacionados con una implementación:

- Los **errores de validación** se producen antes de que comience una implementación y son debidos a errores de sintaxis en el archivo. El editor puede identificar estos errores.
- Los **errores de validación previa** se producen cuando se ejecuta un comando de implementación, pero los recursos no están implementados. Estos errores se encuentran sin iniciar la implementación. Por ejemplo, si un valor de parámetro es incorrecto, el error se encuentra en la validación previa.
- **Errores de implementación**, que se producen durante el proceso de implementación y solo se pueden encontrar evaluando su progreso.

Todos los tipos de errores devuelven un código de error que se usa para solucionar problemas de implementación. Los errores de validación y de comprobación previa se muestran en el registro de actividad, pero no aparecen en el historial de implementación. Un archivo Bicep con errores de sintaxis no se compila en JSON y no se muestra en el registro de actividad.

Para identificar errores de sintaxis, puede usar [Visual Studio Code](https://code.visualstudio.com) con la [extensión Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) más reciente o con la [extensión Herramientas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).

## <a name="validation-errors"></a>Errores de validación

Las plantillas se validan durante el proceso de implementación y se muestran los códigos de error. Antes de ejecutar una implementación, puede ejecutar pruebas de validación con Azure PowerShell o con la CLI de Azure para identificar errores de validación y de comprobación previa.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Una plantilla de ARM se puede implementar desde el portal. Si la plantilla tiene errores de sintaxis, verá un error de validación al intentar ejecutar la implementación. Para más información sobre las implementaciones del portal, vea [Implementación de recursos desde plantilla personalizada](../templates/deploy-portal.md#deploy-resources-from-custom-template).

En el ejemplo siguiente se intenta implementar una cuenta de almacenamiento y se produce un error de validación.

:::image type="content" source="media/find-error-code/validation-error.png" alt-text="Captura de pantalla de un Azure Portal error de validación.":::

Seleccione el mensaje para más detalles. La plantilla tiene un error de sintaxis con código de error `InvalidTemplate`. El **resumen** que a una expresión le falta un paréntesis de cierre.

:::image type="content" source="media/find-error-code/validation-details.png" alt-text="Captura de pantalla de un mensaje de error de validación que muestra un error de sintaxis.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para validar una plantilla de ARM antes de la implementación, ejecute [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment).

```azurepowershell
Test-AzResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json
```

La salida muestra códigos de error como `InvalidTemplateDeployment` o `AccountNameInvalid` que puede usar para solucionar problemas y corregir la plantilla.

Para un archivo Bicep, la salida de un problema de validación de sintaxis muestra un error de parámetro.

```Output
Test-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet.
Cannot find path '/tmp/11111111-1111-1111-1111-111111111111/main.json' because it does not exist.
```

Para obtener más información sobre la solución de problemas, use el comando [build](../bicep/bicep-cli.md) de Bicep. La salida muestra el número de línea y columna de cada error entre paréntesis, además del mensaje de error.

```bicep
bicep build main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

Hay más cmdlets de PowerShell disponibles para validar las plantillas de implementación:

- [Test-AzDeployment](/powershell/module/az.resources/test-azdeployment) para implementaciones de nivel de suscripción.
- [Test-AzManagementGroupDeployment](/powershell/module/az.resources/test-azmanagementgroupdeployment)
- [Test-AzTenantDeployment](/powershell/module/az.resources/test-aztenantdeployment)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para validar una plantilla de ARM antes de la implementación, ejecute [az deployment group validate](/cli/azure/deployment/group#az_deployment_group_validate).

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file azuredeploy.json
```

La salida muestra códigos de error como `InvalidTemplateDeployment` o `AccountNameInvalid` que puede usar para solucionar problemas y corregir la plantilla.

Para un archivo Bicep, la salida muestra el número de línea y columna de cada error entre paréntesis, además del mensaje de error.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

Hay más comandos de la CLI de Azure disponibles para validar las plantillas de implementación:

- [az deployment sub validate](/cli/azure/deployment/sub#az_deployment_sub_validate)
- [az deployment mg validate](/cli/azure/deployment/mg#az_deployment_mg_validate)
- [az deployment tenant validate](/cli/azure/deployment/tenant#az_deployment_tenant_validate)

---

## <a name="deployment-errors"></a>Errores de implementación

Se procesan varias operaciones para implementar un recurso de Azure. Los errores de implementación se producen cuando una operación pasa la validación pero genera un error durante la implementación. Puede ver mensajes sobre cada operación de implementación y cada implementación de un grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para ver mensajes sobre las operaciones de una implementación, use el **registro de actividad** del grupo de recursos:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
1. Vaya a **Grupos de recursos** y seleccione el nombre del grupo de recursos de la implementación.
1. Seleccione **Registro de actividad**.
1. Use los filtros para buscar el registro de errores de una operación.

    :::image type="content" source="./media/find-error-code/activity-log.png" alt-text="Captura de pantalla del registro de actividad del grupo de recursos donde se resalta una implementación con errores.":::

1. Seleccione el registro de errores para ver los detalles de la operación.

    :::image type="content" source="./media/find-error-code/activity-log-details.png" alt-text="Captura de pantalla de los detalles del registro de actividad donde se muestra el mensaje de error de una implementación con errores.":::

Para ver el resultado de una implementación:

1. Vaya al grupo de recursos.
1. Seleccione **Configuración** > **Implementaciones**.
1. Seleccione **Detalles del error** para la implementación.

    :::image type="content" source="media/find-error-code/deployment-error-details.png" alt-text="Captura de pantalla del vínculo de un grupo de recursos a los detalles del error de una implementación con errores.":::

1. Se muestran el mensaje de error `NoRegisteredProviderFound` y el código de error.

    :::image type="content" source="media/find-error-code/deployment-error-summary.png" alt-text="Captura de pantalla de un mensaje que muestra los detalles del error de implementación.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para ver los mensajes de las operaciones de una implementación con PowerShell, use [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

Para ver todas las operaciones para una implementación:

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

Para especificar un tipo de propiedad específico:

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusCode
```

Para obtener el resultado de una implementación, use [Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment).

```azurepowershell
Get-AzResourceGroupDeployment `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para ver los mensajes de las operaciones de una implementación con la CLI de Azure, use [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list).

Para ver todas las operaciones para una implementación:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties"
```

Para especificar un tipo de propiedad específico:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties.statusCode"
```

Para obtener el resultado de una implementación, use [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show).

```azurecli
az deployment group show \
  --resource-group examplegroup \
  --name exampledeployment
```

---

## <a name="next-steps"></a>Pasos siguientes

- [Errores de implementación frecuentes](common-deployment-errors.md)
- [Habilitación del registro de depuración](enable-debug-logging.md)
- [Creación de una plantilla de solución de problemas](create-troubleshooting-template.md)

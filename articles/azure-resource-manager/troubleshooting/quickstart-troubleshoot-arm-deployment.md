---
title: Solución de problemas de las implementaciones de plantillas de Resource Manager
description: Aprenda a solucionar errores de las implementaciones de plantillas de Azure Resource Manager (ARM).
ms.date: 11/08/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce9bf9ee097a41b3e3ecbc0cd515dd58cf30ea82
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062065"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>Inicio rápido: Solución de problemas de las implementaciones de plantillas de ARM

En este inicio rápido se explica cómo solucionar errores de implementación de las plantillas de Azure Resource Manager (ARM). Configurará una plantilla con errores y aprenderá a corregirlos.

Hay tres tipos de errores relacionados con una implementación:

- **Errores de validación**, que se producen antes de que comience una implementación y son debidos a errores de sintaxis en el archivo. El editor puede identificar estos errores.
- Los **errores de validación previa** se producen cuando se ejecuta un comando de implementación, pero los recursos no están implementados. Estos errores se encuentran sin iniciar la implementación. Por ejemplo, si un valor de parámetro es incorrecto, el error se encuentra en la validación previa.
- **Errores de implementación**, que se producen durante el proceso de implementación y solo se pueden encontrar evaluando su progreso.

Todos los tipos de errores devuelven un código de error que se usa para solucionar problemas de implementación. Los errores de validación y de comprobación previa se muestran en el registro de actividad, pero no aparecen en el historial de implementación.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este inicio rápido, necesita los siguientes elementos:

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- [Visual Studio Code](https://code.visualstudio.com/) con la [extensión Herramientas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) más reciente.
- Instale la versión más reciente de [Azure PowerShell](/powershell/azure/install-az-ps) o la [CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-template-with-errors"></a>Creación de una plantilla con errores

Copie la plantilla siguiente y guárdela localmente. Lo usará para solucionar problemas de validación, previos y de implementación. En este inicio rápido se da por supuesto que el archivo tiene el nombre _troubleshoot.json_, pero puede darle cualquier otro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameterss": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    },
    "vnetResult": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
    }
  }
}
```

## <a name="fix-validation-error"></a>Corrección del error de validación

Abra el archivo en Visual Studio Code. Las líneas onduladas bajo `parameterss:` indican un error. Para ver el error de validación, mantenga el puntero sobre el error.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-error.png" alt-text="Captura de pantalla de un error de validación de plantilla en Visual Studio Code.":::

Observará que `variables` y `resources` tienen errores de _referencia de parámetros no definidos_. Para mostrar los errores de validación de la plantilla, seleccione **Ver** > **Problemas**.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-undefined-parameter.png" alt-text="Captura de pantalla Visual Studio Code que muestra errores de referencia de parámetros no definidos.":::

Todos los errores se producen por la ortografía incorrecta de un nombre de elemento.

```json
"parameterss": {
```

El mensaje de error indica _Error de validación de plantilla: No se pudo encontrar el miembro "parameterss" en el objeto de tipo "Plantilla". Ruta de acceso "parameterss", línea 4, posición 16_.

La sintaxis de plantilla ARM para los [parámetros](../templates/syntax.md#parameters) muestra que `parameters` es el nombre de elemento correcto.

Para corregir el error de validación y los errores de _referencia de parámetros no definidos_, corrija la ortografía y guarde el archivo.

```json
"parameters": {
```

## <a name="fix-preflight-error"></a>Corrección del error previo

Para crear un error de validación previa, usará un valor incorrecto para el parámetro `prefixName`.

En este inicio rápido se usa _troubleshootRG_ para el nombre del grupo de recursos, pero puede usar cualquier nombre.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=long!!StoragePrefix
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName long!!StoragePrefix
```

---

Se produce un error de validación previa en la plantilla y la implementación no se ejecuta. `prefixName` tiene más de 11 caracteres y contiene caracteres especiales y letras mayúsculas.

Los nombres de almacenamiento deben tener entre 3 y 24 caracteres y solo pueden contener letras minúsculas y números. El valor de prefijo creó un nombre de almacenamiento no válido. Para más información, consulte [Resolución de errores de nombres de cuenta de almacenamiento](error-storage-account-name.md). Para corregir el error previo, use un prefijo de 11 caracteres o menos que solo contenga letras minúsculas o números.

Dado que la implementación no se ha ejecutado, no hay ningún historial de implementación.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-no-deploy.png" alt-text="Captura de pantalla de la información general del grupo de recursos que no muestra ninguna implementación por error previo.":::

El registro de actividad muestra el error previo. Seleccione el registro para ver los detalles del error.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-activity-log.png" alt-text="Captura de pantalla del registro de actividad del grupo de recursos con el error previo.":::

## <a name="fix-deployment-error"></a>Corrección del error de implementación

Ejecute la implementación con un valor de prefijo válido, como `storage`.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=storage
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName storage
```

---

La implementación comienza y se puede ver en el historial de implementación. Se produce un error en la implementación porque `outputs` hace referencia a una red virtual que no existe en el grupo de recursos. Sin embargo, no hubo errores para la cuenta de almacenamiento, por lo que el recurso se implementó. El historial de implementación muestra una implementación con errores.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/deployment-failed.png" alt-text="Captura de pantalla de información general del grupo de recursos que muestra una implementación con errores.":::

Para corregir el error de implementación, debería cambiar la función de referencia para usar un recurso válido. Para obtener más información, consulte [Solución de errores de recurso no encontrado.](error-not-found.md) En este inicio rápido, elimine la coma que precede a `vnetResult` y a todos los elementos `vnetResult`. Guarde el archivo y vuelva a ejecutar la implementación.

```json
"vnetResult": {
  "type": "object",
  "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
}
```

Una vez corregidos los errores de validación, comprobación previa e implementación, la plantilla siguiente implementa una cuenta de almacenamiento. El historial de implementación y el registro de actividad muestran una implementación correcta.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, elimine el grupo de recursos.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name troubleshootRG
```

---

Para eliminar el grupo de recursos del portal, siga estos pasos:

1. En Azure Portal, escriba **Grupo de recursos** en el cuadro de búsqueda.
1. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.
1. Seleccione el nombre del grupo de recursos.
1. Seleccione **Eliminar grupo de recursos**.
1. Para confirmar la eliminación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a solucionar errores de implementación de plantillas de ARM.

> [!div class="nextstepaction"]
> [Solución de errores comunes de implementación de Azure](common-deployment-errors.md)

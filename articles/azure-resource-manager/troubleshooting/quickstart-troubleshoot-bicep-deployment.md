---
title: Solución de problemas de implementaciones de archivos Bicep
description: Aprenda a supervisar y solucionar problemas de implementaciones de archivos Bicep. Muestra registros de actividad y el historial de implementación.
ms.date: 11/04/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31d212ef608b5fbabb4430b5320ae033ae2be325
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849451"
---
# <a name="quickstart-troubleshoot-bicep-file-deployments"></a>Inicio rápido: Solución de problemas de implementaciones de archivos Bicep

En este inicio rápido se describe cómo solucionar errores de implementación de archivos Bicep. Creará un archivo con errores y aprenderá a corregirlos.

Hay tres tipos de errores relacionados con una implementación:

- Los **errores de validación** se producen antes de que comience una implementación y son debidos a errores de sintaxis en el archivo. El editor puede identificar estos errores.
- Los **errores de validación previa** se producen cuando se ejecuta un comando de implementación, pero los recursos no están implementados. Estos errores se encuentran sin iniciar la implementación. Por ejemplo, si un valor de parámetro es incorrecto, el error se encuentra en la validación previa.
- **Errores de implementación**, que se producen durante el proceso de implementación y solo se pueden encontrar evaluando su progreso.

Todos los tipos de errores devuelven un código de error que se usa para solucionar problemas de implementación. Los errores de validación y de comprobación previa se muestran en el registro de actividad, pero no aparecen en el historial de implementación. Un archivo Bicep con errores de sintaxis no se compila en JSON y no se muestra en el registro de actividad.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este inicio rápido, necesita los siguientes elementos:

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- [Visual Studio Code](https://code.visualstudio.com) con la [extensión de Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) más reciente.
- La versión más reciente de [Azure Powershell](/powershell/azure/install-az-ps) o la [CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-bicep-file-with-errors"></a>Creación de un archivo Bicep con errores

Copie el siguiente archivo Bicep y guárdelo localmente. Lo usará para solucionar problemas de validación, previos y de implementación. En este inicio rápido se da por supuesto que el archivo tiene el nombre **troubleshoot.bicep**, pero puede darle cualquier otro.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

resource existingVNet 'Microsoft.Network/virtualNetworks@2021-03-01' existing = {
  name: 'doesnotexist'
}

output storageAccountName string = storageAccountName
output vnetResult object = existingVNet
```

## <a name="fix-validation-error"></a>Corrección del error de validación

Abra el archivo en Visual Studio Code. Observará que Visual Studio Code identifica un error de sintaxis. La primera declaración de parámetro se marca con ondulados rojos para indicar un error.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/show-visual-studio-code-error.png" alt-text="Captura de pantalla de Visual Studio Code muestra un error en la sintaxis.":::

Las líneas marcadas con un error son:

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'
```

Al mantener el puntero sobre `parameter`, verá un mensaje de error.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/declaration-not-recognized.png" alt-text="Captura de pantalla del mensaje de error Visual Studio Code.":::

El mensaje indica que no se reconoce este tipo de declaración, y que especifique un parámetro, una variable, un recurso o una declaración de salida. Si intenta implementar este archivo, verá el mismo mensaje de error con el comando de implementación.

Si examina la documentación de una [declaración de parámetro](../bicep/parameters.md), verá que la palabra clave es realmente `param`. Al cambiar esa sintaxis, el error de validación desaparece. El decorador `@allowed` también se marcó como un error, pero ese error también se resuelve cambiando la declaración del parámetro. El decorador se marcó como error porque se espera una declaración de parámetro después de este. Esta condición no era verdadera cuando la declaración era incorrecta.

La línea corregida es:

```bicep
param storageAccountType string = 'Standard_LRS'
```

## <a name="fix-preflight-error"></a>Corrección del error previo

Ahora que ha corregido el error de validación, es el momento de implementar el archivo. Sin embargo, proporcionará un valor de parámetro no válido para ver un error previo.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=longNamewith!!Charactersthatarenotallowed
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName longNamewith!!Charactersthatarenotallowed
```

---

Azure Resource Manager determina que el nombre de la cuenta de almacenamiento contiene caracteres que no se permiten. No intenta realizar la implementación.

Verá un mensaje de error que indica que se produjo un error en la validación previa. También recibirá un mensaje que indica que el nombre de la cuenta de almacenamiento debe tener entre 3 y 24 caracteres y usar solo números y letras minúsculas. El prefijo que proporcionó no cumplía ese requisito. Para más información sobre esto código de error, consulte [Resolución de errores de nombres de cuenta de almacenamiento](error-storage-account-name.md).

Dado que el error se ha capturado en la fase de preparación, no existe ninguna implementación en el historial.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/no-deployment.png" alt-text="Captura de pantalla del portal sin ninguna implementación en el historial.":::

Sin embargo, la implementación con errores sí aparece en el registro de actividad.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/preflight-activity-log.png" alt-text="Captura de pantalla del registro de actividad en el error.":::

Puede abrir los detalles de la entrada del registro para ver el mensaje de error.

## <a name="fix-deployment-error"></a>Corrección del error de implementación

Implementará el archivo de nuevo y proporcionará un valor permitido para el parámetro de prefijo de nombre.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=stg
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName stg
```

---

La implementación se inicia, pero se produce un error y aparece un mensaje que indica que no se encontró la red virtual. Normalmente, este error se corregiría cambiando la referencia al recurso. En este inicio rápido, eliminará la referencia. Para más información sobre este código de error, consulte [Resolución de errores de recursos no encontrados](error-not-found.md).

Observe en el portal que la implementación aparece en el historial.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/view-deployment-history.png" alt-text="Captura de pantalla del historial de implementación en el portal.":::

Puede abrir la entrada en el historial de implementación para obtener detalles sobre el error. El error también aparece en el registro de actividad.

El archivo Bicep intenta hacer referencia a una red virtual que no existe en el grupo de recursos. Para corregir el error, elimine la referencia a la red virtual existente.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName
```

Puede implementar ese archivo Bicep sin errores.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, elimine el grupo de recursos. Puede eliminar el grupo de recursos desde Cloud Shell o el portal.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Reemplace `<rgname>`, incluidos los corchetes angulares, por el nombre del grupo de recursos.

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

En este inicio rápido, ha aprendido a solucionar errores de implementación de archivos Bicep.

> [!div class="nextstepaction"]
> [Solución de errores comunes de implementación de Azure](common-deployment-errors.md)

---
title: Descompilación del código JSON de plantilla de ARM en Bicep
description: Describe los comandos para descompilar plantillas de Azure Resource Manager en archivos Bicep.
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9244eb0a076785c424603dc79ba13af6c29fb03
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537026"
---
# <a name="decompiling-arm-template-json-to-bicep"></a>Descompilación del código JSON de plantilla de ARM en Bicep

En este artículo se describe cómo descompilar plantillas de Azure Resource Manager (plantillas de ARM) en archivos Bicep. Para ejecutar los comandos de conversión, debe tener [instalada la CLI de Bicep](./install.md).

La descompilación de una plantilla de ARM le ayuda a empezar a trabajar con el desarrollo en Bicep. Si tiene una biblioteca de plantillas de ARM y desea usar Bicep para futuros desarrollos, puede descompilarlas en Bicep. Sin embargo, es posible que el archivo Bicep necesite revisiones para implementar los procedimientos recomendados para Bicep.

En este artículo se muestra cómo ejecutar el comando `decompile` en la CLI de Azure. Si no usa la CLI de Azure, ejecute el comando sin `az` al principio del comando. Por ejemplo, `az bicep decompile` se convierte en ``bicep decompile``.

## <a name="decompile-from-json-to-bicep"></a>Descompilación de JSON en Bicep

Para descompilar el código JSON de una plantilla de ARM en Bicep, utilice lo siguiente:

```azurecli
az bicep decompile --file main.json
```

El comando crea un archivo llamado _main.bicep_ en el mismo directorio que la plantilla de ARM.

> [!CAUTION]
> La descompilación intenta convertir el archivo, pero no hay ninguna asignación garantizada del código JSON de la plantilla de ARM a Bicep. Es posible que tenga que corregir advertencias y errores en el archivo Bicep generado. La descompilación también puede producir un error si no es posible realizar una conversión precisa. Para notificar cualquier problema o conversiones inexactas, [abra una incidencia](https://github.com/Azure/bicep/issues).

Los comandos decompile y [build](bicep-cli.md#build) generan plantillas que son funcionalmente equivalentes. Sin embargo, es posible que no sean exactamente iguales en la implementación. Si se convierte una plantilla de JSON a Bicep y, después, se realiza el proceso inverso, es probable que se genere una plantilla con una sintaxis diferente a la de la plantilla original. Cuando se implementan, las plantillas convertidas generan los mismos resultados.

## <a name="fix-conversion-issues"></a>Corrección de problemas de conversión

Supongamos que tiene la siguiente plantilla de ARM:

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
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
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

Al descompilarla, obtiene lo siguiente:

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var storageAccountName_var = 'store${uniqueString(resourceGroup().id)}'

resource storageAccountName 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName_var
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName_var
```

El archivo descompilado funciona, pero tiene algunos nombres que tal vez desee cambiar. La variable `var storageAccountName_var` tiene una convención de nomenclatura inusual. La cambiaremos a:

```bicep
var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'
```

El recurso tiene un nombre simbólico que tal vez desee cambiar. En lugar de `storageAccountName` como nombre simbólico, use `exampleStorage`.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
```

Como ha cambiado el nombre de la variable del nombre de la cuenta de almacenamiento, debe cambiar dónde se usa.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
```

Y, en la salida, use:

```bicep
output storageAccountName string = uniqueStorageName
```

El archivo completo es:

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'

resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = uniqueStorageName
```

## <a name="export-template-and-convert"></a>Exportación de la plantilla y conversión

Puede exportar la plantilla para un grupo de recursos y, después, pasarla directamente al comando `decompile`. En el ejemplo siguiente se muestra cómo descompilar una plantilla exportada.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile --file main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporte la plantilla](../templates/export-template-portal.md) a través del portal.

Use `bicep decompile <filename>` en el archivo descargado.

---

## <a name="side-by-side-view"></a>Vista en paralelo

[Bicep Playground](https://aka.ms/bicepdemo) le permite ver la plantilla de ARM y los archivos Bicep equivalentes en paralelo. Puede seleccionar una plantilla de ejemplo para ver ambas versiones. O bien, seleccione `Decompile` para cargar su propia plantilla de ARM y ver el archivo Bicep equivalente.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre todos los comandos de la CLI de Bicep, consulte [Comandos de la CLI de Bicep](bicep-cli.md).

---
title: Salidas en Bicep
description: Se describe cómo definir variables de salida en Bicep.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4ed640cd639b7a1b69cd07ae8ac50a4ddff436fb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491324"
---
# <a name="outputs-in-bicep"></a>Salidas en Bicep

En este artículo, se describe cómo definir valores de salida en un archivo de Bicep. Las salidas se usan cuando es necesario devolver valores de los recursos implementados.

## <a name="define-output-values"></a>Definición de valores de salida

La sintaxis para definir un valor de salida es:

```bicep
output <name> <data-type> = <value>
```

Una salida puede tener el mismo nombre que un parámetro, una variable, un módulo o un recurso. Cada valor de salida debe resolverse en uno de los [tipos de datos](data-types.md).

En el ejemplo siguiente se muestra cómo devolver una propiedad desde un recurso implementado. En el ejemplo, `publicIP` es el nombre simbólico de una dirección IP pública implementada en el archivo de Bicep. El valor de salida obtiene el nombre de dominio completo de la dirección IP pública.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

En el ejemplo siguiente se muestra cómo devolver salidas de distintos tipos.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/outputs/output.bicep":::

Si necesita generar una propiedad que tenga un guion en el nombre, use corchetes alrededor del nombre en lugar de la notación de puntos. Por ejemplo, use `['property-name']` en lugar de `.property-name`.

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>Salida condicional

Cuando el valor que se va a devolver dependa de una condición de la implementación, use el operador `?`.

```bicep
output <name> <data-type> = <condition> ? <true-value> : <false-value>
```

Por norma general, se usa una salida condicional cuando se [implementa condicionalmente](conditional-resource-deployment.md) un recurso. En el ejemplo siguiente, se muestra cómo se devuelve condicionalmente el identificador de recurso de una dirección IP pública en función de si se ha implementado una nueva.

Para especificar una salida condicional en Bicep, use el operador `?`. En el ejemplo siguiente se devuelve una dirección URL de punto de conexión o una cadena vacía en función de una condición.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>Número dinámico de salidas

En algunos escenarios, no se conoce el número de instancias de un valor que se debe devolver al crear la plantilla. Puede devolver un número variable de valores mediante la expresión `for`.

```bicep
output <name> <data-type> = [for <item> in <collection>: {
  ...
}]
```

En el ejemplo siguiente se recorre en iteración una matriz.

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

Para más información sobre los bucles, consulte [Bucles iterativos en Bicep](loops.md).

## <a name="outputs-from-modules"></a>Salidas de módulos

Para obtener un valor de salida de un módulo, use la sintaxis siguiente:

```bicep
<module-name>.outputs.<property-name>
```

En el ejemplo siguiente, se muestra cómo establecer la dirección IP en un equilibrador de carga mediante la recuperación de un valor desde un módulo. El nombre del módulo es `publicIP`.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="get-output-values"></a>Obtención de valores de salida

Cuando la implementación se realiza correctamente, los valores de salida se devuelven automáticamente en los resultados de la implementación.

Para obtener valores de salida del historial de implementación, puede usar la CLI de Azure o un script de Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre las propiedades disponibles para las salidas, consulte [Descripción de la estructura y la sintaxis de Bicep](./file.md).

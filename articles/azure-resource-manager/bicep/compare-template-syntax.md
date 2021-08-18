---
title: Comparación de la sintaxis de las plantillas de Azure Resource Manager en JSON y Bicep
description: Compara las plantillas de Azure Resource Manager desarrolladas con JSON y Bicep, y muestra cómo realizar la conversión entre los dos lenguajes.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 414e073fe6940cc9a223a25515902b3af3ca0a54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744908"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparación de JSON y Bicep para plantillas

En este artículo se compara la sintaxis de Bicep con la sintaxis de JSON para las plantillas de Azure Resource Manager (plantillas de ARM). En la mayoría de los casos, Bicep proporciona una sintaxis menos detallada que el equivalente en JSON.

Si ya tiene experiencia con JSON para desarrollar plantillas de ARM, use los siguientes ejemplos para obtener información sobre los equivalentes de sintaxis para Bicep.

## <a name="expressions"></a>Expresiones

Para crear una expresión:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parámetros

Para declarar un parámetro con un valor predeterminado:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Para obtener el valor de un parámetro:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variables

Para declarar una variable:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Para obtener el valor de una variable:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Cadenas

Para concatenar cadenas:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Operadores lógicos

Para devolver el operador **AND** lógico:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Para establecer un valor de forma condicional:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Ámbito de la implementación

Para establecer el ámbito de destino de la implementación:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Recursos

Para declarar un recurso:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

Para implementar un recurso de forma condicional:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

Para establecer una propiedad de un recurso:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Para obtener el identificador de un recurso en la plantilla:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Bucles

Para recorrer en iteración los elementos de una matriz o un recuento:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Dependencias de recursos

Para Bicep, puede establecer una dependencia explícita, pero no se recomienda este enfoque. En su lugar, use las dependencias implícitas. Se crea una dependencia implícita cuando una declaración de recursos hace referencia al identificador de otro recurso.

A continuación, se muestra una interfaz de red con una dependencia implícita en un grupo de seguridad de red. Hace referencia al grupo de seguridad de red con `nsg.id`.

```bicep
resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  ...
}

resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: nic1Name
  location: location
  properties: {
    ...
    networkSecurityGroup: {
      id: nsg.id
    }
  }
}
```

Si debe establecer una dependencia explícita, use:

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Recursos de referencia

Para obtener una propiedad de un recurso de la plantilla:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Para obtener una propiedad de un recurso existente que no está implementado en la plantilla:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Salidas

Para enviar la salida de una propiedad de un recurso de la plantilla:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Reutilización del código

Para dividir una solución en varios archivos:

* En el caso de Bicep, use [módulos](modules.md).
* Para las plantillas de ARM, use [plantillas vinculadas](../templates/linked-templates.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre Bicep, consulte el [inicio rápido de Bicep](./quickstart-create-bicep-use-visual-studio-code.md).
* Para más información sobre cómo convertir plantillas de un lenguajes a otro, consulte [Conversión de plantillas de ARM entre JSON y Bicep](decompile.md).

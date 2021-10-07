---
title: Definición de varias instancias de una variable en Bicep
description: Use el bucle de variables Bicep para iterar al crear una variable.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 14721057379217ae9d14b97c94483435dde08203
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831993"
---
# <a name="variable-iteration-in-bicep"></a>Iteración de variables en Bicep

En este artículo, se explica cómo puede crear varios valores para una variable en el archivo Bicep. Puede agregar un bucle a la declaración `variables` y establecer dinámicamente el número de elementos de una variable. Evite repetir la sintaxis en el archivo de Bicep.

También puede utilizar el elemento copy con [módulos](loop-modules.md), [recursos](loop-resources.md), [propiedades de un recurso](loop-properties.md) y [salidas](loop-outputs.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los bucles y para obtener una práctica guía, consulte [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) en **Microsoft Learn**.

## <a name="syntax"></a>Sintaxis

Los bucles se pueden usar para declarar varias variables mediante lo siguiente:

- Uso de un índice de bucle.

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  Para obtener más información, consulte [Índice de bucles](#loop-index).

- Iteración en una matriz.

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

  Para obtener más información, consulte [Matriz de bucles](#loop-array).

- Iteración de una matriz y un índice.

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Límites del bucle

Las iteraciones de bucle del archivo Bicep no pueden ser un número negativo ni superar las 800 iteraciones. 

## <a name="loop-index"></a>Índice de bucles

En el ejemplo siguiente, se muestra cómo se crea una matriz de valores de cadena:

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

La salida devuelve una matriz con los valores siguientes:

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

En el ejemplo siguiente, se muestra cómo se crea una matriz de objetos con tres propiedades: `name`, `diskSizeGB` y `diskIndex`.

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

La salida devuelve una matriz con los valores siguientes:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="loop-array"></a>Matriz de bucles

En el siguiente ejemplo se recorre en bucle una matriz que se pasa como parámetro. La variable construye objetos en el formato necesario a partir del parámetro.

```bicep
@description('An array that contains objects with properties for the security rules.')
param securityRules array = [
  {
    name: 'RDPAllow'
    description: 'allow RDP connections'
    direction: 'Inbound'
    priority: 100
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.0.0/24'
    sourcePortRange: '*'
    destinationPortRange: '3389'
    access: 'Allow'
    protocol: 'Tcp'
  }
  {
    name: 'HTTPAllow'
    description: 'allow HTTP connections'
    direction: 'Inbound'
    priority: 200
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.1.0/24'
    sourcePortRange: '*'
    destinationPortRange: '80'
    access: 'Allow'
    protocol: 'Tcp'
  }
]


var securityRulesVar = [for rule in securityRules: {
  name: rule.name
  properties: {
    description: rule.description
    priority: rule.priority
    protocol: rule.protocol
    sourcePortRange: rule.sourcePortRange
    destinationPortRange: rule.destinationPortRange
    sourceAddressPrefix: rule.sourceAddressPrefix
    destinationAddressPrefix: rule.destinationAddressPrefix
    access: rule.access
    direction: rule.direction
  }
}]

resource netSG 'Microsoft.Network/networkSecurityGroups@2020-11-01' = {
  name: 'NSG1'
  location: resourceGroup().location
  properties: {
    securityRules: securityRulesVar
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para otros usos de bucles, consulte:
  - [Iteración de recursos en Bicep](loop-resources.md)
  - [Iteración de módulos en Bicep](loop-modules.md)
  - [Iteración de propiedades en Bicep](loop-properties.md)
  - [Iteración de salidas en Bicep](loop-outputs.md)
- Para establecer las dependencias de los recursos creados en un bucle, consulte [Establecimiento de dependencias de recursos](./resource-declaration.md#set-resource-dependencies).

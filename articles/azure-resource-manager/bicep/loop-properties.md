---
title: Definición de varias instancias de una propiedad en Bicep
description: Use un bucle de propiedades Bicep para iterar al crear una propiedad de recurso.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59ca13e4f6ab29a71c543fcbe779ef616beba43b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026749"
---
# <a name="property-iteration-in-bicep"></a>Iteración de propiedades en Bicep

En este artículo, se muestra cómo crear más de una instancia de una propiedad en el archivo de Bicep. Puede agregar un bucle a la sección `properties` del recurso y establecer dinámicamente el número de elementos de una variable durante la implementación. También evita repetir la sintaxis en el archivo Bicep.

Solo se puede usar un bucle con recursos de nivel superior, incluso cuando se aplica el bucle a una propiedad. Para obtener información sobre cómo cambiar un recurso secundario a un recurso de nivel superior, vea [Iteración para un recurso secundario](loop-resources.md#iteration-for-a-child-resource).

También puede usar el bucle de copia con [recursos](loop-resources.md), [variables](loop-variables.md) y [salidas](loop-outputs.md).

## <a name="syntax"></a>Sintaxis

Los bucles se pueden usar para declarar varias propiedades mediante:

- Iteración en una matriz.

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Iteración en los elementos de una matriz.

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Uso de un índice de bucle.

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Límites del bucle

Las iteraciones de bucle del archivo Bicep no pueden ser un número negativo ni superar las 800 iteraciones. Para implementar archivos Bicep, instale la versión más reciente de las [herramientas de Bicep](install.md).

## <a name="property-iteration"></a>Iteración de propiedades

En este ejemplo se recorre en iteración una matriz para que la propiedad `subnets` cree dos subredes dentro de una red virtual.

```bicep
param rgLocation string = resourceGroup().location

var subnets = [
  {
    name: 'api'
    subnetPrefix: '10.144.0.0/24'
  }
  {
    name: 'worker'
    subnetPrefix: '10.144.1.0/24'
  }
]

resource vnet 'Microsoft.Network/virtualNetworks@2020-07-01' = {
  name: 'vnet'
  location: rgLocation
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.144.0.0/20'
      ]
    }
    subnets: [for subnet in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.subnetPrefix
      }
    }]
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para otros usos de bucles, consulte:
  - [Iteración de recursos en archivos Bicep](loop-resources.md)
  - [Iteración de variables en archivos Bicep](loop-variables.md)
  - [Iteración de la salida en archivos Bicep](loop-outputs.md)
- Si quiere conocer las secciones de una plantilla, consulte [Nociones sobre la estructura y la sintaxis de los archivos Bicep](file.md).
- Para información sobre cómo implementar varios recursos, vea [Uso de módulos de Bicep.](modules.md)
- Para establecer las dependencias de los recursos creados en un bucle, consulte [Establecimiento de dependencias de recursos](./resource-declaration.md#set-resource-dependencies).
- Para información sobre la implementación con PowerShell, consulte [Implementación de recursos con Bicep y Azure PowerShell](deploy-powershell.md).
- Para información sobre la implementación con la CLI de Azure, consulte [Implementación de recursos con Bicep y la CLI de Azure](deploy-cli.md).

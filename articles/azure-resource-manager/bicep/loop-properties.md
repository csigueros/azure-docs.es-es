---
title: Definición de varias instancias de una propiedad en Bicep
description: Use un bucle de propiedades Bicep para iterar al crear una propiedad de recurso.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: a845ab0b650b6b22459b3dace90bbc09a98de0a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625608"
---
# <a name="property-iteration-in-bicep"></a>Iteración de propiedades en Bicep

En este artículo, se muestra cómo crear más de una instancia de una propiedad en el archivo de Bicep. Puede agregar un bucle a la sección `properties` del recurso y establecer dinámicamente el número de elementos de una propiedad. Se evita repetir la sintaxis en el archivo de Bicep.

Solo se puede usar un bucle con recursos de nivel superior, incluso cuando se aplica el bucle a una propiedad. Para obtener información sobre cómo cambiar un recurso secundario a un recurso de nivel superior, vea [Iteración para un recurso secundario](loop-resources.md#iteration-for-a-child-resource).

También puede usar un bucle con [módulos](loop-modules.md), [recursos](loop-resources.md), [variables](loop-variables.md) y [salidas](loop-outputs.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los bucles y para obtener una práctica guía, consulte [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) en **Microsoft Learn**.

## <a name="syntax"></a>Sintaxis

Los bucles se pueden usar para declarar varias propiedades mediante:

- Uso de un índice de bucle.

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

- Iteración en una matriz.

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

  Para obtener más información, consulte [Matriz de bucles](#loop-array).

- Iteración de una matriz y un índice.

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Límites del bucle

El bucle Bicep tiene estas limitaciones:

- No se puede realizar un bucle en varios niveles de propiedades.
- Las iteraciones de bucle no pueden ser un número negativo ni superar las 800 iteraciones.

## <a name="loop-array"></a>Matriz de bucles

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
  - [Iteración de recursos en Bicep](loop-resources.md)
  - [Iteración de módulos en Bicep](loop-modules.md)
  - [Iteración de variables en Bicep](loop-variables.md)
  - [Iteración de salidas en Bicep](loop-outputs.md)
- Para establecer las dependencias de los recursos creados en un bucle, consulte [Establecimiento de dependencias de recursos](./resource-declaration.md#set-resource-dependencies).

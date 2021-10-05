---
title: Implementación de varias instancias de módulos en Bicep
description: Use bucles y matrices en un archivo de Bicep para implementar varias instancias de módulos.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: f8893fa6716d1b106e54f7eb76002622ce5a4bc3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793443"
---
# <a name="module-iteration-in-bicep"></a>Iteración de módulos en Bicep

En este artículo se muestra cómo implementar más de una instancia de un [módulo](modules.md) en el archivo de Bicep. Puede agregar un bucle a una declaración `module` y establecer de manera dinámica el número de veces que se va a implementar ese módulo. Se evita repetir la sintaxis en el archivo de Bicep.

También puede usar un bucle con [recursos](loop-resources.md), [propiedades](loop-properties.md), [variables](loop-variables.md) y [salidas](loop-outputs.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los bucles y para obtener una práctica guía, consulte [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) en **Microsoft Learn**.

## <a name="syntax"></a>Sintaxis

Los bucles se pueden usar para declarar varios módulos por medio de:

- Uso de un índice de bucle.

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <index> in range(<start>, <stop>): {
    <module-properties>
  }]
  ```

  Para obtener más información, consulte [Índice de bucles](#loop-index).

- Iteración en una matriz.

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <item> in <collection>: {
    <module-properties>
  }]
  ```

  Para obtener más información, vea [Matriz de bucles](#loop-array).

- Iteración en una matriz y un índice:

  ```bicep
  module <module-symbolic-name> 'module-file' = [for (<item>, <index>) in <collection>: {
    <module-properties>
  }]
  ```

## <a name="loop-limits"></a>Límites del bucle

Las iteraciones de bucle del archivo Bicep no pueden ser un número negativo ni superar las 800 iteraciones.

## <a name="loop-index"></a>Índice de bucles

En el ejemplo siguiente se implementa un módulo el número de veces especificado en el parámetro `storageCount`. Cada instancia del módulo crea una cuenta de almacenamiento.

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Observe que el índice `i` se usa para crear el nombre del recurso de la cuenta de almacenamiento. La cuenta de almacenamiento se pasa como un valor de parámetro al módulo.

## <a name="loop-array"></a>Matriz de bucles

En el ejemplo siguiente se implementa un módulo para cada nombre proporcionado en el parámetro `storageNames`. El módulo crea una cuenta de almacenamiento.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

module stgModule './storageAccount.bicep' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  params: {
    storageName: name
    location: location
  }
}]

```

No se admite la referencia a una colección de módulos en los bucles de salida. Para generar resultados a partir de los módulos de una colección, aplique un indizador de matrices a la expresión. Para obtener más información, vea [Iteración de la salida](loop-outputs.md).

## <a name="module-iteration-with-condition"></a>Iteración de módulos con una condición

En el ejemplo siguiente se muestra un archivo de Bicep con un bucle de módulo filtrado. Los filtros deben ser expresiones que se evalúen como un valor booleano.

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

En el ejemplo anterior se llama al módulo solo si el valor booleano es `true`.

Los filtros también se admiten con [bucles de recursos](loop-resources.md).

## <a name="deploy-in-batches"></a>Implementación por lotes

Resource Manager crea los recursos en paralelo de forma predeterminada. Cuando se usa un bucle para crear varias instancias de un tipo de recurso, todas esas instancias se implementan al mismo tiempo. No se garantiza el orden en el que se crean. No hay ningún límite en el número de recursos implementados en paralelo, aparte del límite total de 800 recursos del archivo de Bicep.

Es posible que no quiera actualizar todas las instancias de un tipo de recurso al mismo tiempo. Por ejemplo, al actualizar un entorno de producción, puede que quiera escalonar las actualizaciones para que solo una cantidad determinada se actualice al mismo tiempo. Puede especificar que un subconjunto de las instancias se agrupe por lotes e implemente al mismo tiempo. Las demás instancias esperan a que se complete ese lote.

Para implementar instancias de un módulo en serie, agregue el [decorador batchSize](./file.md#resource-and-module-decorators). Establezca su valor en el número de instancias que se van a implementar a la vez. Se crea una dependencia en las instancias anteriores del bucle, por lo que no se inicia ningún lote hasta que se completa el lote anterior.

```bicep
param location string = resourceGroup().location

@batchSize(2)
module stgModule './storageAccount.bicep' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

Para una implementación puramente secuencial, establezca el tamaño del lote en 1.

## <a name="next-steps"></a>Pasos siguientes

- Para otros usos del bucle, consulte:
  - [Iteración de recursos en Bicep](loop-resources.md)
  - [Iteración de propiedades en Bicep](loop-properties.md)
  - [Iteración de variables en Bicep](loop-variables.md)
  - [Iteración de salidas en Bicep](loop-outputs.md)
- Para obtener más información sobre los módulos, vea [Uso de módulos de Bicep](modules.md).
- Para establecer las dependencias de los recursos creados en un bucle, consulte [Establecimiento de dependencias de recursos](./resource-declaration.md#set-resource-dependencies).

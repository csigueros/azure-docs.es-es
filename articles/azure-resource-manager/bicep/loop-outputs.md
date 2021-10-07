---
title: Definición de varias instancias de un valor de salida en Bicep
description: Use un bucle de salida de Bicep para iterar y devolver valores de implementación.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 28aa16d5f65891df063db7b43211e466855dfeec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832054"
---
# <a name="output-iteration-in-bicep"></a>Iteración de la salida en Bicep

En este artículo, se muestra cómo puede crear más de un valor para una salida en el archivo Bicep. Puede agregar un bucle a la sección `output` del archivo y devolver dinámicamente varios elementos durante la implementación.

También puede usar un bucle con [módulos](loop-modules.md), [recursos](loop-resources.md), [propiedades de un recurso](loop-properties.md) y [variables](loop-variables.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los bucles y para obtener una práctica guía, consulte [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) en **Microsoft Learn**.

## <a name="syntax"></a>Sintaxis

Los bucles se pueden usar para devolver elementos durante la implementación mediante lo siguiente:

- Uso de un índice de bucle.

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  Para obtener más información, consulte [Índice de bucles](#loop-index).

- Iteración en una matriz.

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Iteración en una matriz y un índice.

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

  Para obtener más información, vea [Matriz e índice de bucles](#loop-array-and-index).

## <a name="loop-limits"></a>Límites del bucle

Las iteraciones de bucle del archivo Bicep no pueden ser un número negativo ni superar las 800 iteraciones. 

## <a name="loop-index"></a>Índice de bucles

En el ejemplo siguiente se crea un número variable de cuentas de almacenamiento y se devuelve un punto de conexión para cada cuenta de almacenamiento.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): stg[i].properties.primaryEndpoints.blob]
```

La salida devuelve una matriz con los valores siguientes:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

En el ejemplo siguiente se devuelven tres propiedades de las nuevas cuentas de almacenamiento.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: stg[i].id
  blobEndpoint: stg[i].properties.primaryEndpoints.blob
  status: stg[i].properties.statusOfPrimary
}]
```

La salida devuelve una matriz con los valores siguientes:

```json
[
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="loop-array-and-index"></a>Índice y matriz de bucles

En este ejemplo se usan los elementos de una matriz y un índice.

```bicep
param rgLocation string = resourceGroup().location

var stgNames = [
  'demostg1'
  'demostg2'
  'demostg3'
]

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in stgNames: {
  name: name
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}]

output stgOutput array = [for (name, i) in stgNames: {
  name: stg[i].name
  resourceId: stg[i].id
}]
```

La salida devuelve una matriz con los valores siguientes:

```json
[
  {
    "name": "demostg1",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>Pasos siguientes

- Para otros usos de bucles, consulte:
  - [Iteración de recursos en Bicep](loop-resources.md)
  - [Iteración de módulos en Bicep](loop-modules.md)
  - [Iteración de propiedades en Bicep](loop-properties.md)
  - [Iteración de variables en Bicep](loop-variables.md)
- Para establecer las dependencias de los recursos creados en un bucle, consulte [Establecimiento de dependencias de recursos](./resource-declaration.md#set-resource-dependencies).


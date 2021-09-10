---
title: Implementación de varias instancias de recursos en Bicep
description: Utilice bucles y matrices en un archivo de Bicep para implementar varias instancias de recursos.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 1b044b4ae3f5d73ad535d44153ea3d47023aeaaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225314"
---
# <a name="resource-iteration-in-bicep"></a>Iteración de recursos en Bicep

En este artículo, se muestra cómo crear varias instancias de un recurso en el archivo de Bicep. Puede agregar un bucle a una declaración `resource` y establecer dinámicamente el número de recursos que se van a implementar. Se evita repetir la sintaxis en el archivo Bicep.

También puede usar un bucle con [módulos](loop-modules.md), [propiedades](loop-properties.md), [variables](loop-variables.md) y [salidas](loop-outputs.md).

Si tiene que especificar si un recurso se implementa, consulte [Elemento condition](conditional-resource-deployment.md).

## <a name="syntax"></a>Sintaxis

Los bucles se pueden usar para declarar varios recursos al:

- Uso de un índice de bucle.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

  Para obtener más información, vea [Índice de bucles](#loop-index).

- Iteración en una matriz.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

  Para obtener más información, vea [Matriz de bucles](#loop-array).

- Iteración en una matriz y un índice.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

  Para obtener más información, vea [Matriz e índice de bucles](#loop-array-and-index).

## <a name="loop-limits"></a>Límites del bucle

Las iteraciones de bucle del archivo Bicep no pueden ser un número negativo ni superar las 800 iteraciones.

## <a name="loop-index"></a>Índice de bucles

En el ejemplo siguiente, se crea el número de cuentas de almacenamiento especificado en el parámetro `storageCount`.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Observe que el índice `i` se usa para crear el nombre del recurso de la cuenta de almacenamiento.

## <a name="loop-array"></a>Matriz de bucles

En el ejemplo siguiente, se crea una cuenta de almacenamiento para cada nombre proporcionado en el parámetro `storageNames`.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Si desea devolver valores de los recursos implementados, puede usar un bucle en la [sección output](loop-outputs.md).

## <a name="loop-array-and-index"></a>Índice y matriz de bucles

En el ejemplo siguiente se usa el elemento de matriz y el valor de índice al definir la cuenta de almacenamiento.

```bicep
param storageAccountNamePrefix string

var storageConfigurations = [
  {
    suffix: 'local'
    sku: 'Standard_LRS'
  }
  {
    suffix: 'geo'
    sku: 'Standard_GRS'
  }
]

resource storageAccountResources 'Microsoft.Storage/storageAccounts@2021-02-01' = [for (config, i) in storageConfigurations: {
  name: '${storageAccountNamePrefix}${config.suffix}${i}'
  location: resourceGroup().location
  properties: {
    supportsHttpsTrafficOnly: true
    accessTier: 'Hot'
    encryption: {
      keySource: 'Microsoft.Storage'
      services: {
        blob: {
          enabled: true
        }
        file: {
          enabled: true
        }
      }
    }
  }
  kind: 'StorageV2'
  sku: {
    name: config.sku
  }
}]
```

## <a name="resource-iteration-with-condition"></a>Iteración de recursos con una condición

En el ejemplo siguiente, se muestra un bucle anidado combinado con un bucle de recursos filtrado. Los filtros deben ser expresiones que se evalúen como un valor booleano.

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

Los filtros también se admiten con [bucles de módulo](loop-modules.md).

## <a name="deploy-in-batches"></a>Implementación por lotes

Resource Manager crea los recursos en paralelo de forma predeterminada. Cuando se usa un bucle para crear varias instancias de un tipo de recurso, todas esas instancias se implementan al mismo tiempo. No se garantiza el orden en el que se crean. No hay ningún límite en el número de recursos implementados en paralelo, aparte del límite total de 800 recursos del archivo de Bicep.

Es posible que no quiera actualizar todas las instancias de un tipo de recurso al mismo tiempo. Por ejemplo, al actualizar un entorno de producción, puede que quiera escalonar las actualizaciones para que solo una cantidad determinada se actualice al mismo tiempo. Puede especificar que un subconjunto de las instancias se agrupe por lotes e implemente al mismo tiempo. Las demás instancias esperan a que se complete ese lote.

Para implementar instancias de un recurso en serie, agregue el [decorador batchSize](./file.md#resource-and-module-decorators). Establezca su valor en el número de instancias que se van a implementar a la vez. Se crea una dependencia en las instancias anteriores del bucle, por lo que no se inicia ningún lote hasta que se completa el lote anterior.

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Para una implementación puramente secuencial, establezca el tamaño del lote en 1.

## <a name="iteration-for-a-child-resource"></a>Iteración para un recurso secundario

No puede usar un bucle para un recurso secundario anidado. Para crear más de una instancia de un recurso secundario, cambie el recurso secundario a un recurso de nivel superior.

Por ejemplo, suponga que normalmente define un servicio de archivos y un recurso compartido de archivos como recursos anidados para una cuenta de almacenamiento.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

Para crear más de un recurso compartido de archivos, muévalo fuera de la cuenta de almacenamiento. La relación con el recurso primario se define mediante la propiedad `parent`.

En el ejemplo siguiente, se muestra cómo crear una cuenta de almacenamiento, un servicio de archivos y más de un recurso compartido de archivos:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="next-steps"></a>Pasos siguientes

- Para otros usos del bucle, consulte:
  - [Iteración de propiedades en Bicep](loop-properties.md)
  - [Iteración de variables en Bicep](loop-variables.md)
  - [Iteración de salidas en Bicep](loop-outputs.md)
- Para establecer las dependencias de los recursos creados en un bucle, consulte [Establecimiento de dependencias de recursos](./resource-declaration.md#set-resource-dependencies).

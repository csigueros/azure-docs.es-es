---
title: Bucles iterativos en Bicep
description: Uso de bucles para recorrer en iteración las colecciones de Bicep
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 9c38824fd3fdf10ce807b66aeccda2c0631cbc58
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269867"
---
# <a name="iterative-loops-in-bicep"></a>Bucles iterativos en Bicep

En este artículo se muestra cómo utilizar la sintaxis `for` para recorrer en iteración los elementos de una colección. Puede usar bucles para definir varias copias de un recurso, un módulo, una variable, una propiedad o una salida. Use bucles para evitar la repetición de la sintaxis en el archivo de Bicep y para establecer de manera dinámica la cantidad de copias que se van a crear durante la implementación.

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los bucles y para obtener una práctica guía, consulte [Creación de plantillas de Bicep flexibles mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) en **Microsoft Learn**.

## <a name="loop-syntax"></a>Sintaxis de bucle

Los bucles se pueden declarar como se indica a continuación:

- Mediante el uso de un **índice de enteros**. Esta opción es útil cuando el escenario es: "Quiero crear esta cantidad de instancias". La [función range](bicep-functions-array.md#range) crea una matriz de enteros que comienza en el índice de inicio y contiene el número de elementos especificados. En el bucle, puede utilizar el índice entero para modificar los valores. Para más información, consulte la sección [Índice de enteros](#integer-index).

  ```bicep
  [for <index> in range(<startIndex>, <numberOfElements>): {
    ...
  }]
  ```

- Mediante el uso de los **elementos de una matriz**. Esta opción es útil cuando el escenario es: "Quiero crear una instancia para cada elemento de una matriz". En el bucle, puede usar el valor del elemento de matriz actual para modificar los valores. Para más información, consulte la sección [Elementos de matriz](#array-elements).

  ```bicep
  [for <item> in <collection>: {
    ...
  }]
  ```

- Mediante el uso de los **elementos de un objeto de diccionario**. Esta opción es útil cuando el escenario es: "Quiero crear una instancia para cada elemento de un objeto". La [función items](bicep-functions-array.md#items) convierte el objeto en una matriz. En el bucle, puede usar las propiedades del objeto para crear los valores. Para más información, consulte la sección [Objeto de diccionario](#dictionary-object).

  ```bicep
  [for <item> in items(<object>): {
    ...
  }]
  ```

- Mediante el uso de un **índice de enteros y los elementos de una matriz**. Esta opción es útil cuando el escenario es: "Quiero crear una instancia para cada elemento de una matriz, pero también necesito que el índice actual cree otro valor". Para obtener más información, vea [Matriz e índice de bucles](#array-and-index).

  ```bicep
  [for (<item>, <index>) in <collection>: {
    ...
  }]
  ```

- Mediante la incorporación de una **implementación condicional**. Esta opción es útil cuando el escenario es: "Quiero crear varias instancias pero, para cada instancia, solo quiero realizar la implementación cuando se cumpla una condición". Para más información, consulte la sección [Bucle con condición](#loop-with-condition).

  ```bicep
  [for <item> in <collection>: if(<condition>) {
    ...
  }]
  ```

## <a name="loop-limits"></a>Límites del bucle

El uso de bucles en Bicep tiene las limitaciones siguientes:

- Las iteraciones de bucle no pueden ser un número negativo ni superar las 800 iteraciones.
- No se puede realizar un bucle de un recurso con recursos secundarios anidados. Cambie los recursos secundarios a recursos de nivel superior.  Consulte [Iteración para un recurso secundario](#iteration-for-a-child-resource).
- No se puede realizar un bucle en varios niveles de propiedades.

## <a name="integer-index"></a>Índice de enteros

Para un ejemplo sencillo del uso de un índice, cree una **variable** que contenga cualquier matriz de cadenas.

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

En el ejemplo siguiente, se crea el número de cuentas de almacenamiento especificado en el parámetro `storageCount`. Devuelve tres propiedades para cada cuenta de almacenamiento.

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

output storageInfo array = [for i in range(0, storageCount): {
  id: storageAcct[i].id
  blobEndpoint: storageAcct[i].properties.primaryEndpoints.blob
  status: storageAcct[i].properties.statusOfPrimary
}]
```

Observe que el índice `i` se usa para crear el nombre del recurso de la cuenta de almacenamiento.

En el ejemplo siguiente, un módulo se implementa varias veces.

```bicep
param location string
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

## <a name="array-elements"></a>Elementos de matriz

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

En el ejemplo siguiente, se recorre en iteración una matriz a fin de definir una propiedad. Crea dos subredes dentro de una red virtual.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/loops/loopproperty.bicep" highlight="23-28" :::

## <a name="array-and-index"></a>Matriz e índice

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

En el ejemplo siguiente, se utilizan los elementos de una matriz y un índice para generar información sobre los recursos nuevos.

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

## <a name="dictionary-object"></a>Objeto de diccionario

Para recorrer en iteración los elementos de un objeto de diccionario, use la [función items](bicep-functions-array.md#items), que convierte el objeto en una matriz. Utilice la propiedad `value` para obtener propiedades de los objetos.

```bicep
param nsgValues object = {
  nsg1: {
    name: 'nsg-westus1'
    location: 'westus'
  }
  nsg2: {
    name: 'nsg-east1'
    location: 'eastus'
  }
}

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for nsg in items(nsgValues): {
  name: nsg.value.name
  location: nsg.value.location
}]
```

## <a name="loop-with-condition"></a>Bucle con condición

En el caso de los **recursos y módulos**, puede agregar una expresión `if` con la sintaxis de bucle para implementar condicionalmente la colección.

En el ejemplo siguiente, se muestra un bucle combinado con una instrucción de condición. En este ejemplo, se aplica una condición única a todas las instancias del módulo.

```bicep
param location string
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

En el ejemplo siguiente, se muestra cómo aplicar una condición específica del elemento actual en la matriz.

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

## <a name="deploy-in-batches"></a>Implementación por lotes

De manera predeterminada, los recursos de Azure se implementan en paralelo. Cuando se usa un bucle para crear varias instancias de un tipo de recurso, todas esas instancias se implementan al mismo tiempo. No se garantiza el orden en el que se crean. No hay ningún límite en el número de recursos implementados en paralelo, aparte del límite total de 800 recursos del archivo de Bicep.

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

Para una implementación secuencial, establezca el tamaño del lote en 1.

El decorador `batchSize` está en el [espacio de nombres sys](bicep-functions.md#namespaces-for-functions). Si necesita diferenciar este decorador de otro elemento con el mismo nombre, ponga **sys** delante: `@sys.batchSize(2)`

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

- Para establecer las dependencias de los recursos creados en un bucle, consulte [Dependencias de recursos](./resource-declaration.md#dependencies).

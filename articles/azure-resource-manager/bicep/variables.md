---
title: Variables de Bicep
description: Describe cómo definir variables en Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 13cb7847019e6b8a4e6e00c6be8d5949a03b3072
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219571"
---
# <a name="variables-in-bicep"></a>Variables de Bicep

En este artículo se describe cómo definir y usar variables en el archivo de Bicep. Las variables se usan para simplificar el desarrollo de archivos de Bicep. En lugar de repetir expresiones complicadas en todo el archivo de Bicep, defina una variable que contenga la expresión complicada. A continuación, use esa variable en todo el archivo de Bicep tanto como sea necesario.

Resource Manager resuelve las variables antes de iniciar las operaciones de implementación. Siempre que la variable se use en el archivo de Bicep, Resource Manager la reemplaza por el valor resuelto.

## <a name="define-variable"></a>Definición de la variable

La sintaxis para definir una variable es:

```bicep
var <variable-name> = <variable-value>
```

Al definir una variable, no se especifica un [tipo de datos](data-types.md) para ella. El tipo se deduce del valor. En el siguiente ejemplo se establece una variable en una cadena.

```bicep
var stringVar = 'example value'
```

Puede usar el valor de un parámetro u otra variable al construir la variable.

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'preset variable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'

output addToVar string = concatToVar
output addToParam string = concatToParam
```

El ejemplo anterior devuelve:

```json
{
  "addToParam": {
    "type": "String",
    "value": "deployment parameterAddToParam"
  },
  "addToVar": {
    "type": "String",
    "value": "preset variableAddToVar"
  }
}
```

Puede usar las [funciones de Bicep](bicep-functions.md) para construir el valor de la variable. En el ejemplo siguiente se usan funciones de Bicep para crear un valor de cadena para un nombre de cuenta de almacenamiento.

```bicep
param storageNamePrefix string = 'stg'
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

output uniqueStorageName string = storageName
```

En el ejemplo anterior se devuelve un valor como el siguiente:

```json
"uniqueStorageName": {
  "type": "String",
  "value": "stghzuunrvapn6sw"
}
```

Puede usar bucles iterativos al definir una variable. En el ejemplo siguiente se crea una matriz de objetos con tres propiedades.

```bicep
param itemCount int = 3

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
  }
]
```

Para más información sobre los tipos de bucles que puede usar con variables, consulte [Bucles iterativos en Bicep](loops.md).

## <a name="use-variable"></a>Uso de la variable

En el ejemplo siguiente se muestra cómo usar la variable para una propiedad de recurso. Se hace referencia al valor de la variable al proporcionar el nombre de la variable: `storageName`.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variableswithfunction.bicep" highlight="4,7,15" :::

Dado que los nombres de cuenta de almacenamiento deben usar letras minúsculas, la variable `storageName` usa la función `toLower` para convertir el valor `storageNamePrefix` en minúsculas. La función `uniqueString` crea un valor único a partir del identificador del grupo de recursos. Los valores se concatenan a una cadena.

## <a name="configuration-variables"></a>Variables de configuración

Puede definir variables que contengan valores relacionados para configurar un entorno. La variable se define como un objeto con los valores. En el ejemplo siguiente se muestra un objeto que contiene valores para dos entornos: **test** y **prod**. Pase uno de estos valores durante la implementación.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las propiedades disponibles para variables, consulte [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](file.md).
- Para información sobre el uso de la sintaxis de bucle, consulte [Bucles iterativos en Bicep](loops.md).
---
title: Variables de Bicep
description: Describe cómo definir variables en Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 040e40d20fe81bb72493f087c9d0583a911b1ee7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733345"
---
# <a name="variables-in-bicep"></a>Variables de Bicep

En este artículo se describe cómo definir y usar variables en el archivo de Bicep. Las variables se usan para simplificar el desarrollo de archivos de Bicep. En lugar de repetir expresiones complicadas en todo el archivo de Bicep, defina una variable que contenga la expresión complicada. A continuación, use esa variable en todo el archivo de Bicep tanto como sea necesario.

Resource Manager resuelve las variables antes de iniciar las operaciones de implementación. Siempre que la variable se use en el archivo de Bicep, Resource Manager la reemplaza por el valor resuelto.

## <a name="define-variable"></a>Definición de la variable

Al definir una variable, no se especifica un [tipo de datos](data-types.md) para la variable. En su lugar, se especifica un valor o una expresión de plantilla. El tipo de variable se infiere a partir del valor resuelto. En el siguiente ejemplo se establece una variable en una cadena.

```bicep
var stringVar = 'example value'
```

Puede usar el valor de un parámetro u otra variable al construir la variable.

```bicep
param inputValue string = 'deployment Parameter'

var stringVar = 'myVariable'

var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'
```

Puede usar las [funciones de Bicep](bicep-functions.md) para construir el valor de la variable. Las funciones [reference](bicep-functions-resource.md#reference) y [list](bicep-functions-resource.md#list) son válidas cuando se declara una variable.

En el ejemplo siguiente se crea un valor de cadena para un nombre de cuenta de almacenamiento. Usa varias funciones de Bicep para obtener un valor de parámetro y lo concatena a una cadena única.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

El ejemplo siguiente no implementa ningún recurso. Muestra cómo declarar variables de distintos tipos.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variables.bicep":::

Puede usar bucles para declarar una variable de matriz que tenga un número dinámico de elementos. Para más información, consulte [Iteración de variables en Bicep](loop-variables.md).

## <a name="use-variable"></a>Uso de la variable

En el ejemplo siguiente se muestra cómo usar la variable para una propiedad de recurso. Se hace referencia al valor de la variable al proporcionar el nombre de la variable: `storageName`.

```bicep
param rgLocation string = resourceGroup().location
param storageNamePrefix string = 'STG'

var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}

output stgOutput string = storageName
```

Dado que los nombres de cuenta de almacenamiento deben usar letras minúsculas, la variable `storageName` usa la función `toLower` para convertir el valor `storageNamePrefix` en minúsculas. La función `uniqueString` crea un valor único a partir del identificador del grupo de recursos. Los valores se concatenan a una cadena.

## <a name="configuration-variables"></a>Variables de configuración

Puede definir variables que contengan valores relacionados para configurar un entorno. La variable se define como un objeto con los valores. En el ejemplo siguiente se muestra un objeto que contiene valores para dos entornos: **test** y **prod**. Pase uno de estos valores durante la implementación.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las propiedades disponibles para variables, consulte [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](file.md).
- Para obtener información sobre el uso de bucles con la declaración de variables, consulte [Iteración de variables en Bicep](loop-variables.md).

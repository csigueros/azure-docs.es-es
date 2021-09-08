---
title: Parámetros en archivos Bicep
description: Se describe cómo definir parámetros en un archivo Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 901e95708be75ebd4415c90dbd51eeb46ba492c4
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450258"
---
# <a name="parameters-in-bicep"></a>Parámetros en Bicep

En este artículo se describe cómo definir y usar parámetros en un archivo Bicep. Si proporciona valores diferentes para los parámetros, podrá volver a usar un archivo Bicep para distintos entornos.

Resource Manager resuelve los valores de parámetro antes de iniciar las operaciones de implementación. Siempre que se use el parámetro, Resource Manager lo reemplaza por el valor resuelto.

Cada parámetro debe establecerse en uno de los [tipos de datos](data-types.md).

## <a name="minimal-declaration"></a>Declaración mínima

Cada parámetro necesita un nombre y un tipo. Un parámetro no puede tener el mismo nombre que una variable, recurso, salida u otro parámetro en el mismo ámbito.

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="decorators"></a>Elementos Decorator

Los parámetros usan decoradores para restricciones o metadatos. Los decoradores tienen el formato `@expression` y se colocan encima de la declaración del parámetro.

```bicep
@expression
param stgAcctName string
```

En las secciones siguientes de este artículo se muestra cómo usar los decoradores que están disponibles en un archivo Bicep.

## <a name="secure-parameters"></a>Parámetros seguros

Puede marcar los parámetros de objeto o cadena como seguros. El valor de un parámetro seguro no se guarda en el historial de implementaciones y no se registra.

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

## <a name="allowed-values"></a>Valores permitidos

Puede definir valores permitidos para un parámetro. Los valores permitidos se proporcionan en una matriz. Se produce un error en la implementación durante la validación si se pasa un valor para el parámetro que no es uno de los valores permitidos.

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

## <a name="default-value"></a>Valor predeterminado

Puede especificar un valor predeterminado para un parámetro. El valor predeterminado se usa cuando no se proporciona un valor durante la implementación.

```bicep
param demoParam string = 'Contoso'
```

Para especificar un valor predeterminado junto con otras propiedades para el parámetro, use la sintaxis siguiente.

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

Puede usar expresiones con el valor predeterminado. No se permiten expresiones con otras propiedades de parámetro. La función no puede usar la función [reference](bicep-functions-resource.md#reference) ni ninguna de las funciones [list](bicep-functions-resource.md#list) de la sección de parámetros. Estas funciones obtienen el estado del runtime del recurso y no se pueden ejecutar antes de la implementación cuando se resuelven parámetros.

```bicep
param location string = resourceGroup().location
```

Puede usar otro valor de parámetro para compilar un valor predeterminado. La plantilla siguiente crea un nombre de plan de host a partir del nombre del sitio.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterswithfunctions.bicep":::

## <a name="length-constraints"></a>Restricciones de longitud

Puede especificar la longitud mínima y máxima de los parámetros de matriz y de cadena. Puede establecer una o las dos restricciones. Para las cadenas, la longitud indica el número de caracteres. Para las matrices, la longitud indica el número de elementos de la matriz.

En el ejemplo siguiente se declaran dos parámetros. Un parámetro es para un nombre de cuenta de almacenamiento que debe tener entre 3 y 24 caracteres. El otro parámetro es una matriz que debe tener entre 1 y 5 elementos.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

## <a name="integer-constraints"></a>Restricciones de enteros

Puede establecer valores mínimos y máximos para los parámetros de entero. Puede establecer una o las dos restricciones.

```bicep
@minValue(1)
@maxValue(12)
param month int
```

## <a name="description"></a>Descripción

Para ayudar a los usuarios a comprender el valor que se debe proporcionar, agregue una descripción al parámetro. Al implementar la plantilla a través del portal, el texto que proporciona de la descripción se usa automáticamente como sugerencia para ese parámetro. Agregue solo una descripción cuando el texto proporcione más información de la que se puede deducir del nombre del parámetro.

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

## <a name="use-parameter"></a>Uso del parámetro

Para hacer referencia al valor de un parámetro, use el nombre del parámetro. En el ejemplo siguiente se usa un valor de parámetro para un nombre de almacén de claves.

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

## <a name="objects-as-parameters"></a>Objetos como parámetros

Puede ser más fácil organizar los valores relacionados pasándolos como objetos. Con este enfoque también se reduce el número de parámetros de la plantilla.

En el ejemplo siguiente se muestra un parámetro que es un objeto. El valor predeterminado muestra las propiedades esperadas para el objeto. Esas propiedades se usan al definir el recurso que se va a implementar.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterobject.bicep":::


## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las propiedades disponibles para parámetros, consulte [Nociones sobre la estructura y la sintaxis de los archivos Bicep](file.md).
- Para más información sobre cómo pasar valores de parámetro como un archivo, consulte [Creación de un archivo de parámetros de Bicep](parameter-files.md).

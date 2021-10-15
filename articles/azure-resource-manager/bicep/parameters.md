---
title: Parámetros en archivos Bicep
description: Se describe cómo definir parámetros en un archivo Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: b90fb108df58c41578bf9472390574b4bc174111
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363509"
---
# <a name="parameters-in-bicep"></a>Parámetros en Bicep

En este artículo se describe cómo definir y usar parámetros en un archivo Bicep. Si proporciona valores diferentes para los parámetros, podrá volver a usar un archivo Bicep para distintos entornos.

Resource Manager resuelve los valores de parámetro antes de iniciar las operaciones de implementación. Siempre que se use el parámetro, Resource Manager lo reemplaza por el valor resuelto.

Cada parámetro debe establecerse en uno de los [tipos de datos](data-types.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los parámetros y para obtener instrucciones prácticas, consulte [Creación de plantillas de Bicep reutilizables mediante parámetros](/learn/modules/build-reusable-bicep-templates-parameters) en **Microsoft Learn**.

## <a name="declaration"></a>Declaración

Cada parámetro necesita un nombre y un tipo. Un parámetro no puede tener el mismo nombre que una variable, recurso, salida u otro parámetro en el mismo ámbito.

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="default-value"></a>Valor predeterminado

Puede especificar un valor predeterminado para un parámetro. El valor predeterminado se usa cuando no se proporciona un valor durante la implementación.

```bicep
param demoParam string = 'Contoso'
```

Puede usar expresiones con el valor predeterminado. No se permiten expresiones con otras propiedades de parámetro. La función no puede usar la función [reference](bicep-functions-resource.md#reference) ni ninguna de las funciones [list](bicep-functions-resource.md#list) de la sección de parámetros. Estas funciones obtienen el estado del runtime del recurso y no se pueden ejecutar antes de la implementación cuando se resuelven parámetros.

```bicep
param location string = resourceGroup().location
```

Puede usar otro valor de parámetro para compilar un valor predeterminado. La plantilla siguiente crea un nombre de plan de host a partir del nombre del sitio.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterswithfunctions.bicep" highlight="2":::

## <a name="decorators"></a>Elementos Decorator

Los parámetros usan decoradores para restricciones o metadatos. Los decoradores tienen el formato `@expression` y se colocan encima de la declaración del parámetro. Puede marcar un parámetro como seguro, especificar los valores permitidos, establecer la longitud mínima y máxima de una cadena, establecer el valor mínimo y máximo de un entero y proporcionar una descripción del parámetro.

En el ejemplo siguiente, se muestran dos usos comunes de los decoradores.

```bicep
@secure()
param demoPassword string

@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

Los decoradores están en el [espacio de nombres sys](bicep-functions.md#namespaces-for-functions). Si tiene que diferenciar un decorador de otro elemento con el mismo nombre, anteceda el decorador con `sys`. Por ejemplo, si el archivo de Bicep incluye un parámetro llamado `description`, debe agregar el espacio de nombres sys al usar el decorador **description**.

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

Los decoradores disponibles se describen en las secciones siguientes.

### <a name="secure-parameters"></a>Parámetros seguros

Puede marcar los parámetros de objeto o cadena como seguros. El valor de un parámetro seguro no se guarda en el historial de implementaciones y no se registra.

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

### <a name="allowed-values"></a>Valores permitidos

Puede definir valores permitidos para un parámetro. Los valores permitidos se proporcionan en una matriz. Se produce un error en la implementación durante la validación si se pasa un valor para el parámetro que no es uno de los valores permitidos.

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

### <a name="length-constraints"></a>Restricciones de longitud

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

### <a name="integer-constraints"></a>Restricciones de enteros

Puede establecer valores mínimos y máximos para los parámetros de entero. Puede establecer una o las dos restricciones.

```bicep
@minValue(1)
@maxValue(12)
param month int
```

### <a name="description"></a>Descripción

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

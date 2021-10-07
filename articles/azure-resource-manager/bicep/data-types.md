---
title: Tipos de datos de Bicep
description: Describe los tipos de datos disponibles en Bicep.
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 936f17273a95ceb77030497b27f7f73defc37896
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624405"
---
# <a name="data-types-in-bicep"></a>Tipos de datos de Bicep

En este artículo se describen los tipos de datos admitidos en [Bicep](./overview.md).

## <a name="supported-types"></a>Tipos admitidos

Dentro de Bicep, puede usar estos tipos de datos:

* array
* bool
* int
* object
* secureObject: indicado por el modificador de Bicep.
* secureString: indicado por el modificador de Bicep.
* string

## <a name="arrays"></a>Matrices

Las matrices comienzan con un corchete de apertura (`[`) y terminan con un corchete de cierre (`]`). En Bicep, una matriz se debe declarar en varias líneas. No use comas entre valores.

En una matriz, cada elemento se representa mediante [cualquier tipo](bicep-functions-any.md). Puede tener una matriz en la que cada elemento sea del mismo tipo de datos o una matriz que contenga tipos de datos diferentes.

En el ejemplo siguiente se muestra una matriz de enteros y una matriz de otros tipos.

```bicep
var integerArray = [
  1
  2
  3
]

var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

Las matrices de Bicep son de base cero. En el ejemplo siguiente, la expresión `exampleArray[0]` se evalúa en 1 y `exampleArray[2]` se evalúa en 3. El índice del indexador puede ser otra expresión. La expresión `exampleArray[index]` se evalúa en 2. Los indexadores enteros solo se permiten en la expresión de tipos de matriz.

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

## <a name="booleans"></a>Valores booleanos

Al especificar valores booleanos, use `true` o `false`. No incluya el valor entre comillas.

```bicep
param exampleBool bool = true
```

## <a name="integers"></a>Enteros

Al especificar valores enteros, no use comillas.

```bicep
param exampleInt int = 1
```

En Bicep, los enteros son enteros de 64 bits. Al pasarlos como parámetros en línea, el intervalo de valores puede estar limitado por el SDK o la herramienta de línea de comandos que use para la implementación. Por ejemplo, al usar PowerShell para implementar Bicep, los tipos de enteros pueden oscilar entre -2147483648 y 2147483647. Para evitar esta limitación, especifique valores enteros grandes en un [archivo de parámetros](parameter-files.md). Los tipos de recursos aplican sus propios límites para las propiedades de enteros.

Actualmente no se admiten formatos de número de punto flotante, decimal o binario.

## <a name="objects"></a>Objetos

Los objetos comienzan con una llave de apertura (`{`) y terminan con una llave de cierre (`}`). En Bicep, un objeto se debe declarar en varias líneas. Cada propiedad de un objeto consta de una clave y un valor. La clave y el valor están separados por dos puntos (`:`). Un objeto permite cualquier propiedad de cualquier tipo.

En Bicep, la clave no está entre comillas. No use comas entre las propiedades.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Los descriptores de acceso de propiedad se usan para acceder a las propiedades de un objeto. Se construyen mediante el operador `.`.

```bicep
var a = {
  b: 'Dev'
  c: 42
  d: {
    e: true
  }
}

output result1 string = a.b // returns 'Dev'
output result2 int = a.c // returns 42
output result3 bool = a.d.e // returns true
```

Los descriptores de acceso de propiedad se pueden usar con cualquier objeto, incluidos los parámetros y las variables de tipos de objeto y literales de objeto. Usar un descriptor de acceso de propiedad en una expresión que no sea de tipo objeto es un error.

También puede usar la sintaxis `[]` para acceder a una propiedad. El siguiente ejemplo devuelve `Development`.

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

## <a name="strings"></a>Cadenas

En Bicep, las cadenas se marcan con comillas simples y se deben declarar en una sola línea. Se permiten todos los caracteres Unicode con puntos de código entre *0* y *10FFFF*.

```bicep
param exampleString string = 'test value'
```

En la tabla siguiente se muestra el conjunto de caracteres reservados que se deben escapar mediante una barra diagonal inversa (`\`):

| Secuencia de escape | Valor representado | Notas |
|:-|:-|:-|
| `\\` | `\` ||
| `\'` | `'` ||
| `\n` | Avance de línea (LF) ||
| `\r` | Retorno de carro (CR) ||
| `\t` | Carácter de tabulación ||
| `\u{x}` | Punto de código Unicode `x` | **x** representa un valor de punto de código hexadecimal entre *0* y *10FFFF* (ambos inclusive). Se permiten ceros a la izquierda. Los puntos de código por encima de *FFFF* se emiten como un par suplente.
| `\$` | `$` | La secuencia de escape solo está permitida cuando va seguida de `{`. |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Todas las cadenas de Bicep admiten la interpolación. Para insertar una expresión, debe rodearla por `${` y `}`. Las expresiones a las que se hace referencia no pueden abarcar varias líneas.

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>Cadenas de varias líneas

En Bicep, las cadenas de varias líneas se definen entre tres caracteres de comilla simple (`'''`) seguidos opcionalmente de una nueva línea (la secuencia de apertura) y tres caracteres de comilla simple (`'''`: la secuencia de cierre). Los caracteres que se escriben entre las secuencias de apertura y cierre se leen textualmente y no es necesario ni posible el escape.

> [!NOTE]
> Dado que el analizador de Bicep lee todos los caracteres tal y como están, en función de los finales de línea del archivo Bicep, las nuevas líneas se pueden interpretar como `\r\n` o `\n`.
> La interpolación no se admite actualmente en cadenas de varias líneas.
> No se admiten cadenas de varias líneas que contengan `'''`.

```bicep
// evaluates to "hello!"
var myVar = '''hello!'''

// evaluates to "hello!" because the first newline is skipped
var myVar2 = '''
hello!'''

// evaluates to "hello!\n" because the final newline is included
var myVar3 = '''
hello!
'''

// evaluates to "  this\n    is\n      indented\n"
var myVar4 = '''
  this
    is
      indented
'''

// evaluates to "comments // are included\n/* because everything is read as-is */\n"
var myVar5 = '''
comments // are included
/* because everything is read as-is */
'''

// evaluates to "interpolation\nis ${blocked}"
// note ${blocked} is part of the string, and is not evaluated as an expression
myVar6 = '''interpolation
is ${blocked}'''
```

## <a name="secure-strings-and-objects"></a>Protección de cadenas y objetos

La cadena segura usa el mismo formato que la cadena y el objeto seguro usa el mismo formato que el objeto. Con Bicep, el modificador `@secure()` se agrega a una cadena u objeto.

Cuando se establece un parámetro en una cadena segura o un objeto seguro, el valor del parámetro no se guarda en el historial de implementaciones y no se registra. Sin embargo, si establece ese valor seguro en una propiedad que no espera un valor seguro, el valor no está protegido. Por ejemplo, si establece una cadena segura en una etiqueta, ese valor se almacena como texto sin formato. Use cadenas seguras para contraseñas y secretos.

En el ejemplo siguiente se muestran dos parámetros seguros:

```bicep
@secure()
param password string

@secure()
param configValues object
```

## <a name="data-type-assignability"></a>Capacidad de asignación de tipos de datos

En Bicep, se puede asignar un valor de un tipo (tipo de origen) a otro tipo (tipo de destino). En la tabla siguiente se muestra qué tipo de origen (enumerado horizontalmente) se puede asignar o no a qué tipo de destino (enumerado verticalmente). En la tabla, `X` significa asignable, un espacio vacío significa que no se puede asignar y `?` indica solo si sus tipos son compatibles.

| Tipos | `any` | `error` | `string` | `number` | `int` | `bool` | `null` | `object` | `array` | recurso con nombre | módulo con nombre | `scope` |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| `any`          |X| |X|X|X|X|X|X|X|X|X|X|
| `error`        | | | | | | | | | | | | |
| `string`       |X| |X| | | | | | | | | |
| `number`       |X| | |X|X| | | | | | | |
| `int`          |X| | | |X| | | | | | | |
| `bool`         |X| | | | |X| | | | | | |
| `null`         |X| | | | | |X| | | | | |
| `object`       |X| | | | | | |X| | | | |
| `array`        |X| | | | | | | |X| | | |
| `resource`     |X| | | | | | | | |X| | |
| `module`       |X| | | | | | | | | |X| |
| `scope`        | | | | | | | | | | | |?|
| **recurso con nombre** |X| | | | | | |?| |?| | |
| **módulo con nombre**   |X| | | | | | |?| | |?| |

## <a name="next-steps"></a>Pasos siguientes

Para información sobre la estructura y la sintaxis de Bicep, vea [Estructura de archivos de Bicep](./file.md).

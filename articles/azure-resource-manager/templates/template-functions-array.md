---
title: 'Funciones de plantillas: matrices'
description: Se describen las funciones que se usan en una plantilla de Azure Resource Manager para trabajar con matrices.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 25fb249e1840a7538fa1dd14f3b1a930bbefdfee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124756002"
---
# <a name="array-functions-for-arm-templates"></a>Funciones de matriz para plantillas de Resource Manager

Resource Manager proporciona varias funciones para trabajar con matrices en la plantilla de Resource Manager:

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Para obtener una matriz de valores de cadena delimitada por un valor, consulte [split](template-functions-string.md#split).

## <a name="array"></a>array

`array(convertToArray)`

Convierte el valor en una matriz.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| convertToArray |Sí |int, string, array u object |Valor que se convierte en matriz. |

### <a name="return-value"></a>Valor devuelto

Matriz .

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar la función de matriz con diferentes tipos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/array.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combina varias matrices y devuelve la matriz concatenada, o combina varios valores de cadena y devuelve la cadena concatenada.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |La primera matriz o cadena para la concatenación. |
| más argumentos |No |matriz o cadena |Más matrices o cadenas en orden secuencial para la concatenación. |

Esta función puede tomar cualquier número de argumentos y puede aceptar cadenas o matrices para los parámetros. Sin embargo, no puede proporcionar ambas a la vez para los parámetros. Las matrices solo se concatenan con otras matrices.

### <a name="return-value"></a>Valor devuelto

Una cadena o matriz de valores concatenados.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo combinar dos matrices.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

En el ejemplo siguiente se muestra cómo combinar dos valores de cadena y devolver una cadena concatenada.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Comprueba si una matriz contiene un valor, un objeto contiene una clave o una cadena contiene una subcadena. La comparación de cadena distingue mayúsculas de minúsculas. Pero, cuando se prueba si un objeto contiene una clave, la comparación no distingue mayúsculas de minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| contenedor |Sí |matriz, objeto o cadena |El valor que contiene el valor para buscar. |
| itemToFind |Sí |cadena o entero |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el elemento se encuentra; en caso contrario, **False**.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar contains con diferentes tipos:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

Crea una matriz a partir de los parámetros.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| args |No |Cadena, entero, matriz u objeto |Valores de la matriz. |

### <a name="return-value"></a>Valor devuelto

Matriz . Cuando no se proporciona ningún parámetro, devuelve una matriz vacía.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar createArray con diferentes tipos:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/createarray.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |
| emptyArray | Array | [] |

## <a name="empty"></a>empty

`empty(itemToTest)`

Determina si una matriz, un objeto o una cadena están vacíos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| itemToTest |Sí |matriz, objeto o cadena |El valor para comprobar si está vacío. |

### <a name="return-value"></a>Valor devuelto

Devuelve **True** si el valor está vacío; en caso contrario, **False**.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se comprueba si una matriz, un objeto y una cadena están vacíos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

Devuelve el primer elemento de la matriz o el primer carácter de la cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |El valor para recuperar el primer elemento o carácter. |

### <a name="return-value"></a>Valor devuelto

El tipo (cadena, entero, matriz u objeto) del primer elemento en una matriz o el primer carácter de una cadena.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar la primera función con una matriz y una cadena.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>intersección

`intersection(arg1, arg2, arg3, ...)`

Devuelve una única matriz u objeto con los elementos comunes de los parámetros.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz u objeto |El primer valor que se utilizará para buscar elementos comunes. |
| arg2 |Sí |matriz u objeto |El segundo valor que se utilizará para buscar elementos comunes. |
| más argumentos |No |matriz u objeto |Más valores que se utilizarán para buscar elementos comunes. |

### <a name="return-value"></a>Valor devuelto

Una matriz o un objeto con los elementos comunes.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar la intersección con matrices y objetos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

Devuelve el último elemento de la matriz o el último carácter de la cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |El valor para recuperar el último elemento o carácter. |

### <a name="return-value"></a>Valor devuelto

El tipo (cadena, entero, matriz u objeto) del último elemento de una matriz o el último carácter de una cadena.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar la última función con una matriz y una cadena.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

Devuelve el número de elementos de una matriz, caracteres de una cadena o propiedades de nivel raíz de un objeto.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz, cadena u objeto |La matriz que se usará para obtener el número de elementos, la cadena que se usará para obtener el número de caracteres o el objeto que se usará para obtener el número de propiedades del nivel raíz. |

### <a name="return-value"></a>Valor devuelto

Un entero.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar "length" con una matriz y una cadena.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

Puede usar esta función con una matriz para especificar el número de iteraciones al crear recursos. En el ejemplo siguiente, el parámetro **siteNames** debería hacer referencia a una matriz de nombres que se usará al crear los sitios web.

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

Para más información sobre el uso de esta función con una matriz, consulte [Iteración de recursos en las plantillas de Resource Manager](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Devuelve el valor máximo de una matriz de enteros o una lista separada por comas de enteros.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz de enteros, o lista separada por comas de enteros |La colección para obtener el valor máximo. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa el valor máximo.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar "max" con una matriz y una lista de enteros.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min(arg1)`

Devuelve el valor mínimo de una matriz de enteros o una lista separada por comas de enteros.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz de enteros, o lista separada por comas de enteros |La colección para obtener el valor mínimo. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa el valor mínimo.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar "min" con una matriz y una lista de enteros.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Crea una matriz de enteros a partir de un entero de inicio y contiene un número de elementos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| startIndex |Sí |int |El primer entero de la matriz. La suma de startIndex y count no debe ser mayor que 2 147 483 647. |
| count |Sí |int |El número de enteros en la matriz. Debe ser un entero no negativo de hasta 10 000. |

### <a name="return-value"></a>Valor devuelto

Una matriz de enteros.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar la función "range".

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/range.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Devuelve una matriz con todos los elementos después del número especificado de la matriz, o devuelve una cadena con todos los caracteres después del número especificado en la cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| originalValue |Sí |matriz o cadena |La matriz o cadena que se usará para la omisión. |
| numberToSkip |Sí |int |El número de elementos o caracteres que se van a omitir. Si este valor es 0 o un valor inferior, se devuelven todos los elementos o caracteres del valor. Si es mayor que la longitud de la matriz o la cadena, se devuelve una matriz o cadena vacía. |

### <a name="return-value"></a>Valor devuelto

Una matriz o cadena.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se omite el número especificado de elementos de la matriz, y el número especificado de caracteres de la cadena.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Devuelve una matriz o una cadena. Una matriz tiene el número especificado de elementos desde el principio de la matriz. Una cadena tiene el número especificado de caracteres desde el principio de la cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| originalValue |Sí |matriz o cadena |La matriz o cadena de la que se van a tomar los elementos. |
| numberToTake |Sí |int |El número de elementos o caracteres que se van a tomar. Si este valor es 0 o un valor inferior, se devolverá una matriz o cadena vacía. Si es mayor que la longitud de la matriz o cadena especificada, se devuelven todos los elementos de la matriz o cadena. |

### <a name="return-value"></a>Valor devuelto

Una matriz o cadena.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se toma el número especificado de elementos de la matriz y de caracteres de la cadena.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | en |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Devuelve una única matriz u objeto con todos los elementos de los parámetros. Los valores o las claves duplicados solo se incluyen una vez.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz u objeto |El primer valor que se utiliza para unir elementos. |
| arg2 |Sí |matriz u objeto |El segundo valor que se utiliza para unir elementos. |
| más argumentos |No |matriz u objeto |Más valores que se utilizan para unir elementos. |

### <a name="return-value"></a>Valor devuelto

Una matriz u objeto.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar "union" con matrices y objetos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una descripción de las secciones de una plantilla de ARM, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./syntax.md).

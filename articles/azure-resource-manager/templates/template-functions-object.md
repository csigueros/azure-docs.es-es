---
title: 'Funciones de plantillas: objetos'
description: Se describen las funciones que se usan en una plantilla de Azure Resource Manager para trabajar con objetos.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 3e6069744b1879e97ef3977916acbfc8aa6a9bd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820451"
---
# <a name="object-functions-for-arm-templates"></a>Funciones de objeto para plantillas de Resource Manager

Resource Manager proporciona varias funciones para trabajar con objetos en la plantilla de Resource Manager:

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

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

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

Crea un objeto a partir de las claves y los valores.

La función `createObject` no es compatible con Bicep.  Construya un objeto mediante `{}`. Vea [Objetos](../bicep/data-types.md#objects).

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| key1 |No |string |El nombre de la clave. |
| value1 |No |int, booleano, cadena, objeto o matriz |Valor de la clave. |
| más claves |No |string |Más nombres de las claves. |
| más valores |No |int, booleano, cadena, objeto o matriz |Más valores para las claves. |

La función solo acepta un número par de parámetros. Cada clave debe tener un valor coincidente.

### <a name="return-value"></a>Valor devuelto

Un objeto con cada par de clave y valor.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se crea un objeto a partir de distintos tipos de valores.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/createobject.json":::

La salida del ejemplo anterior con los valores predeterminados es un objeto denominado `newObject`con el siguiente valor:

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

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

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Convierte una cadena JSON válida en un tipo de datos JSON.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |string |Valor que se va a convertir en JSON. La cadena debe ser una cadena JSON con el formato correcto. |

### <a name="return-value"></a>Valor devuelto

El tipo de datos JSON de la cadena especificada o un valor vacío si se especifica **null**.

### <a name="remarks"></a>Observaciones

Si necesita incluir un valor de parámetro o variable en el objeto JSON, use la función [concat](template-functions-string.md#concat) para crear la cadena que se pasa a la función.

También puede usar [null ()](#null) para obtener un valor null.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo usar la función `json`. Tenga en cuenta que puede pasar `null` para un objeto vacío.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/json.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| emptyObjectOutput | Boolean | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | test |
| booleanOutput | Boolean | True |
| intOutput | Entero | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | Object | {"a": "demo value"} |

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

En el ejemplo siguiente se muestra cómo utilizar length con una matriz y una cadena:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>null

`null()`

Devuelve NULL.

La función `null` no está disponible en Bicep. En su lugar, use la palabra clave `null`.

### <a name="parameters"></a>Parámetros

La función null no acepta ningún parámetro.

### <a name="return-value"></a>Valor devuelto

Valor booleano que siempre es null.

### <a name="example"></a>Ejemplo

En el siguiente ejemplo se utiliza la función null.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/null.json":::

El resultado del ejemplo anterior es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

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

En el ejemplo siguiente se muestra cómo utilizar la unión con matrices y objetos:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una descripción de las secciones de una plantilla de ARM, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./syntax.md).

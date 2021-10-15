---
title: 'Funciones de Bicep: numéricas'
description: Se describen las funciones que se usan en un archivo de Bicep para trabajar con números.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: b5230d9fd7f228981c3ac3ec899ae8b641cc74fb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355330"
---
# <a name="numeric-functions-for-bicep"></a>Funciones numéricas de Bicep

En este artículo se describen las funciones de Bicep para trabajar con enteros.

Algunas de las funciones numéricas JSON de Azure Resource Manager se reemplazan por [operadores numéricos de Bicep](./operators-numeric.md).

## <a name="int"></a>int

`int(valueToConvert)`

Convierte el valor especificado en un entero.

Espacio de nombres: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sí |cadena o entero |Valor que se convierte en entero. |

### <a name="return-value"></a>Valor devuelto

Un entero del valor convertido.

### <a name="example"></a>Ejemplo

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en entero.

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Devuelve el valor máximo de una matriz de enteros o una lista separada por comas de enteros.

Espacio de nombres: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz de enteros, o lista separada por comas de enteros |La colección para obtener el valor máximo. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa el valor máximo de la colección.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar max con una matriz y una lista de enteros:

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min (arg1)`

Devuelve el valor mínimo de una matriz de enteros o una lista separada por comas de enteros.

Espacio de nombres: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz de enteros, o lista separada por comas de enteros |La colección para obtener el valor mínimo. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa el valor mínimo de la colección.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo utilizar min con una matriz y una lista de enteros:

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="next-steps"></a>Pasos siguientes

* Para otras acciones relacionadas con números, vea \[Operadores numéricos de Bicep](./operators-numeric.md).

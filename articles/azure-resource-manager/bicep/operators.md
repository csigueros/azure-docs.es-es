---
title: Operadores de Bicep
description: Describe los operadores de Bicep disponibles para las implementaciones de Azure Resource Manager.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 13591112171919d6c58959c40dffa1340f3e8ebd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764321"
---
# <a name="bicep-operators"></a>Operadores de Bicep

En este artículo, se describen los operadores de Bicep. Los operadores se usan para calcular valores, comparar valores o evaluar condiciones. Hay cuatro tipos de operadores de Bicep:

- [descriptor de acceso](#accessor)
- [de comparación](#comparison)
- [lógicos](#logical)
- [numeric](#numeric)

## <a name="operator-precedence-and-associativity"></a>Prioridad y asociatividad de los operadores

Los operadores siguientes se enumeran en orden descendente de precedencia (cuanto mayor sea la posición, mayor será la precedencia). Los operadores que aparecen en el mismo nivel tienen la misma precedencia.

| Símbolo | Tipo de operación | asociatividad |
|:-|:-|:-|
| `(` `)` `[` `]` `.` `::` | Paréntesis, indexadores de matriz, descriptores de acceso de propiedades y descriptores de acceso de recursos anidados  | De izquierda a derecha |
| `!` `-` | Unario | De derecha a izquierda |
| `%` `*` `/` | Multiplicativo | De izquierda a derecha |
| `+` `-` | Aditivo | De izquierda a derecha |
| `<=` `<` `>` `>=` | Relacional | De izquierda a derecha |
| `==` `!=` `=~` `!~` | Igualdad | De izquierda a derecha |
| `&&` | Y lógico | De izquierda a derecha |
| `||` | O lógico | De izquierda a derecha |
| `?` `:` | Expresión condicional (ternaria) | De derecha a izquierda
| `??` | Coalesce | De izquierda a derecha

## <a name="parentheses"></a>Paréntesis

Encerrar una expresión entre paréntesis permite invalidar la precedencia predeterminada del operador Bicep. Por ejemplo, la expresión `x + y / z` evalúa primero la división y, después, la suma. Sin embargo, la expresión`(x + y) / z` evalúa la suma primero y la división después.

## <a name="accessor"></a>Descriptor de acceso

Los operadores de los descriptores de acceso se usan para acceder a recursos anidados y propiedades de los objetos.

| Operator | Nombre | Descripción |
| ---- | ---- | ---- |
| `[]` | [Descriptor de acceso de índice](./operators-access.md#index-accessor) | Acceda a un elemento de una matriz o propiedad en un objeto. |
| `.` | [Descriptor de acceso de funciones](./operators-access.md#function-accessor) | Llame a una función en un recurso. |
| `::` | [Descriptor de acceso de recursos anidados](./operators-access.md#nested-resource-accessor) | Acceda a un recurso anidado desde fuera del recurso primario. |
| `.` | [Descriptor de acceso de propiedades](./operators-access.md#property-accessor) | Propiedades de acceso de un objeto. |

## <a name="comparison"></a>De comparación

Los operadores de comparación comparan valores y devuelven `true` o `false`.

| Operator | Nombre | Descripción |
| ---- | ---- | ---- |
| `>=` | [Mayor que o igual a](./operators-comparison.md#greater-than-or-equal-) | Determina si el primer valor es mayor o igual que el segundo. |
| `>`  | [Mayor que](./operators-comparison.md#greater-than-) | Determina si el primer valor es mayor que el segundo. |
| `<=` | [Menor que o igual a](./operators-comparison.md#less-than-or-equal-) | Determina si el primer valor es menor o igual que el segundo. |
| `<`  | [Menor que](./operators-comparison.md#less-than-) | Determina si el primer valor es menor que el segundo. |
| `==` | [Es igual a](./operators-comparison.md#equals-) | Determina si dos valores son iguales. |
| `!=` | [No igual a](./operators-comparison.md#not-equal-) | Determina si dos valores **no** son iguales. |
| `=~` | [Igual sin distinción de mayúsculas y minúsculas](./operators-comparison.md#equal-case-insensitive-) | Determina si dos valores son iguales sin tener en cuenta la distinción entre mayúsculas y minúsculas. |
| `!~` | [No es igual sin distinción de mayúsculas y minúsculas](./operators-comparison.md#not-equal-case-insensitive-) | Determina si dos valores **no** son iguales sin tener en cuenta la distinción entre mayúsculas y minúsculas. |

## <a name="logical"></a>Lógicos

Los operadores lógicos evalúan valores booleanos, devuelven valores que no son NULL o evalúan expresiones condicionales.

| Operator | Nombre | Descripción |
| ---- | ---- | ---- |
| `&&` | [Y](./operators-logical.md#and-) | Devuelve `true` si todos los valores son TRUE. |
| `||`| [O](./operators-logical.md#or-) | Devuelve `true` si alguno de los valores es TRUE. |
| `!` | [Not](./operators-logical.md#not-) | Niega un valor booleano. Toma un operando. |
| `??` | [Fusionar](./operators-logical.md#coalesce-) | Devuelve el primer valor que no es NULL. |
| `?` `:` | [Expresión condicional](./operators-logical.md#conditional-expression--) | Determina si una condición es TRUE o FALSE y devuelve un valor. |

## <a name="numeric"></a>Numeric

Los operadores numéricos usan enteros para realizar cálculos y devolver valores enteros.

| Operator | Nombre | Descripción |
| ---- | ---- | ---- |
| `*` | [Multiplicar](./operators-numeric.md#multiply-) | Multiplica dos enteros. |
| `/` | [Dividir](./operators-numeric.md#divide-) | Divide un entero por otro. |
| `%` | [Módulo](./operators-numeric.md#modulo-) | Divide un entero por otro y devuelve el resto. |
| `+` | [Add (Agregar)](./operators-numeric.md#add-) | Suma dos enteros. |
| `-` | [Restar](./operators-numeric.md#subtract--) | Resta un entero de otro entero. Toma dos operandos. |
| `-` | [Menos](./operators-numeric.md#minus--) (unario) | Multiplica un entero por `-1`. Toma un operando. |

> [!NOTE]
> "Restar" y "Menos" utilizan el mismo operador. La funcionalidad es diferente porque "Restar" utiliza dos operandos, mientras que "Menos" solo utiliza uno.


## <a name="next-steps"></a>Pasos siguientes

- Para crear un archivo de Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Para obtener información sobre cómo resolver errores de tipos de Bicep, consulte [Función any para Bicep](./bicep-functions-any.md).
- Para comparar la sintaxis de Bicep y JSON, consulte [Comparación de JSON y Bicep para plantillas](./compare-template-syntax.md).
- Para ejemplos de funciones de Bicep, consulte [Funciones de Bicep](./bicep-functions.md).

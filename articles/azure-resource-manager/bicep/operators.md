---
title: Operadores de Bicep
description: Describe los operadores de Bicep disponibles para las implementaciones de Azure Resource Manager.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 154a42d1bcdc78eee63241286e8f65ab7777bc6b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027228"
---
# <a name="bicep-operators"></a>Operadores de Bicep

En este artículo, se describen los operadores de Bicep que están disponibles cuando se crea una plantilla de Bicep y se utiliza Azure Resource Manager para implementar recursos. Los operadores se usan para calcular valores, comparar valores o evaluar condiciones. Hay tres tipos de operadores:

- [de comparación](#comparison)
- [lógicos](#logical)
- [numeric](#numeric)

Encerrar una expresión entre `(` y `)` permite invalidar la precedencia predeterminada del operador Bicep. Por ejemplo, la expresión x + y/z evalúa primero la división y, después, la suma. Sin embargo, la expresión (x + y)/z evalúa la suma primero y la división después.

Para obtener información sobre el acceso a un recurso a través del operador `::`, consulte [Establecimiento del nombre y el tipo de los recursos secundarios en Bicep](child-resource-name-type.md).

## <a name="comparison"></a>De comparación

Los operadores de comparación comparan valores y devuelven `true` o `false`.

| Operator | Nombre | Descripción |
| ---- | ---- | ---- |
| `>=` | [Mayor que o igual a](./operators-comparison.md#greater-than-or-equal-) | Determina si el primer valor es mayor o igual que el segundo. |
| `>`  | [Mayor que](./operators-comparison.md#greater-than-) | Determina si el primer valor es mayor que el segundo. |
| `<=` | [Menor que o igual a](./operators-comparison.md#less-than-or-equal-) | Determina si el primer valor es menor o igual que el segundo. |
| `<`  | [Menor que](./operators-comparison.md#less-than-) | Determina si el primer valor es menor que el segundo. |
| `==` | [Es igual a](./operators-comparison.md#equals-) | Determina si dos valores son iguales. |
| `!=` | [No es igual a](./operators-comparison.md#not-equal-) | Determina si dos valores **no** son iguales. |
| `=~` | [Igual sin distinción de mayúsculas y minúsculas](./operators-comparison.md#equal-case-insensitive-) | Determina si dos valores son iguales sin tener en cuenta la distinción entre mayúsculas y minúsculas. |
| `!~` | [No es igual sin distinción de mayúsculas y minúsculas](./operators-comparison.md#not-equal-case-insensitive-) | Determina si dos valores **no** son iguales sin tener en cuenta la distinción entre mayúsculas y minúsculas. |

## <a name="logical"></a>Lógicos

Los operadores lógicos evalúan valores booleanos, devuelven valores que no son NULL o evalúan expresiones condicionales.

| Operator | Nombre | Descripción |
| ---- | ---- | ---- |
| `&&` | [Y](./operators-logical.md#and-) | Devuelve `true` si todos los valores son TRUE. |
| `||`| [O](./operators-logical.md#or-) | Devuelve `true` si alguno de los valores es TRUE. |
| `!` | [Not](./operators-logical.md#not-) | Niega un valor booleano. |
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
| `-` | [Restar](./operators-numeric.md#subtract--) | Resta un entero de otro. |
| `-` | [Menos](./operators-numeric.md#minus--) | Multiplica un entero por `-1`. |

> [!NOTE]
> "Restar" y "Menos" utilizan el mismo operador. La funcionalidad es diferente porque "Restar" utiliza dos operandos, mientras que "Menos" solo utiliza uno.

## <a name="next-steps"></a>Pasos siguientes

- Para crear un archivo de Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Para obtener información sobre cómo resolver errores de tipos de Bicep, consulte [Función any para Bicep](./bicep-functions-any.md).
- Para comparar la sintaxis de Bicep y JSON, consulte [Comparación de JSON y Bicep para plantillas](./compare-template-syntax.md).
- Para ejemplos de funciones de Bicep, consulte [Funciones de Bicep](./bicep-functions.md).

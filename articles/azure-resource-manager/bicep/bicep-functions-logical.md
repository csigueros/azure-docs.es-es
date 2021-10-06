---
title: 'Funciones de Bicep: logical'
description: Describe las funciones que se usarán en un archivo de Bicep para determinar valores lógicos.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e3a811102c31301386135042c79c205eeb9f7f9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758434"
---
# <a name="logical-functions-for-bicep"></a>Funciones lógicas para Bicep

Bicep proporciona la función `bool` para convertir valores en un valor booleano.

La mayoría de las funciones lógicas de las plantillas de Azure Resource Manager se reemplazan por [operadores lógicos](./operators-logical.md) de Bicep.

## <a name="bool"></a>bool

`bool(arg1)`

Convierte el parámetro en un booleano.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |cadena o entero |El valor para convertir en booleano. |

### <a name="return-value"></a>Valor devuelto

Valor booleano del valor convertido.

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo usar bool con una cadena o un entero.

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>Pasos siguientes

* Para otras acciones que abarcan valores lógicos, consulte los [operadores lógicos](./operators-logical.md).

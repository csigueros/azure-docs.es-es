---
title: 'Funciones de Bicep: logical'
description: Describe las funciones que se usarán en un archivo de Bicep para determinar valores lógicos.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 4ab5f0382d320e31c650dcb6ebee68f39d6e43b1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362977"
---
# <a name="logical-functions-for-bicep"></a>Funciones lógicas para Bicep

Bicep proporciona la función `bool` para convertir valores en un valor booleano.

La mayoría de las funciones lógicas de las plantillas de Azure Resource Manager se reemplazan por [operadores lógicos](./operators-logical.md) de Bicep.

## <a name="bool"></a>bool

`bool(arg1)`

Convierte el parámetro en un booleano.

Espacio de nombres: [sys](bicep-functions.md#namespaces-for-functions).

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

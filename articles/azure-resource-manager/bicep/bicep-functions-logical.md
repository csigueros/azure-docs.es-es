---
title: 'Funciones de Bicep: logical'
description: Describe las funciones que se usarán en un archivo de Bicep para determinar valores lógicos.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 8fe09057ea293300754fc35614c00a9b808da247
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744909"
---
# <a name="logical-functions-for-bicep"></a>Funciones lógicas para Bicep

Resource Manager proporciona una función `bool` para Bicep. 

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

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) siguiente se muestra cómo usar bool con una cadena o un entero.

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

* Para la mayoría de las operaciones lógicas, vea [Operadores lógicos de Bicep](./operators-logical.md).
* Puede encontrar una descripción de las secciones de un archivo de Bicep en [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](./file.md).

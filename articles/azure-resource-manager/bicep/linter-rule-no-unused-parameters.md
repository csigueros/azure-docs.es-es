---
title: 'Regla de linter: sin parámetros no utilizados'
description: 'Regla de linter: sin parámetros no utilizados'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 2e9c602a28b2ba19d8c0a38b1bba3ecc521e6d77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017851"
---
# <a name="linter-rule---no-unused-parameters"></a>Regla de linter: sin parámetros no utilizados

Esta regla encuentra parámetros a los que no se hace referencia en ninguna parte del archivo de Bicep.

## <a name="returned-code"></a>Código devuelto

`no-unused-params`

## <a name="solution"></a>Solución

Para reducir la confusión en la plantilla, elimine los parámetros definidos, pero que no se usan. Esta prueba encuentra cualquier parámetro que no se use en ninguna parte de la plantilla. La eliminación de parámetros no usados también facilita la implementación de la plantilla, ya que no hay que proporcionar valores innecesarios.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de linter, consulte [Uso de linter de Bicep](./linter.md).

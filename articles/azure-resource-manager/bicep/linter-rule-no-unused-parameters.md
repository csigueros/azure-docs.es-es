---
title: 'Regla de linter: sin parámetros no utilizados'
description: 'Regla de linter: sin parámetros no utilizados'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: d02d13c44fd6cc6a174f629c8f3e32777bd643af
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161549"
---
# <a name="linter-rule---no-unused-parameters"></a>Regla de linter: sin parámetros no utilizados

Esta regla encuentra parámetros a los que no se hace referencia en ninguna parte del archivo de Bicep.

## <a name="returned-code"></a>Código devuelto

`no-unused-params`

## <a name="solution"></a>Solución

Para reducir la confusión en la plantilla, elimine los parámetros definidos, pero que no se usan. Esta prueba encuentra cualquier parámetro que no se use en ninguna parte de la plantilla. La eliminación de parámetros no usados también facilita la implementación de la plantilla, ya que no hay que proporcionar valores innecesarios.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de linter, consulte [Uso de Bicep linter](./linter.md).

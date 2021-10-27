---
title: 'Regla de linter: ninguna variable sin usar'
description: 'Regla de linter: ninguna variable sin usar'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 3b59b8d72d12c365952dd8eef2c65a0f851ea561
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161881"
---
# <a name="linter-rule---no-unused-variables"></a>Regla de linter: ninguna variable sin usar

Esta regla encuentra variables a las que no se hace referencia en ninguna parte del archivo de Bicep.

## <a name="returned-code"></a>Código devuelto

`no-unused-vars`

## <a name="solution"></a>Solución

Para reducir la confusión en la plantilla, elimine las variables definidas, pero que no se utilicen. Esta prueba busca las variables que no se usan en ninguna parte de la plantilla.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de linter, consulte [Uso de Bicep linter](./linter.md).

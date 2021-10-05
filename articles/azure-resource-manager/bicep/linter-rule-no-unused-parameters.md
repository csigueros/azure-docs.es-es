---
title: 'Regla de linter: sin parámetros no utilizados'
description: 'Regla de linter: sin parámetros no utilizados'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: e8304948c2b373d16a7fa48c38ac4bac9ec806df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699661"
---
# <a name="linter-rule---no-unused-parameters"></a>Regla de linter: sin parámetros no utilizados

Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo. El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba de arm-ttk](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Para obtener más información, vea [Uso de Bicep linter](./linter.md).

## <a name="code"></a>Código

`no-unused-params`

## <a name="description"></a>Descripción

Para reducir la confusión en la plantilla, elimine los parámetros definidos, pero que no se usan. Esta prueba encuentra cualquier parámetro que no se use en ninguna parte de la plantilla. La eliminación de parámetros no usados también facilita la implementación de la plantilla, ya que no hay que proporcionar valores innecesarios.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

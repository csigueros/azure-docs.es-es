---
title: 'Regla de linter: Simplificación de la interpolación'
description: 'Regla de linter: Simplificación de la interpolación'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: aff3df9bd7b357dcfdfd62f87971580399b3f2ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699536"
---
# <a name="linter-rule---simplify-interpolation"></a>Regla de linter: Simplificación de la interpolación

Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo. El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba de arm-ttk](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Para obtener más información, vea [Uso de Bicep linter](./linter.md).

## <a name="code"></a>Código

`simplify-interpolation`

## <a name="description"></a>Descripción

No es necesario usar la interpolación para hacer referencia a un parámetro o variable.

## <a name="examples"></a>Ejemplos

En el ejemplo siguiente no se supera esta prueba.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

En el ejemplo siguiente se supera esta prueba.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

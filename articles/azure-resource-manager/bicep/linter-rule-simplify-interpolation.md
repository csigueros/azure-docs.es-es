---
title: 'Regla de linter: Simplificación de la interpolación'
description: 'Regla de linter: Simplificación de la interpolación'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: e2e0f9eca607ececad22c025e89b7c976cd0d05a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165341"
---
# <a name="linter-rule---simplify-interpolation"></a>Regla de linter: Simplificación de la interpolación

Esta regla busca la sintaxis que usa la interpolación de cadenas cuando no es necesaria.

## <a name="returned-code"></a>Código devuelto

`simplify-interpolation`

## <a name="solution"></a>Solución

Quite los usos de interpolación de cadenas que no forman parte de una expresión para combinar valores.

En el ejemplo siguiente se produce un error en esta prueba porque simplemente hace referencia a un parámetro.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

Puede corregirlo quitando la sintaxis de interpolación de cadenas.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de linter, consulte [Uso de linter de Bicep](./linter.md).

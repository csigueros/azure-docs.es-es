---
title: 'Regla de Linter: el nombre de usuario administrador no debe ser literal'
description: 'Regla de Linter: el nombre del usuario administrador no debe ser un literal'
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: fe13ed227b0f7c7d5a7b738ed675f6c5d54eded2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167769"
---
# <a name="linter-rule---admin-user-name-should-not-be-literal"></a>Regla de Linter: el nombre de usuario administrador no debe ser literal

Esta regla busca cuándo se establece un nombre de usuario administrador en un valor literal.

## <a name="returned-code"></a>Código devuelto

`adminusername-should-not-be-literal`

## <a name="solution"></a>Solución

No use un valor literal o una expresión que se evalúe como un valor literal. En su lugar, cree un parámetro para el nombre de usuario y asígnelo al nombre de usuario administrador.

En el ejemplo siguiente no se supera esta prueba porque el nombre de usuario es un valor literal.

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: 'adminUsername'
    }
  }
}
```

En el ejemplo siguiente no se supera esta prueba porque la expresión se evalúa como un valor literal cuando se usa el valor predeterminado.

```bicep
var defaultAdmin = 'administrator'
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: defaultAdmin
    }
  }
}
```

Este ejemplo supera la prueba.

```bicep
@secure()
param adminUsername string
param location string
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: adminUsername
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de linter, consulte [Uso de Bicep linter](./linter.md).

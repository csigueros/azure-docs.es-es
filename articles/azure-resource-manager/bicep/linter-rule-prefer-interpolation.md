---
title: 'Regla de linter: preferencia de la interpolación'
description: 'Regla de linter: preferencia de la interpolación'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 648b924b545e801f8a97ef5202f8022252ef94c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594590"
---
# <a name="linter-rule---prefer-interpolation"></a>Regla de linter: preferencia de la interpolación

Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo. El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba de arm-ttk](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Para obtener más información, consulte [Uso de linter de Bicep](./linter.md).

## <a name="code"></a>Código

`prefer-interpolation`

## <a name="description"></a>Descripción

Se debe usar la interpolación de cadenas en lugar de la función concat.

## <a name="examples"></a>Ejemplos

En el siguiente ejemplo no se supera esta prueba porque se usa la función concat.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

En el ejemplo siguiente se supera esta prueba.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

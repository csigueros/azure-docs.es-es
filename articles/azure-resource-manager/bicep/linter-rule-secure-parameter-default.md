---
title: 'Regla de linter: valor predeterminado de parámetro seguro'
description: 'Regla de linter: valor predeterminado de parámetro seguro'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4fa82fbbe74d9bf51d1eb498341b999a89e12978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699537"
---
# <a name="linter-rule---secure-parameter-default"></a>Regla de linter: valor predeterminado de parámetro seguro

Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo. El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba de arm-ttk](../templates/template-test-cases.md):

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Para obtener más información, consulte [Uso de linter de Bicep](./linter.md).

## <a name="code"></a>Código

`secure-parameter-default`

## <a name="description"></a>Descripción

No proporcione un valor predeterminado codificado de forma rígida para un [parámetro seguro](./parameters.md#secure-parameters) en su plantilla, a menos que esté vacío o sea una expresión que contenga una llamada a [newGuid()](./bicep-functions-string.md#newguid).

Use el decorador @secure() en los parámetros que contengan valores confidenciales, por ejemplo, contraseñas. Cuando un parámetro usa un decorador seguro, el valor del parámetro no se registra ni se almacena en el historial de implementación. Esta acción evita que un usuario malintencionado detecte el valor confidencial.

Sin embargo, si proporciona un valor predeterminado para un parámetro seguro, cualquiera que tenga acceso a la plantilla o al historial de implementación puede detectarlo.

## <a name="examples"></a>Ejemplos

En el siguiente ejemplo no se supera esta prueba:

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

En los siguientes ejemplos se supera esta prueba:

```bicep
@secure()
param adminPassword string
```

```bicep
@secure()
param adminPassword string = ''
```

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

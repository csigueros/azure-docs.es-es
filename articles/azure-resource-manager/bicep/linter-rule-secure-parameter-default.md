---
title: 'Regla de linter: valor predeterminado de parámetro seguro'
description: 'Regla de linter: valor predeterminado de parámetro seguro'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 139b19124f22d5cb42be71d6a6042830ad7809f7
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166485"
---
# <a name="linter-rule---secure-parameter-default"></a>Regla de linter: valor predeterminado de parámetro seguro

Esta regla busca valores predeterminados codificados de forma rígida para parámetros seguros.

## <a name="returned-code"></a>Código devuelto

`secure-parameter-default`

## <a name="solution"></a>Solución

No proporcione un valor predeterminado codificado de forma rígida para un [parámetro seguro](./parameters.md#secure-parameters) en su archivo de Bicep, a menos que se trate de una cadena vacía o una expresión que llame a la función [newGuid()](./bicep-functions-string.md#newguid).

Use el decorador `@secure()` en los parámetros que contengan valores confidenciales; por ejemplo, contraseñas. Cuando un parámetro usa un decorador seguro, el valor del parámetro no se registra ni se almacena en el historial de implementación. Esta acción evita que un usuario malintencionado detecte el valor confidencial.

Sin embargo, si proporciona un valor predeterminado para un parámetro seguro, cualquiera que tenga acceso a la plantilla o al historial de implementación puede detectarlo.

En el ejemplo siguiente se produce un error en esta prueba porque el parámetro tiene un valor predeterminado que está codificado de forma rígida.

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

Para corregirlo, quite el valor predeterminado.

```bicep
@secure()
param adminPassword string
```

También puede proporcionar una cadena vacía para el valor predeterminado.

```bicep
@secure()
param adminPassword string = ''
```

También puede usar `newGuid()` para generar el valor predeterminado.

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de linter, consulte [Uso de Bicep linter](./linter.md).

---
title: 'Regla de linter: preferencia de la interpolación'
description: 'Regla de linter: preferencia de la interpolación'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 952359c7c258c084e19d6faf152536a3e50212b2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164481"
---
# <a name="linter-rule---prefer-interpolation"></a>Regla de linter: preferencia de la interpolación

Esta regla busca los usos de la función concat que se pueden reemplazar por la interpolación de cadenas.

## <a name="returned-code"></a>Código devuelto

`prefer-interpolation`

## <a name="solution"></a>Solución

En lugar de usar la función concat, utilice la interpolación de cadenas.

En el siguiente ejemplo no se supera esta prueba porque se usa la función concat.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)
```

Puede corregirlo reemplazando concat por la interpolación de cadenas. En el ejemplo siguiente se supera esta prueba.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de linter, consulte [Uso de Bicep linter](./linter.md).

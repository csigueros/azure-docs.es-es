---
title: Escalado de unidades reservadas de multimedia (MRU) con CLI
description: En este tema se muestra cómo usar la CLI para escalar el procesamiento multimedia con Azure Media Services.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 2525417516691fa4ffb6d681fc23b394f4588649
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129364087"
---
# <a name="how-to-scale-media-reserved-units-legacy"></a>Escalado de unidades reservadas de multimedia (heredado)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En este artículo se describe cómo escalar las unidades reservadas de multimedia (MRU) para conseguir una codificación más rápida.

> [!WARNING]
> Este comando dejará de funcionar en las cuentas de Media Services que se crearon con la versión 2020-05-01 de la API o con una versión posterior. Para estas cuentas, las unidades reservadas de multimedia ya no son necesarias, ya que el sistema se escalará y reducirá automáticamente en función de la carga. Si no ve la opción para administrar MRU en Azure Portal, es que está usando una cuenta creada con la API 2020-05-01 o posterior.
> El propósito de este artículo es documentar el proceso heredado de uso de MRUs.

## <a name="prerequisites"></a>Prerequisites

[Cree una cuenta de Media Services](./account-create-how-to.md).

Conocimientos sobre las [Unidades reservadas de multimedia](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Escalado de unidades reservadas de multimedia con CLI

Ejecute el comando `mru`.

El comando [mru de la cuenta de ams az](/cli/azure/ams/account/mru) siguiente establece las unidades reservadas de multimedia en cuenta "amsaccount" mediante los parámetros **count** y **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Facturación

 Antes, las unidades reservadas de multimedia tenían un coste, pero a partir del 17 de abril de 2021 ya no hay que abonar nada por las cuentas que tengan una configuración de unidades reservadas de multimedia.

## <a name="see-also"></a>Vea también

* [Migración de Media Services v2 a v3](migrate-v-2-v-3-migration-introduction.md)

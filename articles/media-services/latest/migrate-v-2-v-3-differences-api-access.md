---
title: Acceso de API de Media Services V2 y V3
description: En este artículo se describen las diferencias de acceso de API entre Azure Media Services V2 y V3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 594f4d68669b216e4606806a9ac3ef9a5b2f0b5c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358312"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Diferencias de acceso de API entre Azure Media Services V2 y la API v3

![logotipo de la guía de migración](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![pasos de migración 2](./media/migration-guide/steps-2.svg)

En este artículo se describen las diferencias de acceso de API entre Azure Media Services V2 y V3.

## <a name="api-access"></a>Acceso de API

Todas las cuentas de Media Services tendrán acceso a la API V3. Sin embargo, recomendamos encarecidamente el desarrollo de la migración en una cuenta nueva antes de aplicar el código actualizado a una cuenta de V2 existente. Esto se debe a que las entidades V3 no son compatibles con V2. Algunas entidades V2 como recursos son compatibles con V3.
Puede seguir usando cuentas existentes si no combina las API V2 y V3 y, después, intenta volver a V2, pero no es recomendable.

El acceso a la API V2 estará disponible hasta que se retire en 2024.

## <a name="create-a-v3-account"></a>Creación de una cuenta de V3

Mientras realiza la migración, puede crear una cuenta de V3 que siga teniendo acceso a V2.  La creación de la cuenta se puede realizar con:

- la API de REST y una versión anterior
- Activación de la casilla en el portal.

> [!div class="mx-imgBorder"]
> [ ![creación de cuentas en el portal](./media/migration-guide/v-3-v-2-access-account-creation-small.png) ](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

.NET, la CLI y otros SDK establecerán como destino la última API 2020-05-01, de modo que busque o configure las versiones de API anteriores.

> [!NOTE]
> Las nuevas cuentas creadas con la API 2020-05-01 (o posterior) no pueden usar las API V2.

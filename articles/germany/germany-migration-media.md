---
title: Migración de recursos multimedia de Azure desde Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos multimedia de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: ac7cd5ca55525c11d2b985d931be24d1780899fb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029020"
---
# <a name="migrate-media-resources-to-global-azure"></a>Migración de recursos multimedia a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos multimedia de Azure desde Azure Alemania a Azure global.

## <a name="media-services"></a>Media Services

En Azure Media Services, puede configurar su propia cuenta de almacenamiento y todos los recursos multimedia. En primer lugar, cree una nueva cuenta de Media Services en Azure global. A continuación, vuelva a cargar los artefactos multimedia correspondientes y realice la codificación y el streaming en la nueva cuenta de Media Services.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Media Services](../media-services/previous/index.yml).
- Revise la [introducción a Media Services](../media-services/previous/media-services-overview.md).
- Aprenda a [crear una cuenta de Media Services](../media-services/previous/media-services-portal-create-account.md).

## <a name="media-player"></a>Media Player

Puede seleccionar varios puntos de conexión en Azure Media Player. Puede transmitir el contenido desde los puntos de conexión de Azure Alemania o Azure global.

Para más información, consulte [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
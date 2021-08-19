---
title: Guía de migración de empaquetado y entrega
description: En este artículo se proporcionan instrucciones basadas en escenarios de empaquetado y entrega que le ayudarán a migrar de Azure Media Services v2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d8cba5c69802828a0bd486d902d85199f3ae1d00
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322156"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Guía de migración basada en escenarios de empaquetado y entrega

![logotipo de la guía de migración](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![pasos de migración 2](./media/migration-guide/steps-4.svg)

En este artículo se proporcionan instrucciones basadas en escenarios de empaquetado y entrega que le ayudarán a migrar de Azure Media Services v2 a v3.

Cambios importantes en la forma en que se publica el contenido en la API v3. El nuevo modelo de publicación está simplificado y usa menos entidades para crear un localizador de streaming. La API se redujo a solo dos entidades frente a las cuatro entidades necesarias anteriormente. Las directivas de clave de contenido y los localizadores de streaming ahora reemplazarán la necesidad de los elementos `ContentKeyAuthorizationPolicy`, `AssetDeliveryPolicy`, `ContentKey` y `AccessPolicy`.

## <a name="packaging-and-delivery-in-v3"></a>Empaquetado y entrega en v3

1. Cree [directivas de clave de contenido](drm-content-key-policy-concept.md).
1. Cree [localizadores de streaming](stream-streaming-locators-concept.md).
1. Obtenga las [rutas de acceso de streaming](create-streaming-locator-build-url.md). 
    1. Configúrelas para un reproductor [DASH](encode-dynamic-packaging-concept.md#deliver-dash) o [HLS](encode-dynamic-packaging-concept.md#deliver-hls).

Consulte los conceptos, tutoriales y guías de procedimientos de publicación a continuación para conocer los pasos específicos.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Conceptos, tutoriales y guías de procedimientos de publicación

### <a name="concepts"></a>Conceptos

- [Empaquetado dinámico en Media Services v3](encode-dynamic-packaging-concept.md)
- [Filtros](filters-concept.md)
- [Filtrado de los manifiestos mediante el empaquetador dinámico](filters-dynamic-manifest-concept.md)
- [Puntos de conexión de streaming (origen) en Azure Media Services](stream-streaming-endpoint-concept.md)
- [Transmisión de contenido con la integración de CDN](stream-scale-streaming-cdn-concept.md)
- [Localizadores de streaming](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guías de procedimientos

- [Administración de puntos de conexión de streaming con Media Services v3](stream-manage-streaming-endpoints-how-to.md)
- [Ejemplo de CLI: publicación de un recurso](cli-publish-asset.md)
- [Creación de un localizador de streaming y compilación de direcciones URL](create-streaming-locator-build-url.md)
- [Descarga de los resultados de un trabajo](job-download-results-how-to.md)
- [Señalización de pistas descriptivas de audio](signal-descriptive-audio-howto.md)
- [Configuración completa de Azure Media Player](../azure-media-player/azure-media-player-full-setup.md)
- [Uso del reproductor Video.js con Azure Media Services](player-how-to-video-js-player.md)
- [Uso de Shaka Player con Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>Ejemplos

También puede [comparar el código de la versión v2 y v3 en los ejemplos de código](migrate-v-2-v-3-migration-samples.md).

---
title: 'Obtención de imágenes desde la vista personalizada: Bing Custom Search'
titleSuffix: Azure Cognitive Services
description: Información general de alto nivel acerca del uso de Bing Custom Search para obtener imágenes desde la vista personalizada de la web.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.openlocfilehash: 86eb2a3faf2d431fe78a46b1d3935fe60404171a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643106"
---
# <a name="get-images-from-your-custom-view"></a>Obtención de imágenes desde la vista personalizada

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Custom Images Search le permite enriquecer su experiencia de búsqueda personalizada con imágenes. De forma similar a los resultados web, la búsqueda personalizada permite buscar imágenes en la lista de sitios web de su instancia. Puede obtener las imágenes mediante la Bing Custom Images Search API o a través de la característica de interfaz de usuario hospedada. La característica de interfaz de usuario hospedada es fácil usar y se recomienda para que su experiencia de búsqueda esté lista para usarse sin contratiempos.  Para obtener información acerca de cómo configurar la interfaz de usuario hospedada para que incluya imágenes, consulte [Configure su experiencia de interfaz de usuario hospedada](hosted-ui.md).

Si desea más control sobre la visualización de los resultados de búsqueda, puede usar la Bing Custom Images Search API. Dado que una llamada a la API es similar a una llamada a Bing Image Search API, eche un vistazo a [Bing Image Search](../Bing-Image-Search/overview.md) para ver algunos ejemplos de llamadas a la API. Pero antes de hacerlo, familiarícese con el contenido [referencia de la Bing Custom Images Search API](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference). Las principales diferencias son los parámetros de consulta admitidos (debe incluir el parámetro de consulta customConfig) y los puntos de conexión a los que envía las solicitudes.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
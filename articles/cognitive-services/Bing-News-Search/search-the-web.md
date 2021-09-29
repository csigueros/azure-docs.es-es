---
title: ¿Qué es Bing News Search API?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar Bing News Search API para buscar en la Web titulares actuales en distintas categorías, incluidos titulares y temas populares.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.custom: seodec2018
ms.openlocfilehash: 354dd9d01037eb8dbbb931582a935dff59dd4217
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617780"
---
# <a name="what-is-the-bing-news-search-api"></a>¿Qué es Bing News Search API?

> [!WARNING]
> Las Bing Search API se mueven de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing News Search API facilita la integración de las funcionalidades cognitivas de búsqueda de noticias de Bing en tus aplicaciones. La API ofrece una experiencia parecida a la de [Bing News](https://www.bing.com/news), que le permite enviar consultas de búsqueda y recibir los artículos de noticias pertinentes.

Tenga en cuenta que Bing News Search API proporciona solo resultados de búsqueda de noticias. Use [Bing Web Search API](../bing-web-search/overview.md), [Video Search API](../bing-video-search/overview.md) y [Image Search API](../bing-image-search/overview.md) para otros tipos de contenido web.

## <a name="bing-news-search-api-features"></a>Características de Bing News Search API

Aunque Bing News Search API principalmente busca y devuelve los artículos de noticias pertinentes, también proporciona varias características para la recuperación de noticias inteligente y centrada en la web.

|Característica  |Descripción  |
|---------|---------|
|[Sugerencia y uso de términos de búsqueda](concepts/search-for-news.md#suggest-and-use-search-terms)     | Mejore su experiencia de búsqueda mediante [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) para mostrar términos de búsqueda sugeridos a medida que se escriben.         |
|[Obtención de noticias generales](concepts/search-for-news.md#get-general-news)     | Busque noticias mediante el envío de una consulta de búsqueda a Bing News Search API y la obtención de una lista de artículos de noticias pertinentes.           |
|[Noticias importantes de actualidad](concepts/search-for-news.md#get-todays-top-news)      | Obtenga las noticias importantes de actualidad para todas las categorías.       |
|[Noticias por categoría](concepts/search-for-news.md)     | Busque noticias en categorías específicas.        | 
|[Titulares](concepts/search-for-news.md)     | Busque los titulares más importantes en todas las categorías.         |

## <a name="workflow"></a>Flujo de trabajo

Bing News Search API es un servicio web RESTful, lo que significa que es fácil llamarlo desde cualquier lenguaje de programación que pueda realizar solicitudes HTTP y analizar código JSON. Puede usar el servicio con la API REST o el SDK.

1. Cree una [cuenta de API de Cognitive Services](../cognitive-services-apis-create-account.md) con acceso a Bing Search APIs. Si no tiene una suscripción de Azure, puede [crear una cuenta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envíe una solicitud a la API con una consulta de búsqueda válida.
3. Analice el mensaje JSON devuelto para procesar la respuesta de API.

## <a name="next-steps"></a>Pasos siguientes

Primero, pruebe la [demostración interactiva](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) de Bing News Search API. Esta demostración muestra cómo puede personalizar rápidamente una consulta de búsqueda y buscar noticias en la web.

Para empezar rápidamente con la primera solicitud de API, pruebe un inicio rápido para la [API REST](./csharp.md) o uno de los [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp).

## <a name="see-also"></a>Consulte también

* La sección de referencia de [Bing News Search API v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contiene definiciones e información sobre los puntos de conexión, los encabezados, las respuestas de API y los parámetros de consulta que puede usar para solicitar resultados de búsqueda basados en imágenes.
* En los [requisitos de uso y visualización de Bing](../bing-web-search/use-display-requirements.md) se especifican usos aceptables del contenido y la información adquirida mediante las API de búsqueda de Bing.
* Visite la [página central de Bing Search API](../bing-web-search/overview.md) para explorar las otras API disponibles.
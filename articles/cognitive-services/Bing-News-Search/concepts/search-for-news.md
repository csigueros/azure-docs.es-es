---
title: Búsqueda de noticias con Bing News Search API
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar consultas de búsqueda para noticias generales, tendencias y titulares.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: d1a17411429eb9abd95963522086fc2b9605b2f3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656234"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Búsqueda de noticias con Bing News Search API

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Image Search API facilita la integración de las funcionalidades cognitivas de búsqueda de noticias de Bing en tus aplicaciones.

Aunque Bing News Search API principalmente busca y devuelve los artículos de noticias pertinentes, también proporciona varias características para la recuperación de noticias inteligente y centrada en la web.

## <a name="suggest-and-use-search-terms"></a>Sugerencia y uso de términos de búsqueda

Si proporciona un cuadro de búsqueda donde el usuario escribe su término de búsqueda, use [Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) para mejorar la experiencia. La API devuelve cadenas consulta sugeridas basadas en términos de búsqueda parciales a medida que el usuario escribe.

Una vez que el usuario escriba el término de búsqueda, codifíquelo en formato de URL antes de establecer el parámetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query). Por ejemplo, si el usuario escribe *sailing dinghies*, establezca `q` en `sailing+dinghies` o `sailing%20dinghies`.

## <a name="get-general-news"></a>Obtención de noticias generales

Para obtener artículos de prensa generales relacionadas con el término de búsqueda del usuario desde la web, envíe la solicitud GET siguiente:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Si es la primera vez que llama a cualquiera de las API de Bing, no incluya el encabezado de identificador de cliente. Solo debe incluir el identificador de cliente si se ha llamado previamente a una API de Bing y Bing ha devuelto un identificador de cliente para esa combinación de usuario y dispositivo.

Para obtener noticias de un dominio específico, utilice el operador de consulta [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

El ejemplo JSON siguiente muestra la respuesta a la consulta anterior. Como parte de los [requisitos de uso y visualización](../../bing-web-search/use-display-requirements.md) para las API de búsqueda de Bing, debe mostrar cada artículo de noticias en el orden indicado en la respuesta. Si el artículo tiene artículos agrupados, debe indicar que los artículos relacionados existen y mostrarlos previa solicitud.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

La respuesta de [noticias](/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) muestra una lista de artículos de prensa que para Bing eran apropiados para la consulta. El campo `totalEstimatedMatches` contiene una estimación del número de artículos que están disponibles para ver. Para obtener información acerca de la paginación de los artículos, vea [Noticias de Bing](../../bing-web-search/paging-search-results.md).

Cada [artículo de noticias](/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle) de la lista incluye el nombre del artículo, su descripción y su dirección URL al artículo en el sitio web del host. Si el artículo contiene una imagen, el objeto incluye una miniatura de la imagen. Use `name` y `url` para crear un hipervínculo que lleve al usuario al artículo de noticias en el sitio del host. Si el artículo incluye una imagen, asegúrese de que se pueda hacer clic en ella mediante `url`. Asegúrese de utilizar `provider` para atribuir el artículo.

Si Bing puede determinar la categoría del artículo de prensa, el artículo incluye el campo `category`.

## <a name="get-todays-top-news"></a>Obtención de las noticias importantes de actualidad

Para recibir los principales artículos de noticias de hoy, puede enviar la misma solicitud de noticias generales anterior, a la vez que deja nulo el parámetro `q`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La respuesta para obtener noticias destacadas es prácticamente la misma que para obtener noticias generales. Sin embargo, la respuesta `news` no incluye el campo `totalEstimatedMatches` porque existe un número de resultados establecido. El número de artículos de prensa destacados puede variar según el ciclo de noticias. Asegúrese de usar el campo `provider` para atribuir el artículo.

## <a name="get-news-by-category"></a>Obtención de noticias por categoría

Para obtener artículos de prensa por categoría, como artículos destacados de deportes o de entretenimiento, envíe la siguiente solicitud GET a Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Use el parámetro de consulta [category](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) para especificar la categoría de artículos que quiere obtener. Para obtener una lista de categorías de noticias posibles que puede especificar, vea [News Categories by Market](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market) (Categorías de noticias por mercado).

La respuesta al obtener noticias por categoría es prácticamente la misma que al obtener noticias generales. Sin embargo, los artículos son todos de la categoría especificada.

## <a name="get-headline-news"></a>Obtención de titulares

Para solicitar artículos de titulares y obtener artículos de todas las categorías de noticias, envíe la siguiente solicitud GET a Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

No incluya el parámetro de consulta [categoría](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category).

La respuesta al obtener titulares es igual que al obtener las noticias destacadas del día. Si el artículo es un titular, su campo `headline` se establece en **true**.

De forma predeterminada, la respuesta incluye hasta 12 artículos titulares. Para cambiar el número de artículos titulares que se van a devolver, especifique el parámetro de consulta [headlineCount](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount). La respuesta también incluye hasta cuatro artículos no titulares por categoría de noticias.

La respuesta tiene en cuenta los clústeres como un artículo. Dado que un clúster puede tener varios artículos, la respuesta puede incluir más de 12 artículos titulares y más de cuatro artículos no titulares por categoría.

## <a name="get-trending-news"></a>Obtención de noticias de tendencias

Para obtener nuevos temas que son tendencia en las redes sociales, envíe la siguiente solicitud GET a Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Temas populares solo está disponible en los mercados en-US y zh-CN.

El archivo JSON siguiente es la respuesta a la solicitud anterior. Cada artículo de prensa popular incluye una imagen relacionada, una marca de noticia de última hora y una dirección URL a los resultados de la búsqueda de Bing para el artículo. Use la dirección URL del campo `webSearchUrl` para enviar al usuario a la página de resultados de la búsqueda de Bing. O bien, use el texto de consulta para llamar a [Web Search API](../../bing-web-search/overview.md) y ver los resultados por sí mismo.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Obtener noticias relacionadas

Si hay otros artículos relacionados con un artículo de prensa, el artículo de prensa puede incluir el campo [clusteredArticles](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles). A continuación se muestra un artículo con artículos agrupados.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Solicitudes de limitación

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Navegación por las páginas de resultados de Bing News Search](../../bing-web-search/paging-search-results.md)
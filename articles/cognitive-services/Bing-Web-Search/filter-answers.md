---
title: 'Cómo filtrar los resultados de búsqueda: Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: Puede filtrar los tipos de respuestas que Bing incluye en la respuesta (por ejemplo, imágenes, vídeos y noticias) mediante el parámetro de consulta "responseFilter".
services: cognitive-services
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 8bc11b3f59246c736f8c099e97637c3605405924
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625497"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrado de los resultados incluidos en la respuesta de búsqueda  

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Al consultar la web, Bing devuelve todo el contenido relevante para la búsqueda. Por ejemplo, si la consulta de búsqueda es "navegar+barco", la respuesta podría contener los resultados siguientes:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Parámetros de consulta

Para filtrar las respuestas devueltas por Bing, utilice los siguientes parámetros de consulta cuando llame a la API.  

### <a name="responsefilter"></a>ResponseFilter

Puede filtrar los tipos de respuestas que Bing incluye en la respuesta (por ejemplo, imágenes, vídeos y noticias) mediante el parámetro de consulta [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter), que es una lista de respuestas delimitada por comas. Se incluirá una respuesta si Bing encuentra contenido relevante para ella. 

Para excluir respuestas específicas de la respuesta, como imágenes, anteponga un carácter `-` al tipo de respuesta. Por ejemplo:

```
&responseFilter=-images,-videos
```

A continuación se muestra cómo utilizar `responseFilter` para solicitar imágenes, vídeos y noticias de barcos de navegación. Cuando se codifica la cadena de consulta, las comas cambian a %2c.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A continuación se muestra la respuesta de la consulta anterior. Como Bing no encontró resultados relevantes de vídeo y de noticias, estos no se incluyen en la respuesta.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Aunque Bing no devolvió resultados de vídeo y de noticias en la respuesta anterior, no significa que no exista contenido de vídeo y de noticias. Solo quiere decir que la página no los incluyó. Sin embargo, si [página](./paging-search-results.md) más resultados, probablemente las páginas siguientes los incluyan. Además, si se llama directamente a los puntos de conexión de [Video Search API](../bing-video-search/overview.md) y [News Search API](../bing-news-search/search-the-web.md), es probable que la respuesta contenga resultados.

No recomienda no utilizar `responseFilter` para obtener los resultados de una sola API. Si desea contenido de una sola API de Bing, llame directamente a dicha API. Por ejemplo, para recibir solo las imágenes, envíe una solicitud al punto de conexión de Image Search API, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` u otros de los puntos de conexión de [imágenes](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints). Llamar a una sola API es importante no solo por motivos de rendimiento, también porque las API específicas de contenido ofrecen mejores resultados. Por ejemplo, puede utilizar filtros que no están disponibles en Web Search API para filtrar los resultados.  

### <a name="site"></a>Sitio

Para obtener resultados de búsqueda parámetro de un dominio específico, incluya el operador de consulta `site:` en la cadena de consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> En función de la consulta, si usa el operador de consulta `site:`, existe la posibilidad de que la respuesta incluya contenido para adultos sin tener en cuenta la configuración de [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) (búsqueda segura). Debería usar `site:` solo si es consciente del contenido del sitio y el escenario admite la posibilidad de contenido para adultos.

### <a name="freshness"></a>Actualización

Para limitar los resultados de la respuesta web a las páginas web que Bing ha detectado durante un período específico, establezca el parámetro de consulta [freshness](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) en uno de los siguientes valores que no distinguen mayúsculas de minúsculas:

* `Day`: devuelve páginas web que Bing ha detectado en las últimas 24 horas
* `Week`: devuelve las páginas web que Bing ha detectado en los últimos 7 días
* `Month`: devuelve las páginas web que ha descubierto en los últimos 30 días

También puede establecer este parámetro en un intervalo de fechas personalizado con la forma, `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Para limitar los resultados a una sola fecha, establezca el parámetro freshness en una fecha específica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Los resultados pueden incluir páginas web que se encuentran fuera del período especificado si el número de páginas web que Bing coincide con sus criterios de filtro es menor que el número de páginas web que se han solicitado (o el número predeterminado que Bing devuelve).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitación del número de resultados en la respuesta

Bing puede devolver varios tipos de respuesta en la respuesta JSON. Por ejemplo, si consulta *sailing+dinghies* (navegar+barco), Bing devuelve `webpages`, `images`, `videos` y `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Para limitar el número de resultados que Bing devuelve a los dos primeros resultados (páginas web e imágenes), establezca el parámetro de consulta [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) en 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

La respuesta solo incluye `webPages` y `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Si agrega el parámetro de consulta `responseFilter` a la consulta anterior y lo establece en páginas web y noticias, la respuesta contiene solo páginas web porque no se han clasificado las noticias.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promoción de resultados no clasificados

Si los principales resultados que Bing devuelve para una consulta son páginas web, imágenes, vídeos y búsquedas relacionadas, la respuesta incluirá dichos resultados. Si establece [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) en dos (2), Bing devuelve los dos primeros resultados clasificados: páginas web e imágenes. Si desea que Bing incluya imágenes y vídeos en la respuesta, especifique el parámetro de consulta [promote](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) y establézcalo en images y videos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

La siguiente es la respuesta a la solicitud anterior. Bing devuelve los dos principales resultados, páginas web e imágenes, y promociona los vídeos en la respuesta.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Si establece `promote` en news, la respuesta no incluye los resultados de noticias porque no es un resultado clasificado. Solo se pueden promocionar resultados clasificados.

Los resultados que desea promocionar no cuentan para el límite de `answerCount`. Por ejemplo, si los resultados clasificados son noticias, imágenes y vídeos, y establece `answerCount` en 1 y `promote` en news, la respuesta contiene noticias e imágenes. O bien, si los resultados clasificados son vídeos, imágenes y noticias, la respuesta contiene vídeos y noticias.

Puede usar `promote` únicamente si se especifica el parámetro de consulta `answerCount`.
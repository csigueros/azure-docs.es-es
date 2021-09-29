---
title: 'Cómo usar las clasificaciones para mostrar los resultados de búsqueda: Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: Cómo usar las clasificaciones para mostrar los resultados de búsqueda de Bing Web Search API.
services: cognitive-services
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.openlocfilehash: 4c7ffac31f5b5d6fecacb78487f8b7400d109e6d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620548"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Cómo usar las clasificaciones para mostrar los resultados de Bing Web Search API  

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Cada respuesta de búsqueda de entidad incluye una respuesta [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse), que especifica cómo debe mostrar los resultados de la búsqueda. Resultados de los grupos de respuesta de la clasificación por contenido de la línea principal y contenido de la barra lateral de una página de resultados de la búsqueda tradicional. Si no muestra los resultados en un formato tradicional de línea principal y barra lateral, debe dar mayor visibilidad al contenido de la línea principal que al contenido de la barra lateral.  

Dentro de cada grupo (línea principal o barra lateral), la matriz [Elementos](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) identifica el orden en que debe aparecer el contenido. Cada elemento ofrece la siguientes dos formas de identificar el resultado dentro de una respuesta.  

-   `answerType` y `resultIndex`: el campo `answerType` identifica la respuesta (por ejemplo, Página web o Noticias) y `resultIndex` identifica un resultado dentro de una respuesta (por ejemplo, un artículo de noticias). El índice es de base cero.  

-   `value`: el campo `value` contiene un identificador que coincide con el identificador de una respuesta o de un resultado dentro de una respuesta. La respuesta o los resultados contienen el identificador, pero no ambos.  

El uso del identificador es más simple porque solo debe hacer coincidir el identificador de la clasificación con el identificador de una respuesta o de uno de sus resultados. Si el objeto de una respuesta incluye un campo `id`, muestre todos los resultados de la respuesta juntos. Por ejemplo, si el objeto `News` incluye el campo `id`, muestre todos los artículos de noticias juntos. Si el objeto `News` no incluye el campo `id`, cada artículo de noticias contiene un campo `id` y la respuesta de clasificación combina los artículos de noticias con los resultados de otras respuestas.  

El uso de `answerType` y `resultIndex` es un poco más complicado. Debe usar `answerType` para identificar la respuesta que contiene el resultado que se va a mostrar. A continuación, debe usar `resultIndex` para la indexación en dichos resultados de la respuesta para obtener el resultado que se va a mostrar. (El valor `answerType` es el nombre del campo en el objeto [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse)). Si se supone que se van a mostrar todos los resultados de la respuesta juntos, el elemento de la respuesta de clasificación no incluye el campo `resultIndex`.  

## <a name="ranking-response-example"></a>Ejemplo de respuesta de clasificación

A continuación se muestra un ejemplo de [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Dado que la respuesta web no incluye un campo `id`, se mostrarán todas las páginas web individualmente en función de la clasificación (cada página web incluye un campo `id`). Y dado que las respuestas de las búsquedas de imágenes, vídeos y otras búsquedas relacionadas incluyen el campo `id`, se mostrarán los resultados de cada una de estas respuestas juntas en función de la clasificación.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Según esta respuesta de clasificación, la línea principal mostrará los resultados de la búsqueda siguientes:  

-   El primer resultado de la página web
-   Todas las imágenes  
-   El segundo y el tercer resultados de la página web  
-   Todos los vídeos  
-   Los resultados cuarto, quinto y sexto de la página web  

La barra lateral mostrará los resultados de la búsqueda siguientes:  

-   Todas las búsquedas relacionadas  


## <a name="next-steps"></a>Pasos siguientes

Para obtener información acerca de la promoción de resultados no clasificados, consulte [Promoting answers that are not ranked](./filter-answers.md#promoting-answers-that-are-not-ranked) (Promoción de respuestas no clasificadas).

Para obtener información acerca de cómo limitar el número de respuestas clasificadas en la respuesta, consulte [Limiting the number of answers in the response](./filter-answers.md#limiting-the-number-of-answers-in-the-response) (Limitación del número de respuestas en la respuesta).

Para obtener un ejemplo C# que use la clasificación para mostrar los resultados, consulte el [tutorial de clasificación de C#](./csharp-ranking-tutorial.md).
---
title: 'Etiqueta de conclusiones predeterminada: Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Proporciona detalles sobre de las conclusiones predeterminadas que devuelve Bing Visual Search sobre una imagen.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: b9090925785fb32da4934f98b6efd7b249053112
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669576"
---
# <a name="default-insights-tag"></a>Etiqueta de conclusiones predeterminada

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

La etiqueta de conclusiones predeterminada es la que tiene el campo `displayName` establecido en una cadena vacía. En el ejemplo siguiente se muestra la lista posibles conclusiones predeterminadas (acciones). La lista de acciones que incluye la respuesta depende de la imagen. Y para cada acción, la lista de propiedades puede variar según la imagen, así que compruebe si la propiedad existe antes de intentar usarla.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {
          "_type" : "ImageModuleAction",
          "actionType" : "PagesIncluding",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageShoppingSourcesAction",
          "actionType" : "ShoppingSources",
          "data" : {
            "offers" : [...]
          }
        },
        {
          "image" : {...},
          "actionType" : "MoreSizes"
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "VisualSearch",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageRecipesAction",
          "actionType" : "Recipes",
          "data" : {
            "value" : [...]
          }
        },
        {
          "image" : {...},
          "actionType" : "ImageById"
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "ProductVisualSearch",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageRelatedSearchesAction",
          "actionType" : "RelatedSearches",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageRelatedSearchesAction",
          "actionType" : "DocumentLevelSuggestions",
          "data" : {
            "value" : [...]
          }
        }
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

## <a name="pagesincluding-insight"></a>Conclusión PagesIncluding

La conclusión PagesIncluding proporciona una lista de las páginas web que incluyan esta imagen. En realidad es una lista de objetos `Image` y el campo `hostPageUrl` contiene la dirección URL a la página web que incluye la imagen. Para ver un ejemplo de su uso, consulte el [ejemplo de conclusión de PagesIncluding](./bing-insights-usage.md#pagesincluding-insight-example).

```json
      {
        "_type" : "ImageModuleAction",
        "actionType" : "PagesIncluding",
        "data" : {
          "value" : [
            {
              "webSearchUrl" : "https://www.bing.com\/images\/search?",
              "name" : "Today's smoking hot country",
              "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
              "datePublished" : "2017-09-20T12:00:00.0000000Z",
              "contentUrl" : "http:\/\/contoso.com\/wordstuff",
              "hostPageUrl" : "http:\/\/contoso.com\/2017\/09\/20\/car",
              "contentSize" : "122540 B",
              "encodingFormat" : "jpeg",
              "hostPageDisplayUrl" : "contoso.com\/2017\/09\/20\/car",
              "width" : 894,
              "height" : 1200,
              "thumbnail" : {
                "width" : 474,
                "height" : 636
              },
              "imageInsightsToken" : "ccid_CO5GEthj*mid_5323B1",
              "insightsMetadata" : {
                "pagesIncludingCount" : 12,
                "availableSizesCount" : 7
              },
              "imageId" : "5323B1900FB9087B6B45D176D234E1F2F23CD3A5",
              "accentColor" : "55585B"
            }
          ]
        }
      }
```

## <a name="shoppingsources-insight"></a>Conclusión ShoppingSources

La conclusión ShoppingSources proporciona una lista de sitios web en los que el usuario puede adquirir el elemento que se muestra en la imagen. La lista de ofertas incluye la dirección URL de la página web en la que el usuario puede comprar el artículo, el precio del artículo y la valoración o comentarios. Para ver un ejemplo de su uso, consulte el [ejemplo de ShoppingSources](./bing-insights-usage.md#shoppingsources-insight-example).

```json
      {
        "_type" : "ImageShoppingSourcesAction",
        "actionType" : "ShoppingSources",
        "data" : {
          "offers" : [
            {
              "name" : "Apple Pie",
              "url" : "https:\/\/contoso.com\/product_p\/l10.htm",
              "description" : "A taste of the crust, apple, and pie filling...",
              "seller" : {
                "name" : "Contoso"
              },
              "price" : 3.99,
              "priceCurrency" : "USD",
              "aggregateRating" : {
                "ratingValue" : 5
              },
              "lastUpdated" : "2018-04-16T00:00:00.0000000"
            }
          ]
        }
      }
```

## <a name="moresizes-insight"></a>Conclusión MoreSizes

La conclusión MoreSizes identifica el número de tamaños (mayor o menor) de la imagen que Bing ha encontrado en Internet (consulte el campo `availableSizesCount`):

```json
      {
        "image" : {
          "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detai...",
          "name" : "Making Apple Pie",
          "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP....",
          "datePublished" : "2013-06-21T12:00:00.0000000Z",
          "contentUrl" : "http:\/\/contoso.com\/content\/uploads\/2013\/06\/apple-pie.jpg",
          "hostPageUrl" : "http:\/\/contoso.com\/2013\/06\/21\/making-apple-pie\/",
          "contentSize" : "134847 B",
          "encodingFormat" : "jpeg",
          "hostPageDisplayUrl" : "contoso.com\/2013\/06\/21\/making-apple-pie",
          "width" : 1050,
          "height" : 765,
          "thumbnail" : {
            "width" : 474,
            "height" : 345
          },
          "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CF...",
          "insightsMetadata" : {
            "recipeSourcesCount" : 6,
            "pagesIncludingCount" : 103,
            "availableSizesCount" : 28
          },
          "imageId" : "D12339146CFEDF3D409CC7A66D2C98D0D71904D4",
          "accentColor" : "3A0B01"
        },
        "actionType" : "MoreSizes"
      },
```

## <a name="visualsearch-insight"></a>Conclusión VisualSearch

La conclusión VisualSearch proporciona una lista de imágenes que son visualmente similares a la imagen original (incluye contenido similar al que se muestra en la imagen original). Para ver un ejemplo de su uso, consulte el [ejemplo de conclusión de VisualSearch](./bing-insights-usage.md#visualsearch-insight-example).

```json
      {
        "_type" : "ImageModuleAction",
        "actionType" : "VisualSearch",
        "data" : {
          "value" : [
            {
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
              "name" : "An apple pie...",
              "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.z...",
              "datePublished" : "2017-03-18T00:17:00.0000000Z",
              "contentUrl" : "http:\/\/contoso.net\/images\/8\/8a\/an_apple_pie.png",
              "hostPageUrl" : "http:\/\/contoso.com\/wiki\/an_apple_pie.png",
              "contentSize" : "87930 B",
              "encodingFormat" : "png",
              "hostPageDisplayUrl" : "contoso.com\/wiki\/an_apple_pie.png",
              "width" : 263,
              "height" : 192,
              "thumbnail" : {
                "width" : 474,
                "height" : 346
              },
              "imageInsightsToken" : "ccid_zhRxfGkI*mid_1DCBA7AA6D231...",
              "insightsMetadata" : {
                "recipeSourcesCount" : 6,
                "pagesIncludingCount" : 103,
                "availableSizesCount" : 28
              },
              "imageId" : "1DCBA7AA6D23147F9DD06D47DB3A38EB25389",
              "accentColor" : "3E0D01"
            }
          ]
        }
      }
```

## <a name="recipes-insight"></a>Conclusión Recipes

La conclusión Recipes proporciona una lista de páginas web que incluyen una receta para hacer la comida que se muestra en la imagen. Para ver un ejemplo de su uso, consulte el [ejemplo de conclusión de Recipes](./bing-insights-usage.md#recipes-insight-example).

```json
      {
        "_type" : "ImageRecipesAction",
        "actionType" : "Recipes",
        "data" : {
          "value" : [
            {
              "name" : "Granny's Apple Pie",
              "url" : "http:\/\/contoso.com\/recipes\/appetizer\/apple-pie.html",
              "description" : "I love Granny's apple pie. Sooooo delicious...",
              "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=A63002cd9",
              "creator" : {
                "_type" : "Person",
                "name" : "Charlene Whitney"
              },
              "aggregateRating" : {
                "text" : "5",
                "ratingValue" : 5,
                "bestRating" : 5,
                "reviewCount" : 1
              },
              "cookTime" : "PT45M",
              "prepTime" : "PT1H",
              "totalTime" : "PT1H45M"
            }
          ]
        }
      }
```


## <a name="imagebyid-insight"></a>Conclusión ImageById

La conclusión ImageById proporciona un objeto `Image` de la imagen de la que solicitó conclusiones:

```json
      {
        "image" : {
          "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=deta...",
          "name" : "Making Apple Pie",
          "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP...",
          "datePublished" : "2013-06-21T12:00:00.0000000Z",
          "contentUrl" : "http:\/\/contoso.com\/content\/uploads\/2013\/06\/apple-pie.jpg",
          "hostPageUrl" : "http:\/\/contoso.com\/2013\/06\/21\/making-apple-pie\/",
          "contentSize" : "134847 B",
          "encodingFormat" : "jpeg",
          "hostPageDisplayUrl" : "contoso.com\/2013\/06\/21\/making-apple-pie",
          "width" : 1050,
          "height" : 765,
          "thumbnail" : {
            "width" : 474,
            "height" : 345
          },
          "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFE...",
          "insightsMetadata" : {
            "recipeSourcesCount" : 6,
            "pagesIncludingCount" : 103,
            "availableSizesCount" : 28
          },
          "imageId" : "D12339146CFEDF3D409A66D2C98D0D71904D4",
          "accentColor" : "3A0B01"
        },
        "actionType" : "ImageById"
      },
```

## <a name="productvisualsearch-insight"></a>Conclusión ProductVisualSearch

La conclusión ProductVisualSearch proporciona una lista de imágenes de productos que son visualmente similares a los productos que se muestra en la imagen original. El campo `insightsMetadata` puede contener información acerca de ofertas en las que se puede adquirir el producto y el precio del producto.

```json
      {
        "_type" : "ImageModuleAction",
        "actionType" : "ProductVisualSearch",
        "data" : {
          "value" : [
            {
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detail...",
              "name" : "Contoso 4-Piece Kitchen Package...",
              "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.l9hzaabu-RJd...",
              "datePublished" : "2017-07-16T04:28:00.0000000Z",
              "contentUrl" : "https:\/\/www.contoso.com\/assets\/media\/images\/prod...",
              "hostPageUrl" : "https:\/\/www.contoso.com\/4-piece-kitchen-package...",
              "contentSize" : "13594 B",
              "encodingFormat" : "jpeg",
              "hostPageDisplayUrl" : "https:\/\/www.contoso.com\/4-piece-kitchen-package...",
              "width" : 450,
              "height" : 332,
              "thumbnail" : {
                "width" : 474,
                "height" : 349
              },
              "imageInsightsToken" : "ccid_l9hzaabu*mid_70A8B616355D681DB9A5A...",
              "insightsMetadata" : {
                "shoppingSourcesCount" : 1,
                "recipeSourcesCount" : 0,
                "aggregateOffer" : {
                  "name":"4-Piece Kitchen Package with...",
                  "priceCurrency":"USD",
                  "lowPrice":2756,
                  "offers" : [
                    {
                      "name" : "4-Piece Kitchen Package with...",
                      "url" : "https:\/\/www.fabrikam.com\/1234.html?ref=bing",
                      "description" : "This 36 Frenchdoor refrigerator by...",
                      "seller" : {
                        "name" : "Fabrikam",
                        "image" : {
                          "url" : "https:\/\/tse1.mm.bing.net\/th?id=A818f811..."
                        }
                      },
                      "price" : 2756,
                      "priceCurrency" : "USD",
                      "availability" : "InStock",
                      "lastUpdated" : "2018-02-20T00:00:00.0000000"
                    }
                  ],
                  "offerCount":1
                },
                "pagesIncludingCount" : 4,
                "availableSizesCount" : 2
              },
              "imageId" : "70A8B616355D681DA5980A8D0514BCC995A3",
              "accentColor" : "60646B"
            }
          ]
        }
      }
```

## <a name="relatedsearches-insight"></a>Conclusión RelatedSearches

La conclusión RelatedSearches proporciona una lista de búsquedas relacionadas realizadas por otros usuarios (según los términos de búsqueda de otros usuarios). Para ver un ejemplo de uso, consulte el [ejemplo de conclusión de RelatedSearches](./bing-insights-usage.md#relatedsearches-insight-example).

```json
      {
        "_type" : "ImageRelatedSearchesAction",
        "actionType" : "RelatedSearches",
        "data" : {
          "value" : [
            {
              "text" : "Homemade Apple Pies Recipes",
              "displayText" : "Homemade Apple Pies Recipes",
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Homemade...",
              "thumbnail" : {
                "url" : "https:\/\/tse1.mm.bing.net\/th?q=Homemade+Apple+Pies"
              }
            }
          ]
        }
      }
```

## <a name="documentlevelsuggestions-insight"></a>Conclusión DocumentLevelSuggestions

La conclusión DocumentLevelSuggestions proporciona una lista de términos de búsqueda sugeridos en función del contenido de la imagen:

```json
      {
        "_type" : "ImageRelatedSearchesAction",
        "actionType" : "DocumentLevelSuggestions",
        "data" : {
          "value" : [
            {
              "text" : "American Apple Pie",
              "displayText" : "American Apple Pie",
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=American",
              "thumbnail" : {
                "url" : "https:\/\/tse3.mm.bing.net\/th?q=American+Apple+Pie."
              }
            }
          ]
        }
      }
```

## <a name="next-steps"></a>Pasos siguientes

Consulte [Ejemplos de uso de conclusiones de Bing](bing-insights-usage.md) para ver cómo Bing puede mostrar las conclusiones visuales.

Para empezar a trabajar rápidamente con la primera solicitud, consulte los artículos de inicio rápido:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)
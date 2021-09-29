---
title: 'Obtener conclusiones acerca de una imagen: Bing Image Search API'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo usar Bing Image Search API para obtener más información acerca de una imagen.
services: cognitive-services
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: f4f9ce34f35b32c580a5cbf0f579765e55a4c2d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672521"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Obtener conclusiones acerca de una imagen con Bing Image Search API

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

> [!IMPORTANT]
> En lugar de usar el punto de conexión /images/details para obtener conclusiones de imágenes, use [Visual Search](../bing-visual-search/overview.md), ya que proporciona conclusiones más completas.


Cada imagen incluye un token de conclusión que puede usar para obtener información acerca de la imagen. Por ejemplo, puede obtener una colección de imágenes relacionadas, páginas web que incluyen la imagen o una lista de los comercios donde puede adquirir el producto que se muestra en la imagen.  

Para obtener conclusiones sobre una imagen, capture el token [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) en la respuesta.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

A continuación, llame al punto de conexión Detalles de imagen y defina el parámetro de consulta [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) con el token de `imageInsightsToken`.  

Para especificar la conclusión que quiere obtener, defina el parámetro de consulta `modules`. Para obtener todas las conclusiones, defina `modules` como `All`. Para obtener solo la conclusión de título y colección, defina `modules` como `Caption%2CCollection`. Para obtener una lista completa de posibles conclusiones, consulte [módulos](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). No todas las conclusiones están disponibles para todas las imágenes. La respuesta incluye todas las conclusiones que ha solicitado, si están disponibles.

En el ejemplo siguiente se solicitan todas las conclusiones disponibles para la imagen anterior.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Obtener conclusiones de una imagen conocida

Si tiene la dirección URL de una imagen para la que quiere obtener conclusiones, use el parámetro de consulta [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) en lugar del parámetro [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) para especificar la imagen. O bien, si tiene el archivo de imagen, puede enviar el archivo binario de la imagen en el cuerpo de una solicitud POST. Si usa una solicitud POST, el encabezado `Content-Type` debe establecerse en `multipart/data-form`. Con cualquiera de estas opciones, el tamaño de la imagen no puede superar 1 MB.  

Si tiene una dirección URL a la imagen, en el ejemplo siguiente se muestra cómo solicitar conclusiones de una imagen.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Obtener todas las conclusiones de una imagen  

Para solicitar todas las conclusiones de una imagen, defina el parámetro de consulta [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) como `All`. Para obtener búsquedas relacionadas, la solicitud debe incluir la cadena de consulta del usuario. En este ejemplo se muestra cómo usar [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) para especificar la imagen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

El objeto de nivel superior es un objeto [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse), en lugar de un objeto [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Reconocer entidades en una imagen  

La característica de reconocimiento de entidades identifica las entidades de una imagen (actualmente, solo los usuarios). Para identificar las entidades de una imagen, defina el parámetro de consulta [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) como `RecognizedEntities`.  

> [!NOTE]
> Este módulo no se puede especificar con ningún otro módulo. Si especifica este módulo con otros módulos, la respuesta no incluye entidades reconocidas.  

A continuación se indica cómo especificar la imagen mediante el parámetro [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl). Recuerde codificar como dirección URL los parámetros de consulta.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

A continuación se muestra la respuesta a la solicitud anterior. Dado que la imagen contiene dos personas, la respuesta identifica una región para cada persona. En este caso, las personas se reconocieron en los grupos CelebrityAnnotations y CelebRecognitionAnnotations. Bing enumera las personas de cada grupo en función de la probabilidad de que coincidan con la persona de la imagen original. La lista está en orden descendente de confianza. El grupo CelebRecognitionAnnotations proporciona el mayor nivel de confianza en que la coincidencia es correcta.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

El campo `region` identifica el área de la imagen donde Bing reconoció la entidad. Para las personas, la región representa su cara.  

Los valores del rectángulo están relacionadas con el ancho y alto de la imagen original, y se encuentran dentro de un intervalo entre 0.0 y 1.0. Por ejemplo, si la imagen es de 300 x 200 y la esquina superior izquierda está en el punto (10, 20) y la esquina inferior derecha, en el punto (290, 150), el rectángulo normalizado es:  

-   Left: 10 / 300 = 0.03333...  
-   Top:  20 / 200 = 0.1  
-   Right: 290 / 300 = 0.9667...  
-   Bottom: 150 / 200 = 0.75  

Puede usar la región que devuelve Bing en las llamadas subsiguientes a la conclusión. Por ejemplo, para obtener imágenes visualmente similares de la entidad reconocida. Para más información, consulte Recortar imágenes para usarlas con imágenes visualmente similares o módulos de reconocimiento de entidades. A continuación se muestra la asignación entre los campos de región y los parámetros de consulta que usaría para recortar las imágenes.  

-   La izquierda se asigna a [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   La parte superior se asigna a [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   La derecha se asigna a [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   La parte inferior se asigna a [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Buscar imágenes visualmente similares  

Para buscar imágenes visualmente similares a la imagen original, defina el parámetro de consulta [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) como SimilarImages.  

La solicitud siguiente muestra cómo obtener imágenes visualmente similares. La solicitud usa el parámetro de consulta [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) para identificar la imagen original. Para mejorar la relevancia, debe incluir la cadena de consulta del usuario.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


A continuación se muestra la respuesta a la solicitud anterior.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Recortar imágenes para usarlas con imágenes visualmente similares o módulos de reconocimiento de entidades  

Para especificar la región de la imagen que Bing usa para determinar si las imágenes son visualmente similares o para realizar el reconocimiento de entidades, use los parámetros de consulta [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)y [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car). De forma predeterminada, Bing usa toda la imagen.  

Los parámetros especifican la esquina superior izquierda y la esquina inferior derecha de la región que usa Bing para la comparación. Especifique los valores como fracciones de ancho y alto de la imagen original. Los valores fraccionarios comienzan con (0.0, 0.0) en la esquina superior izquierda y acaban con (1.0, 1.0) en la esquina inferior derecha. Por ejemplo, para especificar que la esquina superior izquierda empieza a un cuarto de la distancia vertical desde la parte superior y a un cuarto de la distancia horizontal desde el lateral izquierdo, defina `cal` como 0.25 y `cat` como 0.25.  

La siguiente secuencia de llamadas muestra el efecto de especificar la región de recorte. La primera llamada no incluye ningún recorte y Bing reconoce dos personas juntas en el centro de la imagen.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

La respuesta muestra dos entidades reconocidas.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

La segunda llamada recorta la imagen verticalmente por la mitad y Bing reconoce a una sola persona en el lado derecho de la imagen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

La respuesta muestra una entidad reconocida.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Buscar productos visualmente similares  

Para buscar imágenes que contengan productos visualmente similares a los productos que aparecen en la imagen original, defina el parámetro de consulta [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) como SimilarProducts.  

La solicitud siguiente muestra cómo obtener imágenes de productos visualmente similares. La solicitud utiliza el parámetro de consulta [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) para identificar la imagen original que se devolvió en una solicitud anterior. Para mejorar la relevancia, debe incluir la cadena de consulta del usuario.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A continuación se muestra la respuesta a la solicitud anterior. La respuesta contiene una imagen de un producto similar e indica cuántos comerciantes ofrecen el producto en línea, si existen clasificaciones de producto y el precio más bajo encontrado (consulte el campo `aggregateOffer`).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Para obtener una lista de los comerciantes que ofrecen el producto en línea (vea el campo [offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)), llame a la API de nuevo y defina `modules` como ShoppingSources. A continuación, defina el parámetro de consulta `insightsToken` con el token encontrado en la imagen de resumen de producto.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A continuación se muestra la respuesta a la solicitud anterior.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
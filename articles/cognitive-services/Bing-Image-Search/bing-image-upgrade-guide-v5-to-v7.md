---
title: Actualización de la v5 a la v7 de Bing Image Search API
titleSuffix: Azure Cognitive Services
description: En esta guía de actualización se describen los cambios de la versión 5 a la 7 de Bing Image Search API. Utilice esta guía para identificar las partes de la aplicación que se deben actualizar para usar la versión 7.
services: cognitive-services
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: e90ce17a5394fcba8a98b64c8959b81f8627d600
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128613030"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guía de actualización de Bing Image Search API v7

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Esta guía de actualización identifica los cambios entre las versiones 5 y 7 de Bing Image Search API. Utilice esta guía para identificar las partes de la aplicación que se deben actualizar para usar la versión 7.

## <a name="breaking-changes"></a>Últimos cambios

### <a name="endpoints"></a>Puntos de conexión

- El número de versión del punto de conexión ha cambiado de v5 a v7. Por ejemplo, https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de la respuesta de error y códigos de error

- Todas las solicitudes con error deben incluir ahora un objeto `ErrorResponse` en el cuerpo de la respuesta.

- Se han agregado los campos siguientes al objeto `Error`.  
  - `subCode`&mdash;Divide el código de error en cubos discretos, si es posible.
  - `moreDetails`&mdash;Información adicional sobre el error descrito en el campo `message`.


- Se han reemplazado los códigos de error v5 por los posibles valores de `code` y `subCode` siguientes.

|Código|Subcódigo|Descripción
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing devuelve ServerError cada vez que se produce alguna de las condiciones del subcódigo. La respuesta incluye estos errores si el código de estado HTTP es 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|Bing devuelve InvalidRequest siempre que alguna parte de la solicitud no es válida. Por ejemplo, falta un parámetro necesario o un valor de parámetro no es válido.<br/><br/>Si el error es ParameterMissing o ParameterInvalidValue, el código de estado HTTP es 400.<br/><br/>Si el error es HttpNotAllowed, el código de estado HTTP es 410.
|RateLimitExceeded||Bing devuelve RateLimitExceeded cada vez que se supera la cuota de consultas por segundo (QPS) o de consultas por mes (QPM).<br/><br/>Bing devuelve el código de estado HTTP 429 si supera la cuota QPS y 403 si supera la cuota QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing devuelve InvalidAuthorization cuando no puede autenticar el autor de la llamada. Por ejemplo, falta el encabezado `Ocp-Apim-Subscription-Key` o la clave de suscripción no es válida.<br/><br/>Se produce redundancia si especifica más de un método de autenticación.<br/><br/>Si el error es InvalidAuthorization, el código de estado HTTP es 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing devuelve InsufficientAuthorization cuando el autor de la llamada no tiene permisos para acceder al recurso. Esto puede ocurrir si la clave de suscripción se ha deshabilitado o ha expirado. <br/><br/>Si el error es InsufficientAuthorization, el código de estado HTTP es 403.

- A continuación se asignan los códigos de error anteriores a los nuevos códigos. Si tiene una dependencia de los códigos de error v5, actualice el código en consecuencia.

|Código de la versión 5|Código.subcódigo de la versión 7
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest.Blocked



### <a name="query-parameters"></a>Parámetros de consulta

- Se ha cambiado el nombre del parámetro de consulta `modulesRequested` por [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Se ha cambiado el nombre de Annotations por Tags. Vea el parámetro de consulta [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) para Tags.  

- Se ha cambiado la lista de mercados compatibles del valor de filtro ShoppingSources a solo en-US. Vea [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Cambios en la información de imagen

- Se ha cambiado el nombre del campo `annotations` de [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) a `imageTags`.  

- Se ha cambiado el nombre del objeto `AnnotationModule` a [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Se ha cambiado el nombre del objeto `Annotation` a [Tag](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag) y se ha quitado el campo `confidence`.  

- Se ha cambiado el nombre del campo `insightsSourcesSummary` del objeto [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) a `insightsMetadata`.  

- Se ha cambiado el nombre del objeto `InsightsSourcesSummary` a [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Se ha agregado el punto de conexión `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Use este punto de conexión para solicitar información de imagen en lugar del punto de conexión /images/search. Consulte el artículo sobre la [información de imagen](./image-insights.md).

- Los siguientes parámetros de consulta ahora solo son válidos con el punto de conexión `/images/details`.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [ct](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Se ha cambiado el nombre del objeto `ImageInsightsResponse` a [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Se han cambiado los tipos de datos de los siguientes campos en el objeto [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

    -   Se ha cambiado el tipo del campo `relatedCollections` de `ImageGallery[]` a [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Se ha cambiado el tipo del campo `pagesIncluding` de `Image[]` a [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Se ha cambiado el tipo del campo `relatedSearches` de `Query[]` a [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Se ha cambiado el tipo del campo `recipes` de `Recipe[]` a [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Se ha cambiado el tipo del campo `visuallySimilarImages` de `Image[]` a [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Se ha cambiado el tipo del campo `visuallySimilarProducts` de `ProductSummaryImage[]` a [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Se ha quitado el objeto `ProductSummaryImage` y se han movido los campos relacionados con el producto al objeto [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image). El objeto `Image` incluye los campos relacionados con el producto solo cuando la imagen se incluye como parte de productos visualmente similares en una respuesta de información de imagen.  

    -   Se ha cambiado el tipo del campo `recognizedEntityGroups` de `RecognizedEntityGroup[]` a [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Se ha cambiado el nombre del campo `categoryClassification` de [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) a `annotations` y se ha cambiado su tipo a `AnnotationsModule`.  

### <a name="images-answer"></a>Respuesta de imágenes

-   Se han quitado los campos displayShoppingSourcesBadges y displayRecipeSourcesBadges de [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Se ha cambiado el nombre del campo `nextOffsetAddCount` de [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) a `nextOffset`. También ha cambiado la forma de usar el desplazamiento. Anteriormente, se establecía el parámetro de consulta [offset](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) en el valor `nextOffsetAddCount` más el valor de desplazamiento anterior más el número de imágenes en el resultado. Ahora, establezca `offset` en el valor `nextOffset`.  


## <a name="non-breaking-changes"></a>Cambios secundarios

### <a name="query-parameters"></a>Parámetros de consulta

- Se ha agregado Transparent como un posible valor del filtro [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype). El filtro Transparent devuelve solo imágenes con un fondo transparente.

- Se ha agregado Any como un posible valor de filtro [license](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license). El filtro Any devuelve solo imágenes bajo licencia.

- Se ha agregado los parámetros de consulta [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) y [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize). Utilice estos filtros para devolver imágenes dentro del intervalo de tamaños de archivo.  

- Se han añadido los parámetros de consulta [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight) y [maxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth). Utilice estos filtros para devolver imágenes dentro de un intervalo de alturas y anchos.  

### <a name="object-changes"></a>Cambios en objetos

- Se han agregado los campos `description` y `lastUpdated` al objeto [Offer](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer).  

- Se ha agregado el campo `name` al objeto [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery).  

- Se ha agregado `similarTerms` al objeto [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images). Este campo contiene una lista de términos que son similares en significado a la cadena de consulta del usuario.
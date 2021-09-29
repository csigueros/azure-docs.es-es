---
title: Actualización de Bing News Search API v5 a v7
titleSuffix: Azure Cognitive Services
description: Se identifican las partes de la aplicación de Bing News Search que se deben actualizar para usar la versión 7.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 9f9688d0c952925fa17867ecd8599d84eee40e71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587050"
---
# <a name="news-search-api-upgrade-guide"></a>Guía de actualización de News Search API

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Esta guía de actualización identifica los cambios entre las versiones 5 y 7 de Bing News Search API. Utilice esta guía para identificar las partes de la aplicación que se deben actualizar para usar la versión 7.

## <a name="breaking-changes"></a>Últimos cambios

### <a name="endpoints"></a>Puntos de conexión

- El número de versión del punto de conexión ha cambiado de v5 a v7. Por ejemplo, `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

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

### <a name="object-changes"></a>Cambios en objetos

- Se ha agregado el campo `contractualRules` al objeto [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle). El campo `contractualRules` contiene una lista de reglas que se deben seguir (por ejemplo, la atribución de artículos). Debe aplicar la atribución proporcionada en `contractualRules` en lugar de usar `provider`. El artículo incluye `contractualRules` solo si la respuesta de [Web Search API](../bing-web-search/overview.md) contiene una respuesta de noticias.

## <a name="non-breaking-changes"></a>Cambios secundarios

### <a name="query-parameters"></a>Parámetros de consulta

- Productos agregados como un posible valor en el que puede establecer el parámetro de consulta [category](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category). Consulte [Categories By Markets](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) (Categorías por mercados).

- Se ha agregado el parámetro de consulta [SortBy](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby), que devuelve tendencias populares ordenadas por fecha con la más reciente primero.

- Se ha agregado el parámetro de consulta [Since](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) que devuelve tendencias populares que se encontraron en Bing en el mismo momento especificado por la marca de tiempo de época de Unix especificada o posteriormente.

### <a name="object-changes"></a>Cambios en objetos

- Se ha agregado el campo `mentions` al objeto [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle). El campo `mentions` contiene una lista de entidades (personas o lugares) que se encontraron en el artículo.

- Se ha agregado el campo `video` al objeto [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle). El campo `video` contiene un vídeo que está relacionado con el artículo de noticias. El vídeo es un \<iframe\> que se puede insertar o una miniatura de vídeo.

- Se ha agregado el campo `sort` al objeto [News](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news). El campo `sort` muestra el criterio de ordenación de los artículos. Por ejemplo, los artículos se ordenan por relevancia (valor predeterminado) o fecha.

- Se ha agregado el objeto [SortValue](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) que define un criterio de ordenación. El campo `isSelected` indica si la respuesta utiliza el criterio de ordenación. Si es **true**, la respuesta utiliza el criterio de ordenación. Si `isSelected` es **false**, puede usar la dirección URL del campo `url` para solicitar un criterio de ordenación diferente.
---
title: Actualizar Bing Web Search API v5 a v7
titleSuffix: Azure Cognitive Services
description: Determinar qué partes de la aplicación es necesario actualizar para usar Bing Web Search v7 API.
services: cognitive-services
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 485505fec1a3f187b16af3496c6385be117d931b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128613011"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Actualizar Bing Web Search API v5 a v7

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Esta guía de actualización identifica los cambios entre las versiones 5 y 7 de Bing Web Search API. Utilice esta guía para identificar las partes de la aplicación que se deben actualizar para usar la versión 7.

## <a name="breaking-changes"></a>Últimos cambios

### <a name="endpoints"></a>Puntos de conexión

- El número de versión del punto de conexión ha cambiado de v5 a v7. Por ejemplo, https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de la respuesta de error y códigos de error

- Todas las solicitudes con error deben incluir ahora un objeto `ErrorResponse` en el cuerpo de la respuesta.

- Se han agregado los campos siguientes al objeto `Error`.  
  - `subCode`&mdash;Divide el código de error en cubos discretos, si es posible.
  - `moreDetails`&mdash;Información adicional sobre el error descrito en el campo `message`.


- Se han reemplazado los códigos de error v5 por los posibles valores de `code` y `subCode` siguientes.

|Código|Subcódigo|Descripción
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing devuelve ServerError cada vez que se produce alguna de las condiciones del subcódigo. La respuesta incluirá estos errores si el código de estado HTTP es 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|Bing devuelve InvalidRequest siempre que alguna parte de la solicitud no es válida. Por ejemplo, falta un parámetro necesario o un valor de parámetro no es válido.<br/><br/>Si el error es ParameterMissing o ParameterInvalidValue, el código de estado HTTP es 400.<br/><br/>Si el error es HttpNotAllowed, el código de estado HTTP es 410.
|RateLimitExceeded||Bing devuelve RateLimitExceeded cada vez que se supera la cuota de consultas por segundo (QPS) o de consultas por mes (QPM).<br/><br/>Bing devuelve el código de estado HTTP 429 si supera la cuota QPS y 403 si supera la cuota QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing devuelve InvalidAuthorization cuando no puede autenticar el autor de la llamada. Por ejemplo, falta el encabezado `Ocp-Apim-Subscription-Key` o la clave de suscripción no es válida.<br/><br/>Se produce redundancia si especifica más de un método de autenticación.<br/><br/>Si el error es InvalidAuthorization, el código de estado HTTP es 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing devuelve InsufficientAuthorization cuando el autor de la llamada no tiene permisos para acceder al recurso. Este error puede ocurrir si la clave de suscripción se ha deshabilitado o ha expirado. <br/><br/>Si el error es InsufficientAuthorization, el código de estado HTTP es 403.

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


## <a name="non-breaking-changes"></a>Cambios secundarios  

### <a name="headers"></a>encabezados

- Se ha agregado el encabezado de solicitud [Pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) opcional. De forma predeterminada, Bing devuelve contenido almacenado en caché, si está disponible. Para que Bing no devuelva contenido almacenado en caché, establezca el encabezado Pragma en no-cache (por ejemplo, Pragma: no-cache).

### <a name="query-parameters"></a>Parámetros de consulta

- Se ha agregado el parámetro de consulta [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount). Utilice este parámetro para especificar el número de respuestas que quiere que incluya la respuesta. Las respuestas se eligen en función de la clasificación. Por ejemplo, si establece este parámetro en tres (3), la respuesta incluye las tres primeras respuestas clasificadas.  

- Se ha agregado el parámetro de consulta [promote](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote). Utilice este parámetro junto con `answerCount` incluir explícitamente uno o más tipos de respuesta, independientemente de su clasificación. Por ejemplo, para promover vídeos e imágenes en la respuesta, habría que establecer promote en *videos, images*. La lista de respuestas que quiere promover no se tiene en cuenta para el límite de `answerCount`. Por ejemplo, si `answerCount` es 2 y `promote` está establecido en *videos, images*, la respuesta puede incluir páginas web, noticias, vídeos e imágenes.

### <a name="object-changes"></a>Cambios en objetos

- Se ha agregado el campo `someResultsRemoved` al objeto [WebAnswer](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer). El campo contiene un valor booleano que indica si la respuesta excluyó algunos resultados de la respuesta web.
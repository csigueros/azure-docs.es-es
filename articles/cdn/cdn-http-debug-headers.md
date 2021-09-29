---
title: Encabezados HTTP X-EC-Debug para el motor de reglas de Azure CDN | Microsoft Docs
description: El encabezado de solicitud de caché de depuración X-EC-Debug proporciona información adicional acerca de la directiva de caché que se aplica al recurso solicitado. Estos encabezados son específicos de Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 7b0171b2bf74f60e2cc1ea7600f313934737103e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639648"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Encabezados HTTP X-EC-Debug para el motor de reglas de Azure CDN
El encabezado de solicitud de caché de depuración, `X-EC-Debug`, proporciona información adicional acerca de la directiva de caché que se aplica al recurso solicitado. Estos encabezados son específicos de los productos **Azure CDN Premium de Verizon**.

## <a name="usage"></a>Uso
La respuesta que se envía desde los servidores POP a un usuario incluye el encabezado `X-EC-Debug` solo si se cumplen las condiciones siguientes:

- La [característica Encabezados de respuesta de caché de depuración](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) se ha habilitado en el motor de reglas para la solicitud especificada.
- La solicitud especificada define el conjunto de encabezados de respuesta de caché de depuración que se incluirán en la respuesta.

## <a name="requesting-debug-cache-information"></a>Solicitud de información de la caché de depuración
Use las siguientes directivas de la solicitud especificada para definir la información de la caché de depuración que se incluirá en la respuesta:

Encabezado de solicitud | Descripción |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Código de estado de la caché](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Código de estado de la caché](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Almacenable en caché](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cache-key](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Estado de la caché](#cache-state-response-header)

### <a name="syntax"></a>Sintaxis

Para solicitar los encabezados de respuesta de caché de depuración, incluya el siguiente encabezado y las directivas especificadas en la solicitud:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Ejemplo de encabezado X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Información sobre el código de estado de la caché
El encabezado de respuesta X-EC-Debug puede identificar un servidor y cómo controlar la respuesta a través de las siguientes directivas:

Header | Descripción
-------|------------
X-EC-Debug: x-ec-cache | Este encabezado se notifica cada vez que el contenido se enruta a través de CDN. Identifica el servidor POP que satisfizo la solicitud.
X-EC-Debug: x-ec-cache-remote | Este encabezado se notifica solo si el contenido solicitado se almacenó en caché en un servidor de escudo de origen o en un servidor de puerta de enlace de ADN.

### <a name="response-header-format"></a>Formato del encabezado de respuesta

El encabezado X-EC-Debug notifica la información sobre el código de estado de la caché en el siguiente formato:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Los términos que se usan en la sintaxis del encabezado de respuesta anterior se definen de la siguiente manera:
- StatusCode: indica cómo administró la red CDN el contenido solicitado, el cual se representa mediante un código de estado de caché.
    
    El código de estado TCP_DENIED se puede notificar en lugar de NONE cuando se deniega una solicitud no autorizada debido a la autenticación basada en token. Sin embargo, el código de estado NONE se continuará usando al ver los informes de estado de la caché o los datos de registro sin procesar.

- Plataforma: indica la plataforma en la que se solicitó el contenido. Los códigos siguientes son válidos para este campo:

    Código  | Plataforma
    ------| --------
    ECAcc | HTTP grande
    ECS   | HTTP pequeño
    ECD   | Red de entrega de aplicaciones (ADN)

- POP: indica el servidor [POP](cdn-pop-abbreviations.md) que procesó la solicitud. 

### <a name="sample-response-headers"></a>Ejemplos de encabezados de respuesta

Los siguientes ejemplos de encabezados proporcionan información sobre el código de estado de la caché para una solicitud:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Encabezado de respuesta almacenable en caché
El encabezado de respuesta `X-EC-Debug: x-ec-check-cacheable` indica si el contenido solicitado se podría haber almacenado en caché.

Este encabezado de respuesta no indica si se produjo el almacenamiento en caché. En vez de eso, indica si la solicitud era apta para este tipo de almacenamiento.

### <a name="response-header-format"></a>Formato del encabezado de respuesta

El encabezado de respuesta `X-EC-Debug` indica si una solicitud que se podría haber almacenado en caché tiene el siguiente formato:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Los términos que se usan en la sintaxis del encabezado de respuesta anterior se definen de la siguiente manera:

Value  | Descripción
-------| --------
SÍ    | Indica que el contenido solicitado era apto para el almacenamiento en caché.
No     | Indica que el contenido solicitado no era apto para el almacenamiento en caché. Este estado podría deberse a uno de los siguientes motivos: <br /> - Configuración específica del cliente: una configuración específica de la cuenta puede impedir a los servidores POP el almacenamiento en caché de un recurso. Por ejemplo, el motor de reglas puede impedir que un recurso se almacene en caché habilitando la característica Omisión de la memoria caché para calificar las solicitudes.<br /> - Encabezados de respuesta de la caché: los encabezados Cache-Control y Expires del recurso solicitado pueden impedir que los servidores POP almacenen el contenido en caché.
UNKNOWN | Indica que los servidores no pudieron determinar si el recurso solicitado era almacenable en caché. Este estado se produce normalmente si la solicitud se deniega debido a una autenticación basada en token.

### <a name="sample-response-header"></a>Ejemplo de encabezado de respuesta

El siguiente ejemplo de encabezado de respuesta indica si el contenido solicitado se podría haber almacenado en caché:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Encabezado de respuesta Cache-Key
El encabezado de respuesta `X-EC-Debug: x-ec-cache-key` indica la clave de caché física asociada al contenido solicitado. Una clave de caché física es una ruta de acceso que identifica un recurso con fines de almacenamiento en caché. En otras palabras, los servidores buscarán una versión almacenada en caché de un recurso de acuerdo a la ruta de acceso que definió la clave de caché.

Esta clave de caché física empieza por una doble barra diagonal (/ /) seguida por el protocolo que se usa para solicitar el contenido (HTTP o HTTPS). Este protocolo viene seguido por la ruta de acceso relativa al recurso solicitado, que comienza por el punto de acceso al contenido (por ejemplo, _/000001/_).

De forma predeterminada, las plataformas HTTP están configuradas para utilizar *standard-cache*, lo que significa que el mecanismo de almacenamiento en caché ignorará las cadenas de consulta. Este tipo de configuración impide que la clave de caché incluya los datos de la cadena de consulta.

Si una cadena de consulta se registra en la clave de caché, se convertirá en su equivalente hash y, a continuación, se insertará entre el nombre del recurso solicitado y su extensión de archivo (por ejemplo, recurso&lt;valor hash&gt;.html).

### <a name="response-header-format"></a>Formato del encabezado de respuesta

El encabezado de respuesta `X-EC-Debug` muestra información de la clave de caché física en el siguiente formato:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Ejemplo de encabezado de respuesta

El siguiente ejemplo de encabezado de respuesta indica la clave de caché física del contenido solicitado:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Encabezado de respuesta Cache-state
El encabezado de respuesta `X-EC-Debug: x-ec-cache-state` indica el estado de la caché del contenido solicitado en el momento en que se solicitó.

### <a name="response-header-format"></a>Formato del encabezado de respuesta

El encabezado de respuesta `X-EC-Debug` notifica la información sobre el estado de la caché en el siguiente formato:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Los términos que se usan en la sintaxis del encabezado de respuesta anterior se definen de la siguiente manera:

- MASeconds: indica la duración máxima (en segundos) de acuerdo con los encabezados Cache-Control del contenido solicitado.

- MATimePeriod: convierte el valor de la duración máxima (es decir, MASeconds) en el equivalente aproximado de una unidad mayor (por ejemplo, días). 

- UnixTime: indica la marca de tiempo de caché del contenido solicitado en tiempo Unix (también conocido como tiempo POSIX o epoch Unix). La marca de tiempo de la caché indica la fecha y hora de inicio desde la que se empezará a calcular el período de vida de un recurso. 

    Si el servidor de origen no utiliza un servidor de caché HTTP de un tercero o si ese servidor no devuelve el encabezado de respuesta Age, la marca de tiempo de la caché será siempre la fecha y hora en la que se recuperó o revalidó el recurso. En caso contrario, los servidores POP usarán el campo Age para calcular el período de vida del recurso de la siguiente manera: Retrieval/RevalidateDateTime - Age.

- ddd, dd MMM yyyy HH:mm:ss GMT: indica la marca de tiempo de la caché del contenido solicitado. Para más información, consulte el término UnixTime descrito anteriormente.

- CASeconds: indica el número de segundos transcurridos desde la marca de tiempo de la caché.

- RTSeconds: indica el número de segundos que quedan antes de que el contenido almacenado en caché deje de considerarse nuevo. Este valor se calcula de la siguiente manera: RTSeconds = max-age - cache age.

- RTTimePeriod: convierte el valor del período de vida (es decir, RTSeconds) en el equivalente aproximado de una unidad mayor (por ejemplo, días).

- ExpiresSeconds: indica el número de segundos que quedan antes de la fecha y hora especificada en el encabezado de respuesta `Expires`. Si el encabezado de respuesta `Expires` no se incluyó en la respuesta, el valor de este término será *none*.

### <a name="sample-response-header"></a>Ejemplo de encabezado de respuesta

El siguiente ejemplo de encabezado de respuesta indica el estado de la caché del contenido solicitado en el momento en que se solicitó:

`X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none`

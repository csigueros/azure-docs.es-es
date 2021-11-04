---
title: Encabezados HTTP específicos de Verizon para el motor de reglas de Azure CDN | Microsoft Docs
description: En este artículo se describe cómo usar los encabezados HTTP específicos de Verizon con el motor de reglas de Azure CDN.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: duau
ms.openlocfilehash: 25e51bbc96f07f29fe62e85366d1d6359dffaaea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439317"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Encabezados HTTP específicos de Verizon para el motor de reglas de Azure CDN

En el caso de los productos de **Azure CDN Premium de Verizon**, cuando se envía una solicitud HTTP al servidor de origen, el servidor de punto de presencia (POP) puede agregar uno o varios encabezados reservados (o encabezados de proxy especiales) en la solicitud del cliente al POP. Estos encabezados son adicionales a los encabezados de reenvío estándar recibidos. Para más información acerca de los encabezados de solicitud estándar, consulte [Request fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields) (Campos de solicitud).

Si desea impedir que se agregue uno de estos encabezados reservados en la solicitud POP de Azure CDN (Content Delivery Network) al servidor de origen, deberá crear una regla con la [característica Encabezados de proxy especiales](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) en el motor de reglas. En esta regla, excluya el encabezado que desee quitar de la lista predeterminada de encabezados del campo correspondiente. Si ha habilitado la [característica Depurar encabezados de respuesta de la caché](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm), no olvide agregar los encabezados `X-EC-Debug` necesarios. 

Por ejemplo, para quitar el encabezado `Via`, el campo de encabezados de la regla debería incluir la siguiente lista de encabezados: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regla de encabezados de proxy especiales](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

En la tabla siguiente se describen los encabezados que se pueden agregar mediante el servidor POP de CDN de Verizon en la solicitud:

Encabezado de solicitud | Descripción | Ejemplo
---------------|-------------|--------
[Via](#via-request-header) | Identifica el servidor POP que redirigió mediante un proxy la solicitud a un servidor de origen. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | Indica la dirección IP del solicitante.| 10.10.10.10
X-Forwarded-Proto | Indica el protocolo de la solicitud. | http
X-Host | Indica el nombre de host de la solicitud. | cdn.mydomain.com
X-Midgress | Indica si la solicitud se redirigió mediante un proxy a través de un servidor de CDN adicional. Por ejemplo, un servidor POP de escudo de servidor a origen o un servidor POP de puerta de enlace de servidor a ADN. <br />Este encabezado se agrega a la solicitud solo cuando tiene lugar el tráfico de midgress. En este caso, el encabezado se establece en 1 para indicar que la solicitud se redirigió mediante un proxy a través de un servidor de CDN adicional.| 1
[Host](#host-request-header) | Identifica el host y el puerto en los que se puede encontrar el contenido solicitado. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: identifica la lista de conmutaciones por error de los servidores de puerta de enlace de ADN asignados a un origen de cliente. <br />Escudo de origen: indica el conjunto de servidores de escudo de origen asignados a un origen de cliente. | `icn1,hhp1,hnd1`
X-EC- _&lt;name&gt;_ | Los encabezados de solicitud que comienzan por *X-EC* (por ejemplo, X-EC-Tag, [X-EC-Debug](cdn-http-debug-headers.md)) están reservados para su uso por la CDN.| waf-production

## <a name="via-request-header"></a>Encabezado de solicitud Via
El formato a través del cual el encabezado de solicitud `Via` identifica un servidor POP se especifica mediante la sintaxis siguiente:

`Via: Protocol from Platform (POP/ID)` 

Los términos que se usan en la sintaxis se definen de la siguiente manera:
- Protocolo: indica la versión del protocolo (por ejemplo, HTTP/1.1) que se usa para redirigir mediante proxy la solicitud. 

- Plataforma: indica la plataforma en la que se solicitó el contenido. Los códigos siguientes son válidos para este campo: 

    Código | Plataforma
    -----|---------
    ECAcc | HTTP grande
    ECS   | HTTP pequeño
    ECD   | Red de entrega de aplicaciones (ADN)

- POP: indica el servidor [POP](cdn-pop-abbreviations.md) que procesó la solicitud. 

- ID: solo para uso interno.

### <a name="example-via-request-header"></a>Ejemplo de encabezado de solicitud Via

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Encabezado de solicitud Host
Los servidores POP sobrescribirán el encabezado `Host` si se cumplen las dos condiciones siguientes:
- El origen del contenido solicitado es un servidor de origen del cliente.
- La opción de encabezado Host HTTP de origen del cliente correspondiente no está en blanco.

El encabezado de solicitud `Host` se sobrescribirá para reflejar el valor definido en la opción del encabezado Host HTTP.
Si la opción de encabezado Host HTTP del origen del cliente está en blanco, el encabezado de solicitud `Host` que envía el solicitante se reenviará al servidor de origen del cliente.

## <a name="x-gateway-list-request-header"></a>Encabezado de solicitud X-Gateway-List
Un servidor POP agregará o sobrescribirá el "encabezado de solicitud X-Gateway-List si se cumple alguna de estas condiciones:
- La solicitud apunta a la plataforma de ADN.
- La solicitud se reenvía a un servidor de origen del cliente que está protegido por la característica de escudo de origen.


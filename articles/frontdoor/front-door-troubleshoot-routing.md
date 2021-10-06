---
title: Solución de problemas de configuración de Azure Front Door
description: En este tutorial, aprenderá a solucionar por sí mismo algunos de los problemas comunes que pueden surgir con la instancia de Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: ed47d310f418936b84c505fcf254947a67f0eb6d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824413"
---
# <a name="troubleshooting-common-routing-problems"></a>Solución de problemas comunes de enrutamiento

En este artículo se describe cómo solucionar problemas comunes de enrutamiento que pueden surgir con la configuración de Azure Front Door.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>Respuesta 503 de Azure Front Door después de unos segundos

### <a name="symptom"></a>Síntoma

* Las solicitudes normales enviadas al back-end sin pasar a través de Azure Front Door son correctas. Al pasar a través de Azure Front Door, se producen respuestas de error 503.
* El error de Azure Front Door suele aparecer después de unos 30 segundos.
* Errores intermitentes de 503 con el registro `ErrorInfo: OriginInvalidResponse`.

### <a name="cause"></a>Causa

La causa del problema puede ser una de estas tres cosas:
 
* El back-end es mayor que el tiempo de expiración configurado (el valor predeterminado es 30 segundos) para recibir la solicitud de Azure Front Door.
* El tiempo que se tarda en enviar una respuesta a la solicitud desde Azure Front Door es mayor que el valor de tiempo de expiración.
* El cliente envió una solicitud de intervalo de bytes con `Accept-Encoding header` (compresión habilitada).

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

* Envíe la solicitud al back-end directamente (sin pasar a través de Azure Front Door). Vea cuánto tiempo tarda el back-end en responder.
* Envíe la solicitud a través de Azure Front Door y vea si se obtienen respuestas 503. Si no es así, es posible que no se trate de un problema de tiempo de expiración. Póngase en contacto con el servicio de soporte técnico.
* Si las solicitudes que pasan por Azure Front Door dan lugar a un código de respuesta de error 503, configure **Send/receive timeout (in seconds)** (Tiempo de espera de envío o recepción (en segundos)) para Azure Front Door. Puede ampliar el tiempo de espera predeterminado hasta 4 minutos (240 segundos). Para configurar esta opción, vaya al *diseñador de Front Door* y seleccione **Configuración**.

    :::image type="content" source=".\media\troubleshoot-route-issues\send-receive-timeout.png" alt-text="Captura de pantalla del campo de tiempo de espera de envío o recepción en el diseñador de Front Door.":::

* Si con la configuración del tiempo de espera no se resuelve el problema, use una herramienta como Fiddler o la herramienta para desarrolladores del explorador para comprobar si el cliente envía solicitudes de intervalo de bytes con encabezados Accept-Encoding, lo que provoca que el origen responda con distintas longitudes de contenido. En caso afirmativo, puede deshabilitar la compresión en Origen/Azure Front Door, o bien crear una regla de conjunto de reglas para quitar `accept-encoding` de las solicitudes de intervalo de bytes.

    :::image type="content" source=".\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="Captura de pantalla de la regla Accept-Encoding en el motor de reglas.":::

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Las solicitudes enviadas al dominio personalizado devuelven el código de estado 400

### <a name="symptom"></a>Síntoma

* Creó una instancia de Azure Front Door, pero una solicitud al dominio o el host de front-end devuelve un código de estado HTTP 400.
* Creó una asignación de DNS para un dominio personalizado para el host de front-end que configuró. Sin embargo, el envío de una solicitud al nombre de host de dominio personalizado devuelve un código de estado HTTP 400. No parece enrutarse al back-end que configuró.

### <a name="cause"></a>Causa

El problema se produce si no configuró una regla de enrutamiento para el dominio personalizado que se agregó como host de front-end. Es necesario agregar explícitamente una regla de enrutamiento para ese host de front-end. Esto es así incluso si ya se ha configurado una regla de enrutamiento para el host de front-end en el subdominio de Azure Front Door (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

Agregue una regla de enrutamiento para que el dominio personalizado dirija el tráfico al grupo de back-end seleccionado.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door no redirige HTTP a HTTPS

### <a name="symptom"></a>Síntoma

Azure Front Door tiene una regla de enrutamiento que redirige HTTP a HTTPS, pero el acceso al dominio sigue manteniendo HTTP como protocolo.

### <a name="cause"></a>Causa

Este comportamiento puede producirse si no configuró correctamente las reglas de enrutamiento para Azure Front Door. Básicamente, la configuración actual no se ha especificado y puede tener reglas en conflicto.

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>La solicitud a un nombre de host de front-end devuelve el código de estado 404

### <a name="symptom"></a>Síntoma

Creó una instancia de Azure Front Door configurando un host de front-end, un grupo de back-end con al menos un back-end y una regla de enrutamiento que conecta el host de front-end con el grupo de back-end. El contenido no está disponible al realizar una solicitud al host de front-end configurado. Como resultado, la solicitud devuelve un código de estado HTTP 404.

### <a name="cause"></a>Causa

Hay varias causas posibles para este síntoma:

* El back-end no es un back-end orientado al público y no es visible para Azure Front Door.
* La configuración del back-end es errónea, lo que hace que Azure Front Door envíe la solicitud equivocada. Es decir, el back-end solo acepta HTTP y no ha deshabilitado HTTPS. Así pues, Azure Front Door intenta reenviar solicitudes HTTPS.
* El back-end rechaza el encabezado de host que se ha reenviado con la solicitud al back-end.
* La configuración para el back-end aún no se ha implementado por completo.

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

* Compruebe el tiempo de implementación:
   * Asegúrese de haber esperado al menos 10 minutos para que se implemente la configuración.

* Compruebe la configuración de back-end:
    * Vaya al grupo de back-end al que se debe enrutar la solicitud. (Depende de cómo se haya configurado la regla de enrutamiento). Compruebe que el tipo de host de back-end y el nombre de host de back-end sean correctos. Si el back-end es un host personalizado, asegúrese de haberlo escrito correctamente. 

    * Compruebe sus puertos HTTP y HTTPS. En la mayoría de los casos, 80 y 443 (respectivamente) son correctos y no es necesario realizar ningún cambio. Sin embargo, existe la posibilidad de que el back-end no esté configurado de este modo y esté escuchando en otro puerto.

    * Compruebe el encabezado host de back-end configurado para los back-ends a los que debería enrutarse el host de front-end. En la mayoría de los casos, este encabezado debería ser el mismo que el nombre de host de back-end. Sin embargo, un valor incorrecto puede provocar distintos códigos de estado HTTP 4xx si el back-end espera algo diferente. Si escribe la dirección IP de su back-end, es posible que deba establecer el encabezado host de back-end en el nombre de host del back-end.

* Compruebe la configuración de la regla de enrutamiento:
    * Vaya a la regla de enrutamiento que debe enrutar del nombre de host de front-end en cuestión a un grupo de back-end. Asegúrese de que los protocolos aceptados estén configurados correctamente cuando se reenvía la solicitud. El campo **Protocolos aceptados** determina qué solicitudes debe aceptar Azure Front Door. El protocolo de reenvío determina qué protocolo debe usar Azure Front Door para reenviar la solicitud al back-end.
      
      Por ejemplo, si el back-end solo acepta solicitudes HTTP, las siguientes configuraciones serán válidas:
            
      * Los protocolos aceptados son HTTP y HTTPS. El protocolo de reenvío es HTTP. Una solicitud de coincidencia no funcionará porque HTTPS es un protocolo permitido. Si una solicitud llega como HTTPS, Azure Front Door intentará reenviarla mediante HTTPS.

      * El protocolo aceptado es HTTP. El protocolo de reenvío es una solicitud de coincidencia o HTTP.
    - **Reescritura de direcciones URL** está deshabilitada de forma predeterminada. Este campo solo se usa si quiere restringir el ámbito de los recursos hospedados en back-end que quiera que estén disponibles. Cuando este campo esté deshabilitado, Azure Front Door reenviará la misma ruta de acceso de solicitud que reciba. Se puede configurar incorrectamente este campo. Por tanto, cuando Azure Front Door solicita un recurso del back-end que no está disponible, devolverá un código de estado HTTP 404.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>La solicitud al nombre de host de front-end devuelve el código de estado 411

### <a name="symptom"></a>Síntoma

Creó una instancia de Azure Front Door y configuró un host de front-end, un grupo de back-end con al menos un back-end y una regla de enrutamiento que conecta el host de front-end con el grupo de back-end. El contenido parece no estar disponible cuando una solicitud se dirige al host de front-end configurado, ya que se devuelve un código de estado HTTP 411.

Las respuestas a estas solicitudes también pueden contener una página de error HTML en el cuerpo de la respuesta que incluya una declaración explicativa. Por ejemplo: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Hay varias causas posibles para este síntoma. El motivo general es que la solicitud HTTP no es totalmente compatible con RFC. 

Un ejemplo de incumplimiento es una solicitud `POST` enviada sin un encabezado `Content-Length` o `Transfer-Encoding` (por ejemplo, con `curl -X POST https://example-front-door.domain.com`). Esta solicitud no cumple los requisitos establecidos en [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azure Front Door la bloqueará con una respuesta HTTP 411.

Este comportamiento es independiente de la funcionalidad Web Application Firewall (WAF) de Azure Front Door. Actualmente, no hay ninguna manera de deshabilitar este comportamiento. Todas las solicitudes HTTP deben cumplir los requisitos, incluso si la funcionalidad WAF no está en uso.

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

- Compruebe que las solicitudes cumplen los requisitos establecidos en las RFC necesarias.

- Tome nota de cualquier cuerpo de mensaje HTML que se devuelva en respuesta a su solicitud. Un cuerpo de mensaje suele explicar exactamente *cómo* la solicitud no es conforme.

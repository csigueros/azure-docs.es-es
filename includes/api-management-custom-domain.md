---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 08/23/2021
ms.author: vlvinogr
ms.openlocfilehash: 8a213834ede720a3a7f9df39d6030bd48d7d7b09
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779810"
---
## <a name="how-api-management-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Respuesta del servidor proxy de API Management con certificados SSL en el protocolo de enlace TLS

### <a name="clients-calling-with-server-name-indication-sni-header"></a>Clientes que llaman con el encabezado Indicación de nombre de servidor (SNI)
Si tiene uno o varios dominios personalizados configurados para el proxy (puerta de enlace), API Management puede responder a solicitudes HTTPS desde ambos:
* Dominio personalizado (por ejemplo, `contoso.com`)
* Dominio predeterminado (por ejemplo, `apim-service-name.azure-api.net`). 

En función de la información del encabezado SNI, API Management responde con el certificado de servidor adecuado.

### <a name="clients-calling-without-sni-header"></a>Clientes que llaman sin encabezado SNI
Si el usuario usa un cliente que no envía el encabezado [SNI](https://tools.ietf.org/html/rfc6066#section-3), API Management crea respuestas basadas en la lógica siguiente:

* **Si el servicio tiene un solo dominio personalizado configurado para el proxy**, el certificado predeterminado es el que se emite para el dominio personalizado de proxy.
* **Si el servicio ha configurado múltiples dominios personalizados para el proxy (compatibles en el nivel **Desarrollador** y **Prémium**)** , para designar el certificado predeterminado, establezca la propiedad [defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) en "true" ("defaultSslBinding": "true"). Si no establece la propiedad, el certificado predeterminado es el que se emite para el dominio de proxy predeterminado hospedado en `*.azure-api.net`.

## <a name="support-for-putpost-request-with-large-payload"></a>Compatibilidad para la solicitud PUT/POST con una carga grande

El servidor proxy de API Management admite las solicitudes con cargas grandes (> 40 KB) cuando se usan certificados del lado cliente en HTTPS. Para evitar que la solicitud del servidor se quede congelada, puede establecer la propiedad ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) en el nombre de host del proxy. 

Si la propiedad está establecida en true, el certificado de cliente se solicita en el tiempo de conexión SSL/TLS, antes de intercambiar cualquier solicitud HTTP. Puesto que la configuración se aplica al nivel de **Nombre de host del proxy**, todas las solicitudes de conexión piden el certificado de cliente. Puede evitar esta limitación y configurar un total de 20 dominios personalizados para el proxy (solo se admite en el nivel **Prémium**).

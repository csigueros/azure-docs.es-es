---
title: Ejemplos de directivas de Azure API Management | Microsoft Docs
description: Aprenda sobre las directivas de transformación disponibles para su uso en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: d361a7a5d5f0f6b536caf9918dc3e1578b6f364e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128554577"
---
# <a name="api-management-policy-samples"></a>Ejemplos de directivas de API Management

Las [directivas](../api-management-howto-policies.md) constituyen una funcionalidad del sistema eficaz que permite al editor cambiar el comportamiento de la API mediante la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. En la tabla siguiente se incluyen vínculos a ejemplos y una breve descripción de cada ejemplo.

| Directivas de entrada | Descripción |
| ---------------- | ----------- |
| [Incorporación de un encabezado de reenviado para permitir que la API de back-end cree direcciones URL correctas](./set-header-to-enable-backend-to-construct-urls.md) | Muestra cómo agregar un encabezado de reenviado a la solicitud de entrada para permitir que la API de back-end cree direcciones URL correctas.                                                                                                        |
| [Incorporación de un encabezado con un identificador de correlación](./add-correlation-id.md)                                                             | Muestra cómo agregar un encabezado con un identificador de correlación para la solicitud de entrada.                                                                                                                                        |
| [Incorporación de funcionalidades a un servicio back-end y almacenamiento de la respuesta en caché](./cache-response.md)                                             | Muestra cómo agregar funcionalidades a un servicio back-end. Por ejemplo, aceptar el nombre de un lugar en vez de su latitud y longitud en una API de pronóstico meteorológico.                                                                    |
| [Autorización de acceso con notificaciones JWT](./authorize-request-based-on-jwt-claims.md)                                              | Muestra cómo autorizar el acceso a los métodos HTTP específicos en una API con notificaciones JWT.                                                                                                                                       |
| [Autorización de solicitudes mediante un autorizador externo](./authorize-request-using-external-authorizer.md)                                                   | Muestra cómo utilizar un autorizador externo para proteger el acceso de una API.                                                                                                                                                               |
| [Autorización de acceso con el token de OAuth de Google](./use-google-as-oauth-token-provider.md)                                            | Muestra cómo autorizar el acceso a los puntos de conexión con Google como proveedor de tokens de OAuth.                                                                                                                                    |
| [Filtrado de direcciones IP al usar una instancia de Application Gateway](./filter-ip-addresses-when-using-appgw.md) | Muestra cómo filtrar direcciones IP en las directivas cuando se accede a la instancia de API Management mediante una instancia de Application Gateway.
| [Generación de la firma de acceso compartido y reenvío de la solicitud a Azure Storage](./generate-shared-access-signature.md)                  | Muestra cómo generar una [firma de acceso compartido](../../storage/common/storage-sas-overview.md) mediante expresiones y reenviar la solicitud a Azure Storage con la directiva de reescritura del identificador URI. |
| [Obtención del token de acceso de OAuth2 desde AAD y reenvío al back-end](./use-oauth2-for-authorization.md)                             | Proporciona un ejemplo de uso de OAuth2 para la autorización entre la puerta de enlace y un back-end. Muestra cómo obtener un token de acceso en AAD y reenviarlo al back-end.                                                    |
| [Obtención del token de X-CSRF en la puerta de enlace de SAP mediante la directiva de solicitud de envío](./get-x-csrf-token-from-sap-gateway.md)                           | Muestra cómo implementar el patrón X-CSRF que utilizan muchas API. Este ejemplo es específico de puerta de enlace de SAP.                                                                                                                           |
| [Enrutamiento de la solicitud en función del tamaño de su cuerpo](./route-requests-based-on-size.md)                                            | Muestra cómo enrutar las solicitudes en función del tamaño de su cuerpo.                                                                                                                                                       |
| [Envío de información contextual de la solicitud al servicio de back-end](./send-request-context-info-to-backend-service.md)                    | Muestra cómo enviar información contextual al servicio de back-end para el registro o el procesamiento.                                                                                                                                |
| [Establecimiento de la duración en caché de las respuestas](./set-cache-duration.md)                                                                          | Muestra cómo establecer la duración en caché de las respuestas con el valor maxAge en el encabezado de control de caché que envía el back-end.                                                                                                             |
| **Directivas de salida** | **Descripción** |
| [Filtrado del contenido de la respuesta](./filter-response-content.md)                                                                         | Muestra cómo filtrar los elementos de datos de la carga de respuesta según el producto asociado a la solicitud.                                                                                                        |
| **Directivas de errores** | **Descripción** |
| [Registro de errores en Stackify](./log-errors-to-stackify.md)                                                                           | Muestra cómo agregar una directiva de registro de errores para enviarlos a Stackify para el registro.                                                                                                                                            |

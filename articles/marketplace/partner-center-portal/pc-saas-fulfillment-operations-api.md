---
title: SaaS Fulfillment Operations API v2 en el marketplace comercial de Microsoft
description: Aprenda a usar las API de operaciones, que forman parte de SaaS Fulfillment API versión 2 para administrar una oferta de SaaS en Microsoft AppSource, Azure Marketplace y Azure Portal.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 11/10/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ef0e735206df6556bd7a7a7b2cf584bc79566a0f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063913"
---
# <a name="saas-fulfillment-operations-apis-v2-in-the-microsoft-commercial-marketplace"></a>SaaS Fulfillment Operations API v2 en el marketplace comercial de Microsoft

En este artículo se describe la versión 2 de SaaS Fulfillment Operations API.

## <a name="list-outstanding-operations"></a>Lista de operaciones pendientes

Obtiene una lista de las operaciones pendientes para la suscripción de SaaS especificada. El editor debe confirmar las operaciones devueltas mediante una llamada a la [API Operation Patch](#update-the-status-of-an-operation).

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Use 2018-08-31.         |
|    `subscriptionId` | Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve.  |

*Encabezados de la solicitud*:
 
|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `authorization`     |  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de respuesta*:

Código: 200 Devuelve operaciones pendientes en la suscripción de SaaS especificada.

*Ejemplo de carga de respuesta:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Devuelve JSON vacío si no hay operaciones pendientes.

Código: 400 Solicitud incorrecta: errores de validación.

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado. La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 404 No encontrado.  No se encuentra la suscripción de SaaS con el valor de `subscriptionId`.

Código: 500 Error interno del servidor. Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-operation-status"></a>Obtener el estado de la operación

Esta API permite al editor hacer un seguimiento del estado de la operación asincrónica especificada:  **Unsubscribe**, **ChangePlan** o **ChangeQuantity**.

El valor de `operationId` para esta llamada API se puede recuperar a partir del valor devuelto por **Operation-Location**, la llamada API para obtener operaciones pendientes o el valor del parámetro `<id>` recibido en una llamada de webhook.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve. |
|  `operationId`       |  Identificador único de la operación que se va a recuperar. |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `authorization`     |  Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de respuesta*:

Código: 200 Obtiene los detalles de la operación de SaaS especificada. 

*Ejemplo de carga de respuesta:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado. La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 404 No encontrado.  

* No se encuentra la suscripción con el valor de `subscriptionId`.
* No se encuentra la operación con el valor de `operationId`.

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="update-the-status-of-an-operation"></a>Actualizar el estado de una operación

Use esta API para actualizar el estado de una operación pendiente para indicar si se ha realizado correctamente en el lado del editor.

El valor de `operationId` para esta llamada API se puede recuperar a partir del valor devuelto por **Operation-Location**, la llamada API para obtener operaciones pendientes o el valor del parámetro `<id>` recibido en una llamada de webhook.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Use 2018-08-31.  |
|   `subscriptionId`   |  Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve.  |
|   `operationId`      |  Identificador único de la operación que se va a completar. |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|   `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `authorization`     |  Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Ejemplo de solicitud de carga:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Códigos de respuesta*:

Código: 200 Llamada para informar de la finalización de una operación por parte del partner.  Por ejemplo, esta respuesta podría la finalización del cambio de puestos o planes en el lado del editor.

Código: 403
- Prohibido.  El token de autorización no está disponible, no es válido o ha expirado. La solicitud podría estar intentando acceder a una suscripción que no pertenece al editor actual.
- Prohibido.  El token de autorización no es válido, ha expirado o no se ha proporcionado. La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 404 No encontrado.

* No se encuentra la suscripción con el valor de `subscriptionId`.
* No se encuentra la operación con el valor de `operationId`.

Código: 409 Conflicto.  Por ejemplo, ya se suministra una actualización más reciente.

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="next-steps"></a>Pasos siguientes

Consulte las [API del servicio de medición del marketplace comercial](../marketplace-metering-service-apis.md) si quiere descubrir más opciones para las ofertas de SaaS en el marketplace comercial.

Revise y use los [clientes para obtener ejemplos y lenguajes de programación diferentes](https://github.com/microsoft/commercial-marketplace-samples).

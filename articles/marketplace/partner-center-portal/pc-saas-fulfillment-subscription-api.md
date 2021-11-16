---
title: API de suscripción de suministro de SaaS v2 en el Marketplace comercial de Microsoft
description: Aprenda a usar las API de suscripción, que forman parte de la versión 2 de las API de suministro de SaaS para administrar una oferta de SaaS en Microsoft AppSource, Azure Marketplace y Azure Portal.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ace7aac5a308621ca8032677e7ca7eec3858bdb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063917"
---
# <a name="saas-fulfillment-subscription-apis-v2-in-microsoft-commercial-marketplace"></a>API de suscripción de suministro de SaaS v2 en el Marketplace comercial de Microsoft

En este artículo se describe la versión 2 de las API de suscripción de suministro de SaaS.

## <a name="resolve-a-purchased-subscription"></a>Resolución de una suscripción comprada

El punto de conexión de resolución permite al editor cambiar el token de identificación de la compra del marketplace comercial (conocido como *token* en [Comprada pero todavía no activada](pc-saas-fulfillment-life-cycle.md#purchased-but-not-yet-activated-pendingfulfillmentstart)) a un identificador de suscripción de SaaS comprada persistente y sus detalles.

Cuando se redirige a un cliente a la dirección URL de la página de aterrizaje del partner, se pasa el token de identificación del cliente como parámetro *token* en esta llamada a la URL. Se espera que el partner use este token y haga una solicitud para resolverlo. La respuesta de la API Resolve contiene el identificador de la suscripción de SaaS y otros detalles para identificar la compra de forma única. El *token* proporcionado con la llamada a la dirección URL de la página de aterrizaje suele ser válido durante 24 horas. Si el *token* que recibe ya ha expirado, le recomendamos que proporcione las instrucciones siguientes al usuario final:

"No pudimos identificar esta compra. Vuelva a abrir esta suscripción de SaaS en Azure Portal o en el Centro de administración de Microsoft 365 y seleccione 'Configurar cuenta' o 'Administrar cuenta' de nuevo".

Al llamar a la API Resolve, se devolverán los detalles y el estado de la suscripción para las suscripciones de SaaS en todos los estados admitidos.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.   |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID. Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente. Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor. Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `authorization`     |  Token de acceso único que identifica al editor que realiza esta llamada API. El formato es `"Bearer <accessaccess_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Parámetro *token* de identificación de la compra que se va a resolver.  El token se pasa en la llamada a la dirección URL de la página de aterrizaje cuando se redirige al cliente al sitio web del partner de SaaS (por ejemplo, `https://contoso.com/signup?token=<token><authorization_token>`). <br> <br>  Tenga en cuenta que el valor de *token* que se está codificando forma parte de la dirección URL de la página de aterrizaje y, por tanto, debe descodificarse antes de usarse como un parámetro en esta llamada API.  <br> <br> Un ejemplo de una cadena codificada en la dirección URL tiene el siguiente aspecto: `contoso.com/signup?token=ab%2Bcd%2Fef`, donde el *token* es `ab%2Bcd%2Fef`.  El mismo token descodificado será `Ab+cd/ef`. |
| | |

*Códigos de respuesta*:

Código: 200 Devuelve identificadores de suscripción de SaaS únicos basados en el `x-ms-marketplace-token` proporcionado.

Ejemplo de cuerpo de respuesta:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Código: 400 Solicitud incorrecta. Falta el `x-ms-marketplace-token`, o bien tiene un formato incorrecto, no es válido o ha expirado.

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado.  La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="activate-a-subscription"></a>Activar una suscripción

Después de configurar la cuenta de SaaS para un cliente final, el editor debe llamar a la API Activate Subscription en el lado de Microsoft.  No se facturará al cliente a menos que esta llamada API sea correcta.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.   |
| `subscriptionId` | Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la [API Resolve](#resolve-a-purchased-subscription).
 |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
| `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente.  Esta cadena pone en correlación todos los eventos de la operación en el cliente con los eventos en el servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
| `authorization`      |  Token de acceso único que identifica al editor que realiza esta llamada API. El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Ejemplo de solicitud de carga:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Códigos de respuesta*:

Código: 200 La suscripción se marcó como Suscrita en el lado de Microsoft.

No hay ningún cuerpo de respuesta para esta llamada.

Código: 400 Solicitud incorrecta: error de validación.

* El valor de `planId` no existe en la carga de la solicitud.
* El valor de `planId` de la carga de la solicitud no coincide con el de la compra.
* El valor de `quantity` de la carga de la solicitud no coincide con el de la compra.
* La suscripción de SaaS se encuentra en el estado *Suscrito* o *Suspendido*.

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado. La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 404 No encontrado. La suscripción de SaaS se encuentra en el estado *Suscripción cancelada*.

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-list-of-all-subscriptions"></a>Obtención de una lista de todas las suscripciones

Esta API recupera una lista de todas las suscripciones de SaaS compradas para todas las ofertas que el editor ha publicado en el marketplace comercial.  Se devolverán las suscripciones de SaaS en todos los estados posibles. También se devuelven las suscripciones de SaaS canceladas, ya que esta información no se elimina en el lado de Microsoft.

Esta API devuelve resultados paginados (100 por página).

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.  |
| `continuationToken`  | Parámetro opcional. Para recuperar la primera página de resultados, déjelo vacío.  Use el valor devuelto en el parámetro `@nextLink` para recuperar la página siguiente. |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID. Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
| `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
| `authorization`      |  Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de respuesta*:

Código: 200 Devuelve la lista de todas las suscripciones existentes para todas las ofertas realizadas por este editor, en función del token de autorización del editor.

*Ejemplo de cuerpo de respuesta:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Si no se encuentra ninguna suscripción de SaaS comprada para este editor, el cuerpo de respuesta se devuelve vacío.

Código: 403 Prohibido. El token de autorización no está disponible, no es válido o ha expirado.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md). 

Código: 500 Error interno del servidor. Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-subscription"></a>Obtener una suscripción

Esta API recupera una suscripción de SaaS comprada especificada para una oferta de SaaS que el editor ha publicado en el marketplace comercial. Use esta llamada para obtener toda la información disponible para una suscripción de SaaS específica por su identificador, en lugar de llamar a la API que se usa para obtener una lista de todas las suscripciones.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Use 2018-08-31. |
| `subscriptionId`     |  Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve. |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID. Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `x-ms-correlationid` |  Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `authorization`     | Token de acceso único que identifica al editor que realiza esta llamada API. El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de respuesta*:

Código: 200 Devuelve los detalles de una suscripción de SaaS en función del valor de `subscriptionId` proporcionado.

*Ejemplo de cuerpo de respuesta:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado. La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md). 

Código: 404 No encontrado.  No se encuentra la suscripción de SaaS con el valor de `subscriptionId` especificado.

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="list-available-plans"></a>Lista de planes disponibles

Esta API recupera todos los planes de una oferta de SaaS identificados por el valor `subscriptionId` de una compra específica de esta oferta.  Use esta llamada para obtener una lista de todos los planes públicos y privados que el beneficiario de una suscripción de SaaS puede actualizar para la suscripción.  Los planes devueltos estarán disponibles en la misma geografía que el plan comprado.

Esta llamada devuelve una lista de los planes disponibles para ese cliente, además de los que se han comprado.  La lista se puede presentar a un usuario final en el sitio del editor.  Un usuario final puede cambiar el plan de suscripción a uno de los planes de la lista devuelta.  Se producirá un error si se cambia el plan a uno que no aparezca en la lista.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve. |

*Encabezados de la solicitud*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `x-ms-correlationid`  |  Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta. |
|  `authorization`     |  Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de respuesta*:

Código: 200 Devuelve una lista de todos los planes disponibles para una suscripción de SaaS existente, incluido el que se ha comprado.

Ejemplo de cuerpo de respuesta:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Si no se encuentra `subscriptionId`, el cuerpo de respuesta se devuelve vacío.

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado.  La solicitud podría intentar acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md). 

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="change-the-plan-on-the-subscription"></a>Cambiar el plan de la suscripción

Use esta API para actualizar el plan existente que se ha comprado para una suscripción de SaaS a un nuevo plan (público o privado).  El editor debe llamar a esta API cuando se cambia un plan en el lado del editor para una suscripción de SaaS comprada en el marketplace comercial.

Solo se puede llamar a esta API para suscripciones *activas*.  Un plan se puede cambiar a cualquier otro plan existente (público o privado), pero no a sí mismo.  En el caso de los planes privados, el inquilino del cliente debe definirse como parte del público del plan en el Centro de partners.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
| `subscriptionId`     | Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve. |

*Encabezados de la solicitud*:
 
|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID. Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `x-ms-correlationid`  | Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `authorization`     |  Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Ejemplo de solicitud de carga:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Códigos de respuesta*:

Código: 202 Se ha aceptado la solicitud para cambiar el plan y se ha administrado de manera asincrónica.  Se espera que el partner sondee la **dirección URL de Operation-Location** para determinar si la solicitud de cambio de plan se ha realizado correctamente o no.  El sondeo debe realizarse cada pocos segundos hasta que se reciba el estado final de *Error*, *Correcto* o *Conflicto* para la operación.  El estado de la operación final debe devolverse rápidamente, pero en algunos casos puede tardar varios minutos.

El partner también recibirá una notificación de webhook cuando la acción esté lista para completarse correctamente en el lado del marketplace comercial.  Solo entonces debe el editor cambiar el plan en su lado.

*Encabezados de respuesta:*

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Dirección URL para obtener el estado de la operación.  Por ejemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 Solicitud incorrecta: errores de validación.

* El nuevo plan no existe o no está disponible para esta suscripción de SaaS específica.
* El nuevo plan es el mismo que el actual.
* El estado de la suscripción de SaaS es *Suscripción cancelada*.
* La operación de actualización de una suscripción de SaaS no se incluye en `allowedCustomerOperations`.

Código: 403 Prohibido. El token de autorización no es válido, ha expirado o no se ha proporcionado.  La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 404 No encontrado.  No se encuentra la suscripción de SaaS con el valor de `subscriptionId`.

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

>[!NOTE]
>Se puede cambiar el plan o la cantidad de puestos de cada vez, pero no ambos.

>[!Note]
>Solo se puede llamar a esta API después de obtener la aprobación explícita del usuario final para el cambio.

## <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Cambio de la cantidad de puestos de la suscripción de SaaS

Use esta API para actualizar (es decir, aumentar o disminuir) la cantidad de puestos comprados para una suscripción de SaaS.  El editor debe llamar a esta API cuando se cambia el número de puestos en el lado del editor para una suscripción de SaaS creada en el marketplace comercial.

La cantidad de puestos no puede ser superior a la permitida en el plan actual.  En este caso, el editor debe cambiar el plan antes de cambiar la cantidad de puestos.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve.  |

*Encabezados de la solicitud*:
 
|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `x-ms-correlationid`  | Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `authorization`     | Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Ejemplo de solicitud de carga:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Códigos de respuesta*:

Código: 202 Se ha aceptado la solicitud para cambiar la cantidad y se ha administrado de manera asincrónica. Se espera que el partner sondee la **dirección URL de Operation-Location** para determinar si la solicitud de cambio de cantidad se ha realizado correctamente o no.  El sondeo debe realizarse cada pocos segundos hasta que se reciba el estado final de *Error*, *Correcto* o *Conflicto* para la operación.  El estado de la operación final debe devolverse rápidamente, pero en algunos casos puede tardar varios minutos.

El partner también recibirá una notificación de webhook cuando la acción esté lista para completarse correctamente en el lado del marketplace comercial.  Solo entonces debe el editor cambiar la cantidad en su lado.

*Encabezados de respuesta:*

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Vínculo a un recurso para obtener el estado de la operación.  Por ejemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Código: 400 Solicitud incorrecta: errores de validación.

* La nueva cantidad es superior o inferior al límite del plan actual.
* Falta la nueva cantidad.
* La nueva cantidad es la misma que la actual.
* El estado de la suscripción de SaaS es Suscripción cancelada.
* La operación de actualización de una suscripción de SaaS no se incluye en `allowedCustomerOperations`.

Código: 403 Prohibido.  El token de autorización no es válido, ha expirado o no se ha proporcionado.  La solicitud está intentando acceder a una suscripción que no pertenece al editor actual.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md). 

Código: 404 No encontrado.  No se encuentra la suscripción de SaaS con el valor de `subscriptionId`.

Código: 500 Error interno del servidor.  Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

>[!Note]
>Solo se puede cambiar un plan o cantidad a la vez, pero no ambos.

>[!Note]
>Solo se puede llamar a esta API después de obtener la aprobación explícita del usuario final para el cambio.

## <a name="cancel-a-subscription"></a>Cancelación de una suscripción

Use esta API para cancelar una suscripción de SaaS especificada.  No es necesario que el editor use esta API. Se recomienda redirigir a los clientes al marketplace comercial para cancelar las suscripciones de SaaS.

Si el editor decide implementar la cancelación de una suscripción de SaaS comprada en el marketplace comercial en el lado del editor, debe llamar a esta API.  Después de la finalización de esta llamada, el estado de la suscripción se convertirá en *Suscripción cancelada* en el lado de Microsoft.

No se facturará al cliente si se cancela una suscripción dentro de los períodos de gracia siguientes:

* Veinticuatro horas en el caso de una suscripción mensual tras la activación.
* Catorce días en el caso de una suscripción anual tras la activación.

Se facturará al cliente si la suscripción se cancela tras los períodos de gracia anteriores.  El cliente perderá inmediatamente el acceso a la suscripción de SaaS en el lado de Microsoft tras la cancelación. 

### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parámetros de consulta*:

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | Identificador único de la suscripción de SaaS comprada.  Este identificador se obtiene después de resolver el token de autorización del marketplace comercial mediante la API Resolve.  |

*Encabezados de la solicitud*:
 
|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valor de cadena único para el seguimiento de la solicitud del cliente, preferiblemente un GUID.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `x-ms-correlationid`  | Valor de cadena único para la operación en el cliente.  Este parámetro pone en correlación todos los eventos de la operación del cliente con los eventos del servidor.  Si este valor no se proporciona, se generará uno y se proporcionará en los encabezados de respuesta.  |
|  `authorization`     |  Token de acceso único que identifica al editor que realiza esta llamada API.  El formato es `"Bearer <access_token>"` cuando el editor recupera el valor del token, tal como se explica en [Obtención de un token basado en la aplicación de Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de respuesta*:

Código: 202 Se ha aceptado la solicitud para cancelar la suscripción y se ha administrado de manera asincrónica.  Se espera que el partner sondee la **dirección URL de Operation-Location** para determinar si la solicitud se ha realizado correctamente o no.  El sondeo debe realizarse cada pocos segundos hasta que se reciba el estado final de *Error*, *Correcto* o *Conflicto* para la operación.  El estado de la operación final debe devolverse rápidamente, pero en algunos casos puede tardar varios minutos.

El partner también recibirá una notificación de webhook cuando la acción se haya completado correctamente en el lado del marketplace comercial.  Solo entonces debe el editor cancelar la suscripción en su lado.

*Encabezados de respuesta:*

|  Parámetro         | Value             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Vínculo a un recurso para obtener el estado de la operación.  Por ejemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 Solicitud incorrecta.  La operación de eliminación no está en la lista `allowedCustomerOperations` para esta suscripción de SaaS.

Código: 403 Prohibido.  El token de autorización no es válido, ha expirado o no está disponible. La solicitud intenta acceder a una suscripción de SaaS para una oferta que se publicó con un identificador de aplicación de Azure AD diferente del que se usó para crear el token de autorización.

Este error suele indicar que no se ha realizado correctamente el [registro de SaaS](pc-saas-registration.md).

Código: 404 No encontrado.  No se encuentra la suscripción de SaaS con el valor de `subscriptionId`.

Código: 500 Error interno del servidor. Vuelva a intentar la llamada API.  Si el error persiste, póngase en contacto con el [servicio de soporte técnico de Microsoft](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="next-steps"></a>Pasos siguientes

Consulte las [API del servicio de medición del marketplace comercial](../marketplace-metering-service-apis.md) si quiere descubrir más opciones para las ofertas de SaaS en el marketplace comercial.

Revise y use los [clientes para obtener ejemplos y lenguajes de programación diferentes](https://github.com/microsoft/commercial-marketplace-samples).

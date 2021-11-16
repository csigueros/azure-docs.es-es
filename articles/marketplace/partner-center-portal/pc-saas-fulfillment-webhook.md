---
title: Implementación de webhooks en el servicio SaaS | Marketplace comercial de Microsoft
description: Aprenda a implementar webhooks en el servicio SaaS mediante la versión 2 de las API de suministro.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 6cf50327719b434cda7d5df4e3df99b9d96a1dcd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063916"
---
# <a name="implementing-a-webhook-on-the-saas-service"></a>Implementación de un webhook en el servicio de SaaS

Al crear una oferta de SaaS comercializable en el Centro de partners, el partner proporciona la dirección URL del campo **Webhook de conexión** que se va a usar como punto de conexión HTTP.  Microsoft llama a este webhook mediante la llamada HTTP POST para notificar al lado del editor los siguientes eventos que se producen en el lado de Microsoft:

* Cuando la suscripción de SaaS se encuentra en el estado *Suscrito*:
    * ChangePlan
    * ChangeQuantity
    * Renovación
    * Suspender
    * Cancelar suscripción
* Cuando la suscripción de SaaS se encuentra en el estado *Suspendido*:
    * Reinstate
    * Cancelar suscripción

El editor debe implementar un webhook en el servicio de SaaS para mantener la coherencia del estado de la suscripción de SaaS con el del lado de Microsoft.  El servicio de SaaS debe llamar a la API Get Operation para validar y autorizar la llamada de webhook y los datos de carga antes de realizar una acción basada en la notificación de webhook.  El editor debe devolver HTTP 200 a Microsoft en cuanto se procese la llamada de webhook.  Este valor confirma que el editor ha recibido correctamente la llamada de webhook.

> [!IMPORTANT]
> El servicio de dirección URL de webhook debe estar en funcionamiento de forma ininterrumpida y poder recibir nuevas llamadas de Microsoft en todo momento.  Microsoft tiene una directiva de reintentos para la llamada de webhook (500 reintentos durante 8 horas), pero si el editor no acepta la llamada y devuelve una respuesta, la operación de la que informa el webhook producirá un error en el lado de Microsoft.

*Ejemplo de carga de webhook de un evento de compra:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": "25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

*Ejemplo de carga de webhook de un evento de restablecimiento de suscripción:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": "20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "InProgress"
}
```

*Ejemplo de carga de webhook de un evento de renovación:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer1 ",
  "planId": "silver",
  "quantity": "25",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Renew",
  "status": "Success"
}
```

## <a name="development-and-testing"></a>Desarrollo y pruebas

Para iniciar el proceso de desarrollo, se recomienda crear respuestas de API ficticias en el lado del editor.  Pueden basarse en las respuestas de ejemplo que se proporcionan en este artículo.

Cuando el editor esté listo para las pruebas de un extremo a otro:

* Publique una oferta de SaaS para un público preliminar limitado y manténgalo en la fase de versión preliminar.
* Establezca el precio del plan en 0 para evitar que se desencadenen los gastos de facturación reales durante las pruebas.  Otra opción consiste en establecer un precio distinto de cero y cancelar todas las compras de prueba durante un plazo de 24 horas.
* Asegúrese de que todos los flujos se invocan de un extremo a otro para simular un escenario de cliente real.
* Si el partner quiere probar el flujo completo de compra y facturación, hágalo con una oferta cuyo precio sea superior a 0 USD.  La compra se facturará y se generará una factura.

Se puede desencadenar un flujo de compra desde Azure Portal o sitios de Microsoft AppSource, en función de dónde se publique la oferta.

Las acciones de *cambiar plan*, *cambiar cantidad* y *cancelar suscripción* se prueban desde el lado del editor.  En el lado de Microsoft, se pueden desencadenar la acción de *cancelar suscripción* desde Azure Portal y el Centro de administración (el portal donde se administran las compras de Microsoft AppSource).  Las acciones de *cambiar cantidad y plan* solo se pueden desencadenar desde el Centro de administración.

## <a name="get-support"></a>Obtención de soporte técnico

Consulte [Soporte técnico para el programa Marketplace comercial en el Centro de partners](../support.md) para ver las opciones de soporte técnico para editores.

## <a name="next-steps"></a>Pasos siguientes

Consulte las [API del servicio de medición del marketplace comercial](../marketplace-metering-service-apis.md) si quiere descubrir más opciones para las ofertas de SaaS en el marketplace comercial.

Revise y use los [clientes para obtener ejemplos y lenguajes de programación diferentes](https://github.com/microsoft/commercial-marketplace-samples).

---
title: 'Azure Event Grid: solución de problemas de validación de suscripciones'
description: En este artículo se muestra cómo se pueden solucionar los problemas de las validaciones de suscripciones.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 8db9d36b31c96187f985f98c4c2a1c79f4e58218
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230501"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Solución de problemas de validación de suscripciones de Azure Event Grid
Durante la creación de suscripciones a eventos, si ve un mensaje de error como `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`, indica que hay un error en el protocolo de enlace de la validación. Para resolver este error, compruebe los siguientes aspectos:

- Realice una solicitud HTTP POST a la dirección URL de webhook con un cuerpo de la solicitud [sample SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) mediante Postman, Curl o una herramienta similar.
- Si el webhook está implementando un mecanismo de enlace de validación sincrónica, compruebe que ValidationCode se devuelve como parte de la respuesta.
- Si el webhook está implementando un mecanismo del protocolo de enlace de validación asincrónica, compruebe que la solicitud HTTP POST devuelve 200 OK.
- Si el webhook devuelve `403 (Forbidden)` en la respuesta, compruebe si el webhook está detrás de una puerta de enlace de aplicaciones de Azure o del firewall de aplicaciones web. Si es así, tendrá que deshabilitar estas reglas de firewall y volver a realizar una solicitud HTTP POST:
    - 920300 (falta un encabezado de aceptación en la solicitud)
    - 942430 (restringe la detección de anomalías de caracteres de SQL [args]: número de caracteres especiales que se han excedido [12])
    - 920230 (varias codificaciones de direcciones URL detectadas)
    - 942130 (ataque por inyección de código SQL: tautología de SQL detectada)
    - 931130 (posible ataque remoto de inclusión de archivos [RFI] = Referencia o vínculo fuera del dominio)

> [!IMPORTANT]
> Para más información sobre la validación de los puntos de conexión para webhooks, consulte [Entrega de eventos de webhook](webhook-event-delivery.md).

En las secciones siguientes se muestra cómo validar las suscripciones a un evento mediante Postman y Curl.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Validación de suscripciones a eventos de Event Grid mediante Postman
Este es un ejemplo del uso de Postman para validar una suscripción de webhook de un evento de Event Grid: 

![Validación de suscripciones a eventos de Event Grid mediante Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Este es un ejemplo del código JSON de **SubscriptionValidationEvent**:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Este es el ejemplo de respuesta correcta:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Para obtener más información sobre la validación de eventos de Event Grid para webhooks, consulte [Validación de puntos de conexión con eventos de Event Grid](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Validación de suscripciones a eventos de Event Grid mediante Curl 
Este es el comando de Curl de ejemplo para validar una suscripción de webhook de un evento de Event Grid: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Validación de suscripciones a eventos de nube mediante Postman
Este es un ejemplo de uso de Postman para validar una suscripción de webhook de un evento de nube: 

![Validación de suscripciones a eventos de nube mediante Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Use el método **HTTP OPTIONS** para la validación con eventos de nube. Para más información sobre la validación de eventos de nube para webhooks, consulte [Validación de puntos de conexión con eventos de nube](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="troubleshoot-event-subscription-validation"></a>Solución de problemas de validación de suscripciones a eventos

## <a name="next-steps"></a>Pasos siguientes
Si necesita más ayuda, publique su problema en el [foro de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/). 

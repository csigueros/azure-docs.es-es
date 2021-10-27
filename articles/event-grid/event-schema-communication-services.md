---
title: 'Azure Communication Services como origen de Event Grid: información general'
description: En este artículo se describe cómo utilizar Azure Communication Services como origen de eventos de Event Grid.
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: mikben
ms.openlocfilehash: 590e0a53ca701a6b293dd52b2f7c30c5e873df30
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129498"
---
# <a name="event-handling-in-azure-communication-services"></a>Control de eventos en Azure Communication Services

Azure Communication Services se integra con [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para ofrecer notificaciones de eventos en tiempo real de manera confiable, escalable y segura. Este artículo tiene como objetivo ayudarle a configurar las aplicaciones para que escuchen eventos de Communication Services. Por ejemplo, puede que quiera actualizar una base de datos, crear un elemento de trabajo y entregar una notificación de inserción al recibir un SMS de un número de teléfono asociado al recurso de Communication Services.

Azure Event Grid es un servicio de enrutamiento de eventos totalmente administrado que usa un modelo de publicación-suscripción. Event Grid tiene compatibilidad integrada con servicios de Azure, como [Azure Functions](../azure-functions/functions-overview.md) y [Azure Logic Apps](../azure-functions/functions-overview.md). Puede proporcionar alertas de eventos para servicios que no sean de Azure mediante webhooks. Para obtener una lista completa de los controladores de eventos que Event Grid admite, vea [una introducción a Azure Event Grid](overview.md).

:::image type="content" source="./media/overview/functional-model.png" alt-text="Diagrama que muestra el modelo de evento de Azure Event Grid.":::

> [!NOTE]
> Para más información sobre cómo se relaciona la residencia de datos con el control de eventos, visite la [documentación conceptual sobre residencia de datos](../communication-services/concepts/privacy.md).

## <a name="events-types"></a>Tipos de eventos

Event Grid usa las [suscripciones a eventos](concepts.md#event-subscriptions) para enrutar los mensajes de eventos a los suscriptores.

Azure Communication Services emite los siguientes tipos de evento:

* [Eventos de chat](./communication-services-chat-events.md)
* [Eventos de telefonía y SMS](./communication-services-telephony-sms-events.md)
* [Eventos de llamadas de voz y videollamadas](./communication-services-voice-video-events.md)
* [Eventos presenciales](./communication-services-presence-events.md)

Puede usar Azure Portal o la CLI de Azure para suscribirse a eventos emitidos por el recurso de Communication Services. 

## <a name="event-subjects"></a>Asuntos de eventos

El campo `subject` de todos los eventos de Communication Services identifica el usuario, el número de teléfono o la entidad de destino del evento. Los prefijos comunes se utilizan para permitir el [filtrado de Event Grid](event-filtering.md) simple.

| Prefijo de asunto                              | Entidad de Communication Services |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | Número de teléfono RTC            |
| `user/`                                     | Usuario de Communication Services  |
| `thread/`                                   | Subproceso de chat                 |

En el ejemplo siguiente se muestra un filtro para todos los mensajes SMS y los informes de entrega enviados a todos los números de teléfono de código de área 555 propiedad de un recurso de Communication Services:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](./overview.md).
* Para obtener una introducción a Azure Event Grid, consulte [Conceptos de Event Grid](./concepts.md).
* Para obtener una introducción a Azure Event Grid, consulte [Temas del sistema en Azure Event Grid](./system-topics.md).

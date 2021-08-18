---
title: Agregar datos personalizados a eventos en Azure Event Hubs
description: Este artículo muestra cómo añadir datos personalizados a los eventos en Azure Event Hubs.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 2e3f94228c6f2fbe5d533635a01062c701aa9664
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297539"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Agregar datos personalizados a eventos en Azure Event Hubs
Dado que un evento se compone principalmente de un conjunto opaco de bytes, puede ser difícil para los consumidores de esos eventos tomar decisiones informadas sobre cómo procesarlos. Para permitir que los publicadores de eventos ofrezcan mejor contexto a los consumidores, los eventos también pueden contener metadatos personalizados, en forma de un conjunto de pares clave-valor. Un escenario común para la inclusión de metadatos es proporcionar una sugerencia sobre el tipo de datos que contiene un evento, para que los consumidores sepan su formato y puedan deserializarlo adecuadamente.

> [!NOTE]
> Estos metadatos no se usan en el servicio Event Hubs, o de forma que sea significativo. solo existe para la coordinación entre los publicadores de eventos y los consumidores. 

En las secciones siguientes se muestra cómo agregar datos personalizados a eventos en diferentes lenguajes de programación. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Para obtener el ejemplo de código completo, consulte [publicación de eventos con metadatos personalizados](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Para obtener el ejemplo de código completo, vea [publicar eventos con metadatos personalizados](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Para obtener el ejemplo de código completo, consulte [Enviar un lote de datos de eventos con propiedades](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Pasos siguientes
Consulte las guías de inicio rápido y los ejemplos siguientes. 

- Inicios rápidos: [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md) y [JavaScript](event-hubs-node-get-started-send.md)
- Ejemplos en GitHub: [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/javascript) y [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/typescript)

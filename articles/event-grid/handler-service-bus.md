---
title: Colas y temas de Service Bus como controladores de eventos para eventos de Azure Event Grid
description: Aquí se describe cómo puede usar las colas y temas de Service Bus como controladores de eventos para eventos de Azure Event Grid.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 676a9fcc6e260f2226e103ede26398b384e046ca
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354380"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Colas y temas de Service Bus como controladores de eventos para eventos de Azure Event Grid
Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar eventos y **Azure Service Bus** es uno de ellos. 

Puede usar una cola o un tema de Service como un controlador para los eventos de Event Grid. 

## <a name="service-bus-queues"></a>Colas de Service Bus

> [!NOTE]
> Las colas habilitadas para sesión no se admiten como controladores de eventos en eventos de Azure Event Grid
 
Puede redirigir los eventos de Event Grid directamente a las colas de Service Bus para usarlos en escenarios de almacenamiento en búfer o comando y control en aplicaciones empresariales.

En Azure Portal, al crear una suscripción de eventos, seleccione **Cola de Service Bus** como tipo de punto de conexión y, a continuación, haga clic en **Seleccionar un punto de conexión** para elegir una cola de Service Bus.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Uso de la CLI para agregar un controlador de colas de Service Bus

Para la CLI de Azure, en el siguiente ejemplo, se suscribe un tema y conecta un tema de Event Grid a una cola de Service Bus:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Temas de Service Bus

Puede enrutar los eventos de Event Grid directamente a los temas de Service Bus para controlar los eventos del sistema de Azure con temas de Service Bus o para escenarios de mensajería de comando y control.

En Azure Portal, al crear una suscripción de eventos, seleccione **Tema de Service Bus** como tipo de punto de conexión y, a continuación, haga clic en **Seleccionar un punto de conexión** para elegir un tema de Service Bus.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Uso de la CLI para agregar un controlador de temas de Service Bus

Para la CLI de Azure, en el siguiente ejemplo, se suscribe un tema y conecta un tema de Event Grid a un tema de Service Bus:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

Cuando se envía un evento a una cola o tema de Service Bus como un mensaje asincrónico, el valor de `messageid` del mensaje asincrónico es un id. de sistema interno.

El id. de sistema interno del mensaje se mantendrá cuando se vuelva a entregar el evento, para que pueda evitar entregas duplicadas; para ello, active la **detección de duplicados** en la entidad de Service Bus. Le recomendamos que habilite la duración de la detección de duplicados en la entidad Service Bus para que sea el período de vida (TTL) del evento o la duración máxima de reintentos, lo que sea más largo.

## <a name="rest-examples-for-put"></a>Ejemplos de REST (para PUT)

### <a name="service-bus-queue"></a>Cola de Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Cola de Service Bus: entrega con identidad administrada

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Tema de Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Tema de Service Bus: entrega con identidad administrada

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="delivery-properties"></a>Propiedades de entrega
Las suscripciones a eventos permiten configurar encabezados HTTP que se incluyen en los eventos entregados. Esta capacidad permite establecer encabezados personalizados que un destino requiere. Puede establecer encabezados personalizados en los eventos que se entregan a colas y temas de Azure Service Bus.

Azure Service Bus permite el uso de las propiedades de mensaje siguientes al enviar mensajes individuales. 

| Nombre de encabezado | Tipo de encabezado |
| :-- | :-- |
| `MessageId` | Dinámica |  
| `PartitionKey` | Estático o dinámico |
| `SessionId` | Estático o dinámico |
| `CorrelationId` | Estático o dinámico |
| `Label` | Estático o dinámico |
| `ReplyTo` | Estático o dinámico | 
| `ReplyToSessionId` | Estático o dinámico |
| `To` |Estático o dinámico |
| `ViaPartitionKey` | Estático o dinámico |

> [!NOTE]
> - El valor predeterminado de `MessageId` es el identificador interno del evento de Event Grid. Puede invalidarlo. Por ejemplo, `data.field`.
> - Solo puede establecer `SessionId` o `MessageId`. 

Para obtener más información, vea [Propiedades de entrega personalizadas](delivery-properties.md). 

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo [Controladores de eventos](event-handlers.md) para obtener una lista de controladores de eventos compatibles. 

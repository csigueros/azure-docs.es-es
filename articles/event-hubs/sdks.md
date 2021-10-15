---
title: 'Azure Event Hubs: SDK cliente | Microsoft Docs'
description: En este artículo se proporciona información sobre los SDK cliente de Azure Event Hubs.
ms.topic: article
ms.date: 09/21/2021
ms.openlocfilehash: b1a802e5d9829f533187f477bb28037c22c02f3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215897"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs: SDK cliente
En este artículo se proporciona la siguiente información sobre los SDK compatibles con Azure Event Hubs: 

- Ubicación de paquete que se puede usar en las aplicaciones 
- Ubicación de GitHub donde se puede encontrar código fuente, ejemplos, archivo Léame, registro de cambios, problemas detectados y además notificar nuevos problemas 
- Vínculos a tutoriales de inicio rápido 

## <a name="client-sdks"></a>SDK de cliente
En la siguiente tabla se describen todos los clientes del entorno de ejecución de Azure Event Hubs más recientes disponibles. El principal objetivo de estas bibliotecas es **enviar y recibir mensajes** en un centro de eventos.

| Idioma | Paquete | Referencia | 
| -------- | ------- | --------------- | 
| . NET Standard | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Tutorial](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Tutorial](event-hubs-c-getstarted-send.md)</li></ul> |

En la tabla siguiente se enumeran los clientes del entorno de ejecución de Azure Event Hubs anteriores. Aunque estos paquetes pueden recibir correcciones de errores críticos, no están en desarrollo activo. En su lugar, se recomienda usar los SDK más recientes enumerados en la tabla anterior.

| Idioma | Paquete | Referencia | 
| -------- | ------- | --------------- | 
| . NET Standard  | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) (**heredado**) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) (**heredado**) | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) (**heredado**) | |
|   Java   | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(heredado)** | <ul><li>[Ubicación de GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |

## <a name="management-sdks"></a>SDK de administración
Esta es una lista de bibliotecas específicas de administración disponibles actualmente. Ninguna de estas bibliotecas contiene operaciones de runtime; además, su único propósito es **administrar entidades de Event Hubs**.

- [.NET Standard](/dotnet/api/microsoft.azure.management.eventhub)
- [Java](/java/api/com.microsoft.azure.management.eventhub)
- [Python](/python/api/azure-mgmt-eventhub)
- [JavaScript](/javascript/api/@azure/arm-eventhub/)


## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](./event-hubs-about.md)
* [Creación de un Centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.yml)

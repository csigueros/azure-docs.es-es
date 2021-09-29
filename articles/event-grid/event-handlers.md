---
title: Controladores de eventos de Azure Event Grid
description: En este artículo se describen los controladores de eventos compatibles con Azure Event Grid. Azure Automation, Functions, Event Hubs, Conexiones híbridas, Logic Apps, Service Bus, Queue Storage, Webhooks.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: e741a3500c9b6a0ecda9a6dcc6e2d3514df45edc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659198"
---
# <a name="event-handlers-in-azure-event-grid"></a>Controladores de eventos de Azure Event Grid
Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar los eventos. También puede usar cualquier webhook para controlar los eventos. El webhook no necesita estar hospedado en Azure para controlar los eventos. Event Grid solo admite puntos de conexión de webhook HTTPS.

## <a name="supported-event-handlers"></a>Controladores de evento admitidos
Estos son los controladores de eventos admitidos: 

- [Webhooks](handler-webhooks.md). Los runbooks y aplicaciones lógicas de Azure Automation se admiten a través de webhooks. 
- [Azure Functions](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [Colas y temas de Service Bus](handler-service-bus.md)
- [Retransmisión de conexiones híbridas](handler-relay-hybrid-connections.md)
- [Colas de almacenamiento](handler-storage-queues.md)

## <a name="next-steps"></a>Pasos siguientes
- Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).

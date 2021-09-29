---
title: Webhooks como controladores de eventos para eventos de Azure Event Grid
description: Aquí se describe cómo puede usar los webhooks como controladores de eventos para eventos de Event Grid. Los runbooks y aplicaciones lógicas de Azure Automation se admiten como controladores de eventos a través de webhooks.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 4f790c6a4ced30d0a0e34cb594581aae09ed1004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550481"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooks, runbooks de Automation, aplicaciones lógicas como controladores de eventos para eventos de Azure Event Grid
Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar los eventos. También puede usar cualquier webhook para controlar los eventos. El webhook no necesita estar hospedado en Azure para controlar los eventos. Event Grid solo admite puntos de conexión de webhook HTTPS.

> [!NOTE]
> - Los runbooks y aplicaciones lógicas de Azure Automation se admiten como controladores de eventos a través de webhooks. 
> - Aunque puede usar un método **webhook** como un **tipo de punto de conexión** para configurar una función de Azure como un controlador de eventos, use una **función de Azure** como un tipo de punto de conexión. Para obtener más información, consulte [Función de Azure como un controlador de eventos](handler-functions.md).

## <a name="webhooks"></a>webhooks
Consulte los artículos siguientes para obtener información general y ejemplos de uso de webhooks como controladores de eventos. 

|Título  |Descripción  |
|---------|---------|
| Guía de inicio rápido: Creación y enrutamiento de eventos personalizados con la [CLI de Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) y [Azure Portal](custom-event-quickstart-portal.md). | Explica cómo enviar eventos personalizados a un webhook. |
| Guía de inicio rápido: Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado con la [CLI de Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) y [Azure Portal](blob-event-quickstart-portal.md). | Explica cómo enviar eventos de Blob Storage a un webhook. |
| [Guía de inicio rápido: Envío de eventos de Container Registry](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Explica cómo utilizar la CLI de Azure para enviar eventos de Container Registry. |
| [Introducción: Recepción de eventos en un punto de conexión de HTTP](receive-events.md) | Se describe cómo validar un punto de conexión de HTTP para recibir eventos de una suscripción a eventos y, a continuación, recibir y deserializar los eventos. |


## <a name="azure-automation"></a>Azure Automation
Puede procesar eventos mediante runbooks de Azure Automation. El procesamiento de eventos mediante runbooks automatizados se admite a través de webhooks. Cree un webhook para el runbook y luego use el controlador de webhook. Consulte el siguiente tutorial para obtener un ejemplo: 

|Título  |Descripción  |
|---------|---------|
|[Tutorial: Integración de Azure Automation con Event Grid y Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Cree una máquina virtual, que envía un evento. El evento desencadena un runbook de Automation que etiqueta la máquina virtual y genera un mensaje que se envía a un canal de Equipos de Microsoft. |


## <a name="logic-apps"></a>Logic Apps
Use **Logic Apps** para implementar procesos empresariales y así poder procesar eventos de Event Grid. En este ejemplo no se crea un webhook explícitamente. El webhook se crea automáticamente cuando se configura la aplicación lógica para que controle los eventos de Event Grid. Consulte los siguientes tutoriales para obtener ejemplos: 

|Título  |Descripción  |
|---------|---------|
| [Tutorial: Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Tutorial: Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps) | Una aplicación lógica supervisa los cambios realizados en una máquina virtual y envía mensajes de correo electrónico sobre dichos cambios. |
| [Tutorial: envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Logic Apps.](publish-iot-hub-events-to-logic-apps.md) | Una aplicación lógica envía un correo electrónico de notificación cada vez que se agrega un dispositivo al centro de IoT. |
| [Tutorial: respuesta a eventos de Azure Service Bus recibidos a través de Azure Event Grid con Azure Functions y Azure Logic Apps.](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envía mensajes de temas de Service Bus a la aplicación de función y a la aplicación lógica. |

## <a name="rest-example-for-put"></a>Ejemplo de REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo [Controladores de eventos](event-handlers.md) para obtener una lista de controladores de eventos compatibles. 

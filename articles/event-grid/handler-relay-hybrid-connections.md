---
title: Conexión híbrida de Relay como controlador de eventos para eventos de Azure Event Grid
description: Aquí se describe cómo puede usar las conexiones híbridas de Azure Relay como controladores de eventos para eventos de Event Grid.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: fb36e391663ee53729175a2768d765521b03f4da
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214947"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Conexión híbrida de Relay como controlador de eventos para eventos de Azure Event Grid
Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar los eventos y **Azure Relay** es uno de ellos. 

Use las **conexiones híbridas de Azure Relay** para enviar eventos a las aplicaciones que están dentro de una red empresarial y que no tienen un punto de conexión de acceso público.

## <a name="tutorials"></a>Tutoriales
Consulte el siguiente tutorial para obtener un ejemplo del uso de una conexión híbrida de Azure Relay como un controlador de eventos. 

|Título  |Descripción  |
|---------|---------|
| [Tutorial: Envío de eventos a una conexión híbrida](custom-event-to-hybrid-connection.md) | Envía un evento personalizado a una conexión híbrida existente para su procesamiento mediante una aplicación de escucha. |

## <a name="rest-example-for-put"></a>Ejemplo de REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo [Controladores de eventos](event-handlers.md) para obtener una lista de controladores de eventos compatibles. 
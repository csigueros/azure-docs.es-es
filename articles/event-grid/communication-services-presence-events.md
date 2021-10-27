---
title: 'Azure Communication Services: eventos de presencia'
description: En este artículo se explica cómo usar Azure Communication Services como origen del evento de Event Grid de eventos de presencia de usuario.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 75e23d5de55ab205cc35bf6e7496413f4dd4be9a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134285"
---
# <a name="azure-communication-services---presence-events"></a>Azure Communication Services: eventos de presencia

En este artículo se proporcionan las propiedades y el esquema de los eventos de presencia de Communication Services.  Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

## <a name="events-types"></a>Tipos de eventos

Azure Communication Services emite los siguientes tipos de evento de presencia de usuario:

| Tipo de evento                                                  | Descripción                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.UserDisconnected | Se publica después de que un usuario de Communication Services se haya desconectado de Communication Services. |

## <a name="event-responses"></a>Respuestas de eventos

Cuando se desencadena un evento, el servicio Event Grid envía datos sobre ese evento a los puntos de conexión correspondientes.

Esta sección contiene un ejemplo del aspecto que deben tener los datos para cada evento.

> [!IMPORTANT]
> El evento Microsoft.Communication.UserDisconnected está en versión preliminar pública. Durante esta versión preliminar, los registros asociados al estado "desconectado" del usuario se pueden replicar globalmente. Puede obtener el estado "desconectado" si se suscribe a este evento a través de Event Grid.

> [!NOTE]
> El evento Microsoft.Communication.UserDisconnected solo es aplicable en el contexto de un chat.
 
### <a name="microsoftcommunicationuserdisconnected"></a>Microsoft.Communication.UserDisconnected

```json
[
 {
  "id": "8f60490d-0719-4d9d-a1a6-835362fb752e",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "user/{rawId}",
  "data": {
    "userCommunicationIdentifier": {
      "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40",
      "communicationUser": {
        "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40"
      }
    }
  },
  "eventType": "Microsoft.Communication.UserDisconnected",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-08-10T20:25:38Z"
 }
]
```

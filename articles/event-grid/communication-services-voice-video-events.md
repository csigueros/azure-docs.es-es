---
title: 'Azure Communication Services: eventos de llamada de voz y vídeo'
description: En este artículo, se describe cómo usar Azure Communication Services como un origen de eventos de Event Grid para eventos de llamada de voz y vídeo.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 9259a5ce7151197f1155bfb91f23f3c98afca9b4
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134281"
---
# <a name="azure-communication-services---voice-and-video-calling-events"></a>Azure Communication Services: eventos de llamada de voz y vídeo

En este artículo, se proporcionan las propiedades y el esquema de los eventos de llamadas de voz y vídeo de los servicios de comunicación.  Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

## <a name="events-types"></a>Tipos de eventos

Azure Communication Services emite los siguientes tipos de eventos de llamada de voz y vídeo:

| Tipo de evento                                                  | Descripción                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.RecordingFileStatusUpdated | Se publica cuando el archivo de grabación está disponible. |

## <a name="event-responses"></a>Respuestas de eventos

Cuando se desencadena un evento, el servicio Event Grid envía datos sobre ese evento a los puntos de conexión correspondientes.

Esta sección contiene un ejemplo del aspecto que deben tener los datos para cada evento.

> [!IMPORTANT]
> La característica Grabación de llamadas todavía está en versión preliminar pública.

### <a name="microsoftcommunicationrecordingfilestatusupdated"></a>Microsoft.Communication.RecordingFileStatusUpdated

```json
[
 {
  "id": "7283825e-f8f1-4c61-a9ea-752c56890500",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/recording/call/{call-id}/recordingId/{recording-id}",
  "data": {
    "recordingStorageInfo": {
      "recordingChunks": [
        {
          "documentId": "0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8",
          "index": 0,
          "endReason": "SessionEnded",
          "contentLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/video",
          "metadataLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/acsmetadata"
        }
      ]
    },
    "recordingStartTime": "2021-07-27T15:20:23.6089755Z",
    "recordingDurationMs": 6620,
    "sessionEndReason": "CallEnded"
  },
  "eventType": "Microsoft.Communication.RecordingFileStatusUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-07-27T15:20:34.2199328Z"
 }
]
```

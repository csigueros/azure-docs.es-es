---
title: 'Azure Communication Services: eventos de telefonía y SMS'
description: En este artículo, se describe cómo usar Azure Communication Services como un origen de eventos de Event Grid para eventos de telefonía y SMS.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 2456df394a25df3b64f464a144224d9f9b365408
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134278"
---
# <a name="azure-communication-services---telephony-and-sms-events"></a>Azure Communication Services: eventos de telefonía y SMS

En este artículo, se proporcionan las propiedades y el esquema de los eventos de telefonía y SMS de Communication Services.  Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

## <a name="events-types"></a>Tipos de eventos

Azure Communication Services emite los siguientes tipos de eventos de telefonía y SMS:

| Tipo de evento                                                  | Descripción                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Se publica cuando se recibe un SMS de un número de teléfono asociado al servicio de Communication Services. |
| Microsoft.Communication.SMSDeliveryReportReceived           | Se publica cuando se recibe un informe de entrega de un SMS enviado por el servicio de Communication Services.     |

## <a name="event-responses"></a>Respuestas de eventos

Cuando se desencadena un evento, el servicio Event Grid envía datos sobre ese evento a los puntos de conexión correspondientes.

Esta sección contiene un ejemplo del aspecto que deben tener los datos para cada evento.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Evento Microsoft.Communication.SMSDeliveryReportReceived

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Evento Microsoft.Communication.SMSReceived

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```

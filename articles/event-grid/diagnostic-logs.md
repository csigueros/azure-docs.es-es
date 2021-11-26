---
title: 'Azure Event Grid: Registros de diagnóstico para temas y dominios de Azure Event Grid'
description: En este artículo se proporciona información conceptual sobre los registros de diagnóstico de un tema o un dominio de Azure Event Grid.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: df3fe9eaab544e3e52ff3a2da24fe7b624292367
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546830"
---
# <a name="diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Registros de diagnóstico para temas y dominios de Azure Event Grid

La configuración de diagnóstico permite a los usuarios de Event Grid capturar y ver los registros de **errores de publicación y entrega** en una cuenta de almacenamiento, un centro de eventos o un área de trabajo de Log Analytics. En este artículo se proporciona un esquema para los registros y una entrada de registro de ejemplo.

## <a name="schema-for-publishdelivery-failure-logs"></a>Esquema para registros de errores de publicación y entrega

| Nombre de propiedad | Tipo de datos | Descripción |
| ------------- | --------- | ----------- |
| Time | DateTime | Hora a la que se generó la entrada de registro <p>**Valor de ejemplo:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | El nombre de la suscripción de eventos <p>**Valor de ejemplo:** "EVENTSUB1"</p> <p>Esta propiedad solo existe para los registros de errores de entrega.</p>  |
| Category | String | Nombre de la categoría del registro. <p>**Valores de ejemplo:** "DeliveryFailures" o "PublishFailures" | 
| OperationName | String | El nombre de la operación que provocó el error.<p>**Valores de ejemplo:** "Deliver" para errores de entrega. |
| Message | String | Mensaje de registro para el usuario que explica el motivo del error y otros detalles adicionales. |
| ResourceId | String | El identificador de recurso para el recurso de tema/dominio<p>**Valores de ejemplo:** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example---schema-for-publishdelivery-failure-logs"></a>Ejemplo: Esquema para registros de errores de publicación y entrega

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

Los valores posibles de `Outcome` son `NotFound`, `Aborted`, `TimedOut`, `GenericError` y `Busy`. Event Grid registra cualquier información que reciba del controlador de eventos en `message`. Por ejemplo, para `GenericError`, se registra el código de estado HTTP, el código de error y el mensaje de error.

## <a name="schema-for-data-plane-requests"></a>Esquema para solicitudes de plano de datos

| Nombre de propiedad | Tipo de datos | Descripción |
| ------------- | --------- | ----------- |
| NetworkAccess | String | **PublicAccess**: cuando la conexión es a través de una dirección IP pública <br /> **PrivateAccess**: cuando la conexión es a través de un vínculo privado |
| ClientIpAddress | String | Dirección IP de origen de las solicitudes entrantes |
| TlsVersion | String | La versión de Tls utilizada por la conexión de cliente. **1.0**, **1.1** y **1.2** son los posibles valores |
| Authentication/Type | String | Tipo de secreto usado para la autenticación al publicar mensajes. <br /> **Desconocido**: ninguno de los otros tipos de autenticación. Las solicitudes de OPTIONS tendrán este tipo de autenticación <br /> **Clave**: la solicitud usa la clave de SAS <br /> **SASToken**: la solicitud usa un token de SAS generado a partir de la clave de SAS <br /> **AADAccessToken**: token de JWT emitido por AAD |
| Authentication/ObjectId | String | ObjectId de la entidad de servicio que usó el tipo de autenticación de AADAccessToken |
| OperationResult | String | Resultado de la publicación. **Correcta**, **No autorizada**, **Prohibida**, **RequestEntityTooLarge**, **BadRequest** & **InternalServerError** |
| TotalOperations | String | Estos seguimientos no se emiten para cada solicitud de publicación. Cada minuto se emite un agregado para cada combinación única de los valores anteriores. |

## <a name="example---schema-for-data-plane-requests"></a>Ejemplo: esquema para solicitudes de plano de datos

```json
{
    "time": "2021-10-26T21:44:16.8117322Z",
    "resourceId": "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/RESOURCEGROUPS/BMT-TEST/PROVIDERS/MICROSOFT.EVENTGRID/DOMAINS/BMTAUDITLOGDOMAIN",
    "operationName": "Microsoft.EventGrid/events/send",
    "category": "DataPlaneRequests",
    "level": "Information",
    "region": "CENTRALUSEUAP",
    "properties": {
        "aggregatedRequests": [
            {
                "networkAccess": "PublicAccess",
                "clientIpAddress": "xx.xx.xx.xxx",
                "tlsVersion": "1.2",
                "authentication": {
                            "type": "AADAccessToken",
                            "objectId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
                },
                "operationResult": "Success",
                "totalOperations": 1
            }
        ]
    }
}
```

Una vez seleccionada la configuración de diagnóstico `DataPlaneRequests`, los recursos de Event Grid comenzarán a publicar los seguimientos de auditoría de las operaciones del plano de datos, incluido el acceso público y privado. Este seguimiento puede registrar una o varias solicitudes si es necesario.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a habilitar registros de diagnóstico de temas o dominios, consulte [Habilitación de registros de diagnóstico](enable-diagnostic-logs-topic.md).

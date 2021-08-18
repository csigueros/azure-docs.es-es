---
title: Reaccionar a eventos de Azure Maps mediante Event Grid
description: Descubra cómo reaccionar a eventos de Azure Maps relacionados con geovallas. Vea cómo escuchar eventos de mapa y cómo usar Event Grid para redirigir eventos a controladores de eventos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: ff5b14705f05debf97fb0fe26f88993b001a52c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727981"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reaccionar a eventos de Azure Maps mediante Event Grid

Azure Maps se integra con Azure Event Grid, de forma que los usuarios pueden enviar notificaciones de eventos a otros servicios y desencadenar procesos de bajada. Este artículo tiene como objetivo ayudarle a configurar las aplicaciones empresariales para que escuchen eventos de Azure Maps. Esto permite a los usuarios reaccionar ante eventos críticos de forma confiable, escalable y segura. Por ejemplo, los usuarios pueden compilar una aplicación para actualizar una base de datos, crear un vale y enviar una notificación por correo electrónico cada vez que un dispositivo entra en una geovalla.

Azure Event Grid es un servicio de enrutamiento de eventos totalmente administrado que usa un modelo de publicación-suscripción. Event Grid tiene compatibilidad integrada con servicios de Azure, como [Azure Functions](../azure-functions/functions-overview.md) y [Azure Logic Apps](../azure-functions/functions-overview.md). Puede proporcionar alertas de eventos para servicios que no sean de Azure mediante webhooks. Para obtener una lista completa de los controladores de eventos que Event Grid admite, vea [una introducción a Azure Event Grid](../event-grid/overview.md).


![Modelo funcional de Azure Event Grid](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos de Azure Maps

Event Grid usa las [suscripciones a eventos](../event-grid/concepts.md#event-subscriptions) para enrutar los mensajes de eventos a los suscriptores. Una cuenta de Azure Maps emite los siguientes tipos de eventos: 

| Tipo de evento | Descripción |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Se genera cuando se reciben coordenadas desde fuera de una determinada geovalla hacia dentro |
| Microsoft.Maps.GeofenceExited | Se genera cuando las coordenadas recibidas se mueven desde dentro de una determinada geovalla hacia fuera |
| Microsoft.Maps.GeofenceResult | Se genera cada vez que una consulta de geovalla devuelve un resultado, independientemente del estado |

## <a name="event-schema"></a>Esquema del evento

En el ejemplo siguiente, se muestra el esquema de GeofenceResult:

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>Sugerencias para consumir eventos

Las aplicaciones que controlan los eventos de geovallas de Azure Maps deben seguir algunos procedimientos recomendados:

* Configure varias suscripciones para enrutar eventos al mismo controlador de eventos. Es importante no asumir que los eventos proceden de un origen determinado. Compruebe siempre el tema del mensaje para asegurarse de que procede del mensaje que está esperando.
* Use el campo `X-Correlation-id` del encabezado de la respuesta para saber si la información sobre los objetos está actualizada. Los mensajes pueden llegar desordenados o después de un retraso.
* Cuando se llama a una solicitud GET o POST en Geofence API con el parámetro de modo establecido en `EnterAndExit`, se genera un evento de entrada o de salida para cada geometría de la geovalla en la que se ha cambiado el estado desde la anterior llamada a Geofence API.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo usar las geovallas para controlar operaciones en un sitio de construcción, consulte:

> [!div class="nextstepaction"] 
> [Configuración de una geovalla con Azure Maps](tutorial-geofence.md)
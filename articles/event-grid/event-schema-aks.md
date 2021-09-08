---
title: Azure Kubernetes Service como origen de Event Grid (versión preliminar)
description: En este artículo se describe cómo utilizar Azure Kubernetes Service como origen de eventos de Event Grid. Proporciona el esquema y los vínculos a los artículos de procedimientos y tutorial.
author: zr-msft
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: 16b82ea59e69457475966fecfb8a0ca8d7dd20ce
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734117"
---
# <a name="azure-kubernetes-service-aks-as-an-event-grid-source-preview"></a>Azure Kubernetes Service (AKS) como origen de Event Grid (versión preliminar)

En este artículo se proporcionan las propiedades y los esquemas de los eventos de AKS.  Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md). También proporciona una lista de inicios rápidos y tutoriales para usar AKS como un origen de eventos.

[!INCLUDE [preview features callout](../aks/includes/preview/preview-callout.md)]

## <a name="available-event-types"></a>Tipos de eventos disponibles

AKS emite los siguientes tipos de eventos:

|    Tipo de evento                                             |    Descripción                                                       |
|-----------------------------------------------------------|----------------------------------------------------------------------|
| Microsoft.ContainerService.NewKubernetesVersionAvailable  | Se desencadena cuando se actualiza la lista de versiones disponibles de Kubernetes. |

## <a name="properties-common-to-all-events"></a>Propiedades comunes para todos los eventos

# <a name="event-grid-event-schema"></a>[Esquema de eventos de Event Grid](#tab/event-grid-event-schema)
Cuando se desencadena un evento, el servicio Event Grid envía datos sobre ese evento al punto de conexión correspondiente.
Esta sección contiene un ejemplo del aspecto que deben tener los datos para cada evento. Cada evento tiene los siguientes datos de nivel superior:

|     Propiedad          |     Tipo     |     Descripción                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    string    |    Ruta de acceso completa al origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor.                                      |
|    `subject`            |    string    |    Ruta de acceso al asunto del evento definido por el anunciante.                                                                                              |
|    `eventType`          |    string    |    Uno de los tipos de eventos registrados para este origen de eventos.                                                                                  |
|    `eventTime`          |    string    |    La hora de generación del evento en función de la hora UTC del proveedor.                                                                         |
|    `id`                 |    string    |    Identificador único para el evento                                                                                                            |
|    `data`               |    object    |    Datos de eventos de Blob Storage.                                                                                                                    |
|    `dataVersion`        |    string    |    Versión del esquema del objeto de datos. El publicador define la versión del esquema.                                                          |
|    `metadataVersion`    |    string    |    Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor.    |

# <a name="cloud-event-schema"></a>[Esquema de eventos en la nube](#tab/cloud-event-schema)

Cuando se desencadena un evento, el servicio Event Grid envía datos sobre ese evento al punto de conexión correspondiente.
Esta sección contiene un ejemplo del aspecto que deben tener los datos para cada evento. Cada evento tiene los siguientes datos de nivel superior:

|     Propiedad          |     Tipo     |     Descripción                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    string    |    Ruta de acceso completa al origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor.                                      |
|    `subject`            |    string    |    Ruta de acceso al asunto del evento definido por el anunciante.                                                                                              |
|    `type`          |    string    |    Uno de los tipos de eventos registrados para este origen de eventos.                                                                                  |
|    `time`          |    string    |    La hora de generación del evento en función de la hora UTC del proveedor.                                                                         |
|    `id`                 |    string    |    Identificador único para el evento                                                                                                            |
|    `data`               |    object    |    Datos de eventos de Blob Storage.                                                                                                                    |
| `specversion` | string | Versión de especificación del esquema CloudEvents. |

---

## <a name="example-events"></a>Eventos de ejemplo

### <a name="newkubernetesversionavailable"></a>NewKubernetesVersionAvailable

# <a name="event-grid-event-schema"></a>[Esquema de eventos de Event Grid](#tab/event-grid-event-schema)

```json
{
    "topic": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "eventType": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "dataVersion": "1",
    "metadataVersion": "1",
    "eventTime": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```
# <a name="cloud-event-schema"></a>[Esquema de eventos en la nube](#tab/cloud-event-schema)

```json

{
    "source": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "type": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "specversion": "1.0",
    "time": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```

---

El objeto de datos contiene las siguientes propiedades:

|    Propiedad                        | Tipo   | Descripción                                                  |
|------------------------------------|--------|--------------------------------------------------------------|
| `latestSupportedKubernetesVersion` | string | La versión compatible más reciente de Kubernetes disponible.        |
| `latestStableKubernetesVersion`    | string | La versión compatible más reciente y estable de Kubernetes disponible. |
| `lowestMinorKubernetesVersion`     | string | La versión compatible más baja de Kubernetes disponible.        |
| `latestPreviewKubernetesVersion`   | string | La versión preliminar más baja de Kubernetes disponible.          |

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para obtener más información sobre la creación de una suscripción de Azure Event Grid, vea [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
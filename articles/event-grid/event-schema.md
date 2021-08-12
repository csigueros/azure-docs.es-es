---
title: Esquema de eventos de Azure Event Grid
description: En este artículo se describen las propiedades y el esquema que están presentes para todos los eventos. Los eventos constan de un conjunto de cuatro propiedades de cadena.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 6354401902a841322e58dc7989f10279efee6c2c
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854925"
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos de Azure Event Grid

En este artículo se describen las propiedades y el esquema que están presentes para todos los eventos. Los eventos constan de un conjunto de cuatro propiedades de cadena. Las propiedades son comunes a todos los eventos de cualquier anunciante. El objeto de datos tiene propiedades específicas de cada publicador. Para los temas de sistema, estas propiedades son específicas del proveedor de recursos, como Azure Storage o Azure Event Hubs.

Los orígenes de eventos envían eventos a Azure Event Grid en una matriz que puede tener varios objetos de evento. Al publicar eventos en un tema de Event Grid, la matriz puede tener un tamaño total de hasta 1 MB. Cada evento de la matriz tiene 1 MB, como máximo. Si un evento o la matriz superan los límites de tamaño, recibirá la respuesta **413 Payload Too Large** (Carga útil demasiado grande). No obstante, las operaciones se cobran en incrementos de 64 KB. Por lo tanto, los eventos de más de 64 KB incurrirán en cargos de operaciones como si fueran varios eventos. Por ejemplo, un evento que tenga 130 KB incurrirá en operaciones como si fueran tres eventos independientes.

Event Grid envía los eventos a los suscriptores en una matriz que tiene un solo evento. Este comportamiento puede cambiar en el futuro.

Puede encontrar el esquema JSON para el evento de Event Grid y cada carga de datos del publicador de Azure en el [almacén de esquemas de eventos](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Esquema del evento

En el ejemplo siguiente se muestran las propiedades que todos los publicadores de eventos usan:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Por ejemplo, el esquema publicado para un evento de Azure Blob Storage es:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Propiedades de evento

Todos los eventos tienen los mismos datos de nivel superior, que son los siguientes:

| Propiedad | Tipo | Requerido | Descripción |
| -------- | ---- | -------- | ----------- |
| topic | string | No, pero si se incluye, debe coincidir exactamente con el identificador de Azure Resource Manager del tema Event Grid. Si no se incluye, Event Grid se marcará en el evento. | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | string | Sí | Ruta al asunto del evento definida por el anunciante. |
| eventType | string | Sí | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | string | Sí | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | string | Sí | Identificador único para el evento |
| datos | object | No | Los datos del evento específicos del proveedor de recursos. |
| dataVersion | string | No, pero se marcará con un valor vacío. | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | No es necesario, pero si se incluye, debe coincidir exactamente con el esquema `metadataVersion` de Event Grid (actualmente, solo `1`). Si no se incluye, Event Grid se marcará en el evento. | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

Para aprender acerca de las propiedades del objeto de datos, vea el origen del evento:

* [Azure Policy](./event-schema-policy.md)
* [Suscripciones de Azure (operaciones de administración)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupos de recursos (operaciones de administración)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Para temas personalizados, el publicador de eventos determina el objeto de datos. Los datos de nivel superior deben tener los mismos campos que los eventos estándar definidos por recursos.

Al publicar eventos en temas personalizados, cree asuntos para los eventos que faciliten que los suscriptores sepan si les interesa el evento. Los suscriptores usan el asunto para filtrar y redirigir eventos. Considere la posibilidad de proporcionar la ruta de acceso de donde se produjo el evento, para que los suscriptores pueden filtrar por los segmentos de esa ruta de acceso. La ruta de acceso permite que los suscriptores filtren eventos de una manera más amplia o más restringida. Por ejemplo, si se proporciona una ruta de acceso de tres segmentos como `/A/B/C` en el asunto, los suscriptores pueden filtrar por el primer segmento `/A` para obtener un amplio conjunto de eventos. Esos suscriptores obtienen eventos con asuntos como `/A/B/C` o `/A/D/E`. Otros suscriptores pueden filtrar por `/A/B` para obtener un conjunto de eventos más reducido.

A veces el asunto necesita información más detallada sobre qué ocurrió. Por ejemplo, el publicador de **cuentas de almacenamiento** proporciona el asunto `/blobServices/default/containers/<container-name>/blobs/<file>` cuando se agrega un archivo a un contenedor. Un suscriptor puede filtrar por la ruta de acceso `/blobServices/default/containers/testcontainer` para obtener todos los eventos de ese contenedor, pero no otros contenedores en la cuenta de almacenamiento. Un suscriptor también puede filtrar o redirigir por el sufijo `.txt` para que funcione solo con archivos de texto.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).

---
title: Esquema CloudEvents v1.0 con Azure Event Grid
description: Se describe cómo utilizar el esquema CloudEvents v1.0 para eventos de Azure Event Grid. El servicio admite eventos en la implementación JSON de eventos en la nube.
ms.topic: conceptual
ms.date: 07/22/2021
ms.openlocfilehash: 668ff0c1d09441459b28112173735828f347c50a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442689"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>Esquema CloudEvents v1.0 con Azure Event Grid

Además de su [esquema de eventos predeterminado](event-schema.md), Azure Event Grid admite de forma nativa eventos de la [implementación de JSON de CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) y el [enlace del protocolo HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) es una [especificación abierta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para la descripción de datos de eventos.

CloudEvents simplifica la interoperabilidad al proporcionar un esquema de eventos común para la publicación y el consumo de eventos basados en la nube. Este esquema permite herramientas uniformes, formas estándar de enrutar y administrar eventos y formas universales de deserializar el esquema de eventos externo. Con un esquema común, puede integrar más fácilmente el trabajo entre plataformas.

En la compilación de CloudEvents participan varios [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), entre ellos Microsoft, mediante [Cloud Native Compute Foundation](https://www.cncf.io/). Actualmente está disponible en versión 1.0.

En este artículo se describe el esquema CloudEvents con Event Grid.

## <a name="sample-event-using-cloudevents-schema"></a>Evento de ejemplo con el esquema CloudEvents

Este es un ejemplo de un evento de Azure Blob Storage en formato de CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Una descripción detallada de los campos disponibles, sus tipos y definiciones en CloudEvents v1.0 se encuentra [disponible aquí](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Los valores de encabezados de los eventos proporcionados en el esquema de CloudEvents y el esquema de Event Grid son los mismos excepto para `content-type`. En el esquema de CloudEvents, ese valor de encabezado es `"content-type":"application/cloudevents+json; charset=utf-8"`. En el esquema de Event Grid, ese valor de encabezado es `"content-type":"application/json; charset=utf-8"`.

## <a name="event-grid-for-cloudevents"></a>Event Grid para CloudEvents

Puede usar Event Grid con eventos de entrada y salida de esquema CloudEvents. CloudEvents se puede usar con eventos del sistema, como eventos de Blob Storage y eventos de IoT Hub, y con eventos personalizados. También pueden transformar esos eventos en la red de un formato a otro.


| Esquema de entrada       | Esquema de salida
|--------------------|---------------------
| Formato de CloudEvents | Formato de CloudEvents
| Formato de Event Grid  | Formato de CloudEvents
| Formato de Event Grid  | Formato de Event Grid

Con todos los esquemas de eventos, Event Grid requiere validación al publicar en un tema de Event Grid y al crear una suscripción de eventos. Para más información, vea [Event Grid security and authentication](security-authentication.md) (Seguridad y autenticación de Event Grid).

## <a name="next-steps"></a>Pasos siguientes
Consulte [Uso del esquema CloudEvents v1.0 con Event Grid](cloudevents-schema.md).  

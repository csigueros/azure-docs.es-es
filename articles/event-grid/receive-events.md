---
title: Recepción de eventos de Azure Event Grid en un punto de conexión de HTTP
description: Describe cómo validar un punto de conexión HTTP y luego recibir y deserializar eventos procedentes de Azure Event Grid.
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f655982203c81ef6ff32a01566e37c346cf78138
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730285"
---
# <a name="receive-events-to-an-http-endpoint"></a>Recepción de eventos en un punto de conexión de HTTP

En este artículo se describe cómo [validar un punto de conexión de HTTP](webhook-event-delivery.md) para recibir eventos de una suscripción a eventos y, a continuación, recibir y deserializar los eventos. En este artículo se utiliza una función de Azure para fines de demostración; sin embargo, se aplican los mismos conceptos con independencia de dónde se hospede la aplicación.

> [!NOTE]
> Se recomienda que use un [desencadenador de Event Grid](../azure-functions/functions-bindings-event-grid.md) al desencadenar una instancia de Azure Functions con Event Grid. Proporciona una integración más sencilla y rápida entre Event Grid y Azure Functions. Sin embargo, tenga en cuenta que el desencadenador de Event Grid para Azure Functions no admite el escenario en el que el código hospedado necesita controlar el código de estado HTTP devuelto a Event Grid. Dada esta limitación, el código que se ejecuta en una instancia de Azure Functions no podría devolver un error 5XX para iniciar un reintento de entrega de eventos con Event Grid, por ejemplo.

## <a name="prerequisites"></a>Prerrequisitos

Necesita una aplicación de función con una función desencadenada por HTTP.

## <a name="add-dependencies"></a>Adición de dependencias

Si está desarrollando en .NET, [agregue una dependencia](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) a la función para el `Azure.Messaging.EventGrid` [paquete de NuGet](https://www.nuget.org/packages/Azure.Messaging.EventGrid).

Hay SDK para otros lenguajes disponibles a través de la referencia [SDK de publicación](./sdk-overview.md#data-plane-sdks). Estos paquetes contienen los modelos para los tipos de evento nativos, como `EventGridEvent`, `StorageBlobCreatedEventData` y `EventHubCaptureFileCreatedEventData`.

## <a name="endpoint-validation"></a>Validación de punto de conexión

Lo primero que querrá hacer es controlar eventos `Microsoft.EventGrid.SubscriptionValidationEvent`. Cada vez que alguien se suscribe a un evento, Event Grid envía un evento de validación al punto de conexión con `validationCode` en la carga de datos. El punto de conexión debe reproducirlo en el cuerpo de la respuesta [para demostrar que el punto de conexión es válido y le pertenece](webhook-event-delivery.md). Si usa un [desencadenador de Event Grid](../azure-functions/functions-bindings-event-grid.md) en lugar de una función desencadenada por WebHook, la validación del punto de conexión se completa de manera automática. Si usa un servicio de API de terceros (como [Zapier](https://zapier.com/home) o [IFTTT](https://ifttt.com/)), puede que no sea capaz de reflejar el código de validación mediante programación. Con esos servicios, puede validar manualmente la suscripción mediante una dirección URL de validación que se envía en el evento de validación de la suscripción. Copie esa dirección URL en la propiedad `validationUrl` y envíe una solicitud GET mediante un cliente de REST o el explorador web.

En C#, el método `ParseMany()` se usa para deserializar una instancia de `BinaryData` que contiene uno o más eventos en una matriz de `EventGridEvent`. Si sabía con antelación que solo va a deserializar un evento, en su lugar podría usar el método `Parse`.

Para reflejar el código de validación mediante programación, use el código siguiente.

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Prueba de respuesta de validación

Pruebe la función de la respuesta de validación pegando el evento de ejemplo en el campo de prueba para la función:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Al hacer clic en Ejecutar, el resultado debería ser 200 OK y `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` en el cuerpo:

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Solicitud de validación":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Resultados de la validación":::

## <a name="handle-blob-storage-events"></a>Control de eventos de Blob Storage

Ahora, se ampliará la función para controlar el evento del sistema `Microsoft.Storage.BlobCreated`:

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Prueba del control de evento creado por blob

Pruebe la nueva funcionalidad de la función; para ello, coloque un [evento de Blob Storage](./event-schema-blob-storage.md#example-event) en el campo de prueba y ejecute:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Debería ver el resultado de la dirección URL del blob en el registro de la función:

![Registro de salida](./media/receive-events/blob-event-response.png)

También puede probar esta salida mediante la creación de una cuenta de Blob Storage o una cuenta de almacenamiento de uso general V2 (GPv2), [la adición de una suscripción de eventos](../storage/blobs/storage-blob-event-quickstart.md) y la configuración del punto de conexión a la dirección URL de la función:

![Dirección URL de la función](./media/receive-events/function-url.png)

## <a name="handle-custom-events&quot;></a>Control de eventos personalizados

Por último, ampliemos una vez más la función para que también pueda controlar eventos personalizados. 

Agregue una comprobación para el evento `Contoso.Items.ItemReceived`. El código final debe tener el aspecto siguiente:

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName(&quot;Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
                // Handle the custom contoso event
                else if (eventGridEvent.EventType == "Contoso.Items.ItemReceived")
                {
                    var contosoEventData = eventGridEvent.Data.ToObjectFromJson<ContosoItemReceivedEventData>();
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {contosoEventData.ItemSku}");
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Prueba del control de eventos personalizados

Por último, pruebe que la función ahora pueda controlar el tipo de evento personalizado:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

También puede probar esta funcionalidad en vivo mediante el [envío de un evento personalizado con CURL desde Portal](./custom-event-quickstart-portal.md) o [la publicación en un tema personalizado](./post-to-custom-topic.md) con cualquier servicio o aplicación que pueda ejecutar POST en un punto de conexión, como [Postman](https://www.getpostman.com/). Cree un tema y una suscripción a eventos personalizados con el punto de conexión establecido como dirección URL de la función.

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Pasos siguientes

* Explore los [SDK de Event Grid para administración y publicación](./sdk-overview.md).
* Obtenga información acerca de cómo [publicar en un tema personalizado](./post-to-custom-topic.md).
* Pruebe uno de los tutoriales detallados sobre Event Grid y funciones, como [el cambio de tamaño de imágenes cargadas a Blob Storage](resize-images-on-storage-blob-upload-event.md).

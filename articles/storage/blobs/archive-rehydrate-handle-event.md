---
title: Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs
titleSuffix: Azure Storage
description: Aprenda a desarrollar una función de Azure con .NET y, a continuación, configure Azure Event Grid para ejecutar la función en respuesta a un evento desencadenado cuando un blob se rehidrata desde el nivel de archivo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/11/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 06239708293be94f13c62cab7004c7a57d00eea1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275513"
---
# <a name="run-an-azure-function-in-response-to-a-blob-rehydration-event"></a>Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs

Para leer un blob que se encuentra en el nivel de archivo, primero debe rehidratarlo al nivel de acceso esporádico o frecuente. El proceso de rehidratación puede tardar varias horas en finalizar. En lugar de sondear repetidamente el estado de la operación de rehidratación, puede configurar [Azure Event Grid](../../event-grid/overview.md) para que se despeje un evento cuando se complete la operación de rehidratación de blobs y controle este evento en la aplicación.

Cuando se produce un evento, Event Grid envía el evento a un controlador de eventos a través de un punto de conexión. Varios servicios de Azure pueden actuar como controladores de eventos, incluidos [Azure Functions](../../azure-functions/functions-overview.md). Una función de Azure es un bloque de código que se puede ejecutar en respuesta a un evento. Estas instrucciones le guían por el proceso de desarrollo de una función de Azure y, a continuación, la configuración de Event Grid para ejecutar la función en respuesta a un evento que se produce cuando se rehidrata un blob.

En este artículo se muestra cómo crear y probar una función de Azure con .NET desde Visual Studio. Puede crear funciones de Azure desde una variedad de entornos de desarrollo locales y mediante lenguajes de programación diferentes. Para obtener más información sobre los lenguajes admitidos para Azure Functions, vea [Lenguajes admitidos en Azure Functions](../../azure-functions/supported-languages.md). Para obtener más información sobre las opciones de desarrollo para Azure Functions, consulte [Codifique y pruebe Azure Functions localmente](../../azure-functions/functions-develop-local.md).

Para obtener más información sobre la rehidratación de blobs desde el nivel de archivo, consulte [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).

## <a name="prerequisites"></a>Requisitos previos

En este artículo se muestra cómo usar [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para desarrollar una función de Azure con .NET Puede instalar Visual Studio Community de manera gratuita. Asegúrese de [configurar Visual Studio para desarrollo de Azure con .NET](/dotnet/azure/configure-visual-studio).

Para depurar la función de Azure localmente, deberá usar una herramienta que pueda enviar una solicitud HTTP, como Postman.

Se necesita una [suscripción de Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Si aún no tiene ninguna cuenta, [cree una gratuita](https://azure.microsoft.com/free/dotnet/) antes de empezar.

## <a name="create-an-azure-function-app"></a>Creación de una Function App de Azure

Una aplicación de funciones es un recurso de Azure que actúa como contenedor para su Azure Functions. Puede usar una aplicación de funciones nueva o existente para completar los pasos descritos en este artículo.

Para crear una aplicación de funciones en Azure Portal, siga estos pasos:

1. En Azure Portal, busque *Aplicación de funciones*. Seleccione el icono **Aplicación de funciones** para ir a la lista de aplicaciones de función de su suscripción.
1. Seleccione el botón **Crear** para crear una nueva aplicación de funciones.
1. En la pestaña **Aspectos básicos**, especifique un grupo de recursos y proporcione un nombre único para la nueva aplicación de funciones.
1. Asegúrese de que la opción **Publicar** está establecida en *Código*.
1. En la lista desplegable **Pila en tiempo de ejecución**, seleccione *.NET*. El campo **Versión** se rellena automáticamente para usar la versión más reciente de .NET Core.
1. Seleccione la región de la nueva aplicación de funciones.

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-basics-tab.png" alt-text="Captura de pantalla que muestra cómo crear una nueva aplicación de funciones en Azure: pestaña Aspectos básicos":::

1. Cuando haya completado la pestaña **Aspectos básicos**, vaya a la pestaña **Hospedaje**.
1. En la pestaña **Hospedaje**, seleccione la cuenta de almacenamiento donde se almacenará la función de Azure. Puede usar una cuenta de almacenamiento que ya exista o crear una.
1. Asegúrese de que el campo **Sistema operativo** está establecido en *Windows*.
1. En el campo **Tipo de plan**, seleccione *Consumo (sin servidor)* . Para obtener más información sobre este plan, consulte [Hospedaje de plan de consumo de Azure Functions](../../azure-functions/consumption-plan.md).

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-hosting-tab.png" alt-text="Captura de pantalla que muestra cómo crear una nueva aplicación de funciones en Azure: pestaña Hospedaje":::

1. Seleccione **Revisar y crear** para crear la aplicación de funciones.

Para obtener más información sobre cómo configurar la aplicación de funciones, consulte [Administración de la aplicación de funciones](../../azure-functions/functions-how-to-use-azure-function-app-settings.md) en la documentación de Azure Functions.

## <a name="create-an-azure-function-as-an-event-grid-trigger"></a>Creación de una función de Azure como un desencadenador de Event Grid

A continuación, cree una función de Azure que se ejecutará cuando un blob se rehidrate en una cuenta de almacenamiento determinada. Siga estos pasos para crear una función de Azure en Visual Studio con C# y .NET Core:

1. Inicie Visual Studio 2019 y cree un nuevo proyecto de Azure Functions. Para obtener más información, siga las instrucciones descritas en [Creación de un proyecto de aplicación de funciones](../../azure-functions/functions-create-your-first-function-visual-studio.md#create-a-function-app-project).
1. En el paso **Crear una aplicación de Azure Functions**, seleccione los valores siguientes:
    - De forma predeterminada, el tiempo de ejecución de Azure Functions está establecido en **Azure Functions v3 (.NET Core)** . Microsoft recomienda usar esta versión del tiempo de ejecución de Azure Functions.
    - En la lista de posibles desencadenadores, seleccione **Desencadenador Event Grid**. Para obtener más información sobre por qué un desencadenador de Event Grid es el tipo de desencadenador recomendado para controlar un evento de Blob Storage con una función de Azure, consulte [Uso de una función como controlador de eventos para eventos de Event Grid](../../event-grid/handler-functions.md).
    - La opción **cuenta de almacenamiento** indica donde se almacenará la función de Azure. Puede seleccionar una cuenta de almacenamiento existente o crear una nueva.
1. Seleccione **Crear** para crear el nuevo proyecto en Visual Studio.
1. A continuación, cambie el nombre de la clase y la función de Azure, como se describe en [Cambio de nombre de la función](../../azure-functions/functions-create-your-first-function-visual-studio.md#rename-the-function). Elija un nombre adecuado para su escenario.
1. En Visual Studio, seleccione **Herramientas** | **Administrador de paquetes NuGet** | **Consola del Administrador de paquetes**, e instale los siguientes paquetes desde la consola:

    ```powershell
    Install-Package Azure.Storage.Blobs
    Install-Package Microsoft.ApplicationInsights.WorkerService
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights
    ```

1. En el archivo de clase de la función de Azure, pegue las siguientes instrucciones using:

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    ```

1. Busque el método **Run** en el archivo de clase. Este es el método que se ejecuta cuando se produce un evento. Pegue el código siguiente en el cuerpo del método **Run**. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

    ```csharp
    // When either Microsoft.Storage.BlobCreated or Microsoft.Storage.BlobTierChanged
    // event occurs, write the event details to a log blob in the same container
    // as the event subject (the blob for which the event occurred).

    // Create a unique name for the log blob.
    string logBlobName = string.Format("function-log-{0}.txt", DateTime.UtcNow.Ticks);

    // Populate connection string with your Shared Key credentials.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net";

    // Get data from the event.
    dynamic data = eventGridEvent.Data;
    string eventBlobUrl = Convert.ToString(data.url);
    string eventApi = Convert.ToString(data.api);

    // Build string containing log information.
    StringBuilder eventInfo = new StringBuilder();
    eventInfo.AppendLine(string.Format("{0} operation occurred.", eventApi));
    eventInfo.AppendLine(string.Format("Blob URL: {0}", eventBlobUrl));
    eventInfo.AppendLine($@"Additional event details:
        Id=[{eventGridEvent.Id}]
        EventType=[{eventGridEvent.EventType}]
        EventTime=[{eventGridEvent.EventTime}]
        Subject=[{eventGridEvent.Subject}]
        Topic=[{eventGridEvent.Topic}]");

    // If event was BlobCreated and API call was CopyBlob, respond to the event.
    bool copyBlobEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated") &&
                                 (eventApi == "CopyBlob");

    // If event was BlobTierChanged and API call was SetBlobTier, respond to the event.
    bool setTierEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobTierChanged") &&
                                (eventApi == "SetBlobTier");

    // If one of these two events occurred, write event info to a log blob.
    if (copyBlobEventOccurred | setTierEventOccurred)
    {
        // Create log blob in same account and container.
        BlobUriBuilder logBlobUriBuilder = new BlobUriBuilder(new Uri(eventBlobUrl))
        {
            BlobName = logBlobName
        };

        BlobClient logBlobClient = new BlobClient(ConnectionString,
                                                  logBlobUriBuilder.BlobContainerName,
                                                  logBlobName);

        byte[] byteArray = Encoding.ASCII.GetBytes(eventInfo.ToString());

        try
        {
            // Write the log info to the blob.
            // Overwrite if the blob already exists.
            using (MemoryStream memoryStream = new MemoryStream(byteArray))
            {
                BlobContentInfo blobContentInfo =
                    logBlobClient.Upload(memoryStream, overwrite: true);
            }
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine(e.Message);
            throw;
        }
    }
    ```

Para obtener más información sobre cómo desarrollar Azure Functions, consulte [Guía para desarrollar Azure Functions](../../azure-functions/functions-reference.md).

Para obtener más información sobre aquello que se incluye cuando se publica un evento Blob Storage en un controlador de eventos, consulte [Azure Blob Storage como origen de Event Grid](../../event-grid/event-schema-blob-storage.md).

## <a name="run-the-azure-function-locally-in-the-debugger"></a>Ejecución local de la función de Azure en el depurador

Para probar el código de Azure Function localmente, debe enviar de forma manual una solicitud HTTP que desencadena el evento. Puede publicar la solicitud mediante una herramienta como Postman.

En la parte superior del archivo de clase de la función de Azure hay un punto de conexión de dirección URL que puede usar para realizar pruebas en el entorno local. La publicación de la solicitud con esta dirección URL desencadena el evento en el entorno local para que pueda depurar el código. La URL tiene el siguiente formato:

```http
http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
```

La solicitud que envía a este punto de conexión es una solicitud simulada. No envía ni recibe datos de su cuenta Azure Storage.

Siga estos pasos para construir y enviar una solicitud a este punto de conexión. En este ejemplo se muestra cómo enviar la solicitud con Postman.

1. En Postman, cree una nueva solicitud.
1. Pegue la dirección URL mostrada anteriormente en el campo de la dirección URL de solicitud, sustituyendo el nombre de la función por `{functionname}` y quitando las llaves. Asegúrese de que el verbo de solicitud está establecido en GET.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-url.png" alt-text="Captura de pantalla que muestra cómo especificar la dirección URL local para el desencadenador de eventos en Postman ":::

1. Agregue un encabezado **Content-Type** y establézcalo en *application/json*.
1. Agregue un encabezado **aeg-event-type** y establézcalo en *Notification*.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-headers.png" alt-text="Captura de pantalla que muestra la configuración del encabezado para la solicitud local para desencadenar el evento":::

1. En Postman, especifique el cuerpo de la solicitud, con el tipo de cuerpo establecido en *JSON* y el formato en *raw*. En el ejemplo siguiente se simula una solicitud **Copia de blob**. Reemplace los valores del marcador de posición entre corchetes angulares por sus propios valores. Tenga en cuenta que no es necesario cambiar los valores de fecha y hora o identificador, ya que se trata de una solicitud simulada:

    ```json
    [{
      "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
      "subject": "/blobServices/default/containers/<container-name>/blobs/<blob-name>",
      "eventType": "Microsoft.Storage.BlobCreated",
      "id": "2bfb587b-501e-0094-2746-8b2884065d32",
      "data": {
        "api": "CopyBlob",
        "clientRequestId": "3d4dedc7-6c27-4816-9405-fdbfa806b00c",
        "requestId": "2bfb587b-501e-0094-2746-8b2884000000",
        "eTag": "0x8D9595DCA505BDF",
        "contentType": "text/plain",
        "contentLength": 48,
        "blobType": "BlockBlob",
        "url": "https://<storage-account>.blob.core.windows.net/<container-name>/<blob-name>",
        "sequencer": "0000000000000000000000000000201B00000000004092a5",
        "storageDiagnostics": {
          "batchId": "8a92736a-6006-0026-0046-8bd7f5000000"
        }
      },
      "dataVersion": "",
      "metadataVersion": "1",
      "eventTime": "2021-08-07T04:42:41.0730463Z"
    }]
    ```

1. En Visual Studio, coloque los puntos de interrupción deseados en el código y pulse **F5** para ejecutar el depurador.
1. En Postman, seleccione el botón **Enviar** para enviar la solicitud al punto de conexión.

Al enviar la solicitud, Event Grid llama a la función de Azure y puede depurarla con normalidad. Para obtener más información y ejemplos, consulte [Publicación manual de la solicitud](../../azure-functions/functions-bindings-event-grid-trigger.md#manually-post-the-request) en la documentación Azure Functions.

La solicitud que desencadena el evento se simula, pero la función de Azure que se ejecuta cuando se activa el evento escribe información de registro en un nuevo blob de la cuenta de almacenamiento. Puede comprobar el contenido del blob y ver cuándo se produjo la última modificación en Azure Portal, como se muestra en la siguiente imagen:

:::image type="content" source="media/archive-rehydrate-handle-event/log-blob-contents-portal.png" alt-text="Captura de pantalla que muestra el contenido del blob de registro en Azure Portal":::

## <a name="publish-the-azure-function"></a>Publicación de la función de Azure

Después de probar la función de Azure localmente, el siguiente paso es publicar la función de Azure en la aplicación de funciones de Azure que creó anteriormente. La función debe publicarse para que pueda configurar Event Grid para enviar eventos que se suceden en la cuenta de almacenamiento al punto de conexión de la función.

Siga estos pasos para publicar la función:

1. En el Explorador de soluciones, mantenga presionado el proyecto de Azure Functions (o haga clic con el botón derecho en él) y elija **Publicar**.
1. En la ventana **Publicar**, seleccione **Azure** como destino y, a continuación, elija **Siguiente**.
1. Seleccione **Aplicación de funciones de Azure (Windows)** como el destino específico, después, elija **Siguiente**.
1. En la pestaña **Instancia de funciones**, seleccione su suscripción en el menú desplegable y, a continuación, busque la aplicación de funciones de Azure en la lista de aplicaciones de función disponibles.
1. Asegúrese de que la casilla **Ejecutar desde el archivo de paquete** está activada.
1. Seleccione **Finalizar** para prepararse para publicar la función.
1. En la página **Publicar**, comprobar que la configuración es correcta. Si ve una advertencia de que la dependencia del servicio a Application Insights no está configurada, puede configurarla desde esta página.
1. Seleccione el botón **Publicar** para empezar a publicar la función de Azure en la aplicación de funciones de Azure que creó anteriormente.

    :::image type="content" source="media/archive-rehydrate-handle-event/visual-studio-publish-azure-function.png" alt-text="Captura de pantalla que muestra la página para publicar la función de Azure desde Visual Studio":::

Siempre que realice cambios en el código de la función de Azure, debe publicar la función actualizada en Azure.

## <a name="subscribe-to-blob-rehydration-events-from-a-storage-account"></a>Suscripción a eventos de rehidratación de blobs desde una cuenta de almacenamiento

Ahora tiene una aplicación de funciones que contiene una función de Azure que se puede ejecutar en respuesta a un evento. El siguiente paso consiste en crear una suscripción a eventos desde la cuenta de almacenamiento. La suscripción a eventos configura la cuenta de almacenamiento para publicar un evento a través de Event Grid en respuesta a una operación en un blob de la cuenta de almacenamiento. Event Grid envía el evento al punto de conexión del controlador de eventos que ha especificado. En este caso, el controlador de eventos es la función de Azure que creó en la sección anterior.

Al crear la suscripción a eventos, puede filtrar qué eventos se envían al controlador de eventos. Los eventos que se van a capturar al rehidratar un blob desde el nivel de archivo son **Microsoft.Storage.BlobTierChanged**, correspondiente a una operación [Establecer nivel de blob](/rest/api/storageservices/set-blob-tier) y a eventos de **Microsoft.Storage.BlobCreated**, correspondientes a una operación [Copiar Blob](/rest/api/storageservices/copy-blob) o [Copiar Blob desde URL](/rest/api/storageservices/copy-blob-from-url). En función de su escenario, es posible que desee controlar solo uno de estos eventos.

Al crear una suscripción a eventos, siga los siguientes pasos:

1. En Azure Portal, vaya a la cuenta de almacenamiento que contiene blobs para rehidratar desde el nivel de archivo.
1. En el panel de navegación izquierdo, seleccione la opción **Eventos**.
1. En la página **Eventos**, seleccione **Más opciones**.
1. Seleccione **Creación de la suscripción a eventos**.
1. En la página **Creación de la suscripción de eventos**, en la sección **Detalles de la suscripción a eventos**, proporcione un nombre para la suscripción a eventos.
1. En la sección **Detalles del tema**, proporcione un nombre para el tema del sistema. El tema del sistema representa uno o varios eventos publicados por Azure Storage. Para obtener más información acerca de los temas del sistema, consulte [Temas del sistema en Azure Event Grid](../../event-grid/system-topics.md).
1. En la sección **Tipos de eventos**, seleccione los eventos **Blobs Creados** y **Nivel de Blob Cambiado**. Dependiendo de cómo decida rehidratar un blob desde el nivel de archivo, uno de estos dos eventos se desencadenará.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-event-types-portal.png" alt-text="Captura de pantalla que muestra cómo seleccionar tipos de eventos para eventos de rehidratación de blobs en Azure Portal":::

1. En la sección **Detalles del punto de conexión**, seleccione *Función de Azure* en el menú desplegable.
1. Elija **Seleccionar un punto de conexión** para especificar la función que creó en la sección anterior. En el diálogo **Seleccionar función de Azure**, elija la suscripción, el grupo de recursos y la aplicación de funciones para la función de Azure. Por último, seleccione el nombre de la función en la lista desplegable y elija **Confirmar selección**.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-azure-function-endpoint-portal.png" alt-text="Captura de pantalla que muestra cómo seleccionar una función de Azure como punto de conexión para una suscripción a Event Grid":::

1. Seleccione el botón **Crear** para crear la suscripción a eventos y empezar a enviar eventos al controlador de eventos de Azure Function.

Para más información sobre suscripciones a eventos, consulte [Conceptos de Azure Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="test-the-azure-function-event-handler"></a>Prueba del controlador de eventos de la función de Azure

Para probar la función de Azure, puede desencadenar un evento en la cuenta de almacenamiento que contiene la suscripción a eventos. La suscripción a eventos que creó anteriormente filtra dos eventos, **Microsoft.Storage.BlobCreated** y **Microsoft.Storage.BlobTierChanged**. Cuando se activa cualquiera de estos eventos, se desencadenará la función de Azure.

La función de Azure que se muestra en este artículo escribe en un blob de registro en dos escenarios:

- Cuando el evento es **Microsoft.Storage.BlobCreated** y la operación de API es **Copiar Blob**.
- Cuando el evento es **Microsoft.Storage.BlobTierChanged** y la operación de API es **Establecer Nivel de Blob**.

Para obtener información sobre cómo probar la función mediante la rehidración de un blob, consulte uno de estos dos procedimientos:

- [Rehidratación de un blob con una operación de copia](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)
- [Rehidratación de un blob mediante el cambio de su nivel](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)

Una vez completada la rehidratación, el blob de registro se escribe en el mismo contenedor que el blob rehidratado. Por ejemplo, después de rehidratar un blob con una operación de copia, puede ver en Azure Portal que el blob inicial permanece en el nivel de archivo, el blob de destino totalmente rehidratado aparece en el nivel en línea de destino y el blob de registro creado por la función de Azure también aparece en la lista.

:::image type="content" source="media/archive-rehydrate-handle-event/copy-blob-archive-tier-rehydrated-with-log-blob.png" alt-text="Captura de pantalla que muestra el blob original en el nivel de archivo, el blob rehidratado en el nivel de acceso frecuente y el blob de registro escrito por el controlador de eventos":::

Tenga en cuenta que la rehidratación de un blob puede tardar hasta 15 horas, dependiendo de la configuración de prioridad de rehidratación. Si establece la prioridad de rehidratación en **Alta**, la rehidratación puede completarse en menos de una hora si los blobs son de menos de 10 GB de tamaño. Sin embargo, una rehidratación de alta prioridad incurre en un costo mayor. Para más información, consulte [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).

> [!TIP]
> Aunque el objetivo de estas instrucciones es gestionar estos eventos en el contexto de la rehidratación de blobs, con fines de prueba también puede ser útil observar estos eventos en respuesta a la carga de un blob o al cambiar el nivel de un blob en línea (*es decir*, de frecuente a esporádico), ya que el evento se desencadena inmediatamente.

Para obtener más información sobre cómo filtrar eventos en Event Grid, consulte [Cómo filtrar eventos para Azure Event Grid](../../event-grid/how-to-filter-events.md).

## <a name="see-also"></a>Vea también

- [Niveles de acceso frecuente, esporádico y de archivo de los datos de blob](access-tiers-overview.md)
- [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md)
- [Rehidratación de un blob archivado en un nivel en línea](archive-rehydrate-to-online-tier.md)
- [Reacción a eventos de Blob Storage](storage-blob-event-overview.md)

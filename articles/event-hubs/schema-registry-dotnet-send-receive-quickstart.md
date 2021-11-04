---
title: 'Validación del esquema al enviar y recibir eventos: AMQP y .NET'
description: Este artículo proporciona un tutorial para crear una aplicación .NET Core que envíe eventos a Azure Event Hubs, o los reciba de él, con validación de esquemas utilizando el registro de esquema.
ms.topic: quickstart
ms.date: 10/20/2021
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: a0c14102f4db010b641685d89f2ac1bb09022ee9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030501"
---
# <a name="validate-schema-when-sending-and-receiving-events---amqp-and-net"></a>Validación del esquema al enviar y recibir eventos: AMQP y .NET 
En este inicio rápido se muestra cómo enviar eventos a un centro de eventos, y recibirlos de él, con la validación de esquemas mediante la biblioteca de .NET **Azure.Messaging.EventHubs**. 

## <a name="prerequisites"></a>Prerrequisitos
Si es la primera vez que usa Azure Event Hubs, consulte la [información general de Event Hubs](event-hubs-about.md) antes de continuar con este inicio rápido. 

Para completar este tutorial de inicio rápido, debe cumplir los siguientes requisitos previos:
- Siga las instrucciones del inicio rápido [Creación de un espacio de nombres y un centro de eventos de Event Hubs](event-hubs-create.md).
- Para obtener una cadena de conexión al espacio de nombres de Event Hubs, siga las instrucciones que se indican en [Obtención de una cadena de conexión](event-hubs-get-connection-string.md). Anote la siguiente configuración que usará en el inicio rápido actual:
    - Cadena de conexión del espacio de nombres de Event Hubs
    - Nombre del centro de eventos
- **Complete el [inicio rápido de .NET](event-hubs-dotnet-standard-getstarted-send.md)** para familiarizarse con el envío de eventos a los centros de eventos, y su recepción desde estos, mediante .NET. Si ya ha realizado el inicio rápido de .NET antes, puede omitir este paso. 
- **Siga las instrucciones que se indican en [Creación de esquemas mediante el registro de esquema](create-schema-registry.md)** para crear un grupo de esquemas y un esquema. Al crear un esquema, siga las instrucciones que se indican en [Creación de un esquema](#create-a-schema) del artículo de inicio rápido actual. 
- **Microsoft Visual Studio 2019**. La biblioteca cliente de Azure Event Hubs utiliza las nuevas características que se introdujeron en C# 8.0.  Aunque puede seguir usando la biblioteca con versiones anteriores de C#, la nueva sintaxis no estará disponible. Para usar la sintaxis completa, se recomienda realizar la compilación con el [SDK para .NET Core](https://dotnet.microsoft.com/download) 3.0 o posterior y la [versión de lenguaje](/dotnet/csharp/language-reference/configure-language-version#override-a-default) establecida en `latest`. Si usa Visual Studio, las versiones anteriores a Visual Studio 2019 no son compatibles con las herramientas necesarias para compilar proyectos de C# 8.0. Visual Studio 2019, incluida la edición gratuita Community, se puede descargar [aquí](https://visualstudio.microsoft.com/vs/).


## <a name="create-a-schema"></a>Creación de un esquema 
1. Cree un grupo de esquemas denominado **contoso-sg** mediante el portal del registro de esquema. Use Avro como tipo de serialización y **Ninguno** como modo de compatibilidad. 
1. En ese grupo de esquemas, cree un esquema de Avro con el nombre del esquema ``Microsoft.Azure.Data.SchemaRegistry.example.Order`` mediante el siguiente contenido de esquema. 

    ```json 
    {
      "namespace": "Microsoft.Azure.Data.SchemaRegistry.example",
      "type": "record",
      "name": "Order",
      "fields": [
        {
          "name": "id",
          "type": "string"
        },
        {
          "name": "amount",
          "type": "double"
        },
        {
          "name": "description",
          "type": "string"
        }
      ]
    } 
    ```

## <a name="produce-events-to-event-hubs-with-schema-validation"></a>Producción de eventos en Event Hubs con validación de esquema


### <a name="create-console-application-for-event-producer"></a>Creación de una aplicación de consola para el productor de eventos 

1. Inicie Visual Studio 2019. 
1. Seleccione **Crear un proyecto**. 
1. En el cuadro de diálogo **Crear un nuevo proyecto**, siga estos pasos: Si no ve este cuadro de diálogo, seleccione **Archivo** en el menú, seleccione **Nuevo** y, después, seleccione **Proyecto**. 
    1. Seleccione **C#** como lenguaje de programación.
    1. Seleccione **Consola** como tipo de aplicación. 
    1. Seleccione **Aplicación de consola** en la lista de resultados. 
    1. Después, seleccione **Siguiente**. 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="Imagen que muestra el cuadro de diálogo Nuevo proyecto.":::
1. Escriba **OrderProducer** como nombre del proyecto, **SRQuickStart** como nombre de la solución y, después, seleccione **Aceptar** para crear el proyecto. 


### <a name="add-the-event-hubs-nuget-package"></a>Incorporación del paquete NuGet de Event Hubs

1. Seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** en el menú. 
1. Ejecute el siguiente comando para instalar el paquete NuGet **Azure.Messaging.EventHubs**:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. Autentique las aplicaciones de productor para conectarse a Azure mediante Visual Studio, como se muestra [aquí](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio).  

### <a name="code-generation-using-the-avro-schema"></a>Generación de código mediante el esquema de Avro  
1. Puede usar el mismo contenido de esquema y crear el archivo de archivo de esquema de Avro ``Order.avsc`` dentro del proyecto OrderProducer. 
1. Luego, puede usar este archivo de esquema para generar código para .NET. Puede usar cualquier herramienta externa de generación de código como [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/) para la generación de código. (Por ejemplo, puede ejecutar `` avrogen -s .\Order.avsc `` para generar código). 
1. Una vez generado el código, debe tener los tipos de C# correspondientes disponibles dentro del proyecto. Para el esquema de Avro anterior, se generan los tipos de C# en el espacio de nombres ``Microsoft.Azure.Data.SchemaRegistry.example``. 


### <a name="write-code-to-serialize-and-send-events-to-the-event-hub"></a>Escritura de código para serializar y enviar eventos al centro de eventos 

1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    using System.Threading.Tasks;
    ```
1. También puede importar los tipos generados relacionados con el esquema ``Order``, como se muestra a continuación. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Agregue constantes a la clase `Program` de la cadena de conexión de Event Hubs y del nombre del centro de eventos.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

    > [!NOTE]
    > Reemplace los valores de marcador de posición por la cadena de conexión al espacio de nombres, el nombre del centro de eventos y el grupo de esquemas.
3. Agregue la siguiente propiedad estática a la clase `Program`. Vea los comentarios de código. 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. Reemplace el método `Main` por el siguiente método `async Main`. Vea los comentarios de código para obtener más detalles. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName); 
    
            // Create a schema registry client that you can use to serialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();
            
            // Create a new order object using the generated type/class 'Order'. 
            var sampleOrder = new Order { id = "12345", amount = 55.99, description = "This is a sample order." };
    
            using var memoryStream = new MemoryStream();
            // Create an Avro object serializer using the Schema Registry client object. 
            var producerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = true });
    
            // Serialize events data to the memory stream object. 
            producerSerializer.Serialize(memoryStream, sampleOrder, typeof(Order), CancellationToken.None);
    
            byte[] _memoryStreamBytes;
            _memoryStreamBytes = memoryStream.ToArray();
    
            // Create event data with serialized data and add it to an event batch. 
            eventBatch.TryAdd(new EventData(_memoryStreamBytes));
    
            // Send serilized event data to event hub. 
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 1 order has been published.");
        }
    ```
5. Compile el proyecto y asegúrese de que no hay errores.
6. Ejecute el programa y espere el mensaje de confirmación. 

    ```csharp
    A batch of 1 order has been published.
    ```
1. En Azure Portal, puede comprobar que el centro de eventos ha recibido los eventos. Cambie a la vista **Mensajes** en la sección **Métricas**. Actualice la página para actualizar el gráfico. Puede tardar unos segundos en mostrar que los mensajes se han recibido. 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="Imagen de la página de Azure Portal para comprobar que el centro de eventos recibió los eventos." lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::




## <a name="consume-events-from-event-hubs-with-schema-validation"></a>Consumo de eventos de centros de eventos con validación de esquemas
En esta sección se muestra cómo escribir una aplicación de consola de .NET Core que recibe eventos de un centro de eventos y cómo usar el registro de esquema para deserializar los datos de eventos. 


### <a name="create-consumer-application"></a>Creación de una aplicación de consumidor

1. En la ventana del Explorador de soluciones, haga clic con el botón derecho en la solución **SRQuickStart**, apunte a **Agregar** y seleccione **Nuevo proyecto**. 
1. Seleccione **Aplicación de consola** y seleccione **Siguiente**. 
1. Escriba **OrderConsumer** en **Nombre de proyecto** y seleccione **Crear**. 
1. En la ventana del **Explorador de soluciones**, haga clic con el botón derecho en **OrderConsumer** y seleccione **Set as a Startup Project** (Establecer como proyecto de inicio). 

### <a name="add-the-event-hubs-nuget-package"></a>Incorporación del paquete NuGet de Event Hubs

1. Seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** en el menú. 
1. En la ventana de la **Consola del Administrador de paquetes**, confirme que **OrderConsumer** está seleccionado como **Proyecto predeterminado**. Si no es así, use la lista desplegable para seleccionar **OrderConsumer**.


1. Ejecute el siguiente comando para instalar el paquete NuGet necesario:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Messaging.EventHubs.Processor
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. Autentique las aplicaciones de productor para conectarse a Azure mediante Visual Studio, como se muestra [aquí](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio). 



### <a name="code-generation-using-the-avro-schema"></a>Generación de código mediante el esquema de Avro  
1. Puede usar el mismo contenido de esquema y crear el archivo de esquema de Avro ``Order.avsc`` dentro del proyecto ``OrderProducer``. 
1. Luego, puede usar este archivo de esquema para generar código para .NET. Para ello, puede usar cualquier herramienta de generación de código externa, como [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/). (Por ejemplo, puede ejecutar `` avrogen -s .\Order.avsc `` para generar código). 
1. Una vez generado el código, debe tener los tipos de C# correspondientes disponibles dentro del proyecto. Para el esquema de Avro anterior, se generan los tipos de C# en el espacio de nombres ``Microsoft.Azure.Data.SchemaRegistry.example``. 


### <a name="write-code-to-receive-events-and-deserialize-them-using-schema-registry"></a>Escritura de código para recibir eventos y deserializarlos mediante el registro de esquema


1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. También puede importar los tipos generados relacionados con el esquema ``Order``, como se muestra a continuación. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Agregue constantes a la clase `Program` de la cadena de conexión de Event Hubs y del nombre del centro de eventos.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

3. Agregue las siguientes propiedades estáticas a la clase `Program`. 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. Reemplace el método `Main` por el siguiente método `async Main`. Vea los comentarios de código para obtener más detalles. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. Ahora, agregue el siguiente método de controlador de eventos que incluye la lógica de deserialización de eventos con el registro de esquema. 
    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Create a schema registry client that you can use to deserialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Retrieve event data and convert it to a byte array. 
            byte[] _memoryStreamBytes = eventArgs.Data.Body.ToArray();
            using var consumerMemoryStream = new MemoryStream(_memoryStreamBytes);
    
            var consumerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = false });
            consumerMemoryStream.Position = 0;
    
            // Deserialize event data and create order object using schema registry. 
            Order sampleOrder = (Order)consumerSerializer.Deserialize(consumerMemoryStream, typeof(Order), CancellationToken.None);
            Console.WriteLine("Received - Order ID: " + sampleOrder.id);                 
    
            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);   
        }

    ```

 
1. Agregue también los siguientes métodos de controlador de eventos y errores a la clase. 

    ```csharp
        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
  
    ```
1. Compile el proyecto y asegúrese de que no hay errores.


6. Ejecute la aplicación del destinatario. 
1. Debería ver un mensaje que indica que se han recibido los eventos. 

    ```bash
    Received - Order ID: 12345
    ```
    Estos eventos son los tres que envió al centro de eventos anteriormente mediante la ejecución del programa del emisor. 


## <a name="next-steps"></a>Pasos siguientes
Para más información, consulte [Biblioteca cliente del registro de esquema de Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/schemaregistry/Azure.Data.SchemaRegistry). 

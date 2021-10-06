---
title: Publicación de mensajes en los temas de Atlas Kafka para Azure Purview y procesamiento de estos mensajes desde allí a través de Event Hubs mediante .NET
description: Este artículo es un tutorial para crear una aplicación de .NET Core que envíe eventos a temas de Apache Atlas Kafka para Purview, o los reciba de este, mediante el paquete Azure.Messaging.EventHubs más reciente.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 09/27/2021
ms.openlocfilehash: 670e7425c8b56732a32e1deb0b664bc6544eda0c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209630"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Publicación de mensajes en los temas de Atlas Kafka para Azure Purview y procesamiento de estos mensajes desde allí a través de Event Hubs mediante .NET 
En este inicio rápido se muestra cómo enviar y recibir eventos desde los temas de Atlas Kafka para Azure Purview a través de un centro de eventos mediante la biblioteca **Azure.Messaging.EventHubs** de .NET. 

> [!IMPORTANT]
> Se va a crear un centro de eventos administrado como parte de la creación de cuentas de Purview. Para más información, consulte [Creación de cuentas de Purview.](create-catalog-portal.md) Puede publicar mensajes en el tema ATLAS_HOOK de Kafka del centro de eventos y Purview lo consumirá y procesará. Purview notificará los cambios de entidad en el tema ATLAS_ENTITIES de Kafka del centro de eventos y el usuario puede consumirlo y procesarlo. En este inicio rápido se usa la nueva biblioteca **Azure.Messaging.EventHubs**. 


## <a name="prerequisites"></a>Requisitos previos
Si es la primera vez que usa Azure Event Hubs, consulte la [información general de Event Hubs](../event-hubs/event-hubs-about.md) antes de continuar con este inicio rápido. 

Para completar este tutorial de inicio rápido, debe cumplir los siguientes requisitos previos:

- Una **suscripción a Microsoft Azure**. Para usar los servicios de Azure, entre los que se incluye Azure Event Hubs, se necesita una suscripción.  Si no se dispone de una cuenta de Azure, es posible registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/free/), o bien usar las ventajas que disfrutan los suscriptores MSDN al [crear una cuenta](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. La biblioteca cliente de Azure Event Hubs utiliza las nuevas características que se introdujeron en C# 8.0.  Aunque puede seguir usando la biblioteca con versiones anteriores de C#, la nueva sintaxis no estará disponible. Para usar la sintaxis completa, se recomienda realizar la compilación con el [SDK de .NET Core](https://dotnet.microsoft.com/download) 3.0 o superior y la [versión de lenguaje](/dotnet/csharp/language-reference/configure-language-version#override-a-default) establecida en `latest`. Si usa Visual Studio, las versiones anteriores a Visual Studio 2019 no son compatibles con las herramientas necesarias para compilar proyectos de C# 8.0. Visual Studio 2019, incluida la edición gratuita Community, se puede descargar [aquí](https://visualstudio.microsoft.com/vs/).

## <a name="publish-messages-to-purview"></a>Publicación de mensajes en Purview 
En esta sección se muestra cómo crear una aplicación de consola de .NET Core para enviar eventos a Purview mediante un tema **ATLAS_HOOK** de Kafka de un centro de eventos. 

## <a name="create-a-visual-studio-project"></a>Creación de un proyecto de Visual Studio

A continuación, cree una aplicación de consola .NET de C# en Visual Studio:

1. Inicie **Visual Studio**.
2. En la ventana Inicio, seleccione **Crear un nuevo proyecto** > **Aplicación de consola (.NET Framework)** . Se requiere .NET versión 4.5.2 o posterior.
3. En **Nombre del proyecto**, escriba **PurviewKafkaProducer**.
4. Seleccione **Crear** para crear el proyecto.

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

1. Inicie Visual Studio 2019. 
1. Seleccione **Crear un proyecto**. 
1. En el cuadro de diálogo **Crear un nuevo proyecto**, siga estos pasos: Si no ve este cuadro de diálogo, seleccione **Archivo** en el menú, seleccione **Nuevo** y, después, seleccione **Proyecto**. 
    1. Seleccione **C#** como lenguaje de programación.
    1. Seleccione **Consola** como tipo de aplicación. 
    1. Seleccione **Aplicación de consola (.NET Core)** en la lista de resultados. 
    1. Después, seleccione **Siguiente**. 


### <a name="add-the-event-hubs-nuget-package"></a>Incorporación del paquete NuGet de Event Hubs

1. Seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** en el menú. 
1. Ejecute el siguiente comando para instalar el paquete NuGet **Azure.Messaging.EventHubs** y el paquete NuGet **Azure.Messaging.EventHubs.Producer**:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escritura de código para enviar mensajes al centro de eventos

1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Agregue constantes a la clase `Program` de la cadena de conexión de Event Hubs y del nombre del centro de eventos. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Puede obtener el espacio de nombres del centro de eventos asociado a la cuenta de Purview si consulta las cadenas de conexión principal y secundaria del punto de conexión de Atlas Kafka en la pestaña de propiedades de la cuenta de Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Espacio de nombres del centro de eventos":::

    El nombre del centro de eventos debe ser **ATLAS_HOOK** para enviar mensajes a Purview.

3. Reemplace el método `Main` por el método `async Main` siguiente y agregue un elemento `async ProduceMessage` para insertar mensajes en Purview. Vea los comentarios de código para obtener más detalles. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Compile el proyecto y asegúrese de que no hay errores.
6. Ejecute el programa y espere el mensaje de confirmación. 

    > [!NOTE]
    > Para ver el código fuente completo con comentarios muy útiles, consulte [este archivo en GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md).

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Ejemplo de mensaje Create Entity JSON para crear una tabla de SQL con dos columnas.

```json
    
    {
    "msgCreatedBy":"nayenama",
    "message":{
        "type":"ENTITY_CREATE_V2",
        "user":"admin",
        "entities":{
            "entities":[
                {
                    "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "temporary":false,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table added via Kafka"
                    },
                    "relationshipAttributes":{
                        "columns":[
                            {
                                "guid":"-1102395743156037",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid":"-1102395743156038",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                    },
                    "guid":"-1102395743156036",
                    "version":0
                }
            ],
            "referredEntities":{
                "-1102395743156037":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                        "precision":23,
                        "length":8,
                        "description":"Sales Order ID",
                        "scale":3,
                        "name":"OrderID",
                        "data_type":"int"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156037",
                    "version":2
                },
                "-1102395743156038":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                        "description":"Sales Order Date",
                        "scale":3,
                        "name":"OrderDate",
                        "data_type":"datetime"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156038",
                    "status":"ACTIVE",
                    "createdBy":"ServiceAdmin",
                    "version":0
                }
            }
        }
    },
    "version":{
        "version":"1.0.0"
    },
    "msgCompressionKind":"NONE",
    "msgSplitIdx":1,
    "msgSplitCount":1
}


``` 

## <a name="consume-messages-from-purview"></a>Uso de mensajes en Purview
En esta sección se muestra cómo escribir una aplicación de consola de .NET Core que reciba mensajes de un centro de eventos mediante un procesador de eventos. Debe usar un centro de eventos ATLAS_ENTITIES para recibir mensajes de Purview. El procesador de eventos simplifica la recepción de eventos de los centros de eventos mediante la administración de puntos de control persistentes y recepciones paralelas desde esos centros de eventos. 

> [!WARNING]
> Si ejecuta este código en Azure Stack Hub, experimentará errores en tiempo de ejecución a menos que tenga como destino una versión específica de la API de Storage. Esto se debe a que el SDK de Event Hubs usa la API de Azure Storage más reciente disponible en Azure, que puede que no esté disponible en la plataforma de Azure Stack Hub. Azure Stack Hub puede admitir una versión diferente del SDK de Blob Storage que las que suelen estar disponibles en Azure. Si usa Azure Blob Storage como almacén de puntos de control, compruebe la [versión de la API de Azure Storage admitida para la compilación de Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) y establezca esa versión como destino en el código. 
>
> Por ejemplo, si trabaja en la versión 2005 de Azure Stack Hub, la versión más reciente disponible para el servicio Storage es la 2019-02-02. De forma predeterminada, la biblioteca de cliente del SDK de Event Hubs usa la versión más reciente disponible en Azure (2019-07-07 en el momento de la versión del SDK). En este caso, además de seguir los pasos de esta sección, también tendrá que agregar código para usar como destino la versión 2019-02-02 de la API del servicio de almacenamiento. Consulte [este ejemplo en GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) para ver cómo usar como destino una versión específica de la API de Storage. 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Creación de una instancia de Azure Storage y un contenedor de blobs de Azure Storage
En este inicio rápido, se usa Azure Storage como almacén de puntos de control. Siga estos pasos para crear una cuenta de Azure Storage. 

1. [Creación de una cuenta de Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creación de un contenedor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtención de la cadena de conexión para una cuenta de almacenamiento](../storage/common/storage-configure-connection-string.md)

    Anote la cadena de conexión y el nombre del contenedor. Los usará en el código de recepción. 


### <a name="create-a-project-for-the-receiver"></a>Creación de un proyecto para el destinatario

1. En la ventana del Explorador de soluciones, mantenga presionada la solución **EventHubQuickStart** o haga clic en ella con el botón derecho, apunte a **Agregar** y seleccione **Nuevo proyecto**. 
1. Seleccione **Aplicación de consola (.NET Core)** y, después, **Siguiente**. 
1. Escriba **PurviewKafkaConsumer** en **Nombre de proyecto** y seleccione **Crear**. 

### <a name="add-the-event-hubs-nuget-package"></a>Incorporación del paquete NuGet de Event Hubs

1. Seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** en el menú. 
1. Ejecute el siguiente comando para instalar el paquete NuGet **Azure.Messaging.EventHubs**:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Ejecute el siguiente comando para instalar el paquete NuGet **Azure.Messaging.EventHubs.Processor**:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Actualización del método Main 

1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Agregue constantes a la clase `Program` de la cadena de conexión de Event Hubs y del nombre del centro de eventos. Reemplace los marcadores de posición entre llaves por los valores adecuados que recibió al crear el centro de eventos. Reemplace los marcadores de posición entre llaves por los valores adecuados que recibió al crear el centro de eventos y la cuenta de almacenamiento (claves de acceso y cadena de conexión principal). Asegúrese de que `{Event Hubs namespace connection string}` es la cadena de conexión en el nivel de espacio de nombres y no la cadena de Event Hub.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
    
    Puede obtener el espacio de nombres del centro de eventos asociado a la cuenta de Purview si consulta las cadenas de conexión principal y secundaria del punto de conexión de Atlas Kafka en la pestaña de propiedades de la cuenta de Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Espacio de nombres del centro de eventos":::

    El nombre del centro de eventos debe ser **ATLAS_ENTITIES** para enviar mensajes a Purview.

3. Reemplace el método `Main` por el siguiente método `async Main`. Vea los comentarios de código para obtener más detalles. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Ahora, agregue los siguientes métodos de control de eventos y errores a la clase. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Compile el proyecto y asegúrese de que no hay errores.

    > [!NOTE]
    > Para ver el código fuente completo con comentarios muy útiles, consulte [este archivo en GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Ejecute la aplicación del destinatario. 

### <a name="sample-message-received-from-purview"></a>Mensaje de ejemplo recibido de Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas admite actualmente los siguientes tipos de operación: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. La inserción de mensajes en Purview está habilitada actualmente de forma predeterminada. Si el escenario conlleva la lectura desde Purview, póngase en contacto con nosotros, ya que debe aparecer en la lista de permitidos. (proporcione el identificador de la suscripción y el nombre de la cuenta de Purview).


## <a name="next-steps"></a>Pasos siguientes
Consulte los ejemplos de GitHub. 

- [Ejemplos de Event Hubs en GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ejemplos de procesador de eventos en GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Introducción a las notificaciones de Atlas](https://atlas.apache.org/2.0.0/Notifications.html)

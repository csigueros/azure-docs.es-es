---
title: 'Inicio rápido: Publicación de mensajes mediante el SDK de servicio para la instancia de Azure Web PubSub'
description: Inicio rápido que muestra cómo usar el SDK de servicio
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 08/06/2021
ms.openlocfilehash: 42bcaa9697733662b3a5b890461b917fc466c492
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445179"
---
# <a name="quickstart-publish-messages-using-the-service-sdk-for-the-azure-web-pubsub-instance"></a>Inicio rápido: Publicación de mensajes mediante el SDK de servicio para la instancia de Azure Web PubSub

En este inicio rápido, se muestra cómo publicar mensajes en los clientes mediante el SDK de servicio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Para realizar este inicio rápido es necesaria la versión 2.22.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Creación de una instancia de Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="get-the-connectionstring-for-future-use"></a>Obtención de la cadena de conexión para su uso en el futuro

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Copie el elemento **ConnectionString** capturado, que se usará más adelante en al utilizar el SDK de servicio como valor de `<connection_string>`.

## <a name="connect-to-the-instance"></a>Conexión a la instancia

[!INCLUDE [az webpubsub client](includes/cli-awps-client-connect.md)]

## <a name="publish-messages-using-service-sdk"></a>Publicación de mensajes mediante el SDK del servicio

Ahora, vamos a usar el SDK de Azure Web PubSub para publicar un mensaje para el cliente conectado.

### <a name="prerequisites"></a>Prerrequisitos

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 o una versión posterior](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x o una versión posterior](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Kit de desarrollo de Java (JDK)](/java/azure/jdk/), versión 8 o posterior.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

### <a name="set-up-the-project-to-publish-messages"></a>Configuración del proyecto para publicar mensajes

# <a name="c"></a>[C#](#tab/csharp)

1. Agregue un nuevo proyecto llamado `publisher` y agregue el paquete del SDK `package Azure.Messaging.WebPubSub`.

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2. Actualice el archivo `Program.cs` para usar la clase `WebPubSubServiceClient` y enviar mensajes a los clientes.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await serviceClient.SendToAllAsync(message);
            }
        }
    }
    ```

    La llamada `SendToAllAsync()` simplemente envía un mensaje a todos los clientes conectados en el centro.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. Puede ver que el cliente anterior de la CLI recibió el mensaje.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. En primer lugar, vamos a crear la carpeta `publisher` para este proyecto e instalar las dependencias necesarias:

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub

    ```
2. Ahora vamos a usar el SDK de Azure Web PubSub para publicar un mensaje para el servicio. Cree un archivo `publish.js` con el código siguiente:

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    if (process.argv.length !== 5) {
    console.log('Usage: node publish <connection-string> <hub-name> <message>');
    return 1;
    }

    let serviceClient = new WebPubSubServiceClient(process.argv[2], process.argv[3]);

    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    serviceClient.sendToAll(process.argv[4], { contentType: "text/plain" });
    ```

    La llamada `sendToAll()` simplemente envía un mensaje a todos los clientes conectados en un centro.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    node publish "<connection_string>" "myHub1" "Hello World"
    ```

4. Puede ver que el cliente anterior de la CLI recibió el mensaje.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="python"></a>[Python](#tab/python)

1. En primer lugar, vamos a crear la carpeta `publisher` para este proyecto e instalar las dependencias necesarias:
    * Al usar bash
        ```bash
        mkdir publisher
        cd publisher
        # Create venv
        python -m venv env

        # Active venv
        ./env/Scripts/activate

        # Or call .\env\Scripts\activate when you are using CMD

        pip install azure-messaging-webpubsubservice

        ```
2. Ahora vamos a usar el SDK de Azure Web PubSub para publicar un mensaje para el servicio. Cree un archivo `publish.py` con el código siguiente:

    ```python
    import sys
    from azure.messaging.webpubsubservice import (
        WebPubSubServiceClient
    )
    from azure.messaging.webpubsubservice.rest import *

    if len(sys.argv) != 4:
        print('Usage: python publish.py <connection-string> <hub-name> <message>')
        exit(1)

    connection_string = sys.argv[1]
    hub_name = sys.argv[2]
    message = sys.argv[3]

    service_client = WebPubSubServiceClient.from_connection_string(connection_string)
    res = service_client.send_request(build_send_to_all_request(hub_name, content=message, content_type='text/plain'))
    # res should be <HttpResponse: 202 Accepted>
    print(res)

    ```

    `build_send_to_all_request()` crea un mensaje y `send_request()` lo envía a todos los clientes conectados de un centro.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. Puede ver que el cliente anterior de la CLI recibió el mensaje.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="java"></a>[Java](#tab/java)

1. En primer lugar, vamos a usar Maven para crear la nueva aplicación de consola `webpubsub-quickstart-publisher` y cambiar a la carpeta *webpubsub-quickstart-subscriber*:
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. Vamos a agregar la dependencia del SDK de Azure Web PubSub en el nodo `dependencies` de `pom.xml`:

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

3. Ahora vamos a usar el SDK de Azure Web PubSub para publicar un mensaje para el servicio. Vamos a ir al directorio */src/main/java/com/webpubsub/quickstart*, abrir el archivo *App.java* en el editor y reemplazar el código por lo siguiente:

    ```java
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;

    /**
    * Quickstart - Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }

            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            client.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    La llamada `sendToAll()` simplemente envía un mensaje a todos los clientes conectados en un centro.

4. Navegue hasta el directorio que contiene el archivo *pom.xml* y compile el proyecto mediante el siguiente comando `mvn`.

    ```console
    mvn compile
    ```
5. A continuación, compile el paquete.

    ```console
    mvn package
    ```
6. Ejecute el siguiente comando `mvn` para ejecutar la aplicación y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```console
    mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

7. Puede ver que el cliente anterior de la CLI recibió el mensaje.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

---

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, se proporciona una idea básica de cómo conectarse al servicio Web PubSub y cómo publicar mensajes en los clientes conectados.

En las aplicaciones del mundo real, puede usar los SDK en varios lenguajes para compilar su propia aplicación. También proporcionamos extensiones de función para que pueda compilar aplicaciones sin servidor fácilmente.

[!INCLUDE [next step](includes/include-next-step.md)]

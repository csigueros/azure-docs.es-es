---
title: 'Tutorial: Publicación y suscripción de mensajes mediante la API de WebSocket y el SDK del servicio Azure Web PubSub'
description: Tutorial en el que se explica cómo usar el servicio Azure Web PubSub y Azure Functions para compilar una aplicación sin servidor.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: b0027bfd1f214ecba347652ce37009103b76ff00
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255428"
---
# <a name="tutorial-publish-and-subscribe-messages-using-websocket-api-and-azure-web-pubsub-service-sdk"></a>Tutorial: Publicación y suscripción de mensajes mediante la API de WebSocket y el SDK del servicio Azure Web PubSub

El servicio Azure Web PubSub le ayuda a crear aplicaciones web de mensajería en tiempo real mediante WebSockets y el patrón de publicación y suscripción fácilmente. En este tutorial, aprenderá a suscribirse al servicio mediante la API de WebSocket y a publicar mensajes y a usar el SDK del servicio.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una instancia del servicio Web PubSub
> * Generar la dirección URL completa para establecer la conexión de WebSocket
> * Suscripción de mensajes mediante el protocolo WebSocket estándar
> * Publicación de mensajes mediante el SDK del servicio

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Esta configuración requiere la versión 2.22.0, o cualquier versión superior, de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-an-azure-web-pubsub-instance"></a>Creación de una instancia de Azure Web PubSub

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Creación de una instancia de Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>Obtención de ConnectionString para usarlo en el futuro

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Copie el elemento **ConnectionString** capturado y se usará más adelante en este tutorial como el valor de `<connection_string>`.

## <a name="set-up-the-project"></a>Configuración del proyecto
### <a name="prerequisites"></a>Prerequisites

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 o una versión posterior](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x o una versión posterior](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Kit de desarrollo de Java (JDK)](/java/azure/jdk/), versión 8 o posterior
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="set-up-the-subscriber"></a>Configuración del suscriptor

Los clientes se conectan al servicio Azure Web PubSub mediante el protocolo WebSocket estándar con el uso de la autenticación de [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). El SDK del servicio proporciona métodos de asistencia para generar el token. En este tutorial, el suscriptor genera directamente el token de *ConnectionString*. En aplicaciones reales, normalmente se usa una aplicación del lado del servidor para controlar el flujo de trabajo de autenticación o autorización. Pruebe el tutorial [Compilación de una aplicación de chat](./tutorial-build-chat.md) para comprender mejor el flujo de trabajo.

# <a name="c"></a>[C#](#tab/csharp)

1. En primer lugar, vamos a crear una nueva carpeta `subscribe` para este proyecto e instalar las dependencias necesarias:
    * El paquete [Websocket.Client](https://github.com/Marfusios/websocket-client) es un paquete de terceros que admite la conexión de WebSocket. Puede usar cualquier API o biblioteca que admita WebSocket para hacerlo.
    * El paquete del SDK `Azure.Messaging.WebPubSub` ayuda a generar el token JWT. 

    ```bash
    mkdir subscriber
    cd subscriber
    dotnet new console
    dotnet add package Websocket.Client --version 4.3.30
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2. Actualice el archivo `Program.cs` para conectarse al servicio:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;
    using Websocket.Client;

    namespace subscriber
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 2)
                {
                    Console.WriteLine("Usage: subscriber <connectionString> <hub>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];

                // Either generate the URL or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                var url = serviceClient.GenerateClientAccessUri();

                using (var client = new WebsocketClient(url))
                {
                    // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
                    client.ReconnectTimeout = null;
                    client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                    await client.Start();
                    Console.WriteLine("Connected.");
                    Console.Read();
                }
            }
        }
    }
    ```

    El código anterior crea una conexión de WebSocket para conectarse a un centro en Azure Web PubSub. El centro es una unidad lógica de Azure Web PubSub donde puede publicar mensajes a un grupo de clientes. [Los conceptos clave](./key-concepts.md) contienen la explicación detallada sobre los términos usados en Azure Web PubSub.
    
    El servicio Azure Web PubSub usa la autenticación [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) por lo que en el ejemplo de código se usa `WebPubSubServiceClient.GenerateClientAccessUri()` en el SDK de Web PubSub para generar una dirección URL al servicio que contiene la dirección URL completa con un token de acceso válido.
    
    Una vez establecida la conexión, recibirá mensajes a través de la conexión de WebSocket. Por lo tanto, usamos `client.MessageReceived.Subscribe(msg => ...));` para escuchar los mensajes entrantes.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    dotnet run "<connection_string>" "myHub1"
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. En primer lugar, vamos a crear una nueva carpeta `subscriber` para este proyecto e instalar las dependencias necesarias:

    ```bash
    mkdir subscriber
    cd subscriber
    npm init -y
    npm install --save ws
    npm install --save @azure/web-pubsub

    ```
2. A continuación, use la API de WebSocket para conectarse al servicio. Cree un archivo `subscribe.js` con el código siguiente:

    ```javascript
    const WebSocket = require('ws');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    async function main() {
      if (process.argv.length !== 4) {
        console.log('Usage: node subscribe <connection-string> <hub-name>');
        return 1;
      }

      let serviceClient = new WebPubSubServiceClient(process.argv[2], process.argv[3]);
      let token = await serviceClient.getAuthenticationToken();
      let ws = new WebSocket(token.url);
      ws.on('open', () => console.log('connected'));
      ws.on('message', data => console.log('Message received: %s', data));
    }

    main();
    ```
    
    El código anterior crea una conexión de WebSocket para conectarse a un centro en Azure Web PubSub. El centro es una unidad lógica de Azure Web PubSub donde puede publicar mensajes a un grupo de clientes. [Los conceptos clave](./key-concepts.md) contienen la explicación detallada sobre los términos usados en Azure Web PubSub.
    
    El servicio Azure Web PubSub usa la autenticación [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) por lo que en el ejemplo de código se usa `WebPubSubServiceClient.getAuthenticationToken()` en el SDK de Web PubSub para generar una dirección URL al servicio que contiene la dirección URL completa con un token de acceso válido.
    
    Una vez establecida la conexión, recibirá mensajes a través de la conexión de WebSocket. Por lo tanto, usamos `WebSocket.on('message', ...)` para escuchar los mensajes entrantes.
    
3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    node subscribe "<connection_string>" "myHub1"
    ```

# <a name="python"></a>[Python](#tab/python)

1. En primer lugar, vamos a crear una nueva carpeta `subscriber` para este proyecto e instalar las dependencias necesarias:

    ```bash
    mkdir subscriber
    cd subscriber
    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows

    pip install azure-messaging-webpubsubservice
    pip install websockets

    ```

2. A continuación, use la API de WebSocket para conectarse al servicio. Cree un archivo `subscribe.py` con el código siguiente:

    ```python
    import asyncio
    import sys
    import websockets
    from azure.messaging.webpubsubservice import (
        build_authentication_token
    )

    async def connect(url):
        async with websockets.connect(url) as ws:
            print('connected')
            while True:
                print('Message received: ' + await ws.recv())

    if len(sys.argv) != 3:
        print('Usage: python subscribe.py <connection-string> <hub-name>')
        exit(1)

    connection_string = sys.argv[1]
    hub_name = sys.argv[2]

    token = build_authentication_token(connection_string, hub_name)

    try:
        asyncio.get_event_loop().run_until_complete(connect(token['url']))
    except KeyboardInterrupt:
        pass

    ```

    El código anterior crea una conexión de WebSocket para conectarse a un centro en Azure Web PubSub. El centro es una unidad lógica de Azure Web PubSub donde puede publicar mensajes a un grupo de clientes. [Los conceptos clave](./key-concepts.md) contienen la explicación detallada sobre los términos usados en Azure Web PubSub.
    
    El servicio Azure Web PubSub usa la autenticación [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) por lo que en el ejemplo de código se usa `build_authentication_token()` en el SDK de Web PubSub para generar una dirección URL al servicio que contiene la dirección URL completa con un token de acceso válido.
    
    Una vez establecida la conexión, recibirá mensajes a través de la conexión de WebSocket. Por lo tanto, usamos `await ws.recv()` para escuchar los mensajes entrantes.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    python subscribe.py "<connection_string>" "myHub1"
    ```

# <a name="java"></a>[Java](#tab/java)
1. Primero, cree una nueva carpeta `pubsub` para este tutorial
    ```cmd
    mkdir pubsub
    cd pubsub
    ```

1. A continuación, dentro de esta carpeta `pubsub`, vamos a usar Maven para crear una nueva aplicación de consola `webpubsub-quickstart-subscriber` y cambiar a la carpeta *webpubsub-quickstart-subscriber*:

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-subscriber --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-subscriber
    ```

2. Vamos a agregar la dependencia de WebSocket y del SDK de Azure Web PubSub en el nodo `dependencies` de `pom.xml`:

    * `azure-messaging-webpubsub`: SDK del servicio Web PubSub para Java
    * `Java-WebSocket`: SDK del cliente WebSocket para Java

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>

    <dependency>
        <groupId>org.java-websocket</groupId>
        <artifactId>Java-WebSocket</artifactId>
        <version>1.5.1</version>
    </dependency>

    ```

3. En Azure Web PubSub, puede conectarse al servicio y suscribirse a los mensajes a través de conexiones WebSocket. WebSocket es un canal de comunicación dúplex completo para que el servicio pueda insertar mensajes en el cliente en tiempo real. Puede usar cualquier API o biblioteca que admita WebSocket para hacerlo. Para este ejemplo, usamos el v [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket). Vamos a ir al directorio */src/main/java/com/webpubsub/quickstart*, abrir el archivo *App.java* en el editor y reemplazar el código por lo siguiente:

    ```java
    
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    import org.java_websocket.client.WebSocketClient;
    import org.java_websocket.handshake.ServerHandshake;

    import java.io.IOException;
    import java.net.URI;
    import java.net.URISyntaxException;

    /**
    * Connect to Azure Web PubSub service using WebSocket protocol
    *
    */
    public class App 
    {
        public static void main( String[] args ) throws IOException, URISyntaxException
        {
            if (args.length != 2) {
                System.out.println("Expecting 2 arguments: <connection-string> <hub-name>");
                return;
            }

            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();

            WebPubSubAuthenticationToken token = client.getAuthenticationToken(new GetAuthenticationTokenOptions());

            WebSocketClient webSocketClient = new WebSocketClient(new URI(token.getUrl())) {
                @Override
                public void onMessage(String message) {
                    System.out.println(String.format("Message received: %s", message));
                }

                @Override
                public void onClose(int arg0, String arg1, boolean arg2) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onError(Exception arg0) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onOpen(ServerHandshake arg0) {
                    // TODO Auto-generated method stub
                }
                
            };

            webSocketClient.connect();
            System.in.read();
        }
    }

    ```

    El código anterior crea una conexión de WebSocket para conectarse a un centro en Azure Web PubSub. El centro es una unidad lógica de Azure Web PubSub donde puede publicar mensajes a un grupo de clientes. [Los conceptos clave](./key-concepts.md) contienen la explicación detallada sobre los términos usados en Azure Web PubSub.
    
    El servicio Azure Web PubSub usa la autenticación [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) por lo que en el ejemplo de código se usa `WebPubSubServiceClient.getAuthenticationToken(new GetAuthenticationTokenOptions())` en el SDK de Web PubSub para generar una dirección URL al servicio que contiene la dirección URL completa con un token de acceso válido.
    
    Una vez establecida la conexión, recibirá mensajes a través de la conexión de WebSocket. Por lo tanto, usamos `onMessage(String message)` para escuchar los mensajes entrantes.

4. Vaya al directorio que contiene el archivo *pom.xml* y ejecute la aplicación con el código siguiente, reemplazando `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1'"
    ```

---

## <a name="publish-messages-using-service-sdk"></a>Publicación de mensajes mediante el SDK del servicio

Ahora vamos a usar el SDK de Azure Web PubSub para publicar un mensaje para el cliente conectado.

# <a name="c"></a>[C#](#tab/csharp)

1. En primer lugar, vamos a crear la carpeta `publisher` para este proyecto e instalar las dependencias necesarias:

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2. Ahora vamos a actualizar el archivo `Program.cs` para usar la clase `WebPubSubServiceClient` y enviar mensajes a los clientes.

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

4. Puede ver que el suscriptor anterior recibió el mensaje siguiente:
   
    ```
    Message received: Hello World
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

4. Puede ver que el suscriptor anterior recibió el mensaje siguiente:
   
    ```
    Message received: Hello World
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

        # Or call .\env\Scripts\activate when you are using CMD under windows

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

    `build_send_to_all_request()` compiló un mensaje y `send_request()` lo envió a todos los clientes conectados de un centro.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. Puede ver que el suscriptor anterior recibió el mensaje siguiente:
   
    ```
    Received message: Hello World
    ```

# <a name="java"></a>[Java](#tab/java)

1.  Vamos a usar otro terminal y volver a la carpeta `pubsub` para crear una aplicación de consola de publicador `webpubsub-quickstart-publisher` y cambiar a la carpeta *webpubsub-quickstart-publisher*:
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
    * Publish messages using Azure Web PubSub service SDK
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

4. Vaya al directorio que contiene el archivo *pom.xml* y ejecute el proyecto que usa el comando siguiente, reemplazando `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

5. Puede ver que el suscriptor anterior recibió el mensaje siguiente:
   
    ```
    Message received: Hello World
    ```

---

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se proporciona una idea básica de cómo conectarse al servicio Web PubSub y cómo publicar mensajes en los clientes conectados.

En otros tutoriales podrá profundizar más en cómo usar el servicio.

> [!div class="nextstepaction"]
> [Tutorial: Creación de una sala de chat con Azure Web PubSub](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://aka.ms/awps/samples).

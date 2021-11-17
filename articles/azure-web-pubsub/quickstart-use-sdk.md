---
title: 'Inicio rápido: Publicación de mensajes mediante el SDK de servicio para la instancia de Azure Web PubSub'
description: Inicio rápido que muestra cómo usar el SDK de servicio
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/01/2021
ms.openlocfilehash: c3e422699d03e50bfd860ded2e281c8199ac7a5e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546564"
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

* [Node.js 12.x, o cualquier versión superior](https://nodejs.org)

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
    dotnet add package Azure.Messaging.WebPubSub
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

                var service = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await service.SendToAllAsync(message);
            }
        }
    }
    ```

    La llamada `service.SendToAllAsync()` simplemente envía un mensaje a todos los clientes conectados en el centro.

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

    if (process.argv.length !== 3) {
      console.log('Usage: node publish <message>');
      return 1;
    }
    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    La llamada `sendToAll()` simplemente envía un mensaje a todos los clientes conectados en un centro.

3. Ejecute el siguiente comando y reemplace `<connection_string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use):

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. Puede ver que el cliente anterior de la CLI recibió el mensaje.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="python"></a>[Python](#tab/python)

1. En primer lugar, vamos a crear la carpeta `publisher` para este proyecto e instalar las dependencias necesarias:
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. Ahora vamos a usar el SDK de Azure Web PubSub para publicar un mensaje para el servicio. Cree un archivo `publish.py` con el código siguiente:

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    El método `service.send_to_all()` envía un mensaje a todos los clientes conectados en un centro de conectividad.

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
        <version>1.0.0-beta.6</version>
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
    
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    La llamada `service.sendToAll()` simplemente envía un mensaje a todos los clientes conectados en un centro.

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

En las aplicaciones del mundo real, puede usar SDK en varios lenguajes para crear su propia aplicación. También proporcionamos extensiones de función para que pueda crear aplicaciones sin servidor fácilmente.

[!INCLUDE [next step](includes/include-next-step.md)]

---
title: Inicio de una conexión de WebSocket a Azure Web PubSub
description: Obtenga información sobre cómo iniciar una conexión de WebSocket al servicio Azure Web PubSub en distintos lenguajes.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 02e7afa9b8c454b3520db933d76149973b50c699
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275171"
---
#  <a name="start-a-websocket-connection-to-azure-web-pubsub"></a>Inicio de una conexión de WebSocket a Azure Web PubSub

Los clientes se conectan al servicio Azure Web PubSub mediante el protocolo [WebSocket](https://tools.ietf.org/html/rfc6455) estándar. Puede usar lenguajes que tengan compatibilidad con el cliente de WebSocket para escribir un cliente para el servicio. En este artículo, verá varios ejemplos de cliente de WebSocket en distintos lenguajes.

## <a name="authorization"></a>Authorization

Web PubSub usa un [JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519.html) para validar y autorizar a los clientes. Los clientes pueden colocar el token en el parámetro de consulta `access_token` o en el encabezado `Authorization` al establecer conexión con el servicio.

Normalmente, el cliente se comunica primero con su servidor de aplicaciones para obtener la dirección URL del servicio y el token. A continuación, el cliente abre la conexión de WebSocket con el servicio mediante la dirección URL y el token que recibe.

El portal también proporciona una herramienta para generar la dirección URL del cliente con el token dinámicamente. Esta herramienta puede ser útil para realizar una prueba rápida.

:::image type="content" source="./media/howto-websocket-connect/generate-client-url.png" alt-text="Captura de pantalla que muestra dónde encontrar el generador de direcciones URL de cliente.":::

> [!NOTE]
> Asegúrese de incluir solo los roles necesarios al generar el token.
>

Para simplificar el flujo de trabajo de ejemplo, en las secciones siguientes, usamos esta dirección URL generada temporalmente desde el portal para que el cliente establezca conexión. Usamos `<Client_URL_From_Portal>` para representar el valor. El token generado expira en un plazo de 60 minutos de manera predeterminada, por lo que no olvide volver a generar uno cuando expire.

El servicio admite dos tipos de clientes de WebSocket, uno que es el cliente de WebSocket simple y el otro, el cliente de WebSocket PubSub. A continuación, se muestra cómo estos dos tipos de clientes se conectan al servicio. Para más información sobre estos clientes, consulte [Protocolos de cliente de WebSocket para Azure Web PubSub](./concept-client-protocols.md).

## <a name="dependency"></a>Dependencia

# <a name="in-browser"></a>[En el explorador](#tab/browser)
En la mayoría de los exploradores modernos, la API `WebSocket` se admite de forma nativa. 

# <a name="nodejs"></a>[Node.js](#tab/javascript)

* [Node.js 12.x o posterior](https://nodejs.org)
* `npm install ws`

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)
* `pip install websockets`

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 o posterior](https://dotnet.microsoft.com/download)
* `dotnet add package Websocket.Client`
    * [Websocket.Client](https://github.com/Marfusios/websocket-client) es un cliente de WebSocket de terceros con reconexión y control de errores integrados.

# <a name="java"></a>[Java](#tab/java)
- [Kit de desarrollo de Java (JDK)](/java/azure/jdk/) versión 8 o posterior.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

## <a name="simple-websocket-client"></a>Cliente simple de WebSocket

# <a name="in-browser"></a>[En el explorador](#tab/browser)

En el bloque `script` de la página HTML:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>");
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def hello():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri) as ws:
        while True:
            await ws.send('hello')
            greeting = await ws.recv()
            print(greeting)

asyncio.get_event_loop().run_until_complete(hello())
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Websocket.Client;

namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>")))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
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

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A simple WebSocket Client.
 *
 */
public final class SimpleClient {
    private SimpleClient() {
    }

    /**
     * Starts a simple WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder()
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}

```


---

## <a name="pubsub-websocket-client"></a>Cliente de WebSocket de PubSub

# <a name="in-browser"></a>[En el explorador](#tab/browser)

En el bloque `script` de la página HTML:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>", 'json.webpubsub.azure.v1');
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>", "json.webpubsub.azure.v1");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def join_group():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri, subprotocols=['json.webpubsub.azure.v1']) as ws:
        await ws.send('{"type":"joinGroup","ackId":1,"group":"group1"}')
        return await ws.recv()

print(asyncio.get_event_loop().run_until_complete(join_group()))
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading.Tasks;
namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>"), () =>
            {
                var inner = new ClientWebSocket();
                inner.Options.AddSubProtocol("json.webpubsub.azure.v1");
                return inner;
            }))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
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

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A PubSub WebSocket Client.
 *
 */
public final class SubprotocolClient {
    private SubprotocolClient() {
    }

    /**
     * Starts a PubSub WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();

        ws.sendText("{\"type\":\"joinGroup\",\"ackId\":1,\"group\":\"group1\"}", true);
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}
```

---

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre cómo conectarse al servicio mediante la dirección URL generada desde el portal. Para ver cómo se comunican los clientes con el servidor de aplicaciones con tal de obtener la dirección URL en aplicaciones del mundo real, consulte los tutoriales y ejemplos siguientes.

> [!div class="nextstepaction"]
> [Tutorial: Creación de una sala de chat con Azure Web PubSub](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Tutorial: Streaming de cliente mediante un subprotocolo compatible con el servicio](./tutorial-subprotocol.md)

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://aka.ms/awps/samples).


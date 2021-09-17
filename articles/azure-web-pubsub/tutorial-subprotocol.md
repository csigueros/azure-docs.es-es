---
title: 'Tutorial: Publicación y suscripción de mensajes entre clientes de WebSocket mediante un subprotocolo en el servicio Azure Web PubSub'
description: Tutorial en el que se explica cómo usar el servicio Azure Web PubSub y su subprotocolo WebSocket compatible para realizar sincronizaciones entre clientes.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: 232489ea06020d5f2f06cfc7d841c888b2b5a5d0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773255"
---
# <a name="tutorial-publish-and-subscribe-messages-between-websocket-clients-using-subprotocol"></a>Tutorial: Publicación y suscripción de mensajes entre clientes de WebSocket mediante un subprotocolo

En el [tutorial para la creación de una aplicación de chat](./tutorial-build-chat.md), ha aprendido a usar las API de WebSocket para enviar y recibir datos con Azure Web PubSub. Puede ver que no se necesita ningún protocolo cuando el cliente se comunica con el servicio. Por ejemplo, puede usar `WebSocket.send()` para enviar datos y el servidor los recibirá tal como están. Esto es fácil de usar, pero la funcionalidad también es limitada. Por ejemplo, no puede especificar el nombre del evento al enviar el evento al servidor ni publicar mensajes a otros clientes, en lugar de enviarlo al servidor. En este tutorial, aprenderá a usar el subprotocolo para ampliar la funcionalidad del cliente.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una instancia del servicio Web PubSub.
> * Generar la dirección URL completa para establecer la conexión de WebSocket.
> * Publicar mensajes entre clientes de WebSocket mediante un subprotocolo.

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

### <a name="prerequisites"></a>Prerrequisitos

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 3.1, o cualquier versión superior](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x, o cualquier versión superior](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

---

## <a name="using-a-subprotocol"></a>Uso de un subprotocolo

El cliente puede iniciar una conexión de WebSocket mediante un [subprotocolo](https://datatracker.ietf.org/doc/html/rfc6455#section-1.9) concreto. El servicio Azure Web PubSub admite un subprotocolo denominado `json.webpubsub.azure.v1` que permite a los clientes publicar o suscribirse directamente, en lugar de tener que realizar un recorrido de ida y vuelta al servidor ascendente. Para obtener información sobre el protocolo, consulte el artículo sobre el [ protocolo WebSocket JSON compatible con Azure Web PubSub](./reference-json-webpubsub-subprotocol.md).

> Si usa otros nombres de protocolo, el servicio los omitirá y el acceso directo al servidor en el controlador del eventos connect, para que pueda crear sus propios protocolos.

Ahora vamos a crear una aplicación web mediante el subprotocolo `json.webpubsub.azure.v1`.

1.  Instalar dependencias

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    mkdir logstream
    cd logstream
    dotnet new web
    dotnet add package Microsoft.Extensions.Azure
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    ```bash
    mkdir logstream
    cd logstream
    npm init -y
    npm install --save express
    npm install --save ws
    npm install --save node-fetch
    npm install --save @azure/web-pubsub
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    ```bash
    mkdir logstream
    cd logstream

    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows

    pip install azure-messaging-webpubsubservice
    ```
    
    ---
    
2.  Cree el servidor para hospedar `/negotiate` API y la página web.

    # <a name="c"></a>[C#](#tab/csharp)

    Actualice `Startup.cs` con el código siguiente.
    - Actualice el método `ConfigureServices` para agregar el cliente de servicio y lea la cadena de conexión de la configuración. 
    - Actualice el método `Configure` para agregar `app.UseStaticFiles();` antes de `app.UseRouting();` para admitir los archivos estáticos. 
    - Y actualice `app.UseEndpoints` para generar el token de acceso de cliente con solicitudes `/negotiate`.

    ```csharp
    using Azure.Messaging.WebPubSub;

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    
    namespace logstream
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
    
            public IConfiguration Configuration { get; }
    
            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "stream");
                });
            }
    
            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }
    
                app.UseStaticFiles();
    
                app.UseRouting();
    
                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapGet("/negotiate", async context =>
                    {
                        var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
                        var response = new
                        {
                            url = serviceClient.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" }).AbsoluteUri
                        };
                        await context.Response.WriteAsJsonAsync(response);
                    });
                });
            }
        }
    }
    
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Cree `server.js` y agregue el código siguiente:

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    let endpoint = new WebPubSubServiceClient(process.argv[2], 'stream');
    const app = express();

    app.get('/negotiate', async (req, res) => {
      let token = await endpoint.getAuthenticationToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      res.send({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    Cree `server.py` para hospedar `/negotiate` API y la página web.

    ```python
    import json
    import sys
    from http.server import HTTPServer, SimpleHTTPRequestHandler
    from azure.messaging.webpubsubservice import (
        build_authentication_token
    )

    class Resquest(SimpleHTTPRequestHandler):
        def do_GET(self):
            if self.path == '/':
                self.path = 'public/index.html'
                return SimpleHTTPRequestHandler.do_GET(self)
            elif self.path == '/negotiate':
                token = build_authentication_token(sys.argv[1], 'stream', roles=['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream'])
                print(token)
                self.send_response(200)
                self.send_header('Content-Type', 'application/json')
                self.end_headers()
                self.wfile.write(json.dumps({
                    'url': token['url']
                }).encode())

    if __name__ == '__main__':
        if len(sys.argv) != 2:
            print('Usage: python server.py <connection-string>')
            exit(1)

        server = HTTPServer(('localhost', 8080), Resquest)
        print('server started')
        server.serve_forever()
    ```

    ---
    
3.  Creación de la página web

    # <a name="c"></a>[C#](#tab/csharp)
    Cree una página HTML con el contenido siguiente y guárdela como `wwwroot/index.html`:
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Cree una página HTML con el contenido siguiente y guárdela como `public/index.html`:

    # <a name="python"></a>[Python](#tab/python)

    Cree una página HTML con el contenido siguiente y guárdela como `public/index.html`:
    
    ---
    
    ```html
    <html>

    <body>
      <div id="output"></div>
      <script>
        (async function () {
          let res = await fetch('/negotiate')
          let data = await res.json();
          let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
          ws.onopen = () => {
            console.log('connected');
          };

          let output = document.querySelector('#output');
          ws.onmessage = event => {
            let d = document.createElement('p');
            d.innerText = event.data;
            output.appendChild(d);
          };
        })();
      </script>
    </body>

    </html>
    ```

    Simplemente se conecta al servicio e imprime todos los mensajes que se reciban en la página. El principal cambio es que especificamos el subprotocolo al crear la conexión WebSocket.

4. Ejecute el servidor.
    # <a name="c"></a>[C#](#tab/csharp)
    Usamos la herramienta [Secret Manager](/aspnet/core/security/app-secrets#secret-manager) para .NET Core para establecer la cadena de conexión. Ejecute el siguiente comando, y reemplace `<connection_string>` por el que se ha capturado en el [paso anterior](#get-the-connectionstring-for-future-use) y abra http://localhost:5000/index.html en el explorador:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    dotnet run
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    Ejecute el siguiente comando y reemplace `<connection-string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use) y abra http://localhost:8080 en el explorador:

    ```bash
    
    node server "<connection-string>"
    ```
    
    # <a name="python"></a>[Python](#tab/python)

    Ejecute el siguiente comando y reemplace `<connection-string>` por el elemento **ConnectionString** capturado en el [paso anterior](#get-the-connectionstring-for-future-use) y abra http://localhost:8080 en el explorador:

    ```bash
    python server.py "<connection-string>"
    ```
    ---

    Si usa Chrome, puede presionar F12 o hacer clic con el botón derecho en **Inspeccionar** -> **Herramientas de desarrollo** y seleccione la pestaña **Red**. Cargue la página web y verá que se ha establecido la conexión de WebSocket. Haga clic para inspeccionar la conexión de WebSocket y podrá ver a continuación que el mensaje del evento `connected` se ha recibido en el cliente. Verá que puede obtener el valor de `connectionId` generado para este cliente.
    
    ```json
    {"type":"system","event":"connected","userId":null,"connectionId":"<the_connection_id>"}
    ```

Puede ver que, con la ayuda del subprotocolo, puede obtener algunos metadatos de la conexión cuando la conexión es `connected`.

Tenga en cuenta también que, en lugar de un texto sin formato, el cliente ahora recibe un mensaje JSON que contiene más información, como cuál es el tipo de mensaje y de dónde es. Por consiguiente, esta información se puede usar para realizar más procesamiento en el mensaje (por ejemplo, mostrar el mensaje en un estilo diferente si es de otro origen), que puede encontrar en las secciones posteriores.

## <a name="publish-messages-from-client"></a>Publicación de mensajes desde el cliente

En el tutorial para la [creación de una aplicación de chat](./tutorial-build-chat.md), cuando el cliente envíe un mensaje a través de la conexión de WebSocket, desencadenará un evento de usuario en el servidor. Con el subprotocolo, el cliente tendrá más funcionalidades mediante el envío de un mensaje JSON. Por ejemplo, se pueden publicar mensajes directamente desde el cliente a otros clientes,

lo que resultará útil si se desea transmitir en secuencias una gran cantidad de datos a otros clientes en tiempo real. Vamos a usar esta característica para compilar una aplicación de streaming de registros, que pueda hacer streaming de registros de la consola al explorador en tiempo real.

1. Creación del programa de streaming
 
    # <a name="c"></a>[C#](#tab/csharp)
    Cree un programa `stream`:
    ```bash
    mkdir stream
    cd stream
    dotnet new console
    ```
    
    Actualice `Program.cs` con el siguiente contenido:
    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.WebSockets;
    using System.Text;
    using System.Text.Json;
    using System.Threading.Tasks;
    
    namespace stream
    {
        class Program
        {
            private static readonly HttpClient http = new HttpClient();
            static async Task Main(string[] args)
            {
                // Get client url from remote
                var stream = await http.GetStreamAsync("http://localhost:5000/negotiate");
                var url = (await JsonSerializer.DeserializeAsync<ClientToken>(stream)).url;
                var client = new ClientWebSocket();
                client.Options.AddSubProtocol("json.webpubsub.azure.v1");
    
                await client.ConnectAsync(new Uri(url), default);
    
                Console.WriteLine("Connected.");
                var streaming = Console.ReadLine();
                while (streaming != null)
                {
                    if (!string.IsNullOrEmpty(streaming))
                    {
                        var message = JsonSerializer.Serialize(new
                        {
                            type = "sendToGroup",
                            group = "stream",
                            data = streaming + Environment.NewLine,
                        });
                        Console.WriteLine("Sending " + message);
                        await client.SendAsync(Encoding.UTF8.GetBytes(message), WebSocketMessageType.Text, true, default);
                    }
    
                    streaming = Console.ReadLine();
                }
    
                await client.CloseAsync(WebSocketCloseStatus.NormalClosure, null, default);
            }
    
            private sealed class ClientToken
            {
                public string url { get; set; }
            }
        }
    }
    
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    Cree `stream.js` con el siguiente contenido.
    
    ```javascript
    const WebSocket = require('ws');
    const fetch = require('node-fetch');

    async function main() {
      let res = await fetch(`http://localhost:8080/negotiate`);
      let data = await res.json();
      let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
      ws.on('open', () => {
        process.stdin.on('data', data => {
          ws.send(JSON.stringify({
            type: 'sendToGroup',
            group: 'stream',
            dataType: 'text',
            data: data.toString()
          }));
          process.stdout.write(data);
        });
      });
      process.stdin.on('close', () => ws.close());
    }

    main();
    ```

    El código anterior crea una conexión de WebSocket al servicio y, después, cada vez que recibe algunos datos usa `ws.send()` para publicarlos. Para publicar en otros usuarios, solo tiene que establecer `type` en `sendToGroup` y especificar un nombre de grupo en el mensaje.

    # <a name="python"></a>[Python](#tab/python)

    Abra otra ventana de Bash para el programa `stream` e instale la dependencia `websockets`:

    ```bash
    mkdir stream
    cd stream

    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows
    pip install websockets
    ```

    Cree `stream.py` con el siguiente contenido.

    ```python
    import asyncio
    import sys
    import threading
    import time
    import websockets
    import requests
    import json


    async def connect(url):
        async with websockets.connect(url, subprotocols=['json.webpubsub.azure.v1']) as ws:
            print('connected')
            id = 1
            while True:
                data = input()
                payload = {
                    'type': 'sendToGroup',
                    'group': 'stream',
                    'dataType': 'text',
                    'data': str(data + '\n'),
                    'ackId': id
                }
                id = id + 1
                await ws.send(json.dumps(payload))
                await ws.recv()

    res = requests.get('http://localhost:8080/negotiate').json()

    try:
        asyncio.get_event_loop().run_until_complete(connect(res['url']))
    except KeyboardInterrupt:
        pass

    ```

    El código anterior crea una conexión de WebSocket al servicio y, después, cada vez que recibe algunos datos usa `ws.send()` para publicarlos. Para publicar en otros usuarios, solo tiene que establecer `type` en `sendToGroup` y especificar un nombre de grupo en el mensaje.
    
    ---
    
    Puede ver que hay un nuevo concepto de "grupo" aquí. El grupo es un concepto lógico en un centro en el que puede publicar mensajes a un grupo de conexiones. En un centro, puede tener varios grupos y un cliente puede suscribirse a más de uno a la vez. Al usar un subprotocolo, solo puede publicar en un grupo, en lugar de retransmitir en todo el centro. Para más información sobre los términos, consulte los [conceptos básicos](./key-concepts.md).

2.  Puesto que aquí se usa grupo, también es necesario actualizar la página web `index.html` para unirse al grupo cuando se establece la conexión de WebSocket dentro de la devolución de llamada `ws.onopen`.
    
    ```javascript
    ws.onopen = () => {
      console.log('connected');
      ws.send(JSON.stringify({
        type: 'joinGroup',
        group: 'stream'
      }));
    };
    ```

    Para ver que el cliente se une al grupo, envíe un mensaje del tipo `joinGroup`.

3.  Actualice también ligeramente la lógica de devolución de llamada `ws.onmessage` para analizar la respuesta JSON e imprimir los mensajes solo desde el grupo `stream` para que actúe como impresora de streaming en vivo.

    ```javascript
    ws.onmessage = event => {
      let message = JSON.parse(event.data);
      if (message.type === 'message' && message.group === 'stream') {
        let d = document.createElement('span');
        d.innerText = message.data;
        output.appendChild(d);
        window.scrollTo(0, document.body.scrollHeight);
      }
    };
    ```

4.  Por motivos de seguridad, de forma predeterminada ningún cliente puede publicar en un grupo, ni suscribirse a él, por sí mismo. Por lo tanto, es posible que haya observado que establecemos `roles` en el cliente al generar el token:

    # <a name="c"></a>[C#](#tab/csharp)
    Establezca `roles` cuando `GenerateClientAccessUri` en `Startup.cs` sea como se muestra a continuación:
    ```csharp
    serviceClient.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" })
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    Agregue `roles` cuando `getAuthenticationToken` en `server.js` sea como se muestra a continuación:

    ```javascript
    app.get('/negotiate', async (req, res) => {
      let token = await endpoint.getAuthenticationToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      ...
    });
    
    ```
    
    # <a name="python"></a>[Python](#tab/python)
    
    Actualice el código de generación de tokens para proporcionar al cliente `roles` cuando `build_authentication_token` esté en `server.py`:

    ```python
    token = build_authentication_token(sys.argv[1], 'stream', roles=['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream'])
    
    ```
    ---
    

5.  Por último, aplique también algún estilo a `index.html` para que tenga una apariencia atractiva.

    ```html
    <html>

      <head>
        <style>
          #output {
            white-space: pre;
            font-family: monospace;
          }
        </style>
      </head>
    ```

Ahora ejecute el código siguiente y escriba cualquier texto, y se mostrarán en el explorador en tiempo real:

# <a name="c"></a>[C#](#tab/csharp)

```bash
ls -R | dotnet run

# Or call `dir /s /b | dotnet run` when you are using CMD under Windows

```

O bien, ralentícelo para que pueda ver que los datos se transmiten en secuencias al explorador en tiempo real:

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | dotnet run
```

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code-csharp].

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`node stream`

También puede usar esta aplicación para canalizar cualquier salida de otra aplicación de consola y transmitirla en secuencias al explorador. Por ejemplo:

```bash
ls -R | node stream

# Or call `dir /s /b | node stream` when you are using CMD under Windows
```

O bien, ralentícelo para que pueda ver que los datos se transmiten en secuencias al explorador en tiempo real:

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | node stream
```

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code-js].

# <a name="python"></a>[Python](#tab/python)

Ahora puede ejecutar `python stream.py` y escribir cualquier texto, y se mostrará en el explorador en tiempo real.

También puede usar esta aplicación para canalizar cualquier salida de otra aplicación de consola y transmitirla en secuencias al explorador. Por ejemplo:

```bash
ls -R | python stream.py

# Or call `dir /s /b | python stream.py` when you are using CMD under Windows
```

O bien, ralentícelo para que pueda ver que los datos se transmiten en secuencias al explorador en tiempo real:

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | python stream.py
```

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code-python].


---


## <a name="next-steps"></a>Pasos siguientes

En este tutorial se proporciona una idea básica de cómo conectarse al servicio Web PubSub y cómo publicar mensajes en los clientes conectados mediante un subprotocolo.

En otros tutoriales podrá profundizar más en cómo usar el servicio.

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://aka.ms/awps/samples).

[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/logstream/

[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/logstream/

[code-python]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python/logstream/

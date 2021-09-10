---
title: 'Tutorial: Creación de una aplicación de chat con el servicio Azure Web PubSub'
description: Tutorial en el que se explica cómo crear una aplicación de chat con el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: cf3f6f174cc5302b4215db21ec7362401b3454e9
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829742"
---
# <a name="tutorial-create-a-chat-app-with-azure-web-pubsub-service"></a>Tutorial: Creación de una aplicación de chat con el servicio Azure Web PubSub

En el [tutorial sobre publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), ha aprendido los conceptos básicos de la publicación de mensajes y la suscripción a ellos con Azure Web PubSub. En este tutorial, conocerá y aprenderá a usar el sistema de eventos de Azure Web PubSub para compilar una aplicación web completa con funcionalidad de comunicación en tiempo real. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una instancia del servicio Web PubSub
> * Configurar opciones del controlador de eventos para Azure Web PubSub
> * Compilar una aplicación de chat en tiempo real

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Esta configuración requiere la versión 2.22.0, o cualquier versión posterior, de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-an-azure-web-pubsub-instance"></a>Creación de una instancia de Azure Web PubSub

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Creación de una instancia de Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>Obtención de ConnectionString para usarlo en el futuro

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

Copie el elemento **ConnectionString** capturado; se usará más adelante en este tutorial como el valor de `<connection_string>`.

## <a name="set-up-the-project"></a>Configuración del proyecto

### <a name="prerequisites"></a>Prerequisites

# <a name="c"></a>[C#](#tab/csharp)

* [ASP.NET Core 3.1 o una versión posterior](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x o una versión posterior](https://nodejs.org)

# <a name="java"></a>[Java](#tab/java)

- [Kit de desarrollo de Java (JDK)](/java/azure/jdk/), versión 8 o posterior
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="create-the-application"></a>Creación de la aplicación

En Azure Web PubSub, hay dos roles: servidor y cliente. Este concepto es similar a los roles de servidor y cliente de una aplicación web. El servidor es responsable de administrar los clientes, escuchar y responder a los mensajes del cliente; por su parte, el cliente es responsable de enviar los mensajes del usuario al servidor, recibir los mensajes del servidor y presentarlos al usuario final.

En este tutorial, compilaremos una aplicación web de chat en tiempo real. En una aplicación web real, la responsabilidad del servidor también incluye autenticar los clientes y proporcionar las páginas web estáticas para la interfaz de usuario de la aplicación. 

# <a name="c"></a>[C#](#tab/csharp)

Usaremos [ASP.NET Core](/aspnet/core) para hospedar las páginas web y administrar las solicitudes entrantes.

En primer lugar, vamos a crear una aplicación ASP.NET Core vacía.

1.  Creación de una aplicación web

    ```bash
    dotnet new web
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2.  Después, agregue `app.UseStaticFiles();` antes de `app.UseRouting();` en `Startup.cs` para admitir los archivos estáticos. Quite el valor `endpoints.MapGet` predeterminado dentro de `app.UseEndpoints`.

    ```csharp
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
        });
    }
    ```

3.  Cree también un archivo HTML y guárdelo como `wwwroot/index.html`; lo usaremos para la interfaz de usuario de la aplicación de chat más adelante.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Ejecute `dotnet run --urls http://localhost:8080` para probar el servidor y acceda a http://localhost:8080/index.html en el explorador.

Es posible que recuerde que en el [tutorial de publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), el suscriptor usa una API en el SDK de Web PubSub para generar un token de acceso a partir de la cadena de conexión y lo usa para conectarse al servicio. Normalmente, esto no es seguro en una aplicación real, ya que la cadena de conexión tiene privilegios elevados para realizar cualquier operación en el servicio, por lo que no desea compartirla con ningún cliente. Vamos a cambiar este proceso de generación de tokens de acceso a una API REST en el lado servidor, para que el cliente pueda llamar a esta API para solicitar un token de acceso cada vez que necesite conectarse, sin necesidad de incluir la cadena de conexión.

1.  Instale Microsoft.Extensions.Azure.

    ```bash
    dotnet add package Microsoft.Extensions.Azure
    ```
2. Llame al cliente de servicio dentro de `ConfigureServices` y no olvide reemplazar `<connection_string>` con el valor que usa en sus servicios.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureClients(builder =>
        {
            builder.AddWebPubSubServiceClient("<connection_string>", "chat");
        });
    }
    ```
2.  Agregue una API `/negotiate` al servidor en `app.UseEndpoints` para generar el token.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/negotiate", async context =>
        {
            var id = context.Request.Query["id"];
            if (id.Count != 1)
            {
                context.Response.StatusCode = 400;
                await context.Response.WriteAsync("missing user id");
                return;
            }
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            await context.Response.WriteAsync(serviceClient.GenerateClientAccessUri(userId: id).AbsoluteUri);
        });
    });
    ```

    Este código de generación de tokens es similar al que se usó en el [tutorial de publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), salvo que se pasa un argumento más (`userId`) al generar el token. El identificador de usuario se puede usar para obtener la identidad del cliente, de modo que, cuando reciba un mensaje, sabrá de dónde viene.

    Para probar esta API, ejecute `dotnet run --urls http://localhost:8080` y acceda a `http://localhost:8080/negotiate?id=<user-id>`; obtendrá la dirección URL completa del servicio Azure Web PubSub con un token de acceso.

3.  A continuación, actualice `index.html` para incluir el siguiente script y así obtener el token del servidor y conectarse al servicio.
 
    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Si usa Chrome, puede probarlo abriendo la página principal. Introduzca el nombre de usuario. Presione F12 para abrir la ventana Herramientas de desarrollo, cambie a la pestaña **Console** (Consola) y verá que se imprime `connected` en la consola del explorador.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Usaremos [express.js](https://expressjs.com/), un marco web popular para node.js, para lograr este trabajo.

En primer lugar, cree una aplicación de Express vacía.

1.  Instale express.js.

    ```bash
    npm init -y
    npm install --save express
    ```

2.  A continuación, cree un servidor de Express y guárdelo como `server.js`.

    ```javascript
    const express = require('express');

    const app = express();
    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

3.  Cree también un archivo HTML y guárdelo como `public/index.html`; lo usaremos para la interfaz de usuario de la aplicación de chat más adelante.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Ejecute `node server` para probar el servidor y acceda a http://localhost:8080 en el explorador.


Es posible que recuerde que en el [tutorial de publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), el suscriptor usa una API en el SDK de Web PubSub para generar un token de acceso a partir de la cadena de conexión y lo usa para conectarse al servicio. Normalmente, esto no es seguro en una aplicación real, ya que la cadena de conexión tiene privilegios elevados para realizar cualquier operación en el servicio, por lo que no desea compartirla con ningún cliente. Vamos a cambiar este proceso de generación de tokens de acceso a una API REST en el lado servidor, para que el cliente pueda llamar a esta API para solicitar un token de acceso cada vez que necesite conectarse, sin necesidad de incluir la cadena de conexión.

1.  Instale el SDK de Azure Web PubSub.

    ```bash
    npm install --save @azure/web-pubsub
    ```

2.  Agregue una API `/negotiate` al servidor para generar el token.

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const app = express();
    const hubName = 'chat';

    let serviceClient = new WebPubSubServiceClient(process.argv[2], hubName);

    app.get('/negotiate', async (req, res) => {
      let id = req.query.id;
      if (!id) {
        res.status(400).send('missing user id');
        return;
      }
      let token = await serviceClient.getAuthenticationToken({ userId: id });
      res.json({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    Este código de generación de tokens es similar al que se usó en el [tutorial de publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), salvo que se pasa un argumento más (`userId`) al generar el token. El identificador de usuario se puede usar para obtener la identidad del cliente, de modo que, cuando reciba un mensaje, sabrá de dónde viene.

    Para probar esta API, ejecute `node server "<connection-string>"` y acceda a `http://localhost:8080/negotiate?id=<user-id>`; obtendrá la dirección URL completa del servicio Azure Web PubSub con un token de acceso.

3.  A continuación, actualice `index.html` con el siguiente script para obtener el token del servidor y conectarse al servicio.
 
    ```html

    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let data = await res.json();
          let ws = new WebSocket(data.url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Si usa Chrome, puede probarlo abriendo la página principal. Introduzca el nombre de usuario. Presione F12 para abrir la ventana Herramientas de desarrollo, cambie a la pestaña **Console** (Consola) y verá que se imprime `connected` en la consola del explorador.

# <a name="java"></a>[Java](#tab/java)

Usaremos el marco web [Javalin](https://javalin.io/) para hospedar las páginas web y administrar las solicitudes entrantes.

1. En primer lugar, vamos a usar Maven para crear una nueva aplicación `webpubsub-tutorial-chat` y cambiar a la carpeta *webpubsub-tutorial-chat*:

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=webpubsub-tutorial-chat --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-tutorial-chat
    ```

2. Agregamos la dependencia del marco web `javalin` en el nodo `dependencies` de `pom.xml`:

    * `javalin`: marco web simple para Java
    * `slf4j-simple`: registrador para Java

    ```xml
    <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
    <dependency>
        <groupId>io.javalin</groupId>
        <artifactId>javalin</artifactId>
        <version>3.13.6</version>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.30</version>
    </dependency>
    ```

3. Vamos al directorio */src/main/java/com/webpubsub/tutorial*, abrimos el archivo *App.java* en el editor y usamos `Javalin.create` para servir archivos estáticos:

    ```java
    package com.webpubsub.tutorial;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
        }
    }
    ```

    En función de la configuración, es posible que tenga que establecer explícitamente el nivel de lenguaje en Java 8. Esto se puede hacer en pom.xml. Agregue el siguiente fragmento:
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```

4. Vamos a crear un archivo HTML y guardarlo en */src/main/resources/public/index.html*. Lo usaremos para la interfaz de usuario de la aplicación de chat más adelante.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

Puede probar el servidor ejecutando el siguiente comando en el directorio que contiene el archivo *pom.xml* y accediendo a http://localhost:8080 en el explorador.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false
```

Es posible que recuerde que en el [tutorial de publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), el suscriptor usa una API en el SDK de Web PubSub para generar un token de acceso a partir de la cadena de conexión y lo usa para conectarse al servicio. Normalmente, esto no es seguro en una aplicación real, ya que la cadena de conexión tiene privilegios elevados para realizar cualquier operación en el servicio, por lo que no desea compartirla con ningún cliente. Vamos a cambiar este proceso de generación de tokens de acceso a una API REST en el lado servidor, para que el cliente pueda llamar a esta API para solicitar un token de acceso cada vez que necesite conectarse, sin necesidad de incluir la cadena de conexión.

1. Agregue la dependencia del SDK de Azure Web PubSub en el nodo `dependencies` de `pom.xml`:

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

2. Agregue una API `/negotiate` al archivo `App.java` para generar el token:

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubClientBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.models.GetAuthenticationTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubAuthenticationToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                String id = ctx.queryParam("id");
                if (id == null) {
                    ctx.status(400);
                    ctx.result("missing user id");
                    return;
                }
                GetAuthenticationTokenOptions option = new GetAuthenticationTokenOptions();
                option.setUserId(id);
                WebPubSubAuthenticationToken token = client.getAuthenticationToken(option);
                ctx.result(token.getUrl());
                return;
            });
        }
    }
    ```

    Este código de generación de tokens es similar al que se usó en el [tutorial de publicación de mensajes y suscripción a ellos](./tutorial-pub-sub-messages.md), salvo que se llama al método `setUserId` para establecer el ID de usuario al generar el token. El identificador de usuario se puede usar para obtener la identidad del cliente, de modo que, cuando reciba un mensaje, sabrá de dónde viene.

    Para probar esta API, ejecute el siguiente comando, reemplazando `<connection_string>` por el valor de **ConnectionString** recuperado en el [paso anterior](#get-the-connectionstring-for-future-use), y acceda a `http://localhost:8080/negotiate?id=<user-id>`; obtendrá la dirección URL completa del servicio Azure Web PubSub con un token de acceso.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```

3. A continuación, actualice `index.html` con el siguiente script para obtener el token del servidor y conectarse al servicio.

    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Si usa Chrome, puede probarlo abriendo la página principal. Introduzca el nombre de usuario. Presione F12 para abrir la ventana Herramientas de desarrollo, cambie a la pestaña **Console** (Consola) y verá que se imprime `connected` en la consola del explorador.

---

## <a name="handle-events"></a>Control de eventos

En Azure Web PubSub, cuando suceden ciertas actividades en el lado cliente (por ejemplo, se conecta o se desconecta un cliente), el servicio envía notificaciones al servidor para que pueda reaccionar ante estos eventos.

Los eventos se entregan al servidor en forma de webhook. El servidor de aplicaciones sirve y expone el webhook, que se registra en el lado del servicio Azure Web PubSub. El servicio invoca los webhooks cada vez que se produce un evento.

Azure Web PubSub sigue [CloudEvents](./reference-cloud-events.md) para describir los datos del evento. 

# <a name="c"></a>[C#](#tab/csharp)
Por ahora, debe implementar el controlador de eventos por su cuenta en C#; los pasos son sencillos siguiendo la [especificación del protocolo](./reference-cloud-events.md) y se ilustran a continuación.

1. Agregue controladores de eventos dentro de `UseEndpoints`. Especifique la ruta de acceso del punto de conexión para los eventos; por ejemplo, `/eventhandler`. 

2. En primer lugar, nos gustaría administrar las solicitudes OPTIONS de protección contra abusos, comprobaremos si el encabezado contiene el encabezado `WebHook-Request-Origin` y devolveremos el encabezado `WebHook-Allowed-Origin`. Para simplificar con fines de demostración, devolveremos `*` para permitir todos los orígenes.
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

3. A continuación, nos gustaría comprobar si las solicitudes entrantes son los eventos que esperamos. Supongamos que ahora nos interesa el evento `connected` del sistema, que debe contener el encabezado `ce-type` como `azure.webpubsub.sys.connected`. Agregamos la lógica para la protección contra abusos:
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

En el código anterior, simplemente imprimimos un mensaje en la consola cuando se conecta un cliente. Puede advertir que usamos `context.Request.Headers["ce-userId"]` para poder ver la identidad del cliente conectado.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Si usa el SDK de Web PubSub, ya hay una implementación para analizar y procesar el esquema de CloudEvents, por lo que no es necesario tratar estos detalles.

Agregue el código siguiente para exponer una API REST en `/eventhandler` (lo que se realiza mediante el middleware Express proporcionado por el SDK de Web PubSub) para controlar el evento de cliente conectado:

```bash
npm install --save @azure/web-pubsub-express
```

```javascript
const { WebPubSubEventHandler } = require('@azure/web-pubsub-express');

let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  onConnected: async req => {
    console.log(`${req.context.userId} connected`);
  }
});

app.use(handler.getMiddleware());
```

En el código anterior, simplemente imprimimos un mensaje en la consola cuando se conecta un cliente. Puede advertir que usamos `req.context.userId` para poder ver la identidad del cliente conectado.

# <a name="java"></a>[Java](#tab/java)
Por ahora, debe implementar el controlador de eventos por su cuenta en Java; los pasos son sencillos siguiendo la [especificación del protocolo](./reference-cloud-events.md) y se ilustran a continuación.

1. Agregue el controlador HTTP para la ruta de acceso del controlador de eventos; por ejemplo, `/eventhandler`. 

2. En primer lugar, nos gustaría administrar las solicitudes OPTIONS de protección contra abusos, comprobaremos si el encabezado contiene el encabezado `WebHook-Request-Origin` y devolveremos el encabezado `WebHook-Allowed-Origin`. Para simplificar con fines de demostración, devolveremos `*` para permitir todos los orígenes.
    ```java
    
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });
    ```

3. A continuación, nos gustaría comprobar si las solicitudes entrantes son los eventos que esperamos. Supongamos que ahora nos interesa el evento `connected` del sistema, que debe contener el encabezado `ce-type` como `azure.webpubsub.sys.connected`. Agregamos la lógica para la protección contra abusos:
    ```java
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        }
        ctx.status(200);
    });

    ```

En el código anterior, simplemente imprimimos un mensaje en la consola cuando se conecta un cliente. Puede advertir que usamos `ctx.header("ce-userId")` para poder ver la identidad del cliente conectado.

---

## <a name="set-up-the-event-handler"></a>Configuración del controlador de eventos

### <a name="expose-localhost"></a>Exposición de localhost

A continuación, es necesario establecer la dirección URL del webhook en el servicio para que pueda saber dónde debe llamar cuando hay un nuevo evento. Hay un problema, sin embargo, y es que nuestro servidor se ejecuta en localhost, por lo que no tiene un punto de conexión accesible desde Internet. Aquí usaremos [ngrok](https://ngrok.com/) para exponer el localhost a Internet.

1.  En primer lugar, descargue ngrok desde https://ngrok.com/download y extraiga el ejecutable en la carpeta local o en la carpeta bin del sistema.
2.  Iniciar ngrok
    
    ```bash
    ngrok http 8080
    ```

ngrok imprimirá una dirección URL (`https://<domain-name>.ngrok.io`) a la que se puede acceder desde Internet.

### <a name="set-event-handler"></a>Establecimiento del controlador de eventos

A continuación, actualizamos el controlador de eventos de servicio y establecemos la dirección URL del webhook.

Use el comando [az webpubsub event-handler hub](/cli/azure/webpubsub/event-handler/hub) de la CLI de Azure para actualizar la configuración del controlador de eventos:

  > [!Important]
  > Reemplace &lt;your-unique-resource-name&gt; por el nombre del recurso de Web PubSub que creó en los pasos anteriores.
  > Reemplace &lt;domain-name&lt; por el nombre que ha imprimido ngrok.

```azurecli-interactive
az webpubsub event-handler hub update -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name chat --template url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event-pattern="connected"
```


Una vez completada la actualización, abra la página principal http://localhost:8080/index.html, introduzca el nombre de usuario y verá el mensaje "connected" impreso en la consola del servidor.

## <a name="handle-message-events"></a>Control de eventos de mensaje

Además de los eventos del sistema como `connected` o `disconnected`, el cliente también puede enviar mensajes a través de la conexión de WebSocket; estos mensajes se entregarán al servidor como un tipo especial de evento denominado evento `message`. Podemos usar este evento para recibir mensajes de un cliente y difundirlos a todos los clientes para que puedan hablar entre sí.

# <a name="c"></a>[C#](#tab/csharp)

El valor `ce-type` de `message` siempre es `azure.webpubsub.user.message`; para conocer los detalles, consulte el [evento message](./reference-cloud-events.md#message).

1. Control de eventos de mensaje

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
                else if (context.Request.Headers["ce-type"] == "azure.webpubsub.user.message")
                {
                    using var stream = new StreamReader(context.Request.Body);
                    await serviceClient.SendToAllAsync($"[{userId}] {await stream.ReadToEndAsync()}");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

    Este controlador de eventos usa `WebPubSubServiceClient.SendToAllAsync()` para difundir el mensaje recibido a todos los clientes.

2.  Actualice `index.html` para agregar la lógica que envía el mensaje del usuario al servidor y muestra los mensajes recibidos en la página.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    Puede ver en el código anterior que usamos `WebSocket.send()` para enviar el mensaje y `WebSocket.onmessage` para escucharlo desde el servicio.

3.  Por último, actualice el controlador `onConnected` para difundir el evento de conexión a todos los clientes y que puedan ver quién se ha unido al salón de chat.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    await serviceClient.SendToAllAsync($"[SYSTEM] {userId} joined.");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

Ahora ejecute el servidor con `dotnet run --urls http://localhost:8080` y abra varias instancias del explorador para acceder a http://localhost:8080/index.html; después, ya puede chatear entre las instancias.

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code-csharp].

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. Agregue un nuevo controlador `handleUserEvent`.

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, ['*'], {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') 
            await serviceClient.sendToAll(`[${req.context.userId}] ${req.data}`, { contentType: 'text/plain' });
        res.success();
      }
    });
    ```

    Este controlador de eventos usa `WebPubSubServiceClient.sendToAll()` para difundir el mensaje recibido a todos los clientes.

    Puede ver que `handleUserEvent` también tiene un objeto `res`, donde puede devolver un mensaje al remitente del evento. Aquí simplemente llamaremos a `res.success()` para que el webhook devuelva 200 (tenga en cuenta que esta llamada es necesaria incluso si no desea devolver nada al cliente; de lo contrario, el webhook nunca vuelve y se cierra la conexión de cliente).

2.  Actualice `index.html` para agregar la lógica que envía el mensaje del usuario al servidor y muestra los mensajes recibidos en la página.

    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            ws.onmessage = event => {
              let m = document.createElement('p');
              m.innerText = event.data;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
      </html>
    ```

    Puede ver en el código anterior que usamos `WebSocket.send()` para enviar el mensaje y `WebSocket.onmessage` para escucharlo desde el servicio.

3. `sendToAll` acepta el objeto como entrada y envía texto JSON a los clientes. En escenarios reales, es probable que necesitemos un objeto complejo para llevar más información sobre el mensaje. Por último, actualice los controladores para difundir objetos JSON a todos los clientes:

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, ['*'], {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
        await serviceClient.sendToAll({
          type: "system",
          message: `${req.context.userId} joined`
        });
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') {
          await serviceClient.sendToAll({
            from: req.context.userId,
            message: req.data
          });
        }
        res.success();
      }
    });
    ```

4. Y actualice el cliente para analizar los datos JSON:
    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            
            ws.onmessage = event => {
              let m = document.createElement('p');
              let data = JSON.parse(event.data);
              m.innerText = `[${data.type || ''}${data.from || ''}] ${data.message}`;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
    </html>
    ```

Ahora, ejecute el servidor y abra varias instancias del explorador; después, ya puede chatear entre las instancias.

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code-js].

# <a name="java"></a>[Java](#tab/java)

El valor `ce-type` de `message` siempre es `azure.webpubsub.user.message`; para conocer los detalles, consulte el [evento message](./reference-cloud-events.md#message).

1. Control de eventos de mensaje

    ```java
    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });
    ```

    Este controlador de eventos usa `client.sendToAll()` para difundir el mensaje recibido a todos los clientes.

2.  Actualice `index.html` para agregar la lógica que envía el mensaje del usuario al servidor y muestra los mensajes recibidos en la página.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    Puede ver en el código anterior que usamos `WebSocket.send()` para enviar el mensaje y `WebSocket.onmessage` para escucharlo desde el servicio.

3.  Por último, actualice el controlador de eventos `connected` para difundir el evento de conexión a todos los clientes y que puedan ver quién se ha unido al salón de chat.

    ```java

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            client.sendToAll(String.format("[SYSTEM] %s joined", id), WebPubSubContentType.TEXT_PLAIN);
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });

    ```

Ahora, ejecute el servidor con el comando siguiente y abra varias instancias del explorador; después, ya puede chatear entre las instancias.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
```

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code-java].

---

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se proporciona una idea básica de cómo funciona el sistema de eventos en el servicio Azure Web PubSub.

En otros tutoriales podrá profundizar más en cómo usar el servicio.

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://aka.ms/awps/samples).


[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp/
[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp/
[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp/

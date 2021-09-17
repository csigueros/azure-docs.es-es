---
title: 'Tutorial: Compilación de una aplicación de chat en tiempo real sin servidor con autenticación de cliente'
description: Tutorial en el que se explica cómo usar el servicio Azure Web PubSub y Azure Functions para crear una aplicación de chat sin servidor con autenticación de cliente.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 03/11/2021
ms.openlocfilehash: e4dd54ef01cf93ffa0bb47d4bbdccb1d14695934
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434958"
---
# <a name="tutorial-create-a-serverless-real-time-chat-app-with-azure-functions-and-azure-web-pubsub-service"></a>Tutorial: Creación de una aplicación de chat en tiempo real sin servidor con Azure Functions y el servicio Azure Web PubSub

El servicio Azure Web PubSub le ayuda a crear aplicaciones web de mensajería en tiempo real mediante WebSockets y el patrón de publicación y suscripción fácilmente. Azure Functions es una plataforma sin servidor que le permite ejecutar el código sin tener que administrar ninguna infraestructura. En este tutorial, aprenderá a usar el servicio Azure Web PubSub y Azure Functions para crear una aplicación sin servidor con mensajería en tiempo real y el patrón de publicación y suscripción.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Compilar una aplicación de chat en tiempo real sin servidor
> * Trabajar con enlaces de desencadenador de función y enlaces de salida de Web PubSub
> * Implementar la función en Azure Function App
> * Configurar la autenticación de Azure
> * Configurar el controlador de eventos de Web PubSub para enrutar los eventos y mensajes a la aplicación

## <a name="prerequisites"></a>Requisitos previos

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Un editor de código como [Visual Studio Code](https://code.visualstudio.com/)

* [Node.js](https://nodejs.org/en/download/), versión 10.x.
   > [!NOTE]
   > Para más información sobre las versiones de Node.js que se admiten, consulte la [documentación de las versiones del runtime de Azure Functions.](../azure-functions/functions-versions.md#languages)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (se prefieren la versión 3 o superiores) para ejecutar aplicaciones de Azure Functions localmente y realizar la implementación en Azure.

* [Interfaz de línea de comandos de Azure (CLI de Azure)](/cli/azure) para administrar recursos de Azure.

* (Opcional)[ngrok](https://ngrok.com/download) para exponer la función local como controlador de eventos para el servicio Web PubSub. Esto solo es opcional para la ejecución de la aplicación de función localmente.

# <a name="c"></a>[C#](#tab/csharp)

* Un editor de código, como [Visual Studio Code](https://code.visualstudio.com/).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (se prefieren la versión 3 o superiores) para ejecutar aplicaciones de Azure Functions localmente y realizar la implementación en Azure.

* [Interfaz de línea de comandos de Azure (CLI de Azure)](/cli/azure) para administrar recursos de Azure.

* (Opcional)[ngrok](https://ngrok.com/download) para exponer la función local como controlador de eventos para el servicio Web PubSub. Esto solo es opcional para la ejecución de la aplicación de función localmente.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-the-functions"></a>Creación de las funciones

1. Asegúrese de que [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) está instalado. Luego, cree un directorio vacío para el proyecto. Ejecute el comando en este directorio de trabajo.

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

1. Instale explícitamente el paquete de extensión de la función `Microsoft.Azure.WebJobs.Extensions.WebPubSub`.

   a. Quite la sección `extensionBundle` de `host.json` para habilitar la instalación de un paquete de extensión específico en el paso siguiente. O simplemente cree un archivo host.json tan simple como el siguiente.
    ```json
    {
        "version": "2.0"
    }
    ```
   b. Ejecute el comando para instalar un paquete de extensión de función específico.
    ```bash
    func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
    ```

1. Cree una función `index` para leer y hospedar una página web estática para los clientes.
    ```bash
    func new -n index -t HttpTrigger
    ```
   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Actualice `index/function.json` y copie los siguientes códigos de JSON.
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": [
                      "get",
                      "post"
                    ]
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                }
            ]
        }
        ```
   - Actualice `index/index.js` y copie los siguientes códigos.
        ```js
        var fs = require('fs');
        module.exports = function (context, req) {
            fs.readFile('index.html', 'utf8', function (err, data) {
                if (err) {
                    console.log(err);
                    context.done(err);
                }
                context.res = {
                    status: 200,
                    headers: {
                        'Content-Type': 'text/html'
                    },
                    body: data
                };
                context.done();
            });
        }
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - Actualice `index.cs` y reemplace la función `Run` por los siguientes códigos.
        ```c#
        [FunctionName("index")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
        {
            return new ContentResult
            {
                Content = File.ReadAllText("index.html"),
                ContentType = "text/html",
            };
        }
        ```

1. Cree una función `negotiate` para ayudar a los clientes a obtener la dirección URL de conexión del servicio con el token de acceso.
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    > [!NOTE]
    > En este ejemplo, se usa el encabezado de identidad de usuario `x-ms-client-principal-name` de [AAD](/azure/app-service/configure-authentication-user-identities) para recuperar `userId`. No servirá en una función local. Puede dejarlo vacío o cambiar a otras formas de obtener o generar `userId` al reproducir en local. Por ejemplo, deje que el cliente escriba un nombre de usuario y lo pase en una consulta, como `?user={$username}`, cuando llame a la función `negotiate` para obtener la dirección URL de conexión del servicio. Y en la función `negotiate`, establezca `userId` con el valor `{query.user}`.
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Actualice `negotiate/function.json` y copie los siguientes códigos de JSON.
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req"
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                },
                {
                    "type": "webPubSubConnection",
                    "name": "connection",
                    "hub": "simplechat",
                    "userId": "{headers.x-ms-client-principal-name}",
                    "direction": "in"
                }
            ]
        }
        ```
   - Actualice `negotiate/index.js` y copie los siguientes códigos.
        ```js
        module.exports = function (context, req, connection) {
            context.res = { body: connection };
            context.done();
        };
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - Actualice `negotiate.cs` y reemplace la función `Run` por los siguientes códigos.
        ```c#
        [FunctionName("negotiate")]
        public static WebPubSubConnection Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "simplechat", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");
            return connection;
        }
        ```

2. Cree una función `message` para difundir mensajes de clientes a través del servicio.
   ```bash
   func new -n message -t HttpTrigger
   ```

   > [!NOTE]
   > Esta función en realidad usa `WebPubSubTrigger`. Sin embargo, dado que el servicio todavía está en versión preliminar, `WebPubSubTrigger` no está integrado en la plantilla de la función. `HttpTrigger` se usa para inicializar la plantilla de la función y cambiar el tipo de desencadenador en el código.

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Actualice `message/function.json` y copie los siguientes códigos de JSON.
        ```json
        {
            "bindings": [
                {
                    "type": "webPubSubTrigger",
                    "direction": "in",
                    "name": "message",
                    "dataType": "binary",
                    "hub": "simplechat",
                    "eventName": "message",
                    "eventType": "user"
                },
                {
                    "type": "webPubSub",
                    "name": "webPubSubEvent",
                    "hub": "simplechat",
                    "direction": "out"
                }
            ]
        }
        ```
   - Actualice `message/index.js` y copie los siguientes códigos.
        ```js
        module.exports = async function (context, message) {
            context.bindings.webPubSubEvent = {
                "operationKind": "sendToAll",
                "message": `[${context.bindingData.connectionContext.userId}] ${message}`,
                "dataType": context.bindingData.dataType
            };
            // MessageResponse directly return to caller
            var response = { 
                "message": '[SYSTEM] ack.',
                "dataType" : "text"
            };
            return response;
        };
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - Actualice `message.cs` y reemplace la función `Run` por los siguientes códigos.
        ```c#
        [FunctionName("message")]
        public static async Task<MessageResponse> Run(
            [WebPubSubTrigger(WebPubSubEventType.User, "message")] ConnectionContext context,
            BinaryData message,
            MessageDataType dataType,
            [WebPubSub(Hub = "simplechat")] IAsyncCollector<WebPubSubOperation> operations)
        {
            await operations.AddAsync(new SendToAll
            {
                Message = BinaryData.FromString($"[{context.UserId}] {message.ToString()}"),
                DataType = dataType
            });
            return new MessageResponse
            {
                Message = BinaryData.FromString("[SYSTEM] ack"),
                DataType = MessageDataType.Text
            };
        }
        ```

3. Agregue la página única del cliente `index.html` en la carpeta raíz del proyecto y copie el contenido como se indica a continuación.
    ```html
    <html>
        <body>
            <h1>Azure Web PubSub Serverless Chat App</h1>
            <div id="login"></div>
            <p></p>
            <input id="message" placeholder="Type to chat...">
            <div id="messages"></div>
            <script>
                (async function () {
                    let authenticated = window.location.href.includes('?authenticated=true');
                    if (!authenticated) {
                        // auth
                        let login = document.querySelector("#login");
                        let link = document.createElement('a');
                        link.href = `${window.location.origin}/.auth/login/aad?post_login_redirect_url=/api/index?authenticated=true`;
                        link.text = "login";
                        login.appendChild(link);
                    }
                    else {
                        // negotiate
                        let messages = document.querySelector('#messages');
                        let res = await fetch(`${window.location.origin}/api/negotiate`, {
                            credentials: "include"
                        });
                        let url = await res.json();
                        // connect
                        let ws = new WebSocket(url.url);
                        ws.onopen = () => console.log('connected');
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
                    }
                })();
            </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    Dado que el proyecto de C# va a compilar archivos en otra carpeta de salida, es preciso que actualice `*.csproj` para que la página de contenido vaya a la par que él.
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ``

## Create and Deploy the Azure Function App

Before you can deploy your function code to Azure, you need to create 3 resources:
* A resource group, which is a logical container for related resources.
* A storage account, which is used to maintain state and other information about your functions.
* A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment and sharing of resources.

Use the following commands to create these items. 

1. If you haven't done so already, sign in to Azure:

    ```bash
    az login
    ```

1. Cree un grupo de recursos. Esta acción se puede omitirlo si se vuelve a usar el del servicio Azure Web PubSub:

    ```bash
    az group create -n WebPubSubFunction -l <REGION>
    ```

1. Cree una cuenta de almacenamiento de uso general en el grupo de recursos y la región:

    ```bash
    az storage account create -n <STORAGE_NAME> -l <REGION> -g WebPubSubFunction
    ```

1. Cree la aplicación de funciones en Azure:

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

    # <a name="c"></a>[C#](#tab/csharp)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

1. Implemente el proyecto de función en Azure:

    Después de haber creado correctamente su aplicación de funciones en Azure, estará listo para implementar el proyecto de funciones local mediante el comando [func azure functionapp publish](/azure-functions/functions-run-local).

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > Aquí vamos a implementar la configuración local `local.settings.json` junto con el parámetro del comando `--publish-local-settings`. Si usa el Emulador de Microsoft Azure Storage, puede escribir `no` para omitir la sobreescritura de este valor en Azure siguiendo el mensaje: `App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]`. Además, puede actualizar la configuración de la aplicación de funciones en **Azure Portal** -> **Configuración** -> **Configuración**.

## <a name="configure-the-web-pubsub-service-event-handler"></a>Configuración `Event Handler` del servicio Web PubSub

En este ejemplo, se usa `WebPubSubTrigger` para escuchar las solicitudes de mensajes ascendentes del servicio. Por lo tanto, Web PubSub tiene que conocer la información del punto de conexión de la función para enviar solicitudes de cliente de destino. Y la aplicación de funciones de Azure requiere una clave del sistema para la seguridad con respecto a los métodos de webhook específicos de la extensión. En el paso anterior, después de implementar la aplicación de funciones con funciones `message`, pudimos obtener la clave del sistema.

Vaya a **Azure Portal** -> Busque su recurso de Aplicación de funciones ->**Claves de aplicación** -> **Claves del sistema** ->  **`webpubsub_extension`** . Copie el valor como `<APP_KEY>`.

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="Captura de pantalla de Obtener las claves del sistema de la función.":::

Establezca `Event Handler` en el servicio Azure Web PubSub. Vaya a **Azure Portal** -> Busque su recurso de Azure Web PubSub -> **Configuración**. Agregue una nueva asignación de configuración del centro a la única función en uso, como se indica a continuación. Reemplace `<FUNCTIONAPP_NAME>` y `<APP_KEY>` por los suyos.

   - Nombre del centro: `simplechat`
   - Plantilla de dirección URL:**https://<FUNCTIONAPP_NAME>.azurewebsites.net/runtime/webhooks/webpubsub?code=<APP_KEY>**
   - Patrón de evento de usuario: *
   - Eventos del sistema: (no es necesario configurar en este ejemplo)

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="Captura de pantalla de la configuración del controlador de eventos.":::

> [!NOTE]
> Si ejecuta las funciones en local. Puede exponer la dirección URL de la función con [ngrok](https://ngrok.com/download) mediante el comando `ngrok http 7071` después del inicio de la función. Y configure `Event Handler` del servicio Web PubSub con la dirección URL: `https://<NGROK_ID>.ngrok.io/runtime/webhooks/webpubsub`. 

## <a name="configure-to-enable-client-authentication"></a>Configuración para habilitar la autenticación de cliente

Vaya a **Azure Portal** -> Busque su recurso de Aplicación de funciones ->**Autenticación**. Haga clic en **`Add identity provider`** . Establezca **App Service authentication settings** (Configuración de autenticación de App Service) en **Permitir acceso no autenticado**, para que usuarios anónimos puedan visitar la página de índice del cliente antes de redirigirlos para que se autentiquen. A continuación, **guarde** la operación.

Aquí elegimos `Microsoft` como proveedor de identificación que usará `x-ms-client-principal-name` como `userId` en la función `negotiate`. Además, puede configurar otros proveedores de identidades siguiendo los vínculos a continuación, y no olvide actualizar el valor `userId` de la función `negotiate` en consecuencia.

* [Microsoft (Azure AD)](/azure/app-service/configure-authentication-provider-aad)
* [Facebook](/azure/app-service/configure-authentication-provider-facebook)
* [Google](/azure/app-service/configure-authentication-provider-google)
* [Twitter](/azure/app-service/configure-authentication-provider-twitter)

## <a name="try-the-application"></a>Prueba de la aplicación

Ahora puede probar la página desde la aplicación de funciones: `https://<FUNCTIONAPP_NAME>.azurewebsites.net/api/index`. Consulte la instantánea a continuación.
1. Haga clic en `login` para autenticarse.
2. Escriba el mensaje en el cuadro de entrada para chatear.

En la función message, se difundirá el mensaje del autor de la llamada a todos los clientes, y se devolverá el mensaje `[SYSTEM] ack` al autor de la llamada. Por lo tanto, en la instantánea de chat del ejemplo siguiente, sabemos que los cuatro primeros mensajes son del cliente actual y los dos últimos son de otro cliente.

:::image type="content" source="media/quickstart-serverless/chat-sample.png" alt-text="Captura de pantalla del ejemplo de chat.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga los pasos que se indican a continuación para eliminar todos los recursos creados por este documento, con el fin de que no se le apliquen cargos:

1. En Azure Portal, seleccione **Grupos de recursos** en el extremo izquierdo y luego seleccione el grupo de recursos que creó. Puede usar el cuadro de búsqueda para buscar el grupo de recursos por su nombre en su lugar.

1. En la ventana que se abrirá, seleccione el grupo de recursos y luego seleccione **Eliminar grupo de recursos**.

1. En la nueva ventana, escriba el nombre del grupo de recursos que desea eliminar y, después, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a ejecutar una aplicación de chat sin servidor. Ahora, puede empezar a crear su propia aplicación. 

> [!div class="nextstepaction"]
> [Enlaces de Azure Web PubSub para Azure Functions](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Inicio rápido: Creación de una sala de chat simple con Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples).

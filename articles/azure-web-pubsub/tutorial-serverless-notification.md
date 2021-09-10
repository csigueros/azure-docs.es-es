---
title: 'Tutorial: Creación de una aplicación de notificación sin servidor mediante el servicio Azure Web PubSub y Azure Functions'
description: Tutorial en el que se explica cómo usar el servicio Azure Web PubSub y Azure Functions para compilar una aplicación de notificación sin servidor.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/24/2021
ms.openlocfilehash: 11938094e1e99dc5a8816697030f4b69d6bc9d13
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970504"
---
# <a name="tutorial-create-a-serverless-notification-app-with-azure-functions-and-azure-web-pubsub-service"></a>Tutorial: Creación de una aplicación de notificación sin servidor con Azure Functions y el servicio Azure Web PubSub

El servicio Azure Web PubSub le ayuda a crear aplicaciones web de mensajería en tiempo real mediante WebSockets. Azure Functions es una plataforma sin servidor que le permite ejecutar el código sin tener que administrar ninguna infraestructura. En este tutorial, aprenderá a usar el servicio Azure Web PubSub y Azure Functions para compilar una aplicación sin servidor con mensajería en tiempo real en escenarios de notificación. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Compilar una aplicación de notificación sin servidor.
> * Utilizar los enlaces de entrada y salida de funciones de Web PubSub.
> * Ejecutar localmente funciones de ejemplo.
> * Implementar la función en Azure Function App

## <a name="prerequisites"></a>Requisitos previos

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* Un editor de código como [Visual Studio Code](https://code.visualstudio.com/)

* [Node.js](https://nodejs.org/en/download/), versión 10.x.
   > [!NOTE]
   > Para más información sobre las versiones de Node.js que se admiten, consulte la [documentación de las versiones del runtime de Azure Functions.](../azure-functions/functions-versions.md#languages)

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (se prefieren la versión 3 o superiores) para ejecutar aplicaciones de Azure Functions localmente y realizar la implementación en Azure.

* [Interfaz de línea de comandos de Azure (CLI de Azure)](/cli/azure) para administrar recursos de Azure.

# <a name="c"></a>[C#](#tab/csharp)

* Un editor de código, como [Visual Studio Code](https://code.visualstudio.com/).

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (se prefieren la versión 3 o superiores) para ejecutar aplicaciones de Azure Functions localmente y realizar la implementación en Azure.

* [Interfaz de línea de comandos de Azure (CLI de Azure)](/cli/azure) para administrar recursos de Azure.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-and-run-the-functions-locally"></a>Creación y ejecución locales de las funciones

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

2. Cree una función `negotiate` para ayudar a los clientes a obtener la dirección URL de conexión del servicio con el token de acceso.
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
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
                    "hub": "notification",
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
            [WebPubSubConnection(Hub = "notification")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");

            return connection;
        }
        ```

3. Cree una función `notification` para generar notificaciones con `TimerTrigger`.
   ```bash
    func new -n notification -t TimerTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - Actualice `notification/function.json` y copie los siguientes códigos de JSON.
        ```json
        {
            "bindings": [
                {
                "name": "myTimer",
                "type": "timerTrigger",
                "direction": "in",
                "schedule": "*/10 * * * * *"
                },
                {
                "type": "webPubSub",
                "name": "webPubSubOperation",
                "hub": "notification",
                "direction": "out"
                }
            ]
        }
        ```
   - Actualice `notification/index.js` y copie los siguientes códigos.
        ```js
        module.exports = function (context, myTimer) {
            context.bindings.webPubSubOperation = {
                "operationKind": "sendToAll",
                "message": `[DateTime: ${new Date()}] Temperature: ${getValue(22, 1)}\xB0C, Humidity: ${getValue(40, 2)}%`,
                "dataType": "text"
            }
            context.done();
        };

        function getValue(baseNum, floatNum) {
            return (baseNum + 2 * floatNum * (Math.random() - 0.5)).toFixed(3);
        }
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - Actualice `notification.cs` y reemplace la función `Run` por los siguientes códigos.
        ```c#
        [FunctionName("notification")]
        public static async Task Run([TimerTrigger("*/10 * * * * *")]TimerInfo myTimer, ILogger log,
            [WebPubSub(Hub = "notification")] IAsyncCollector<WebPubSubOperation> operations)
        {
            await operations.AddAsync(new SendToAll
            {
                Message = BinaryData.FromString($"[DateTime: {DateTime.Now}] Temperature: {GetValue(23, 1)}{'\xB0'}C, Humidity: {GetValue(40, 2)}%"),
                DataType = MessageDataType.Text
            });
        }

        private static string GetValue(double baseNum, double floatNum)
        {
            var rng = new Random();
            var value = baseNum + floatNum * 2 * (rng.NextDouble() - 0.5);
            return value.ToString("0.000");
        }
        ``` 

4. Agregue la página única del cliente `index.html` en la carpeta raíz del proyecto y copie el contenido como se indica a continuación.
    ```html
    <html>
        <body>
        <h1>Azure Web PubSub Notification</h1>
        <div id="messages"></div>
        <script>
            (async function () {
                let messages = document.querySelector('#messages');
                let res = await fetch(`${window.location.origin}/api/negotiate`);
                let url = await res.json();
                let ws = new WebSocket(url.url);
                ws.onopen = () => console.log('connected');

                ws.onmessage = event => {
                    let m = document.createElement('p');
                    m.innerText = event.data;
                    messages.appendChild(m);
                };
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
    ```

5. Configuración y ejecución de la aplicación Azure Function

    - En el explorador, abra **Azure Portal** y confirme que la instancia del servicio Azure Web PubSub que ha implementado anteriormente se creó correctamente. Vaya a la instancia.
    - Seleccione **Claves** y copie la cadena de conexión.

    :::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Captura de pantalla de la copia de la cadena de conexión de Web PubSub.":::

    Ejecute el comando siguiente en la carpeta de funciones para establecer la cadena de conexión del servicio. Reemplace `<connection-string`> por su valor si fuera necesario.

    ```bash
    func settings add WebPubSubConnectionString "<connection-string>"
    ```

    > [!NOTE]
    > `TimerTrigger` usado en el ejemplo tiene dependencia de Azure Storage, pero puede usar el emulador de almacenamiento local cuando la función se ejecuta localmente. Si se produce un error como `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, tendrá que descargar y habilitar el [emulador de Storage](../storage/common/storage-use-emulator.md).

    Ya puede ejecutar la función local mediante el comando siguiente.

    ```bash
    func start
    ```

    Y al comprobar los registros en ejecución, puede visitar la página estática del host local visitando: `https://localhost:7071/api/index`.

## <a name="deploy-function-app-to-azure"></a>Implementación de una aplicación de funciones en Azure

Para poder implementar el código de la función en Azure, es preciso crear tres recursos:
* Un grupo de recursos, que es un contenedor lógico de recursos relacionados.
* Una cuenta de almacenamiento, que se usa para mantener el estado y otra información sobre sus funciones.
* Una aplicación de funciones, que proporciona el entorno para ejecutar el código de función. Una aplicación de funciones se asigna a un proyecto de función local y le permite agrupar funciones como una unidad lógica, lo que facilita la administración, la implementación y el uso compartido de recursos.

Utilice los siguientes comandos para crear estos elementos. 

1. Si todavía no lo ha hecho, inicie sesión en Azure:

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

1. Ahora puede comprobar el sitio desde Azure Function App; para hacerlo, vaya a la dirección URL: `https://<FUNCIONAPP_NAME>.azurewebsites.net/api/index`.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga los pasos que se indican a continuación para eliminar todos los recursos creados por este documento, con el fin de que no se le apliquen cargos:

1. En Azure Portal, seleccione **Grupos de recursos** en el extremo izquierdo y luego seleccione el grupo de recursos que creó. Puede usar el cuadro de búsqueda para buscar el grupo de recursos por su nombre en su lugar.

1. En la ventana que se abrirá, seleccione el grupo de recursos y luego seleccione **Eliminar grupo de recursos**.

1. En la nueva ventana, escriba el nombre del grupo de recursos que desea eliminar y, después, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a ejecutar una aplicación de chat sin servidor. Ahora, puede empezar a crear su propia aplicación. 

> [!div class="nextstepaction"]
> [Tutorial: Creación de una sala de chat simple con Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Enlaces de Azure Web PubSub para Azure Functions](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples).
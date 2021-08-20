---
title: Uso de JavaScript para crear un salón de chat con Azure Functions y SignalR Service
description: Inicio rápido para el uso de Azure SignalR Service y Azure Functions a fin de crear una aplicación en la que se muestre el recuento de estrellas de GitHub mediante JavaScript.
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: javascript
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 9e8bb3d49ef236521f7d4a48060b2405dbbbc104
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112462026"
---
# <a name="quickstart-use-javascript-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>Inicio rápido: Uso de JavaScript para crear una aplicación en la que se muestre el número de estrellas de GitHub con Azure Functions y SignalR Service

Azure SignalR Service permite agregar fácilmente funciones en tiempo real a una aplicación y Azure Functions es una plataforma sin servidor que permite ejecutar el código sin tener que administrar ninguna infraestructura. En este inicio rápido, aprenderá a usar SignalR Service y Azure Functions para compilar una aplicación sin servidor con JavaScript para difundir mensajes a los clientes.

> [!NOTE]
> Puede obtener todos los códigos mencionados en el artículo desde [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/javascript).

## <a name="prerequisites"></a>Prerrequisitos

- Un editor de código como [Visual Studio Code](https://code.visualstudio.com/)
- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), versión 2 o superiores. Se usa para ejecutar aplicaciones de Azure Functions de forma local.
- [Node.js](https://nodejs.org/en/download/), versión 10.x

   > [!NOTE]
   > Los ejemplos deberían funcionar con otras versiones de Node.js. Para más información, consulte la [documentación de las versiones del runtime de Azure Functions](../azure-functions/functions-versions.md#languages).

> [!NOTE]
> Esta guía de inicio rápido se puede ejecutar en macOS, Windows o Linux.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjs).

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en <https://portal.azure.com/> con su cuenta de Azure.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjs).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjs).


## <a name="setup-and-run-the-azure-function-locally"></a>Configuración y ejecución de la instancia de Azure Functions localmente

1. Asegúrese de que ha instalado Azure Function Core Tools. Cree un directorio vacío y vaya al directorio con la línea de comandos.

    ```bash
    # Initialize a function project
    func init --worker-runtime javascript
    ```

2. Después de inicializar un proyecto, tendrá que crear funciones. En este ejemplo, es necesario crear tres funciones.

    1. Ejecute el comando siguiente para crear una función `index`, que hospedará una página web para el cliente.

        ```bash
        func new -n index -t HttpTrigger
        ```
        
        Abra `index/index.js` y copie los códigos siguientes.

        ```javascript
        var fs = require('fs').promises
    
        module.exports = async function (context, req) {
            const path = context.executionContext.functionDirectory + '/../content/index.html'
            try {
                var data = await fs.readFile(path);
                context.res = {
                    headers: {
                        'Content-Type': 'text/html'
                    },
                    body: data
                }
                context.done()
            } catch (error) {
                context.log.error(err);
                context.done(err);
            }
        }
        ```
    
    2. Cree una función `negotiate` para que los clientes obtengan el token de acceso.
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        Abra `negotiate/function.json` y copie los códigos JSON siguientes:
    
        ```json
        {
          "disabled": false,
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "methods": [
                "post"
              ],
              "name": "req",
              "route": "negotiate"
            },
            {
              "type": "http",
              "direction": "out",
              "name": "res"
            },
            {
              "type": "signalRConnectionInfo",
              "name": "connectionInfo",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "in"
            }
          ]
        }
        ```
    
    3. Cree una función `broadcast` para difundir mensajes a todos los clientes. En el ejemplo, se usa el desencadenador de tiempo para difundir los mensajes de manera periódica.
    
        ```bash
        func new -n broadcast -t TimerTrigger
        ```
    
        Abra `broadcast/function.json` y copie los códigos siguientes.
    
        ```json
        {
          "bindings": [
            {
              "name": "myTimer",
              "type": "timerTrigger",
              "direction": "in",
              "schedule": "*/5 * * * * *"
            },
            {
              "type": "signalR",
              "name": "signalRMessages",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "out"
            }
          ]
        }
        ```
    
        Abra `broadcast/index.js` y copie los códigos siguientes.
    
        ```javascript
        var https = require('https');
        
        module.exports = function (context) {
            var req = https.request("https://api.github.com/repos/azure/azure-signalr", {
                method: 'GET',
                headers: {'User-Agent': 'serverless'}
            }, res => {
                var body = "";
        
                res.on('data', data => {
                    body += data;
                });
                res.on("end", () => {
                    var jbody = JSON.parse(body);
                    context.bindings.signalRMessages = [{
                        "target": "newMessage",
                        "arguments": [ `Current star count of https://github.com/Azure/azure-signalr is: ${jbody['stargazers_count']}` ]
                    }]
                    context.done();
                });
            }).on("error", (error) => {
                context.log(error);
                context.res = {
                  status: 500,
                  body: error
                };
                context.done();
            });
            req.end();
        }    
        ```

3. La interfaz de cliente de este ejemplo es una página web. Si se lee contenido HTML de `content/index.html` en la función `index`, cree un archivo `index.html` en el directorio `content`. Y copie el contenido siguiente.

    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```

4. Ya casi ha terminado. El último paso consiste en establecer una cadena de conexión de SignalR Service a la configuración de Azure Functions.

    1. En el explorador que se muestra al abrir Azure Portal, confirme que la instancia del servicio SignalR que implementó anteriormente se ha creado correctamente buscando su nombre en el cuadro de búsqueda de la parte superior del portal. Seleccione la instancia para abrirla.

        ![Búsqueda del nombre de la instancia del servicio SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. Seleccione **Claves** para ver las cadenas de conexión para la instancia del servicio SignalR.
    
        ![Captura de pantalla que resalta la cadena de conexión principal.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. Copie la cadena de conexión principal. Ejecute el comando siguiente.
    
        ```bash
        func settings add AzureSignalRConnectionString '<signalr-connection-string>'
        ```
    
5. Ejecute la función de Azure en el entorno local:

    ```bash
    func start
    ```

    Después de que la función de Azure se ejecute en el entorno local. Use el explorador para visitar `http://localhost:7071/api/index` y ver el recuento de estrellas actual. Y si asigna estrellas o las quita en GitHub, obtendrá un recuento de estrellas que se actualiza cada pocos segundos.

    > [!NOTE]
    > Para el enlace de SignalR se necesita Azure Storage, pero puede usar el emulador de almacenamiento local cuando la función se ejecuta localmente.
    > Si se produce un error como `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, tendrá que descargar y habilitar el [emulador de almacenamiento](../storage/common/storage-use-emulator.md).

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjs).

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha compilado y ejecutado una aplicación sin servidor en tiempo real en el entorno local. Obtenga más información sobre cómo usar enlaces de SignalR Service con Azure Functions.
A continuación, obtendrá más información sobre cómo establecer la comunicación bidireccional entre los clientes y Azure Functions con SignalR Service.

> [!div class="nextstepaction"]
> [Enlaces de SignalR Service para Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Comunicación bidireccional sin servidor](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Implementación de Azure Functions con VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

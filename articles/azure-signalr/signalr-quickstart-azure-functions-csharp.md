---
title: 'Inicio rápido de Azure SignalR Service sin servidor: C#'
description: Inicio rápido para el uso de Azure SignalR Service y Azure Functions a fin de crear una aplicación en la que se muestre el recuento de estrellas de GitHub mediante C#.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 06/09/2021
ms.author: zhshang
ms.openlocfilehash: 1856f6e012c2b90e173162f055d64402f0c4c908
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112462116"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-c"></a>Inicio rápido: Creación de una aplicación en la que se muestra el número de estrellas de GitHub con Azure Functions y SignalR Service mediante C\#

El servicio Azure SignalR le permite agregar fácilmente funcionalidad en tiempo real a la aplicación. Azure Functions es una plataforma sin servidor que le permite ejecutar el código sin tener que administrar ninguna infraestructura. En este inicio rápido, aprenderá a usar SignalR Service y Azure Functions para compilar una aplicación sin servidor con C# para difundir mensajes a los clientes.

> [!NOTE]
> Puede obtener todos los códigos mencionados en el artículo desde [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/csharp).

## <a name="prerequisites"></a>Requisitos previos

Si todavía no ha instalado Visual Studio Code, puede descargarlo y usarlo de forma gratuita(https://code.visualstudio.com/Download).

También puede ejecutar este tutorial en la línea de comandos (macOS, Windows o Linux) mediante [Azure Functions Core Tools](../azure-functions/functions-run-local.md?tabs=windows%2Ccsharp%2Cbash#v2). También el [SDK de .NET Core](https://dotnet.microsoft.com/download) y el editor de código que prefiera.

Si no tiene una suscripción de Azure, [cree una gratuita](https://azure.microsoft.com/free/dotnet) antes de comenzar.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure-and-create-signalr-service-instance"></a>Inicio de sesión en Azure y creación de una instancia de SignalR Service

Inicie sesión en Azure Portal en <https://portal.azure.com/> con su cuenta de Azure.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qscsharp).

## <a name="setup-and-run-the-azure-function-locally"></a>Configuración y ejecución de la instancia de Azure Functions localmente

1. Asegúrese de que ha instalado Azure Function Core Tools. Cree un directorio vacío y vaya al directorio con la línea de comandos.

    ```bash
    # Initialize a function project
    func init --worker-runtime dotnet
    
    # Add SignalR Service package reference to the project
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService
    ```

2. Después de inicializar un proyecto. Cree un archivo con el nombre *Function.cs*. Agregue el código siguiente a *Function.cs*.
   
    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Newtonsoft.Json;
    
    namespace CSharp
    {
        public static class Function
        {
            private static HttpClient httpClient = new HttpClient();
    
            [FunctionName("index")]
            public static IActionResult Index([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, ExecutionContext context)
            {
                var path = Path.Combine(context.FunctionAppDirectory, "content", "index.html");
                return new ContentResult
                {
                    Content = File.ReadAllText(path),
                    ContentType = "text/html",
                };
            }
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo Negotiate( 
                [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req,
                [SignalRConnectionInfo(HubName = "serverlessSample")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static async Task Broadcast([TimerTrigger("*/5 * * * * *")] TimerInfo myTimer,
            [SignalR(HubName = "serverlessSample")] IAsyncCollector<SignalRMessage> signalRMessages)
            {
                var request = new HttpRequestMessage(HttpMethod.Get, "https://api.github.com/repos/azure/azure-signalr");
                request.Headers.UserAgent.ParseAdd("Serverless");
                var response = await httpClient.SendAsync(request);
                var result = JsonConvert.DeserializeObject<GitResult>(await response.Content.ReadAsStringAsync());
                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { $"Current start count of https://github.com/Azure/azure-signalr is: {result.StartCount}" }
                    });
            }
    
            private class GitResult
            {
                [JsonRequired]
                [JsonProperty("stargazers_count")]
                public string StartCount { get; set; }
            }
        }
    }
    ```
    Estos códigos tienen tres funciones. `Index` se usa para obtener un sitio web como cliente. `Negotiate` se usa para que el cliente obtenga el token de acceso. `Broadcast` se usa para obtener periódicamente el recuento de estrellas de GitHub y transmitir mensajes a todos los clientes.

3. La interfaz de cliente de este ejemplo es una página web. Si se lee contenido HTML de `content/index.html` en la función `GetHomePage`, cree un archivo `index.html` en el directorio `content`. Y copie el contenido siguiente.
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

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha compilado y ejecutado una aplicación sin servidor en tiempo real en el entorno local. Obtenga más información sobre cómo usar enlaces de SignalR Service con Azure Functions.
A continuación, obtendrá más información sobre cómo establecer la comunicación bidireccional entre los clientes y Azure Functions con SignalR Service.

> [!div class="nextstepaction"]
> [Enlaces de SignalR Service para Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Comunicación bidireccional sin servidor](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md)


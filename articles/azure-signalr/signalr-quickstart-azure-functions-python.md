---
title: 'Inicio rápido de Azure SignalR Service sin servidor: Python'
description: Inicio rápido para el uso de Azure SignalR Service y Azure Functions a fin de crear una aplicación en la que se muestre el recuento de estrellas de GitHub mediante Python.
author: anthonychu
ms.author: antchu
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 99d2f7a67ede762f84e5f6d9abf5af78c5751d22
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444973"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-python"></a>Inicio rápido: Creación de una aplicación en la que se muestre el número de estrellas de GitHub con Azure Functions y SignalR Service mediante Python

El servicio Azure SignalR le permite agregar fácilmente funcionalidad en tiempo real a la aplicación. Azure Functions es una plataforma sin servidor que le permite ejecutar el código sin tener que administrar ninguna infraestructura. En este inicio rápido, aprenderá a usar SignalR Service y Azure Functions para compilar una aplicación sin servidor con Python para difundir mensajes a los clientes.

> [!NOTE]
> Puede obtener todos los códigos mencionados en el artículo desde [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/python).

## <a name="prerequisites"></a>Prerrequisitos

Esta guía de inicio rápido se puede ejecutar en macOS, Windows o Linux.

Asegúrese de tener instalado un editor de código como [Visual Studio Code](https://code.visualstudio.com/).

Instale [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (versión 2.7.1505 o posteriores) para ejecutar las aplicaciones de Azure Functions de Python de forma local.

Azure Functions requiere [Python 3.6+](https://www.python.org/downloads/). (consulte las [versiones de Python que se admiten](../azure-functions/functions-reference-python.md#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en <https://portal.azure.com/> con su cuenta de Azure.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qspython).


## <a name="setup-and-run-the-azure-function-locally"></a>Configuración y ejecución de la instancia de Azure Functions localmente

1. Asegúrese de que ha instalado Azure Function Core Tools. Cree un directorio vacío y vaya al directorio con la línea de comandos.

    ```bash
    # Initialize a function project
    func init --worker-runtime python
    ```

2. Después de inicializar un proyecto, tendrá que crear funciones. En este ejemplo, es necesario crear tres funciones.

    1. Ejecute el comando siguiente para crear una función `index`, que hospedará una página web para el cliente.

        ```bash
        func new -n index -t HttpTrigger
        ```
        Abra `index/function.json` y copie los códigos JSON siguientes:

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

        Abra `index/__init__.py` y copie los códigos siguientes.

        ```javascript
        import os
    
        import azure.functions as func
        
        
        def main(req: func.HttpRequest) -> func.HttpResponse:
            f = open(os.path.dirname(os.path.realpath(__file__)) + '/../content/index.html')
            return func.HttpResponse(f.read(), mimetype='text/html')
        ```
    
    2. Cree una función `negotiate` para que los clientes obtengan el token de acceso.
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        Abra `negotiate/function.json` y copie los códigos JSON siguientes:
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "$return"
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

        Abra `negotiate/__init__.py` y copie los códigos siguientes:

        ```python
        import azure.functions as func
    
        
        def main(req: func.HttpRequest, connectionInfo) -> func.HttpResponse:
            return func.HttpResponse(connectionInfo)
        ```
    
    3. Cree una función `broadcast` para difundir mensajes a todos los clientes. En el ejemplo, se usa el desencadenador de tiempo para difundir los mensajes de manera periódica.
    
        ```bash
        func new -n broadcast -t TimerTrigger
        # install requests
        pip install requests
        ```

        Abra `broadcast/function.json` y copie los códigos siguientes.

        ```json
        {
          "scriptFile": "__init__.py",
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
    
        Abra `broadcast/__init__.py` y copie los códigos siguientes.
    
        ```python
        import requests
        import json
        
        import azure.functions as func
        
        
        def main(myTimer: func.TimerRequest, signalRMessages: func.Out[str]) -> None:
            headers = {'User-Agent': 'serverless'}
            res = requests.get('https://api.github.com/repos/azure/azure-signalr', headers=headers)
            jres = res.json()
        
            signalRMessages.set(json.dumps({
                'target': 'newMessage',
                'arguments': [ 'Current star count of https://github.com/Azure/azure-signalr is: ' + str(jres['stargazers_count']) ]
            }))
        ```

3. La interfaz de cliente de este ejemplo es una página web. Si se lee contenido HTML de `content/index.html` en la función `index`, cree un archivo `index.html` en el directorio `content` bajo la carpeta raíz del proyecto. Y copie el contenido siguiente.

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

    2. Seleccione **Claves** para ver las cadenas de conexión para la instancia del servicio SignalR.
    
        ![Captura de pantalla que resalta la cadena de conexión principal.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. Copie la cadena de conexión principal. Ejecute el comando siguiente.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
5. Ejecute la función de Azure en el entorno local:

    ```bash
    func start
    ```

    Después de que la función de Azure se ejecute en el entorno local. Use el explorador para visitar `http://localhost:7071/api/index` y ver el número de estrellas actual. Y si asigna estrellas o las quita en GitHub, obtendrá un número de estrellas que se actualiza cada pocos segundos.

    > [!NOTE]
    > Para el enlace de SignalR se necesita Azure Storage, pero puede usar el emulador de almacenamiento local cuando la función se ejecuta localmente.
    > Si se produce un error como `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, tendrá que descargar y habilitar el [emulador de almacenamiento](../storage/common/storage-use-emulator.md).

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
> [Implementación de Azure Functions con VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
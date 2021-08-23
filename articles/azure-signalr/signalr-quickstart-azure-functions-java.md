---
title: Uso de Java para crear un salón de chat con Azure Functions y SignalR Service
description: Inicio rápido para el uso de Azure SignalR Service y Azure Functions a fin de crear una aplicación en la que se muestre el recuento de estrellas de GitHub mediante Java.
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: fdcc8b9355556804b2f13fccd206eb13ac7c0cb6
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112462062"
---
# <a name="quickstart-use-java-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>Inicio rápido: Uso de Java para crear una aplicación en la que se muestre el número de estrellas de GitHub con Azure Functions y SignalR Service

Azure SignalR Service permite agregar fácilmente funciones en tiempo real a una aplicación y Azure Functions es una plataforma sin servidor que permite ejecutar el código sin tener que administrar ninguna infraestructura. En este inicio rápido, aprenderá a usar SignalR Service y Azure Functions para compilar una aplicación sin servidor con Java para difundir mensajes a los clientes.

> [!NOTE]
> Puede obtener todos los códigos mencionados en el artículo desde [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/java).

## <a name="prerequisites"></a>Prerrequisitos

- Un editor de código como [Visual Studio Code](https://code.visualstudio.com/)
- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Se usa para ejecutar aplicaciones de Azure Functions de forma local.

   > [!NOTE]
   > Los enlaces de SignalR Service necesarios en Java solo se admiten en la versión 2.4.419 (versión de host 2.0.12332) de Azure Functions Core Tools, o en las versiones superiores.

   > [!NOTE]
   > Para instalar extensiones, Azure Functions Core Tools requiere que esté instalado el [SDK de .NET Core](https://dotnet.microsoft.com/download). Sin embargo, no se requiere de ningún conocimiento de .NET para compilar aplicaciones de Azure Function de JavaScript.

- [Kit para desarrolladores de Java](https://www.azul.com/downloads/zulu/), versión 11
- [Apache Maven](https://maven.apache.org), versión 3.0 o posterior

> [!NOTE]
> Esta guía de inicio rápido se puede ejecutar en macOS, Windows o Linux.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en <https://portal.azure.com/> con su cuenta de Azure.

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjava).


## <a name="configure-and-run-the-azure-function-app"></a>Configuración y ejecución de la aplicación Azure Function

1. Asegúrese de que tiene instalados Azure Function Core Tools, Java (versión 11 en la muestra) y Maven.
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=11
    ```

    Maven le pide que especifique los valores necesarios para terminar de generar el proyecto. Puede especificar los valores siguientes.

    | Prompt | Value | Descripción |
    | ------ | ----- | ----------- |
    | **groupId** | `com.signalr` | Un valor que identifica de forma única su proyecto entre todos los demás y que sigue las [reglas de nomenclatura de paquetes](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) de Java. |
    | **artifactId** | `java` | Un valor que es el nombre del archivo jar, sin un número de versión. |
    | **version** | `1.0-SNAPSHOT` | Elija el valor predeterminado. |
    | **package** | `com.signalr` | Un valor que es el paquete de Java para el código de función generado. Use el valor predeterminado. |   

2. Después de inicializar un proyecto. Vaya a la carpeta `src/main/java/com/signalr` y copie los códigos siguientes a `Function.java`

    ```java
    package com.signalr;
  
    import com.google.gson.Gson;
    import com.microsoft.azure.functions.ExecutionContext;
    import com.microsoft.azure.functions.HttpMethod;
    import com.microsoft.azure.functions.HttpRequestMessage;
    import com.microsoft.azure.functions.HttpResponseMessage;
    import com.microsoft.azure.functions.HttpStatus;
    import com.microsoft.azure.functions.annotation.AuthorizationLevel;
    import com.microsoft.azure.functions.annotation.FunctionName;
    import com.microsoft.azure.functions.annotation.HttpTrigger;
    import com.microsoft.azure.functions.annotation.TimerTrigger;
    import com.microsoft.azure.functions.signalr.*;
    import com.microsoft.azure.functions.signalr.annotation.*;
    
    import org.apache.commons.io.IOUtils;
    
    
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.HttpResponse.BodyHandlers;
    import java.nio.charset.StandardCharsets;
    import java.util.Optional;
    
    public class Function {
        @FunctionName("index")
        public HttpResponseMessage run(
                @HttpTrigger(
                    name = "req",
                    methods = {HttpMethod.GET},
                    authLevel = AuthorizationLevel.ANONYMOUS)HttpRequestMessage<Optional<String>> request,
                final ExecutionContext context) throws IOException {
            
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("content/index.html");
            String text = IOUtils.toString(inputStream, StandardCharsets.UTF_8.name());
            return request.createResponseBuilder(HttpStatus.OK).header("Content-Type", "text/html").body(text).build();
        }
  
        @FunctionName("negotiate")
        public SignalRConnectionInfo negotiate(
                @HttpTrigger(
                    name = "req",
                    methods = { HttpMethod.POST },
                    authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
                @SignalRConnectionInfoInput(
                    name = "connectionInfo",
                    hubName = "serverless") SignalRConnectionInfo connectionInfo) {
                    
            return connectionInfo;
        }
    
        @FunctionName("broadcast")
        @SignalROutput(name = "$return", hubName = "serverless")
        public SignalRMessage broadcast(
            @TimerTrigger(name = "timeTrigger", schedule = "*/5 * * * * *") String timerInfo) throws IOException, InterruptedException {
            
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest req = HttpRequest.newBuilder().uri(URI.create("https://api.github.com/repos/azure/azure-signalr")).header("User-Agent", "serverless").build();
            HttpResponse<String> res = client.send(req, BodyHandlers.ofString());
            Gson gson = new Gson();
            GitResult result = gson.fromJson(res.body(), GitResult.class);
            return new SignalRMessage("newMessage", "Current start count of https://github.com/Azure/azure-signalr is:".concat(result.stargazers_count));
        }
    
        class GitResult {
            public String stargazers_count;
        }
    }
    ```

3. Es necesario agregar algunas dependencias. Por lo tanto, abra `pom.xml` y agregue alguna dependencia que se usa en los códigos.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.functions</groupId>
        <artifactId>azure-functions-java-library-signalr</artifactId>
        <version>1.0.0</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.4</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.7</version>
    </dependency>
    ```

4. La interfaz de cliente de esta muestra es una página web. Si se lee contenido HTML de `content/index.html` en la función `index`, cree un archivo `content/index.html` en el directorio `resources`. El árbol de directorios debe tener el siguiente aspecto.
    
    ```
    FunctionsProject
     | - src
     | | - main
     | | | - java
     | | | | - com
     | | | | | - signalr 
     | | | | | | - Function.java
     | | | - resources
     | | | | - content
     | | | | | - index.html
     | - pom.xml
     | - host.json
     | - local.settings.json
    ```

    Abra `index.html` y copie el contenido siguiente.

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

5. Ya casi ha terminado. El último paso consiste en establecer una cadena de conexión de SignalR Service a la configuración de Azure Functions.

    1. En el explorador que se muestra al abrir Azure Portal, confirme que la instancia del servicio SignalR que implementó anteriormente se ha creado correctamente buscando su nombre en el cuadro de búsqueda de la parte superior del portal. Seleccione la instancia para abrirla.

        ![Búsqueda del nombre de la instancia del servicio SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. Seleccione **Claves** para ver las cadenas de conexión para la instancia del servicio SignalR.
    
        ![Captura de pantalla que resalta la cadena de conexión principal.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. Copie la cadena de conexión principal. Ejecute el comando siguiente.
    
        ```bash
        func settings add AzureSignalRConnectionString '<signalr-connection-string>'
        # Also we need to set AzureWebJobsStorage as Azure Function's requirement
        func settings add AzureWebJobsStorage 'UseDevelopmentStorage=true'
        ```
    
6. Ejecute la función de Azure en el entorno local:

    ```bash
    mvn clean package
    mvn azure-functions:run
    ```

    Después de que la función de Azure se ejecute en el entorno local. Use el explorador para visitar `http://localhost:7071/api/index` y ver el recuento de estrellas actual. Y si asigna estrellas o las quita en GitHub, obtendrá un recuento de estrellas que se actualiza cada pocos segundos.

    > [!NOTE]
    > Para el enlace de SignalR se necesita Azure Storage, pero puede usar el emulador de almacenamiento local cuando la función se ejecuta localmente.
    > Si se produce un error como `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`, tendrá que descargar y habilitar el [emulador de almacenamiento](../storage/common/storage-use-emulator.md).
    
¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjava).


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

¿Tiene problemas? Consulte la [guía de solución de problemas](signalr-howto-troubleshoot-guide.md) o [póngase en contacto con nosotros](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha compilado y ejecutado una aplicación sin servidor en tiempo real en el entorno local. Obtenga más información sobre cómo usar enlaces de SignalR Service con Azure Functions.
A continuación, obtendrá más información sobre cómo establecer la comunicación bidireccional entre los clientes y Azure Functions con SignalR Service.

> [!div class="nextstepaction"]
> [Enlaces de SignalR Service para Azure Functions](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [Comunicación bidireccional sin servidor](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Creación de la primera función con Java y Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)

---
title: Tutorial para el procesamiento en segundo plano basado en eventos con el SDK de WebJobs
description: Obtenga información sobre cómo habilitar las aplicaciones web para ejecutar tareas en segundo plano. Use este tutorial para empezar a trabajar con el SDK de WebJobs.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.date: 06/25/2021
ms.author: glenga
ms.topic: tutorial
ms.openlocfilehash: 6b4875f028fe8645125743d04770c2fd237b4f62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752464"
---
# <a name="tutorial-get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Tutorial: Introducción al SDK de Azure WebJobs para el procesamiento en segundo plano basado en eventos

Introducción al SDK de Azure WebJobs para Azure App Service a fin de permitir que las aplicaciones web ejecuten tareas en segundo plano, tareas programadas y respondan a eventos. 

Use Visual Studio 2019 para crear una aplicación de consola de .NET Core que use el SDK de WebJobs para responder a los mensajes de la cola de Azure Storage, ejecutar el proyecto localmente y, por último, implementarlo en Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una aplicación de consola
> * Incorporación de una función
> * Prueba local
> * Implementar en Azure
> * Habilitar el registro de Application Insights.
> * Agregar enlaces de entrada y de salida.

## <a name="prerequisites"></a>Requisitos previos

* Visual Studio 2019 con la carga de trabajo **Desarrollo de Azure**. [Instale Visual Studio 2019](/visualstudio/install/).

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/dotnet).

## <a name="create-a-console-app"></a>Creación de una aplicación de consola
En esta sección, comenzará con la creación de un proyecto en Visual Studio 2019. Después, agregará herramientas para el desarrollo de Azure, la publicación de código y las funciones que escuchan desencadenadores y llaman a funciones. Por último, configurará el registro de la consola que deshabilita una herramienta de supervisión heredada y habilita un proveedor de consola con filtrado predeterminado. 

>[!NOTE]  
>Los procedimientos de este artículo se han comprobado para la creación de una aplicación de consola de .NET Core que se ejecuta en .NET Core 3.1.

### <a name="create-a-project"></a>Crear un proyecto

1. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.

1. En **Crear un proyecto**, seleccione **Aplicación de consola (C#)** y después **Siguiente**.

1. En **Configurar el nuevo proyecto**, asigne el nombre *WebJobsSDKSample* al proyecto y luego seleccione **Siguiente**.

1. Elija la **Plataforma de destino** y seleccione **Crear**. Este tutorial se ha comprobado con .NET Core 3.1.

### <a name="install-webjobs-nuget-packages"></a>Instalación de paquetes NuGet de WebJobs

Instale el paquete NuGet de WebJobs más reciente. Este paquete incluye Microsoft.Azure.WebJobs (SDK de WebJobs), que le permite publicar el código de función en WebJobs en Azure App Service.

1. Obtenga la versión 3.x estable más reciente del [paquete NuGet Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/).

2. En Visual Studio, vaya a **Herramientas** > **Administrador de paquetes NuGet**.

3. Seleccione **Consola del Administrador de paquetes**. Verá una lista de cmdlets de NuGet, un vínculo a la documentación y un punto de entrada `PM>`.

4. En el comando siguiente, reemplace `<3_X_VERSION>` por el número de versión actual que ha encontrado en el paso 1. 

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```
5. En **Consola del Administrador de paquetes**, ejecute el comando. Aparece la lista de extensiones y se instala de forma automática. 
  
### <a name="create-the-host"></a>Creación del host

El host es el contenedor en tiempo de ejecución de las funciones que escucha a los desencadenadores y llama a las funciones. En los pasos siguientes se crea un host que implementa [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost), que es el host genérico en ASP.NET Core.

1. Seleccione la pestaña **Program.cs** y agregue estas instrucciones `using`:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Además, en **Program.cs**, reemplace el método `Main` por el código siguiente:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

En ASP.NET Core, las configuraciones de host se establecen mediante la llamada a los métodos de la instancia [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Para más información, consulte [Host genérico .NET](/aspnet/core/fundamentals/host/generic-host). El método de extensión `ConfigureWebJobs` inicializa el host de WebJobs. En `ConfigureWebJobs`, inicialice extensiones de enlace específicas, como la de enlace de Storage y establezca las propiedades de esas extensiones.  

### <a name="enable-console-logging"></a>Habilitación del registro de consola

Configure el registro de la consola que usa la [plataforma de registro de ASP.NET Core](/aspnet/core/fundamentals/logging). Este marco, Microsoft.Extensions.Logging, incluye una API que funciona con una variedad de proveedores de registro integrados y de terceros.

1. Obtenga la versión estable más reciente del [paquete NuGet `Microsoft.Extensions.Logging.Console`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), que incluye `Microsoft.Extensions.Logging`.

2. En el comando siguiente, reemplace `<3_X_VERSION>` por el número de versión actual que ha encontrado en el paso 1. Cada tipo de paquete NuGet tiene un número de versión único.

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
3. En la **Consola del Administrador de paquetes**, rellene el número de versión actual y ejecute el comando. Aparece la lista de extensiones y se instala de forma automática. 

4. En la pestaña **Program.cs**, agregue esta instrucción `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```
5. En **Program.cs**, agregue el método [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) a [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), antes del comando `Build`. El método [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) agrega el registro de la consola a la configuración.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    El método `Main` tiene el aspecto siguiente:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Esta adición realiza estos cambios:

    * Deshabilita el [registro del panel](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). El panel es una herramienta de supervisión heredada y no se recomienda el registro del panel para escenarios de producción de alto rendimiento.
    * Agrega el proveedor de consola con el [filtrado](webjobs-sdk-how-to.md#log-filtering) predeterminado.

Ahora, puede agregar una función desencadenada por los mensajes que llegan a una cola de Azure Storage.

## <a name="add-a-function"></a>Incorporación de una función

Una función es una unidad de código que se ejecuta según una programación, se desencadena en función de eventos o se ejecuta a petición. Un desencadenador escucha un evento de servicio. En el contexto del SDK de WebJobs, "desencadenado" no hace referencia al modo de implementación. Los WebJobs programados o controlados por eventos creados con el SDK siempre se debe implementar como WebJobs continuos con "Siempre disponible" habilitado. 

En esta sección, creará una función desencadenada por mensajes en una cola de Azure Storage. En primer lugar, tendrá que agregar una extensión de enlace para conectarse a Azure Storage.

### <a name="install-the-storage-binding-extension"></a>Instalación de la extensión de enlaces de Storage

A partir de la versión 3 del SDK de WebJobs, para conectarse a los servicios de Azure Storage, debe instalar un paquete de extensión de enlace de Storage independiente. 

1. Obtenga la versión estable más reciente del paquete NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versión 3.x.

1. En el comando siguiente, reemplace `<3_X_VERSION>` por el número de versión actual que ha encontrado en el paso 1. Cada tipo de paquete NuGet tiene un número de versión único. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
1. En la **Consola del Administrador de paquetes**, ejecute el comando con el número de versión actual en el punto de entrada `PM>`.

1. En **Program.cs**, en el método de extensión `ConfigureWebJobs`, agregue el método `AddAzureStorage` en la instancia de [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) (antes del comando `Build`) para inicializar la extensión de Storage. En este momento, el método `ConfigureWebJobs` es similar al siguiente:

    ```cs
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    ```
1. Agregue el código siguiente en el método `Main` después de crear una instancia de `builder`:

    ```csharp
    builder.UseEnvironment(EnvironmentName.Development);
     ```

    La ejecución en [modo de desarrollo](webjobs-sdk-how-to.md#host-development-settings) reduce el [retroceso exponencial del sondeo de cola](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm) que puede retrasar significativamente lo que tarda el tiempo de ejecución en encontrar el mensaje e invocar la función. Debe quitar esta línea de código o cambiar a `Production` cuando haya terminado con el desarrollo y las pruebas. 

    Ahora el método `Main` se debe parecer al del ejemplo siguiente:

    ```csharp
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
        });
        builder.ConfigureLogging((context, b) =>
        {
            b.AddConsole();
        });
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
            b.AddAzureStorage();
        });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

### <a name="create-a-queue-triggered-function"></a>Crear una función desencadenada por la cola

El atributo `QueueTrigger` indica el tiempo de ejecución para llamar a esta función cuando se escribe un mensaje nuevo en una cola de Azure Storage denominada `queue`. El contenido del mensaje de cola se proporciona para el código del método en el parámetro `message`. El cuerpo del método es donde se procesan los datos de desencadenador. En este ejemplo, el código solo registra el mensaje.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto, seleccione **Agregar** > **Nuevo elemento** y después **Clase**. 

2. Asigne el nombre *Functions.cs* al nuevo archivo de clase de C# y seleccione **Agregar**.

3. En *Functions.cs*, reemplace la plantilla generada por el código siguiente:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

    Debe marcar la clase *Functions* como `public static` para que el runtime acceda al método y lo ejecute. En el código de ejemplo anterior, cuando se agrega un mensaje a una cola denominada `queue`, se ejecuta la función y la cadena `message` se escribe en los registros. La cola que se supervisa se encuentra en la cuenta de Azure Storage predeterminada, que creará a continuación.
   
El parámetro `message` no tiene que ser una cadena. También puede enlazar a un objeto JSON, una matriz de bytes o un objeto [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage). Consulte [Uso del desencadenador de cola](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Cada tipo de enlace (como colas, blobs o tablas) tiene un conjunto diferente de tipos de parámetros con los que puede enlazar.

### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

El emulador de Azure Storage que se ejecuta localmente no tiene todas las características que necesita el SDK de WebJobs. Creará una cuenta de almacenamiento en Azure y configurará el proyecto para utilizarla. 

Para aprender a crear una cuenta de almacenamiento v2 de uso general, vea [Creación de una cuenta de Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal).

### <a name="locate-and-copy-your-connection-string"></a>Búsqueda y copia de la cadena de conexión
Para configurar el almacenamiento se necesita una cadena de conexión. Conserve esta cadena de conexión para los pasos siguientes.

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento y seleccione **Configuración**.
1. En **Configuración**, seleccione **Claves de acceso**.
1. En **Cadena de conexión** en **key1**, seleccione el icono **Copiar al Portapapeles**.

     ![key](./media/webjobs-sdk-get-started/connection-key.png)

### <a name="configure-storage-to-run-locally"></a>Configuración del almacenamiento para ejecutarse localmente

El SDK de WebJobs busca la cadena de conexión de almacenamiento en la Configuración de la aplicación en Azure. Cuando se ejecuta localmente, busca este valor en el archivo de configuración local o en variables de entorno.

1. Haga clic con el botón derecho en el proyecto, seleccione **Agregar** > **Nuevo elemento**, seleccione **Archivo de configuración JSON de JavaScript**, asigne el nombre *appsettings.json* al nuevo archivo y seleccione **Agregar**. 

1. En el nuevo archivo, agregue un campo `AzureWebJobsStorage`, como en el ejemplo siguiente:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Reemplace *{storage connection string}* (cadena de conexión de almacenamiento) por la cadena de conexión que ha copiado antes.

1. Seleccione el archivo *appsettings.json* en el Explorador de soluciones y, en la ventana **Propiedades**, establezca la acción **Copiar en el directorio de salida** en **Copiar si es posterior**.

Como este archivo contiene un secreto de cadena de conexión, no debe almacenarlo en un repositorio de código remoto. Después de publicar el proyecto en Azure, puede agregar la misma configuración de aplicación de cadena de conexión en la aplicación en Azure App Service.

## <a name="test-locally"></a>Prueba local

Compile y ejecute el proyecto localmente y cree una cola de mensajes para desencadenar la función.

1. En **Cloud Explorer** en Visual Studio, expanda el nodo de la nueva cuenta de almacenamiento y, después, haga clic con el botón derecho en **Colas**.

1. Seleccione **Crear cola**.

1. Escriba *queue* (cola) como nombre de la cola y seleccione luego **Aceptar**.

   ![Captura de pantalla que muestra dónde se crea la cola y le asigna el nombre "queue". ](./media/webjobs-sdk-get-started/create-queue.png)

1. Haga clic con el botón derecho en el nodo de la nueva cola y seleccione **Abrir**.

1. Seleccione el icono **Agregar mensaje**.

   ![Captura de pantalla que resalta el icono Agregar mensaje.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. En el cuadro de diálogo **Agregar mensaje**, escriba *¡Hola mundo!* como **Texto del mensaje** y, a continuación, seleccione **Aceptar**. Ahora hay un mensaje en la cola.

   ![Crear cola](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Presione **Ctrl+F5** para ejecutar el proyecto.

   En la consola se muestra que el tiempo de ejecución ha encontrado la función. Como ha utilizado el atributo `QueueTrigger` en la función `ProcessQueueMessage`, el tiempo de ejecución de WebJobs escucha los mensajes de la cola `queue`. Cuando encuentra un mensaje nuevo en esta cola, el tiempo de ejecución llama a la función y pasa el valor de la cadena del mensaje.

1. Vuelva a la ventana **Cola** y actualícela. El mensaje desaparece, puesto que lo ha procesado la función que se ejecuta localmente.

1. Cierre la ventana de la consola. 

Es el momento de publicar el proyecto del SDK de WebJobs en Azure.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Implementación en Azure

Durante la implementación, cree una instancia de App Service en la que se ejecutarán las funciones. Al publicar una aplicación de consola de .NET Core en App Service de Azure, se ejecuta de forma automática como un WebJob. Para obtener más información sobre la publicación, consulte [Desarrollo e implementación de WebJobs mediante Visual Studio](webjobs-dotnet-deploy-vs.md).

### <a name="create-azure-resources"></a>Creación de recursos de Azure

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

### <a name="enable-always-on"></a>Habilitar Always On

Para un WebJob continuo, debe habilitar la opción Siempre disponible en el sitio para que los trabajos web se ejecuten correctamente. Si no habilita Siempre disponible, el tiempo de ejecución queda inactivo después de unos minutos de inactividad. 

1. En la página **Publicar**, seleccione los tres puntos situados sobre **Hospedaje** para mostrar las **acciones de la sección Perfil de hospedaje** y elija **Abrir en Azure Portal**.  

1. En **Configuración**, seleccione **Configuración** > **Configuración general**, establezca **Siempre disponible** en **Activado** y, después, seleccione **Guardar** y **Continuar** para reiniciar el sitio.

### <a name="publish-the-project"></a>Publicación del proyecto

Con la aplicación web creada en Azure, es el momento de publicar el proyecto de WebJobs. 

1. En la página **Publicar**, en **Hospedaje**, seleccione el botón Editar y cambie **Tipo de WebJob** a `Continuous` y seleccione **Guardar**. Con esto se asegura de que el WebJob está en ejecución cuando se agregan mensajes a la cola. Normalmente, los WebJobs desencadenados solo se usan para webhooks manuales. 

1. Seleccione el botón **Publicar** en la esquina superior derecha de la página **Publicar**. Cuando se completa la operación, el trabajo web se ejecuta en Azure.

### <a name="create-a-storage-connection-app-setting"></a>Creación de una configuración de la aplicación de conexión de almacenamiento

Tendrá que crear la misma configuración de cadena de conexión de almacenamiento en Azure que ha usado localmente en el archivo de configuración appsettings.json. Esto le permite almacenar de forma más segura la cadena de conexión.   

1. En el perfil de la página **Publicar**, seleccione los tres puntos situados sobre **Hospedaje** para mostrar las **acciones de la sección Perfil de hospedaje** y elija **Administrar la configuración de Azure App Service**.

1. En **Configuración de la aplicación**, elija **+ Agregar configuración**.

1. En **Nuevo nombre de configuración de aplicación**, escriba `AzureWebJobsStorage` y seleccione **Aceptar**. 
 
1. En **Remoto**, pegue la cadena de conexión de la configuración local y seleccione **Aceptar**. 

Ahora la cadena de conexión está establecida en la aplicación en Azure.

### <a name="trigger-the-function-in-azure"></a>Desencadenamiento de la función en Azure

1. Asegúrese de que no trabaja en el entorno local. Cierre la ventana de la consola si sigue abierta. En caso contrario, la instancia local podría ser la primera que procese los mensajes de cola que cree.

1. En la página **Cola** de Visual Studio, agregue un mensaje a la cola como antes.

1. Actualice la página **Cola** y el nuevo mensaje desaparecerá porque se ha procesado por la función que se ejecuta en Azure.

## <a name="enable-application-insights-logging"></a>Habilitar el registro de Application Insights.

Cuando WebJob se ejecuta en Azure, no puede supervisar la ejecución de la función mediante la visualización de la salida de la consola. Para poder supervisar el trabajo web, debe crear una instancia de [Application Insights](../azure-monitor/app/app-insights-overview.md) asociada al publicar el proyecto.

### <a name="create-an-application-insights-instance"></a>Crear una instancia de Application Insights

1. En la página del perfil **Publicar**, seleccione los tres puntos situados sobre **Hospedaje** para mostrar las **acciones de la sección Perfil de hospedaje** y elija **Abrir en Azure Portal**.

1. En la aplicación web en **Configuración**, elija **Application Insights** y seleccione **Activar Application Insights**.

1. Compruebe el **Nombre de recurso** generado para la instancia y la **Ubicación**, y seleccione **Aplicar**. 

1. En **Configuración**, elija **Configuración** y compruebe que se ha creado un objeto `APPINSIGHTS_INSTRUMENTATIONKEY`. Esta clave se usa para conectar la instancia de WebJob a Application Insights.   

Para aprovechar el registro de [Application Insights](../azure-monitor/app/app-insights-overview.md), también tendrá que actualizar el código de registro.

### <a name="install-the-application-insights-extension"></a>Instalación de la extensión Application Insights

1. Obtenga la versión estable más reciente del paquete NuGet [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights), versión 3.x.

2. En el comando siguiente, reemplace `<3_X_VERSION>` por el número de versión actual que ha encontrado en el paso 1. Cada tipo de paquete NuGet tiene un número de versión único. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
    ```
3. En la **Consola del Administrador de paquetes**, ejecute el comando con el número de versión actual en el punto de entrada `PM>`.

### <a name="initialize-the-application-insights-logging-provider"></a>Inicialización del proveedor de registro de Application Insights

Abra *Program.cs* y agregue el inicializador siguiente en `ConfigureLogging` después de la llamada a `AddConsole`:

```csharp
// If the key exists in settings, use it to enable Application Insights.
string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
if (!string.IsNullOrEmpty(instrumentationKey))
{
    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
}
```

Ahora el código del método `Main` se debe parecer al del ejemplo siguiente: 

```csharp
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment(EnvironmentName.Development);
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    builder.ConfigureLogging((context, b) =>
            {
                b.AddConsole();

                // If the key exists in settings, use it to enable Application Insights.
                string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                if (!string.IsNullOrEmpty(instrumentationKey))
                {
                    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                }
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Esto inicializa el proveedor de registro de Application Insights con [filtrado](webjobs-sdk-how-to.md#log-filtering) predeterminado. Cuando se ejecuta localmente, toda la información y los registros de nivel superior se escriben en la consola y en Application Insights.

### <a name="republish-the-project-and-trigger-the-function-again"></a>Nueva publicación del proyecto y volver a desencadenar la función

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

1. Como antes, use **Cloud Explorer** en Visual Studio para crear un mensaje de cola, como ha hecho [antes](#test-locally), pero ahora escriba *Hola, App Insights*. como texto del mensaje.

1. En la página del perfil **Publicar**, seleccione los tres puntos situados sobre **Hospedaje** para mostrar las **acciones de la sección Perfil de hospedaje** y elija **Abrir en Azure Portal**.

1. En la aplicación web en **Configuración**, elija **Application Insights** y seleccione **Ver datos de Application Insights**.

1. Seleccione **Buscar** y después **Ver todos los datos de las últimas 24 horas**.

   ![Selección de Buscar](./media/webjobs-sdk-get-started/select-search.png)

1. Si no ve el mensaje *¡Hola App Insights!* , seleccione **Actualizar** periódicamente durante varios minutos. Los registros no aparecen inmediatamente porque el cliente de Application Insights tarda un tiempo en vaciar los registros que procesa.

   ![Registros de Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

## <a name="add-inputoutput-bindings&quot;></a>Agregar enlaces de entrada y de salida.

Los enlaces simplifican el código que lee y escribe los datos. Los enlaces de entrada simplifican el código que lee los datos. Los enlaces de salida simplifican el código que escribe los datos.  

### <a name=&quot;add-input-binding&quot;></a>Adición del enlace de entrada

Los enlaces de entrada simplifican el código que lee los datos. En este ejemplo, el mensaje de cola es el nombre de un blob, que usará para buscar y leer un blob en Azure Storage.

1. En *Functions.cs*, reemplace el método `ProcessQueueMessage` por el código siguiente:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger(&quot;queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   En este código, `queueTrigger` es una [expresión de enlace](../azure-functions/functions-bindings-expressions-patterns.md), lo que significa que se resuelve con un valor diferente en tiempo de ejecución.  En tiempo de ejecución, tiene el contenido del mensaje de cola.

1. Agregue `using`:

   ```cs
   using System.IO;
   ```

1. Cree un contenedor de blobs en la cuenta de almacenamiento.

   a. En **Cloud Explorer** en Visual Studio, expanda el nodo de la cuenta de almacenamiento, haga clic con el botón derecho en **Blobs** y, después, seleccione **Crear contenedor de blobs**.

   b. En el cuadro de diálogo **Crear contenedor de blobs**, escriba *contenedor* como nombre para el contenedor y, después, seleccione **Aceptar**.

1. Cargue el archivo *Program.cs* en el contenedor de blobs. (Este archivo se usa aquí como ejemplo; podría cargar cualquier archivo de texto y crear un mensaje de cola con el nombre del archivo).

   a. En **Cloud Explorer**, haga doble clic en el nodo del contenedor que ha creado.

   b. En la ventana **Contenedor**, seleccione el botón **Cargar**.

   ![Botón de carga de blobs](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Busque y seleccione *Program.cs* y seleccione luego **Aceptar**.

1. Cree un mensaje de cola en la cola que ha creado anteriormente, con *Program.cs* como texto del mensaje.

   ![Mensaje de cola Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Ejecute el proyecto de forma local.

   El mensaje de cola desencadena la función, que lee el blob y registra su longitud. La salida de la consola tendrá un aspecto similar al siguiente:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```
### <a name="add-an-output-binding"></a>Adición de un enlace de salida

Los enlaces de salida simplifican el código que escribe los datos. Este ejemplo modifica el ejemplo anterior al escribir una copia del blob en lugar de registrar su tamaño. Los enlaces de Blob Storage se incluyen en el paquete de extensiones de Azure Storage que se instaló anteriormente.

1. Reemplace el método `ProcessQueueMessage` por el código siguiente:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Cree otro mensaje de cola con *Program.cs* como texto del mensaje.

1. Ejecute el proyecto de forma local.

   El mensaje de cola desencadena la función, que lee el blob, registra su longitud y crea un nuevo blob. La salida de la consola es la misma pero cuando vaya a la ventana del contenedor de blobs y seleccione **Actualizar**, verá un nuevo blob denominado *copy-Program.cs*.

### <a name="republish-the-project"></a>Volver a publicar el proyecto

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

1. En el cuadro de diálogo **Publicar**, asegúrese de que el perfil actual esté seleccionado y seleccione **Publicar**. Los resultados de la publicación se detallan en la ventana **Salida**.
 
1. Compruebe la función en Azure volviendo a cargar un archivo en el contenedor de blobs y agregando un mensaje a la cola que es el nombre del archivo cargado. Verá que el mensaje se quita de la cola y una copia del archivo creado en el contenedor de blobs. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se ha mostrado cómo crear, ejecutar e implementar un proyecto del SDK 3.x de WebJobs.

> [!div class="nextstepaction"]
> [Más información sobre el SDK de WebJobs](webjobs-sdk-how-to.md)
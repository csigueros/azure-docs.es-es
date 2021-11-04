---
title: Inicio rápido para agregar marcas de características a Azure Functions | Microsoft Docs
description: En este inicio rápido, use Azure Functions con marcas de características de Azure App Configuration y pruebe la función localmente.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 22de8b7775d6825d99afe8eb4f0dc043f8ec639c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031563"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Inicio rápido: Adición de marcas de características a una aplicación de Azure Functions

En este inicio rápido, creará una aplicación de Azure Functions y usará marcas de características en ella. La administración de características de Azure App Configuration se usa para almacenar de forma centralizada todas las marcas de características y controlar sus estados.

Las bibliotecas de administración de características de .NET amplían la plataforma con compatibilidad con las marcas de características. Estas bibliotecas se compilan a partir del sistema de configuración de .NET. Se integran con App Configuration mediante su proveedor de configuración de .NET.

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con la carga de trabajo de **desarrollo de Azure**.
- [Herramientas de Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Creación de un almacén de App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Seleccione **Administrador de características** >  **+Agregar** para agregar una marca de características denominada `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Habilitación de la marca de característica denominada Beta](media/add-beta-feature-flag.png)

    Deje `label` y `Description` sin definir por ahora.

8. Seleccione **Aplicar** para guardar la nueva marca de características.

## <a name="create-a-functions-app"></a>Creación de una aplicación de Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conexión a un almacén de App Configuration

En este proyecto se usa la [inserción de dependencias en Azure Functions para .NET](../azure-functions/functions-dotnet-dependency-injection.md). Se agrega Azure App Configuration como origen de configuración adicional donde se almacenan las marcas de características.

1. Haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**. En la pestaña **Examinar**, busque los siguientes paquetes NuGet y agréguelos al proyecto.
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/), versión 4.1.0 o posterior
   - [Microsoft.FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/), versión 2.2.0 o posterior
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/), versión 1.1.0 o posterior 

2. Agregue un nuevo archivo, *Startup.cs*, con el código siguiente. Se define una clase denominada `Startup` que implementa la clase abstracta `FunctionsStartup`. Para especificar el nombre de tipo empleado durante el inicio de Azure Functions, se usa un atributo de ensamblado.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Actualice el método `ConfigureAppConfiguration` y llame a `AddAzureAppConfiguration()` para agregar el proveedor de Azure App Configuration como origen de configuración adicional. 

    El método `UseFeatureFlags()` indica al proveedor que cargue las marcas de características. Todas las marcas de características tienen una expiración de caché predeterminada de 30 segundos antes de que se vuelvan a comprobar los cambios. Para actualizar el intervalo de expiración, se puede establecer la propiedad `FeatureFlagsOptions.CacheExpirationInterval` pasada al método `UseFeatureFlags`. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```

    > [!TIP]
    > Si no quiere que se cargue ninguna configuración que no sea la de las marcas de características en la aplicación, puede llamar a `Select("_")` para cargar solo una clave ficticia no existente`"_"`. De forma predeterminada, todos los valores de clave de configuración del almacén de App Configuration se cargarán si no se llama a ningún método `Select`.

4. Actualice el método `Configure` para que los servicios y el administrador de características de Azure App Configuration estén disponibles mediante la inserción de dependencias.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. Abra *Function1.cs* y agregue los siguientes espacios de nombres.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Agregue un constructor utilizado para obtener instancias de `_featureManagerSnapshot` y `IConfigurationRefresherProvider` mediante la inserción de dependencias. En `IConfigurationRefresherProvider`, puede obtener la instancia de `IConfigurationRefresher`.

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Actualice el método `Run` para cambiar el valor del mensaje mostrado en función del estado de la marca de característica.

   Se llama al método `TryRefreshAsync` al principio de la llamada a Functions para actualizar las marcas de características. Esta operación no tiene efecto si no se alcanza la ventana de tiempo de expiración de la caché. Si prefiere que las marcas de características se actualicen sin bloquear la llamada a Functions actual, quite el operador `await`. En ese caso, las llamadas posteriores a Functions recibirán el valor actualizado.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>Prueba local de la función

1. Establezca una variable de entorno denominada **ConnectionString**, donde el valor es la cadena de conexión que recuperó anteriormente en el almacén de App Configuration en **Claves de acceso**. Si usa el símbolo del sistema de Windows, ejecute el siguiente comando y reinícielo para que se aplique el cambio:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si usa Windows PowerShell, ejecute el siguiente comando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si usa macOS o Linux, ejecute el siguiente comando:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Presione F5 para probar la función. Si se le solicita, acepte la solicitud de Visual Studio para descargar e instalar las herramientas de **Azure Functions Core (CLI)** . También es preciso que habilite una excepción de firewall para que las herramientas para controlen las solicitudes de HTTP.

1. Copie la dirección URL de la función de los resultados del runtime de Azure Functions.

    ![Inicio rápido: depuración de funciones en VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Pegue la dirección URL de la solicitud HTTP en la barra de direcciones del explorador. En la imagen siguiente se muestra la respuesta que indica que la marca de la característica `Beta` está deshabilitada. 

    ![Marca de la característica de función de inicio rápido deshabilitada](./media/quickstarts/functions-launch-ff-disabled.png)

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Todos los recursos** y, después, elija el almacén de App Configuration que creó.

1. Seleccione **Administrador de características** y cambie el estado de la clave **Beta** a **Activado**.

1. Actualice el explorador varias veces. Cuando la marca de característica almacenada en caché expire al cabo de 30 segundos, la página debe haber cambiado para indicar que la marca de característica `Beta` está activada, como se muestra en la imagen siguiente.
 
    ![Marca de la característica de función de inicio rápido habilitada](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> El código de ejemplo que se usa en este tutorial se puede descargar del [repositorio de GitHub de App Configuration](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, creó una marca de característica y la usó con una aplicación de Azure Functions mediante la biblioteca [Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement).

- Más información acerca de la [administración de características](./concept-feature-management.md)
- [Administración de marcas de características](./manage-feature-flags.md)
- [Uso de marcas de características condicionales](./howto-feature-filters-aspnet-core.md)
- [Habilitar el lanzamiento preconfigurado de características para audiencias de destino](./howto-targetingfilter-aspnet-core.md)
- [Uso de la configuración dinámica en una aplicación de Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)
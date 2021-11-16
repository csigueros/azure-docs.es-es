---
title: Tutorial sobre el uso de la configuración dinámica de Azure App Configuration en una aplicación de Azure Functions | Microsoft Docs
description: En este tutorial aprenderá a actualizar dinámicamente los datos de configuración de las aplicaciones de Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ffa2b1a4537093f9a612f0e57b7f416ee7ed0012
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301919"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: Uso de la configuración dinámica en una aplicación de Azure Functions

El proveedor de configuración de App Configuration para .NET admite el almacenamiento en caché y la actualización de la configuración controlada dinámicamente por la actividad de la aplicación. Este tutorial le muestra cómo puede implementar las actualizaciones de configuración dinámica en el código. Se basa en la aplicación de Azure Functions que se presentó en las guías de inicio rápido. Antes de continuar, finalice primero el tutorial [Creación de una aplicación de Azure Functions con Azure App Configuration](./quickstart-azure-functions-csharp.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la aplicación de Azure Functions para actualizar su configuración en respuesta a los cambios en un almacén de App Configuration.
> * Inserte la configuración más reciente en las llamadas a Azure Functions.

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con la carga de trabajo de **desarrollo de Azure**.
- [Herramientas de Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Finalizar la guía de inicio rápido [Creación de una aplicación de Azure Functions con Azure App Configuration](./quickstart-azure-functions-csharp.md).

## <a name="reload-data-from-app-configuration"></a>Recarga de datos de App Configuration

1. Abra *Startup.cs* y actualice el método `ConfigureAppConfiguration`. 

   El método `ConfigureRefresh` registra un valor donde se comprobarán los cambios que se produzcan siempre que se desencadene una actualización en la aplicación, lo cual hará en el paso posterior al agregar `_configurationRefresher.TryRefreshAsync()`. El parámetro `refreshAll` indica al proveedor App Configuration que vuelva a cargar toda la configuración siempre que se detecte un cambio en la configuración registrada.

    Todas las configuraciones registradas para la actualización tienen un tiempo de expiración predeterminado de 30 segundos de la caché. Puede actualizarse al llamar al método `AzureAppConfigurationRefreshOptions.SetCacheExpiration`.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered sentinel key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Al actualizar varios valores de clave en App Configuration, normalmente no se desea que la aplicación vuelva a cargar la configuración hasta que se hayan realizado los cambios. Puede registrar una *clave de Sentinel* y actualizarla solo cuando se hayan completado los demás cambios de configuración. Esto ayuda a garantizar la coherencia de la configuración en la aplicación.

2. Actualice el método `Configure` para que los servicios de Azure App Configuration estén disponibles mediante la inserción de dependencias.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Abra *Function1.cs* y agregue los espacios de nombres siguientes.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Actualice el constructor para obtener la instancia de `IConfigurationRefresherProvider` mediante la inserción de dependencias, desde la que podrá obtener la instancia de `IConfigurationRefresher`.

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Actualice el método `Run` y señalice para actualizar la configuración mediante el método `TryRefreshAsync` al principio de la llamada a Azure Functions. No será operativo si no se alcanza la ventana de tiempo de expiración de la memoria caché. Elimine el operador `await` si prefiere que la configuración se actualice sin bloqueos en la llamada a Functions actual. De ese modo, las llamadas a Functions posteriores obtendrán el valor actualizado.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Prueba local de la función

1. Establezca una variable de entorno llamada **ConnectionString** y defínala como la clave de acceso a su almacén de configuración de aplicaciones. Si usa el símbolo del sistema de Windows, ejecute el siguiente comando y reinícielo para que se aplique el cambio:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Si usa Windows PowerShell, ejecute el siguiente comando:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Si usa macOS o Linux, ejecute el siguiente comando:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Para probar la función, presione F5. Si se le solicita, acepte la solicitud de Visual Studio para descargar e instalar las herramientas de **Azure Functions Core (CLI)** . También es preciso que habilite una excepción de firewall para que las herramientas para controlen las solicitudes de HTTP.

3. Copie la dirección URL de la función de los resultados del runtime de Azure Functions.

    ![Inicio rápido: depuración de funciones en VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Pegue la dirección URL de la solicitud HTTP en la barra de direcciones del explorador. La siguiente imagen muestra la respuesta en el explorador para la solicitud GET local devuelta por la función.

    ![Inicio rápido: inicio de funciones locales](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Todos los recursos** y seleccione el almacén de App Configuration que creó en el inicio rápido.

6. Seleccione **Explorador de configuración** y actualice el valor de la clave siguiente:

    | Clave | Value |
    |---|---|
    | TestApp:Settings:Message | Datos de Azure App Configuration, actualizados |

   A continuación, cree la clave de Sentinel o modifique su valor si ya existe; por ejemplo,

    | Clave | Valor |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. Actualice el explorador varias veces. Cuando la configuración almacenada en la memoria caché expira después de 30 segundos, la página muestra la respuesta de la llamada a Azure Functions con el valor actualizado.

    ![Función de inicio rápido de actualización local](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> El código de ejemplo que se usa en este tutorial se puede descargar desde el [repositorio de GitHub de App Configuration](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado la aplicación de Azure Functions para actualizar dinámicamente la configuración a partir de App Configuration. Para obtener información sobre cómo usar una identidad administrada de Azure para simplificar el acceso a App Configuration, vaya al siguiente tutorial.

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)

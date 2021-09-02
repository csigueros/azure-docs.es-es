---
title: Tutorial para usar las marcas de características en una aplicación de .NET Core | Microsoft Docs
description: En este tutorial, aprenderá a implementar las marcas de características en aplicaciones de .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5f15d016c890ec214fe6b6a3d8af3138d202408b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970345"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Uso de marcas de características en una aplicación de ASP.NET Core

Las bibliotecas de administración de características de .NET Core proporcionan soporte idiomático para implementar las marcas de características en una aplicación de .NET o ASP.NET Core. Estas bibliotecas le permiten agregar mediante declaración marcas de características al código a fin de que no tenga que escribir código manualmente para habilitar o deshabilitar características con las instrucciones `if`.

Las bibliotecas de administración de características también administran los ciclos de vida de las marcas de características en segundo plano. Por ejemplo, actualizan y almacenan en caché los estados de marca y garantizan que un estado de marca sea inmutable durante una llamada de solicitud. Además, la biblioteca de ASP.NET Core ofrece integraciones listas para usar, como acciones de controlador de MVC, vistas, rutas y middleware.

En [Inicio rápido: Adición de marcas de características a una aplicación web de ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) se muestra un ejemplo sencillo de cómo utilizar marcas de características en una aplicación ASP.NET Core. En este tutorial se muestran las funcionalidades y opciones de configuración adicionales de las bibliotecas de administración de características. Puede utilizar la aplicación de ejemplo creada en el inicio rápido para probar el código de ejemplo que se muestra en este tutorial. 

Para ver la documentación de referencia de la API de administración de características de ASP.NET Core, consulte [Espacio de nombres Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Agregar marcas de características en las partes clave de la aplicación para controlar la disponibilidad de características.
> * Integrar con App Configuration cuando se usa para administrar las marcas de características.

## <a name="set-up-feature-management"></a>Configuración de la administración de características

Para tener acceso al administrador de características de .NET Core, la aplicación debe tener referencias al paquete NuGet `Microsoft.FeatureManagement.AspNetCore`.

El administrador de características de .NET Core se configura a partir del sistema de configuración nativo del marco de trabajo. Como resultado, puede definir los valores de las marcas de características de la aplicación con cualquier origen de configuración que .NET Core admita, incluido el archivo local *appsettings.json* o las variables de entorno.

De forma predeterminada, el administrador de características recupera la configuración de las marcas de características de la sección `"FeatureManagement"` de los datos de configuración de .NET Core. Para utilizar la ubicación de configuración predeterminada, llame al método [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) del elemento **IServiceCollection** que se pasa al método **ConfigureServices** de la clase **Startup**.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Puede especificar que la configuración de administración de características se recupere de una sección de configuración diferente llamando a [Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) y pasando el nombre de la sección deseada. En el ejemplo siguiente se le indica al administrador de características que lea de una sección diferente que se llama `"MyFeatureFlags"` en su lugar:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Si utiliza filtros en las marcas de características, debe incluir el espacio de nombres [Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) y agregar una llamada a [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) en la que especifique el nombre de tipo del filtro que desea utilizar como tipo genérico del método. Para más información sobre el uso de filtros de características para habilitar y deshabilitar la funcionalidad de forma dinámica, consulte [Habilitar el lanzamiento preconfigurado de características para audiencias de destino](./howto-targetingfilter-aspnet-core.md).

El ejemplo siguiente muestra cómo usar un filtro de características integrado que se llama `PercentageFilter`:



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

En lugar de codificar de forma rígida las marcas de características en la aplicación, se recomienda mantener las marcas de características fuera de la aplicación y administrarlas por separado. De este modo, podrá modificar los estados de las marcas en cualquier momento y hacer que estos cambios surtan efecto en la aplicación de inmediato. El servicio Azure App Configuration proporciona una interfaz de usuario del portal dedicada para administrar todas las marcas de características. El servicio Azure App Configuration también proporciona las marcas de características a la aplicación directamente mediante sus bibliotecas cliente de .NET Core.

La manera más fácil de conectar la aplicación de ASP.NET Core con App Configuration es mediante el proveedor de configuración que se incluye en el paquete NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`. Después de incluir una referencia al paquete, siga estos pasos para utilizar este paquete NuGet.

1. Abra el archivo *Program.cs* y agregue el código siguiente.
    > [!IMPORTANT]
    > `CreateHostBuilder` reemplaza a `CreateWebHostBuilder` en .NET Core 3.x. Seleccione la sintaxis correcta en función de su entorno.

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Abra *Startup.cs* y actualice el método `Configure` y el `ConfigureServices` para agregar el middleware integrado denominado `UseAzureAppConfiguration`. Este middleware permita actualizar los valores de marca de características según un intervalo periódico mientras la aplicación web de ASP.NET Core sigue recibiendo solicitudes.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
En un escenario típico, actualizará los valores de marcas de características periódicamente a medida que vaya implementando y habilitando diferentes características de la aplicación. De forma predeterminada, los valores de la marca de característica se almacenan en caché durante un período de 30 segundos, por lo que una operación de actualización desencadenada cuando el middleware recibe una solicitud no actualizará el valor hasta que expire el valor almacenado en caché. En el código siguiente se muestra cómo cambiar la hora de expiración de caché o el intervalo de sondeo a 5 minutos estableciendo [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) en la llamada en **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Declaración de la marca de características

Cada declaración de marca de características tiene dos partes: un nombre y una lista de uno o varios filtros que se utilizan para evaluar si el estado de la característica está *activo* (es decir, cuando su valor es `True`). Un filtro define un criterio sobre cuándo se debe activar una característica.

Cuando una marca de características tiene varios filtros, la lista de filtros se recorre en orden hasta que uno de los filtros determina que se debe habilitar la característica. En ese momento, la marca de característica está *activa* y se omiten los resultados del filtro restantes. Si ningún filtro indica que se debe habilitar la característica, la marca de características está *desactivada*.

El administrador de la característica admite *appsettings.json* como origen de configuración para las marcas de características. El ejemplo siguiente muestra cómo establecer las marcas de características en un archivo JSON:

```JSON
{"FeatureManagement": {
        "FeatureA": true, // Feature flag set to on
        "FeatureB": false, // Feature flag set to off
        "FeatureC": {
            "EnabledFor": [
                {
                    "Name": "Percentage",
                    "Parameters": {
                        "Value": 50
                    }
                }
            ]
        }
    }
}
```

Por convención, la sección `FeatureManagement` de este documento JSON se usa para la configuración de la marca de características. El ejemplo anterior muestra tres marcas de características con los filtros definidos en la propiedad `EnabledFor`:

* `FeatureA` está *activada*.
* `FeatureB` está *desactivada*.
* `FeatureC` especifica un filtro denominado `Percentage` con una propiedad `Parameters`. `Percentage` es un filtro configurable. En este ejemplo, `Percentage` especifica una probabilidad del 50 por ciento de que la marca `FeatureC` esté *activada*. Para ver una guía paso a paso sobre el uso de filtros de características, consulte [Uso de filtros de características para habilitar las marcas de características condicionales](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Uso de la inyección de dependencias para acceder a IFeatureManager 

En algunas operaciones, como la comprobación manual de los valores de marcas de características, es necesario obtener una instancia de [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview). En ASP.NET Core MVC, puede acceder al administrador de características `IFeatureManager` mediante la inserción de dependencias. En el ejemplo siguiente, se agrega un argumento de tipo `IFeatureManager` a la firma del constructor para un controlador. El tiempo de ejecución resuelve automáticamente la referencia y proporciona un de la interfaz al llamar al constructor. Si utiliza una plantilla de aplicación en la que el controlador ya tiene uno o más argumentos de inyección de dependencias en el constructor, como `ILogger`, puede agregar `IFeatureManager` como argumento adicional:

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Referencias de marcas de características

Defina marcas de características como variables de cadena para hacer referencia a ellas desde el código:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Comprobaciones de las marcas de características

Un patrón común de administración de características es comprobar si una marca de características está *activada* y, en caso afirmativo, ejecutar una sección de código. Por ejemplo:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Acciones de controlador

Con los controladores MVC, puede usar un atributo `FeatureGate` para controlar si se habilita una clase de controlador completa o una acción específica. El siguiente controlador `HomeController` requiere que `FeatureA` esté *activada* para ejecutar cualquier acción que contenga la clase del contenedor:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

La siguiente acción `Index` requiere que `FeatureA` esté *activada* para ejecutarse:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Cuando una acción o controlador MVC está bloqueado porque la marca de característica de control está *desactivada*, se llama a una interfaz [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) registrada. La interfaz predeterminada `IDisabledFeaturesHandler` devuelve un código de estado 404 al cliente sin cuerpo de respuesta.

## <a name="mvc-views"></a>Vistas de MVC

Abra *_ViewImports.cshtml* en el directorio *Views* y agregue el asistente de etiquetas del administrador de características:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

En las vistas de MVC, se puede usar una etiqueta `<feature>` para representar el contenido en función de si una marca de características está habilitada:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Para mostrar contenido alternativo cuando no se cumplen los requisitos, puede usar el atributo `negate`.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

También se puede usar la etiqueta `<feature>` de la característica para mostrar el contenido si están habilitadas todas o alguna de las características de una lista.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtros MVC

Los filtros MVC se pueden configurar de manera que se activen en función del estado de una marca de características. Esta funcionalidad se limita a los filtros que implementan [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). El código siguiente agrega un filtro MVC denominado `ThirdPartyActionFilter`. Este filtro se desencadena en la canalización de MVC solo si está habilitada `FeatureA`.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Software intermedio

También puede usar las marcas de características para agregar condicionalmente ramas de aplicación y middleware. El código siguiente inserta un componente de middleware en la canalización de la solicitud solo cuando `FeatureA` está habilitada:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Este código desarrolla una funcionalidad más genérica de crear una rama de toda la aplicación en función de una marca de características:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a implementar marcas de características en la aplicación ASP.NET Core mediante el uso de bibliotecas `Microsoft.FeatureManagement`. Consulte los siguientes recursos para más información sobre la compatibilidad de la administración de características en ASP.NET Core y App Configuration:

* [ASP.NET Core feature flag sample code](./quickstart-feature-flag-aspnet-core.md) (Código de ejemplo de marca de características de ASP.NET Core)
* [Microsoft.FeatureManagement documentation](/dotnet/api/microsoft.featuremanagement) (Documentación de Microsoft.FeatureManagement)
* [Administración de marcas de características](./manage-feature-flags.md)
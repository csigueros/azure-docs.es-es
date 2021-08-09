---
title: Uso de etiquetas para proporcionar valores de configuración para cada entorno.
titleSuffix: Azure App Configuration
description: En este artículo se describe cómo usar etiquetas para recuperar los valores de configuración de una aplicación para el entorno en el que esta se ejecuta actualmente.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: e6d9aadff5fba66aef260c674f5a01904b289da2
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592365"
---
# <a name="use-labels-to-provide-per-environment-configuration-values"></a>Uso de etiquetas para proporcionar valores de configuración para cada entorno.

Muchas aplicaciones deben usar diferentes configuraciones en distintos entornos. Supongamos que una aplicación tiene un valor de configuración que define la cadena de conexión que se debe usar para la base de datos de back-end. Los desarrolladores de la aplicación usan una base de datos distinta de la que se usa en producción. La cadena de conexión de base de datos que la aplicación usa debe cambiarse a medida que la aplicación pasa del entorno de desarrollo a uno de producción.

En Azure App Configuration, puede usar *etiquetas* para definir valores diferentes para la misma clave. Por ejemplo, puede definir una clave única con valores diferentes para desarrollo y producción. Puede especificar qué etiqueta se va a cargar al conectarse a App Configuration.

Para demostrar esta funcionalidad, modificará la aplicación web creada en el [Inicio rápido: Creación de una aplicación ASP.NET Core con Azure App Configuration](./quickstart-aspnet-core-app.md) para que use opciones de configuración distintas para el desarrollo y la producción. Complete la guía de inicio rápido antes de continuar.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especificación de una etiqueta al agregar un valor de configuración

En Azure Portal, vaya al **Explorador de configuración** y busque la clave *TestApp:Settings:FontColor* que creó en la guía de inicio rápido. Seleccione el menú contextual y, a continuación, seleccione **Agregar valor**.

> [!div class="mx-imgBorder"]
> ![Elemento de menú Agregar valor](media/labels-add-value.png)

En la pantalla **Agregar valor**, escriba un **Valor** de **red** y una **Etiqueta** de **Development**. Deje el **Tipo de contenido** vacío. Seleccione **Aplicar**.

## <a name="load-configuration-values-with-a-specified-label"></a>Cargar valores de configuración con una etiqueta especificada

De forma predeterminada, Azure App Configuration solo carga los valores de configuración sin etiqueta. Si definió etiquetas para los valores de configuración, querrá especificar las etiquetas que se van a usar al conectarse a App Configuration.

En la sección anterior, creó un valor de configuración diferente para el entorno de desarrollo. Use la variable `HostingEnvironment.EnvironmentName` para determinar de forma dinámica en qué entorno se está ejecutando actualmente la aplicación. Para obtener más información, consulte [Usar varios entornos en ASP.NET Core](/aspnet/core/fundamentals/environments).

Agregue una referencia al espacio de nombres [Microsoft.Extensions.Configuration.AzureAppConfiguration](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration) para acceder a las clases [KeyFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.keyfilter) y [LabelFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.labelfilter).

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
``` 

Cargue los valores de configuración con la etiqueta correspondiente al entorno actual. Para ello, pase el nombre del entorno al método `Select`:

### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>());
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>());
```

### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>();
```
---


> [!IMPORTANT]
> El fragmento de código anterior usa la herramienta Secret Manager para cargar la cadena de conexión de App Configuration. Para obtener información sobre cómo almacenar la cadena de conexión mediante Secret Manager, consulte [Inicio rápido: Creación de una aplicación ASP.NET Core con Azure App Configuration](quickstart-aspnet-core-app.md).

Se llama dos veces al método `Select`. La primera vez, carga los valores de configuración sin etiqueta. A continuación, carga los valores de configuración con la etiqueta correspondiente al entorno actual. Estos valores específicos del entorno invalidan a todos los valores correspondientes sin etiqueta. No es necesario definir valores específicos del entorno para cada clave. Si una clave no tiene algún valor con una etiqueta que corresponda al entorno actual, se usará el valor sin etiqueta.

## <a name="test-in-different-environments"></a>Pruebas en entornos diferentes

Abra el archivo `launchSettings.json` en el directorio `Properties`. Busque la entrada `config` en `profiles`. En la sección `environmentVariables`, establezca la variable `ASPNETCORE_ENVIRONMENT` en `Production`.

Una vez establecidos los nuevos valores, compile y ejecute la aplicación.

```dotnetcli
dotnet build
dotnet run
```

Use un explorador web para ir a `http://localhost:5000`. Observará que el color de la fuente es negro.

![Aplicación web ejecutándose con la configuración para producción](media/labels-website-prod.png)

Actualice `launchSettings.json` para establecer la variable `ASPNETCORE_ENVIRONMENT` en `Development`. Vuelva a ejecutar `dotnet run`. 

Observará que el color de fuente ahora es rojo. Esto se debe a que la aplicación ahora usa el valor de `TestApp:Settings:FontColor` que tiene la etiqueta `Development`. Todos los demás valores de configuración siguen siendo los mismos que los valores de producción.

![Aplicación web ejecutándose con la configuración para desarrollo](media/labels-website-dev.png)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración en ASP.NET Core](/aspnet/core/fundamentals/configuration/)

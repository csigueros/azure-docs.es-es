---
title: Registro de Application Insights con .NET
description: Aprenda a usar Application Insights con la interfaz ILogger en .NET.
ms.topic: conceptual
ms.date: 05/20/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 6b0c746ffda97c22dc42a4c2016413a80552d28d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461523"
---
# <a name="application-insights-logging-with-net"></a>Registro de Application Insights con .NET

En este artículo aprenderá a capturar registros con Application Insights aplicaciones de .NET mediante varios paquetes NuGet:

- **Paquete principal:**
  - [`Microsoft.Extensions.Logging.ApplicationInsights`][nuget-ai]
- **Paquetes de carga de trabajo:**
  - [`Microsoft.ApplicationInsights.AspNetCore`][nuget-ai-anc]
  - [`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`][nuget-ai-ws-tc]

[nuget-ai]: https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights
[nuget-ai-anc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore
[nuget-ai-ws]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService
[nuget-ai-ws-tc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel

> [!TIP]
> El paquete NuGet [`Microsoft.ApplicationInsights.WorkerService`][nuget-ai-ws] está fuera del ámbito de este artículo. Se puede usar para habilitar Application Insights para los servicios en segundo plano. Para más información, consulte [Application Insights para aplicaciones de Worker Service](./worker-service.md).

En función del paquete de registros de Application Insights que use, habrá varias maneras de registrar `ApplicationInsightsLoggerProvider`. `ApplicationInsightsLoggerProvider` es una implementación de <xref:Microsoft.Extensions.Logging.ILoggerProvider>, que es responsable de proporcionar las implementaciones de <xref:Microsoft.Extensions.Logging.ILogger> y <xref:Microsoft.Extensions.Logging.ILogger%601>.

## <a name="aspnet-core-applications"></a>Aplicaciones de ASP.NET Core

Para agregar telemetría de Application Insights a aplicaciones de ASP.NET Core, use el paquete NuGet `Microsoft.ApplicationInsights.AspNetCore`. Se puede configurar desde [Visual Studio como un servicio conectado](/visualstudio/azure/azure-app-insights-add-connected-service), o bien manualmente.

De forma predeterminada, las aplicaciones de ASP.NET Core tienen un proveedor de registro de Application Insights registrado cuando se configuran mediante el enfoque [con código](./asp-net-core.md) o [sin código](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring). El proveedor registrado está configurado para capturar automáticamente eventos de registro con una gravedad de <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> o mayor. La gravedad y las categorías se pueden personalizar. Para más información, consulte [Control del nivel de registro](#control-logging-level).

1. Asegúrese de que el paquete NuGet está instalado:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />
    </ItemGroup>
   ```

1. Asegúrese de que el método `Startup.ConfigureServices` llama a `services.AddApplicationInsightsTelemetry`:

    ```csharp
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Configuration;
    
    namespace WebApplication
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddApplicationInsightsTelemetry(
                    Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                // An alternative overload, when not using appsettings.json or user secrets.
                // services.AddApplicationInsightsTelemetry();
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                // omitted for brevity
            }
        }
    }
    ```

Con el paquete NuGet instalado y el proveedor registrado con la inserción de dependencias, la aplicación está lista para registrarse. Con la inserción de constructores, requiera <xref:Microsoft.Extensions.Logging.ILogger> o la alternativa de tipo genérico <xref:Microsoft.Extensions.Logging.ILogger%601>. Cuando se resuelvan estas implementaciones, `ApplicationInsightsLoggerProvider` las proporcionará. Los mensajes o las excepciones registrados se enviarán a Application Insights. Considere el siguiente controlador de ejemplo:

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");

        return new string[] { "value1", "value2" };
    }
}
```

Consulte el artículo sobre el [registro de ASP.NET Core](/aspnet/core/fundamentals/logging) para más información.

### <a name="capture-logs-within-aspnet-core-startup-code"></a>Captura de registros en el código de inicio de ASP.NET Core

Algunos escenarios requieren la captura de registros como parte de la rutina de inicio de la aplicación, antes de que la canalización de solicitud-respuesta esté lista para aceptar solicitudes. Sin embargo, las implementaciones de `ILogger` no están disponibles fácilmente desde la inserción de dependencias en *Program.cs* y *Startup.cs*. Para obtener más información, consulte [Registro en .NET: Creación de registros en `Main`](/dotnet/core/extensions/logging?tabs=command-line#create-logs-in-main).

Existen varias limitaciones aplicables al realizar el registro desde *Program.cs* y *Startup.cs*:

* La telemetría se envía mediante el canal de telemetría [InMemoryChannel](./telemetry-channels.md).
* No se aplica ningún [muestreo](./sampling.md) a la telemetría.
* Los [inicializadores o procesadores de telemetría](./api-filtering-sampling.md) estándar no están disponibles.

En los ejemplos siguientes se muestra esto mediante la creación de instancias y la configuración explícitas de *Program.cs* y *Startup.cs*:

#### <a name="example-programcs"></a>Ejemplo de Program.cs

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("From Program, running the host now.");

            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                })
                .ConfigureLogging((context, builder) =>
                {
                    // Providing an instrumentation key is required if you're using the
                    // standalone Microsoft.Extensions.Logging.ApplicationInsights package,
                    // or when you need to capture logs during application startup, for example
                    // in the Program.cs or Startup.cs itself.
                    builder.AddApplicationInsights(
                        context.Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                    // Capture all log-level entries from Program
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Program).FullName, LogLevel.Trace);

                    // Capture all log-level entries from Startup
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Startup).FullName, LogLevel.Trace);
                });
    }
}
```

En el código anterior, el objeto `ApplicationInsightsLoggerProvider` se configura con la cadena de conexión `"APPINSIGHTS_CONNECTIONSTRING"` y se aplica un filtro que establece el nivel de registro en <xref:Microsoft.Extensions.Logging.LogLevel.Trace?displayProperty=nameWithType>.

> [!IMPORTANT]
> Las [cadenas de conexión](./sdk-connection-string.md?tabs=net) se recomiendan por encima de las claves de instrumentación. Las nuevas regiones de Azure **requieren** el uso de cadenas de conexión en lugar de claves de instrumentación. La cadena de conexión identifica el recurso con el que se quieren asociar los datos de telemetría. También permite modificar los puntos de conexión que va a usar el recurso como destino de la telemetría. Tiene que copiar la cadena de conexión y agregarla al código de la aplicación o a una variable de entorno.

#### <a name="example-startupcs"></a>Ejemplo de Startup.cs

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace WebApplication
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddApplicationInsightsTelemetry(
                Configuration["APPINSIGHTS_CONNECTIONSTRING"]);
        }

        // The ILogger<Startup> is resolved by dependency injection
        // and available in Startup.Configure.
        public void Configure(
            IApplicationBuilder app, IWebHostEnvironment env, ILogger<Startup> logger)
        {
            logger.LogInformation(
                "Configuring for {Environment} environment",
                env.EnvironmentName);

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

## <a name="console-application"></a>Aplicación de consola

Paquetes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="5.0.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.17.0"/>
</ItemGroup>
```

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new InMemoryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(config => config.TelemetryChannel = channel);
                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by delay is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}

```

En el ejemplo anterior, se utiliza el paquete `Microsoft.Extensions.Logging.ApplicationInsights`. De forma predeterminada, esta configuración utiliza el objeto `TelemetryConfiguration` mínimo necesario para enviar datos a Application Insights. Por "mínimo necesario" se entiende que `InMemoryChannel` es el canal utilizado. No existe ningún muestreo ni ningún objeto `TelemetryInitializer` estándar. Este comportamiento se puede invalidar para una aplicación de consola, tal como se muestra en el siguiente ejemplo.

Instale este paquete adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.17.0" />
```

En la sección siguiente se muestra cómo invalidar el valor predeterminado `TelemetryConfiguration` mediante el método <xref:Microsoft.Extensions.Options.ConfigureOptions%601.Configure(%600)>. En este ejemplo se configura `ServerTelemetryChannel` y el muestreo. Se agrega un elemento ITelemetryInitializer personalizado a TelemetryConfiguration.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new ServerTelemetryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(
                    config =>
                    {
                        config.TelemetryChannel = channel;

                        // Optional: implement your own TelemetryInitializer and configure it here
                        // config.TelemetryInitializers.Add(new MyTelemetryInitializer());

                        config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
                        channel.Initialize(config);
                    });

                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by delay is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}
```

## <a name="control-logging-level"></a>Control del nivel de registro

Las implementaciones de `ILogger` tienen un mecanismo integrado para aplicar el [filtrado de registros](/dotnet/core/extensions/logging#how-filtering-rules-are-applied). Esto permite controlar los registros que se envían a cada proveedor registrado, incluido el proveedor de Application Insights. El filtrado se puede realizar en la configuración (por ejemplo, mediante un archivo *appsettings.json*) o en el código.

En los siguientes ejemplos se muestra el modo de aplicar reglas de filtro a `ApplicationInsightsLoggerProvider`.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Creación de reglas de filtro en la configuración con appsettings.json

El objeto `ApplicationInsightsLoggerProvider` recibe el alias "ApplicationInsights". La siguiente sección del archivo *appsettings.json* invalida el nivel de registro <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> predeterminado de Application Insights para registrar las categorías que comienzan por "Microsoft" en el nivel <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> y superiores.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Creación de reglas de filtro en el código

En el siguiente fragmento de código se configuran los registros en el nivel <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> y superiores de todas las categorías y en el nivel <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> y superiores de las categorías que comienzan por "Microsoft" que se envíen a `ApplicationInsightsLoggerProvider`.

```csharp
Host.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(builder =>
    {
        builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Warning);
        builder.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
    });
```

Este código anterior es funcionalmente equivalente a la sección anterior de *appsettings.json*. Para obtener más información, vea [Configuración en .NET](/dotnet/core/extensions/configuration).

## <a name="logging-scopes"></a>Ámbitos de registro

El objeto `ApplicationInsightsLoggingProvider` admite [Ámbitos de registro](/dotnet/core/extensions/logging#log-scopes), y los ámbitos están habilitados de manera predeterminada. Si el ámbito es de tipo `IReadOnlyCollection<KeyValuePair<string,object>>`, todos los pares clave-valor de la colección se agregan a la telemetría de Application Insights como propiedades personalizadas. En el ejemplo siguiente, los registros se capturarán como `TraceTelemetry` y tendrán `("MyKey", "MyValue")` en las propiedades.

```csharp
using (_logger.BeginScope(new Dictionary<string, object> { ["MyKey"] = "MyValue" }))
{
    _logger.LogError("An example of an Error level message");
}
```

Si se usa cualquier otro tipo como ámbito, se almacenará en la propiedad "Scope" de la telemetría de Application Insights. En el ejemplo siguiente, `TraceTelemetry` tendrá una propiedad denominada "Scope" que contiene el ámbito.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>¿Cuáles son las versiones antigua y nueva de ApplicationInsightsLoggerProvider?

El [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluía un elemento ApplicationInsightsLoggerProvider integrado (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que se habilitaba a través de métodos de extensión de  **ILoggerFactory**. Este proveedor está marcado como obsoleto desde la versión 2.7.1. Se quitará por completo en el siguiente cambio de versión principal. El paquete [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) en sí no está obsoleto. Se necesita para habilitar la supervisión de las solicitudes, las dependencias, etcétera.

La alternativa propuesta es el nuevo paquete independiente [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contiene un proveedor ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) y métodos de extensión en ILoggerBuilder para habilitarlo.

El [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versión 2.7.1 depende del nuevo paquete y habilita la captura de ILogger automáticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>¿Por qué algunos registros de ILogger se muestran dos veces en Application Insights?

La duplicación puede ocurrir si tiene la versión anterior (ya obsoleta) de ApplicationInsightsLoggerProvider habilitada mediante una llamada a `AddApplicationInsights` en `ILoggerFactory`. Compruebe si el método **Configure** incluye la siguiente información y quítela:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Si experimenta un registro doble cuando se lleva a cabo la depuración desde Visual Studio, establezca `EnableDebugLogger` en `false` en el código que habilita Application Insights, como se indica a continuación. Esta duplicación y la corrección solo son pertinentes cuando se depura la aplicación.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions
    {
        EnableDebugLogger = false
    }
    services.AddApplicationInsightsTelemetry(options);
    // ...
}
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Actualicé al [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versión 2.7.1 y los registros de ILogger se capturan automáticamente. ¿Cómo desactivo por completo esta característica?

Consulte en la sección [Control del nivel de registro](#control-logging-level) cómo filtrar los registros en general. Para desactivar ApplicationInsightsLoggerProvider, utilice `LogLevel.None`:

En la llamada de registro de configuración, donde `builder` es un objeto <xref:Microsoft.Extensions.Logging.ILoggingBuilder>:

```csharp
builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.None);
```

Desde el archivo *appsettings.json*:

```json
{
    "Logging": {
        "ApplicationInsights": {
            "LogLevel": {
                "Default": "None"
            }
        }
    }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>¿Por qué algunos registros de ILogger no tiene las mismas propiedades que otros?

Application Insights captura y envía los registros de ILogger con el mismo valor de TelemetryConfiguration que se usa para los demás datos de telemetría. Sin embargo, hay una excepción. De forma predeterminada, la configuración de TelemetryConfiguration no está completa cuando se registra desde *Program.cs* o *Startup.cs*. Los registros con estas procedencias no tendrán la configuración predeterminada, por lo que no se ejecutarán todos los objetos TelemetryInitializer y TelemetryProcessor.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estoy usando el paquete independiente Microsoft.Extensions.Logging.ApplicationInsights y quiero registrar manualmente algunos datos de telemetría personalizados adicionales. ¿Cómo debo hacerlo?

Cuando se usa el paquete independiente, `TelemetryClient` no se inserta en el contenedor de DI, por lo que necesita crear una nueva instancia de `TelemetryClient` y utilizar la misma configuración que utilice el proveedor de registro, como se muestra en el código siguiente. Esto garantiza que se usa la misma configuración para todos los datos de telemetría personalizados y los datos de telemetría de ILogger.

```csharp
public class MyController : ApiController
{
   // This TelemetryClient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Si utiliza el paquete `Microsoft.ApplicationInsights.AspNetCore` para habilitar Application Insights, modifique este código para obtener `TelemetryClient` directamente en el constructor. Para ver un ejemplo, consulte [esta pregunta frecuente](./asp-net-core.md#frequently-asked-questions).

### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>¿Qué tipo de datos de telemetría de Application Insights se generan a partir de los registros de ILogger? O bien, ¿dónde puedo ver los registros de ILogger en Application Insights?

ApplicationInsightsLoggerProvider captura los registros de ILogger y crea un elemento TraceTelemetry a partir de ellos. Si se pasa un objeto Exception al método `Log` de `ILogger`, se crea un elemento *ExceptionTelemetry* en lugar de TraceTelemetry. Estos elementos de telemetría pueden encontrarse en los mismos lugares que otros elementos TraceTelemetry o ExceptionTelemetry para Application Insights, incluido el portal, análisis o el depurador local de Visual Studio.

Si prefiere enviar `TraceTelemetry` siempre, use este fragmento de código:

```csharp
builder.AddApplicationInsights(
    options => options.TrackExceptionsAsExceptionTelemetry = false);
```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>No tengo instalado el SDK y uso la extensión Web Apps de Azure para habilitar Application Insights para mis aplicaciones de ASP.NET Core. ¿Cómo uso el nuevo proveedor? 

La extensión de Application Insights en Azure Web Apps usa el nuevo proveedor. Puede modificar las reglas de filtrado en el archivo *appsettings.json* para la aplicación.

## <a name="next-steps"></a>Pasos siguientes

* [Registro en .NET](/dotnet/core/extensions/logging)
* [Registro en ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Registros de seguimiento de .NET en Application Insights](./asp-net-trace-logs.md)

---
title: Serialización de la caché de tokens (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información acerca de la serialización y la serialización personalizada de la caché de tokens mediante la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 67dbc1ba66f18bb6d779d1185d863541272acd56
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451716"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialización de la caché de tokens en MSAL.NET

Después de [adquirir un token](msal-acquire-cache-tokens.md), la Biblioteca de autenticación de Microsoft (MSAL) lo almacena en caché. Las aplicaciones cliente públicas (aplicaciones de escritorio o móviles) deben intentar obtener un token de la memoria caché antes de adquirir un token mediante otro método. Los métodos de adquisición en aplicaciones cliente confidenciales administran ellos mismos la memoria caché. Este artículo describe la serialización predeterminada y personalizada de la caché de tokens en MSAL.NET.

## <a name="quick-summary"></a>Resumen rápido

La recomendación es:
- En las aplicaciones web y las API web, use [serializadores de caché de tokens de "Microsoft.Identity.Web"](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization). Estos proporcionan una base de datos distribuida o un sistema de caché para almacenar tokens.
  - En las [aplicaciones web](scenario-web-app-call-api-overview.md) y [API web](scenario-web-api-call-api-overview.md) de ASP.NET Core, use Microsoft.Identity.Web como una API de nivel superior.
  - En ASP.NET clásico, .NET Core y .NET Framework, use MSAL.NET directamente con los [adaptadores de serialización de caché de tokens para MSAL]() proporcionados en Microsoft.Identity.Web. 
- En las aplicaciones de escritorio (que pueden usar el sistema de archivos para almacenar tokens), use [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache) con MSAL.Net.
- En las aplicaciones móviles (Xamarin.iOS, Xamarin.Android, Plataforma universal de Windows) no haga nada, ya que MSAL.NET controla la memoria caché automáticamente: estas plataformas tienen un almacenamiento seguro.

## <a name="aspnet-core-web-apps-and-web-apis"></a>[Aplicaciones web y API web de ASP.NET Core](#tab/aspnetcore)

La biblioteca [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) proporciona un paquete NuGet [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) que contiene la serialización de la caché de tokens:

| Método de extensión | Descripción  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | Serialización de la caché de tokens en memoria. Esta implementación es excelente para pruebas. También funciona bien en las aplicaciones de producción, siempre que no le importe que se pierda la caché de tokens cuando se reinicia la aplicación web. `AddInMemoryTokenCaches` toma un parámetro opcional de tipo `MsalMemoryTokenCacheOptions` que le permite especificar la duración transcurrida la cual expirará la entrada de caché a menos que se utilice.
| `AddSessionTokenCaches` | La caché de tokens está enlazada a la sesión de usuario. Esta opción no es conveniente si el token de identificador contiene muchas notificaciones, ya que la cookie se volvería demasiado grande.
| `AddDistributedTokenCaches` | La caché de tokens es un adaptador contra la implementación de `IDistributedCache` ASP.NET Core, lo que permite elegir entre una caché de memoria distribuida, una caché en Redis, una NCache distribuida o una caché de SQL Server. Para más información sobre las implementaciones de `IDistributedCache`, consulte [Caché de memoria distribuida](/aspnet/core/performance/caching/distributed).


Este es un ejemplo de código que usa la caché en memoria en el método [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) de la clase [Startup](/aspnet/core/fundamentals/startup) en una aplicación de ASP.NET Core:

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

Desde el punto de vista de la memoria caché, el código sería similar en las API web de ASP.NET Core.


Estos son ejemplos de posibles cachés distribuidas:

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// and then choose your implementation of distributed cache

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or a Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

Su uso se incluye en el [tutorial de aplicaciones web de ASP.NET Core](/aspnet/core/tutorials/first-mvc-app/) en la fase de la [caché de tokens 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

## <a name="non-aspnet-core-web-apps-and-web-apis"></a>[Aplicaciones web y API web que no son de ASP.NET Core](#tab/aspnet)

Incluso si usa MSAL.NET, puede beneficiarse de los serializadores de caché de tokens que se incluyen en Microsoft.Identity.Web. 

### <a name="referencing-the-nuget-package"></a>Hacer referencia al paquete NuGet

Agregue el paquete NuGet [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) al proyecto además de MSAL.NET.

### <a name="configuring-the-token-cache"></a>Configuración de la caché de tokens

En el código siguiente se muestra cómo agregar a la aplicación una caché de tokens con particiones correctas en memoria.

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

 private static IConfidentialClientApplication app;

public static async Task<IConfidentialClientApplication> BuildConfidentialClientApplication(
  string clientId,
  CertificateDescription certDescription,
  string tenant)
 {
  if (app== null)
  {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(certDescription.Certificate)
       .WithLegacyCacheCompatibility(false)
       .WithTenantId(tenant)
       .Build();

     // Add an in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();
   }
   return app;
  }
```

### <a name="available-caching-technologies"></a>Tecnologías de almacenamiento en caché disponibles

En lugar de `app.AddInMemoryTokenCache();` puede usar otras tecnologías de almacenamiento en caché, incluidas las cachés de tokens distribuidas proporcionadas por .NET.

#### <a name="in-memory-token-cache"></a>Caché de tokens en memoria

La serialización de la caché de tokens en memoria ofrece ejemplos excelentes. También funciona bien en las aplicaciones de producción, siempre que no le importe que se pierda la caché de tokens cuando se reinicia la aplicación web.

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

#### <a name="distributed-caches"></a>Cachés distribuidas

Si usa `app.AddDistributedTokenCache`, la caché de tokens es un adaptador contra la implementación de `IDistributedCache` en .NET, lo que permite elegir entre una caché de memoria distribuida, una caché en Redis, CosmosDb o una caché de SQL Server. Para más información sobre las implementaciones de `IDistributedCache`, consulte [Caché de memoria distribuida](/aspnet/core/performance/caching/distributed).

##### <a name="distributed-in-memory-token-cache"></a>Caché de tokens en memoria distribuida

```CSharp 
     // In memory distributed token cache
     app.AddDistributedTokenCache(services =>
     {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
       services.AddDistributedMemoryCache();
     });
```

##### <a name="sql-server"></a>Servidor SQL

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value which is above 60 mins (or the lifetime of a token in case of longer lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

##### <a name="redis-cache"></a>Redis Cache

```CSharp 
     // Redis token cache
     app.AddDistributedTokenCache(services =>
     {
       // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });
      });
```

Vea también [Deshabilitación de la sincronización de caché](#disabling-cache-synchronization) si observa que la adquisición de tokens en ocasiones tarda tanto tiempo como el tiempo de espera de la caché de Redis. 

##### <a name="cosmos-db"></a>Cosmos DB

```CSharp 
      // Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

### <a name="disabling-legacy-token-cache"></a>Deshabilitación de la caché de tokens heredada

MSAL tiene código interno específicamente para permitir la posibilidad de interactuar con la caché de ADAL heredada. Si MSAL y ADAL no se usan en paralelo (por lo tanto, no se usa la caché heredada), el código de la caché heredada relacionado no es necesario. MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0) agrega la posibilidad de deshabilitar el código de la caché de ADAL heredada y mejorar el rendimiento de la misma. Consulte la solicitud de incorporación de cambios [#2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309) para ver la comparación de rendimiento antes y después de deshabilitar la caché heredada. Llame a `.WithLegacyCacheCompatibility(false)` en un generador de aplicaciones como se muestra a continuación.

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>Deshabilitación de la sincronización de caché

De forma predeterminada, MSAL bloqueará el acceso a la caché en el nivel de aplicación cliente confidencial entre las lecturas de caché y las escrituras en caché. Este bloqueo puede ser un problema si el serializador de caché tarda mucho tiempo hasta que se agota el tiempo de espera, que puede ser el caso de las cachés de Redis. Puede establecer la marca `WithCacheSynchronization` en false para habilitar una estrategia de bloqueo de caché optimista, lo que puede dar lugar a un mejor rendimiento, especialmente cuando los objetos ConfidentialClientApplication se reutilizan entre solicitudes. 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```

### <a name="monitor-cache-hit-ratios-and-cache-performance"></a>Supervisión de las relaciones de aciertos de caché y el rendimiento de la caché

MSAL expone métricas importantes como parte del objeto [AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata): 

| Métrica       | Significado     | ¿Cuándo se debe desencadenar una alarma?    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | Tiempo total invertido en MSAL, incluidas las llamadas de red y la caché   | Alarma sobre la latencia alta general (> 1 s). El valor depende del origen del token. Desde la caché: un acceso a la caché. Desde AAD: dos accesos a la caché y una llamada HTTP. La primera llamada (por proceso) llevará más tiempo debido a una llamada HTTP adicional. |
|  `DurationInCacheInMs` | Tiempo invertido en cargar o guardar la caché de tokens, que el desarrollador de la aplicación personaliza (por ejemplo, se guarda en Redis).| Alarma sobre aumentos. |
|  `DurationInHttpInMs`| Tiempo dedicado a realizar llamadas HTTP a AAD.  | Alarma sobre aumentos.|
|  `TokenSource` | Indica el origen del token. Los tokens se recuperan de la caché mucho más rápido (por ejemplo, ~100 ms frente a ~700 ms). Se puede usar para supervisar y enviar una alarma sobre la proporción de aciertos de caché. | Uso con `DurationTotalInMs` |

### <a name="samples"></a>Ejemplos

- El uso de los serializadores de caché de tokens en aplicaciones de .NET Framework y .NET Core se presenta en esta [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) de ejemplo 
- El ejemplo siguiente es una aplicación web de ASP.NET que usa las mismas técnicas: https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect (consulte [WebApp/Utils/MsalAppBuilder.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)

## <a name="desktop-apps"></a>[Aplicaciones de escritorio](#tab/desktop)

En las aplicaciones de escritorio, la recomendación es usar la caché de tokens multiplataforma.

#### <a name="cross-platform-token-cache-msal-only"></a>Caché de tokens multiplataforma (solo MSAL)

MSAL.NET proporciona una caché de tokens multiplataforma en una biblioteca independiente denominada Microsoft.Identity.Client.Extensions.Msal, cuyo código fuente está disponible en https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet.

##### <a name="referencing-the-nuget-package"></a>Hacer referencia al paquete NuGet

Agregue el paquete NuGet [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) al proyecto.

##### <a name="configuring-the-token-cache"></a>Configuración de la caché de tokens

Para obtener información detallada, vea https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache. Este es un ejemplo del uso de la caché de tokens multiplataforma.

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```

## <a name="mobile-apps"></a>[Aplicaciones móviles](#tab/mobile)

En MSAL.NET, se proporciona una caché de tokens en memoria de forma predeterminada. Se proporciona serialización de forma predeterminada para las plataformas en las que el almacenamiento seguro está disponible para un usuario como parte de la plataforma: Plataforma universal de Windows (UWP), Xamarin.iOS y Xamarin.Android.

## <a name="write-your-own-cache"></a>[Escritura de su propia memoria caché](#tab/custom)

Si realmente desea escribir su propio serializador de caché de tokens, MSAL.NET ofrece serialización de caché de tokens personalizada en las subplataformas .NET Framework y .NET Core. Los eventos se desencadenan cuando se accede a la caché; las aplicaciones pueden elegir si serializar o deserializar la caché. En aplicaciones cliente confidenciales que controlan a los usuarios (aplicaciones web que inician la sesión de los usuarios y llaman a las API web, y API web que llaman a las API web de nivel inferior), puede haber muchos usuarios y los usuarios se procesan en paralelo. Por motivos de seguridad y rendimiento, se recomienda serializar una caché por cada usuario. Los eventos de serialización calculan una clave de caché según la identidad del usuario procesado y serializan o deserializan una caché de tokens para ese usuario.

Recuerde que la serialización personalizada no está disponible en plataformas para dispositivos móviles (UWP, Xamarin.iOS y Xamarin.Android). MSAL ya define un mecanismo de serialización seguro y de alto rendimiento para estas plataformas. Sin embargo, las aplicaciones de escritorio de .NET y de .NET Core tienen diversas arquitecturas y MSAL no puede implementar un mecanismo de serialización de uso general. Por ejemplo, los sitios web puede elegir almacenar los tokens en una caché en Redis y las aplicaciones de escritorio hacerlo en un archivo cifrado. Por tanto, la serialización no se proporciona de fábrica. Para que haya una aplicación de la caché de tokens de forma constante en el escritorio de .NET o en .NET Core, personalice la serialización.

Las siguientes clases e interfaces se usan en la serialización de la caché de tokens:

- `ITokenCache`, que define eventos para suscribirse a las solicitudes de serialización de la caché de tokens, así como métodos para serializar o deserializar la caché en diversos formatos (ADAL v3.0, MSAL 2.x y MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` es una devolución de llamada que se pasa a los eventos para que pueda controlar la serialización. Se les llamará con argumentos del tipo `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` sólo proporciona el `ClientId` de la aplicación y una referencia al usuario para el que está disponible el token.

  ![Diagrama de clases](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET crea automáticamente las cachés de tokens y le ofrece la caché de `IToken` cuando se llama a las propiedades `UserTokenCache` y `AppTokenCache` de una aplicación. Se supone que no va a implementar la interfaz usted mismo. Su responsabilidad al implementar una serialización de caché de tokens personalizada es:
> - Reaccione ante "eventos" `BeforeAccess` y `AfterAccess` (o sus tipos asincrónicos). El delegado de `BeforeAccess` es el responsable de deserializar la memoria caché, mientras que `AfterAccess` es el de serializarla.
> - Una parte de estos eventos almacena o carga blobs, que se pasan a través del argumento del evento al almacenamiento que se desee.

Las estrategias son diferentes en función de si se escribe una serialización de la caché de tokens para una [aplicación cliente pública](msal-client-applications.md) (escritorio) o una [aplicación cliente confidencial](msal-client-applications.md) (aplicación web, API web o aplicación de demonio).

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>Caché de tokens personalizada para una aplicación web o API web (aplicación cliente confidencial)

En las aplicaciones web o API web, la caché puede usar la sesión, una caché en Redis, una base de datos SQL o una base de datos de Cosmos DB. En aplicaciones web o API web, mantenga una caché de tokens por cada cuenta. 
- En el caso de las aplicaciones web, la caché de tokens debe estar protegida con clave mediante el identificador de cuenta.
- En el caso de las API web, la cuenta debe estar protegida con clave mediante el hash del token usado para llamar a la API.

Se proporcionan ejemplos de serializadores de caché de tokens en [Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders).

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>Caché de tokens personalizada para una aplicación de escritorio o móvil (aplicación cliente pública)

Desde MSAL.NET v2.x hay varias opciones para serializar la caché de tokens de un cliente público. La memoria caché solo se puede serializar en formato MSAL.NET (la caché de formato unificado es común para MSAL y para las plataformas).  También puede admitir la serialización de la caché de tokens [heredada](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) de ADAL V3.

La personalización de la serialización de la caché de tokens para compartir el estado de inicio de sesión único entre ADAL.NET 3.x, ADAL.NET 5.x y MSAL.NET se explica en una parte del ejemplo siguiente: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> El formato de la caché de tokens MSAL.NET 1.1.4-preview ya no es compatible con MSAL 2.x. Si tiene aplicaciones que aprovechan MSAL.NET 1.x, los usuarios tendrán que volver a iniciar sesión. Sin embargo, se admite la migración desde ADAL 4.x (y 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serialización de la caché de tokens simple (solo MSAL)

A continuación encontrará un ejemplo de una implementación sencilla de serialización personalizada de una caché de tokens para aplicaciones de escritorio. En este caso, la caché de tokens de usuario es un archivo que está en la misma carpeta que la aplicación.

Después de compilar la aplicación, para habilitar la serialización llame al método `TokenCacheHelper.EnableSerialization()` y use la aplicación `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

La clase auxiliar `TokenCacheHelper` se define como:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Un archivo de la caché de tokens con calidad de producto para aplicaciones cliente públicas (para aplicaciones de escritorio que se ejecutan en Windows, Mac y Linux) disponible en la biblioteca de código abierto [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal). La puede incluir en las aplicaciones desde el siguiente paquete NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialización de la caché de tokens dual (caché unificada de MSAL y ADAL v3)

Si desea implementar la serialización de la caché de tokens tanto con el formato de caché unificado (común a ADAL.NET 4.x, MSAL.NET 2.x y a otros MSALs de la misma generación, o anteriores, que se encuentran en la misma plataforma), eche un vistazo al código siguiente:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Esta vez la clase auxiliar se define como:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

---

## <a name="next-steps"></a>Pasos siguientes

Los ejemplos siguientes ilustran la serialización de la caché de tokens.

| Muestra | Plataforma | Descripción|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Escritorio (WPF) | Aplicación .NET de Windows Desktop (WPF) que llama a la API Microsoft Graph. ![Diagrama que muestra una topología con una aplicación de escritorio WPF TodoListClient que fluye a Azure AD a través de la adquisición de un token de forma interactiva y a Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Escritorio (consola) | Conjunto de soluciones de Visual Studio que ilustran la migración de aplicaciones de Azure AD v1.0 (mediante ADAL.NET) a las aplicaciones de la plataforma de identidad de Microsoft (mediante MSAL.NET). En concreto, vea [Migración de la caché de tokens](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) y [Caché de tokens cliente confidencial](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET (net472) | Ejemplo de serialización de caché de tokens en una aplicación MVC de ASP.NET (mediante MSAL.NET). En concreto, vea [MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs).

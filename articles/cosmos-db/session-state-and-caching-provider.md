---
title: Uso de Azure Cosmos DB como un proveedor de almacenamiento en caché y estado de sesión de ASP.NET
description: Aprenda a usar Azure Cosmos DB como un proveedor de almacenamiento en caché y estado de sesión de ASP.NET.
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: c55428557e17615ed58140cbeab5cbe0b5450608
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290385"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>Uso de Azure Cosmos DB como un proveedor de almacenamiento en caché y estado de sesión de ASP.NET

El proveedor de caché y sesión de Azure Cosmos DB le permite usar Azure Cosmos DB y aprovechar sus funcionalidades de escala global y baja latencia para almacenar datos de estado de sesión y como caché distribuida dentro de la aplicación.

## <a name="what-is-session-state"></a>¿Qué es el estado de sesión?

El [estado de sesión](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true) son los datos de usuario que hacen seguimiento a la exploración de un usuario a través de una aplicación web durante un período de tiempo en el mismo explorador. El estado de sesión expira y se limita a las interacciones que tiene un explorador determinado que no se extienden a otros exploradores. Se considera datos efímeros y, si no está presente, no interrumpirá la aplicación. Sin embargo, cuando sí está presente, hace que la experiencia sea más rápida para el usuario porque la aplicación web no necesita capturarlo en todas las solicitudes del explorador correspondientes al mismo usuario.

A menudo cuenta con el respaldo de algún mecanismo de almacenamiento, que en algunos casos puede ser externo al servidor web actual, y habilitar las solicitudes de equilibrio de carga del mismo explorador en varios servidores web para lograr una mayor escalabilidad.

El proveedor de estado de sesión más sencillo es el proveedor en memoria que solo almacena datos en la memoria del servidor web local y requiere que la aplicación use el [enrutamiento de solicitud de aplicaciones](/iis/extensions/planning-for-arr/using-the-application-request-routing-module). Esto hace que la sesión del explorador sea permanente para un servidor web determinado (todas las solicitudes de ese explorador deben llegar siempre al mismo servidor web). El proveedor funciona bien en escenarios sencillos, pero el requisito de permanencia puede traer problemas de equilibrio de carga cuando se escalan las aplicaciones web.

Hay muchos proveedores de almacenamiento externos disponibles que pueden almacenar los datos de sesión de manera que varios servidores web puedan leerlos y acceder a ellos sin necesidad de mantener la sesión y habilitar una escala mayor.

## <a name="session-state-scenarios"></a>Escenarios de estado de sesión

Cosmos DB se puede usar como proveedor de estado de sesión a través del paquete de extensión [Microsoft.Extensions.Caching.Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos) con el [SDK de .NET de Azure Cosmos DB](sql-api-sdk-dotnet-standard.md). Utiliza un contenedor como almacenamiento de sesión eficaz basado en un enfoque clave-valor donde la clave es el identificador de sesión.

Una vez que se agrega el paquete, es posible usar `AddCosmosCache` como parte del proceso de inicio (services.AddSession y app.UseSession son pasos de [inicialización comunes](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true) necesarios para todo proveedor de estado de sesión):

```csharp
public void ConfigureServices(IServiceCollection services)
{
  /* Other service configurations */
  services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
  {
      CosmosClientBuilder clientBuilder = new CosmosClientBuilder("myConnectionString")
        .WithApplicationRegion("West US");
      cacheOptions.ContainerName = "myContainer";
      cacheOptions.DatabaseName = "myDatabase";
      cacheOptions.ClientBuilder = clientBuilder;
      /* Creates the container if it does not exist */
      cacheOptions.CreateIfNotExists = true; 
  });

  services.AddSession(options =>
  {
      options.IdleTimeout = TimeSpan.FromSeconds(3600);
      options.Cookie.IsEssential = true;
  });
  /* Other service configurations */
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /* Other configurations */

    app.UseSession();

    /* app.UseEndpoints and other configurations */
}
```

Donde especifique la base de datos y el contenedor en los que desea almacenar el estado de sesión y, opcionalmente, puede crearlos si no existen.

Puede personalizar la configuración de cliente del SDK con `CosmosClientBuilder` o si la aplicación ya utiliza `CosmosClient` para otras operaciones con Cosmos DB, también puede insertarla en el proveedor:

```csharp
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = "myContainer";
    cacheOptions.DatabaseName = "myDatabase";
    cacheOptions.CosmosClient = preExistingClient;
    /* Creates the container if it does not exist */
    cacheOptions.CreateIfNotExists = true; 
});
```

Después de esto, puede utilizar sesiones de ASP.NET Core como cualquier otro proveedor y usar el objeto HttpContext.Session. Recuerde siempre intentar cargar la información de sesión de manera asincrónica en función de las [recomendaciones de ASP.NET](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true).

##  <a name="distributed-cache-scenarios"></a>Escenarios de caché distribuida

Dado que el proveedor de Cosmos DB implementa la [interfaz IDistributedCache para que actúe como proveedor de caché distribuida](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true), se puede usar también para cualquier aplicación que requiera la caché distribuida, no solo para una aplicación web que requiera un proveedor de estado de sesión distribuida y eficaz.

Las cachés distribuidas requieren coherencia de datos para proporcionar instancias independientes a fin de poder compartir esos datos almacenados en caché. Cuando use el proveedor de Cosmos DB, puede:

- Use la cuenta de Cosmos DB en **Coherencia de la sesión** si puede habilitar [Enrutamiento de solicitud de aplicaciones](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) y hacer que las solicitudes permanezcan en una instancia determinada.
- Utilice la cuenta de Cosmos DB en **obsolescencia limitada o coherencia fuerte** sin que sea necesario solicitar la permanencia. Esto proporciona la mayor escala en términos de distribución de carga entre las instancias.

A fin de utilizar el proveedor de Cosmos DB como caché distribuida, se debe registrar en los servicios `ConfiguredService` con la llamada a `services.AddCosmosCache`. Una vez hecho esto, cualquier construcción de la aplicación puede pedir la caché al hacer referencia a `IDistributedCache` y recibirá la instancia insertada por la [inserción de dependencias](/dotnet/core/extensions/dependency-injection) que se va a utilizar:

```csharp
public class MyBusinessClass
{
    private readonly IDistributedCache cache;

    public MyBusinessClass(IDistributedCache cache)
    {
        this.cache = cache;
    }
    
    public async Task SomeOperationAsync()
    {
        string someCachedValue = await this.cache.GetStringAsync("someKey");
        /* Use the cache */
    }
}
```

## <a name="troubleshooting-and-diagnosing"></a>Solución de problemas y diagnóstico

Como el proveedor de Cosmos DB utiliza el SDK de .NET de manera subyacente, se aplican todas las [directrices de rendimiento](performance-tips-dotnet-sdk-v3-sql.md) y las [guías para la solución de problemas](troubleshoot-dot-net-sdk.md) para comprender cualquier problema potencial. Tenga en cuenta que hay una manera distinta de obtener acceso a los diagnósticos desde las operaciones de Cosmos DB subyacentes, porque no se pueden exponer a través de las API IDistributedCache.

El registro del delegado de diagnóstico opcional le permitirá capturar y registrar condicionalmente los diagnósticos a fin de solucionar cualquier caso, como una latencia alta:

```csharp
void captureDiagnostics(CosmosDiagnostics diagnostics)
{
    if (diagnostics.GetClientElapsedTime() > SomePredefinedThresholdTime)
    {
        Console.WriteLine(diagnostics.ToString());
    }
}

services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.DiagnosticsHandler = captureDiagnostics;
    /* other options */
});
```

## <a name="next-steps"></a>Pasos siguientes
- Para más detalles sobre el proveedor de caché y sesión de Azure Cosmos DB, consulte el [código fuente en GitHub](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/).
- [Pruebe](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample) el proveedor de caché y sesión de Azure Cosmos DB mediante la exploración de una aplicación web ASP.NET Core de ejemplo.
- Lea más sobre las [cachés distribuidas](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true) en .NET.

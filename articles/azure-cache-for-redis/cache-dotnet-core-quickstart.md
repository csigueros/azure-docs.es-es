---
title: 'Inicio rápido: Uso de Azure Cache for Redis con .NET Core'
description: En este inicio rápido, aprenderá cómo acceder a Azure Redis Cache desde las aplicaciones .NET Core
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 3ca0d730d1c99e985a0b7b96c01fdd1dcef95a18
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538594"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-core"></a>Inicio rápido: Uso de Azure Cache for Redis con .NET Core

En este inicio rápido incorporará Azure Redis Cache en una aplicación .NET Core para acceder a una caché dedicada y segura, a la que se puede acceder desde cualquier aplicación de Azure. Concretamente, usará el cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) con código C# en una aplicación de consola de .NET Core.

## <a name="skip-to-the-code-on-github"></a>Ir al código en GitHub

Si quiere pasar directamente al código, consulte [Inicio rápido: Uso de Azure Cache for Redis en .NET Core](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet-core) en GitHub.

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [SDK de .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Creación de una caché
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]


[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

Tome nota del **NOMBRE DE HOST** y de la **clave de acceso** principal. Usará estos valores más adelante para construir el secreto *CacheConnection*.



## <a name="create-a-console-app"></a>Crear una aplicación de consola

Abra una nueva ventana Comandos y ejecute el siguiente comando para crear una nueva aplicación de consola .NET Core:

```
dotnet new console -o Redistest
```

En la ventana Comandos, cambie al nuevo directorio del proyecto *Redistest*.



## <a name="add-secret-manager-to-the-project"></a>Adición de Secret Manager al proyecto

En esta sección, agregará la [herramienta Secret Manager](/aspnet/core/security/app-secrets) al proyecto. La herramienta Secret Manager almacena información confidencial para el trabajo de desarrollo fuera de su árbol de proyecto. Este enfoque ayuda a evitar el uso compartido accidental de secretos de la aplicación en el código fuente.

Abra el archivo *Redistest.csproj*. Agregue un elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Además, agregue un elemento `UserSecretsId` como se muestra a continuación y guarde el archivo.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

Ejecute el siguiente comando para agregar el paquete *Microsoft.Extensions.Configuration.UserSecrets* al proyecto:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Ejecute el siguiente comando para restaurar los paquetes:

```
dotnet restore
```

En la ventana Comandos, ejecute el siguiente comando para almacenar un secreto nuevo denominado *CacheConnection* después de reemplazar los marcadores de posición (incluidos los corchetes angulares) por el nombre de la caché y la clave de acceso principal:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

Agregue la siguiente instrucción `using` a *Program.cs*:

```csharp
using Microsoft.Extensions.Configuration;
```

Agregue los siguientes miembros a la clase `Program` de *Program.cs*. Este código inicializa una configuración para acceder al secreto de usuario de la cadena de conexión de Azure Redis Cache.

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>Configuración del cliente de caché

En esta sección, configurará la aplicación de consola para utilizar el cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para .NET.

En la ventana Comandos, ejecute el siguiente comando en el directorio del proyecto *Redistest*:

```
dotnet add package StackExchange.Redis
```

Una vez completada la instalación, el cliente de caché *StackExchange.Redis* está disponible para su uso con el proyecto.


## <a name="connect-to-the-cache"></a>Conexión a la memoria caché

Agregue la siguiente instrucción `using` a *Program.cs*:

```csharp
using StackExchange.Redis;
```

La clase `ConnectionMultiplexer` administra la conexión con Azure Redis Cache. Esta clase debe compartirse y reutilizarse en toda la aplicación cliente. No cree una nueva conexión para cada operación. 

En *Program.cs*, agregue los siguientes miembros a la clase `Program` de la aplicación de consola:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

Este enfoque para compartir una instancia de `ConnectionMultiplexer` en la aplicación usa una propiedad estática que devuelve una instancia conectada. El código proporciona una manera segura para subprocesos de inicializar solo una instancia de `ConnectionMultiplexer` conectada. `abortConnect` está establecido en false, lo que significa que la llamada se realizará correctamente incluso si no se establece ninguna conexión a Azure Redis Cache. Una de las características principales de `ConnectionMultiplexer` es que restaura automáticamente la conectividad a la caché una vez que el problema de red u otras causas se resuelven.

Se accede al valor del secreto *CacheConnection* mediante el proveedor de configuración del Administrador de secretos y se utiliza como parámetro de contraseña.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>Control de RedisConnectionException y SocketException mediante reconexión

Un procedimiento recomendado a la hora de llamar a los métodos de `ConnectionMultiplexer` es intentar resolver las excepciones `RedisConnectionException` y `SocketException` automáticamente cerrando y reestableciendo la conexión.

Agregue las siguientes instrucciones `using` a *Program.cs*:

```csharp
using System.Net.Sockets;
using System.Threading;
```

En *Program.cs*, agregue los siguientes miembros a la clase `Program`:

```csharp
private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

## <a name="executing-cache-commands"></a>Ejecución de comandos de caché

En *Program.cs*, agregue el código siguiente al procedimiento `Main` de la clase `Program` de la aplicación de consola:

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Guarde *Program.cs*.

Las instancias de Azure Cache for Redis tienen un número configurable de bases de datos (valor predeterminado de 16) que se pueden usar para separar de forma lógica los datos dentro de una instancia de Azure Redis Cache. El código se conecta a la base de datos predeterminada, DB 0. Para más información, consulte [What are Redis databases?](cache-development-faq.yml#what-are-redis-databases-) (¿Qué son las bases de datos de Redis?) y [Configuración predeterminada del servidor Redis](cache-configure.md#default-redis-server-configuration).

Los elementos en la memoria caché se pueden almacenar y recuperar mediante los métodos `StringSet` y `StringGet`.

Redis almacena la mayoría de los datos como cadenas Redis, pero estas cadenas pueden contener muchos tipos de datos, como por ejemplo datos binarios serializados, que se pueden usar cuando se almacenan objetos .NET en caché.

Ejecute el siguiente comando en la ventana Comandos para compilar la aplicación:

```
dotnet build
```

Luego, ejecute la aplicación con el siguiente comando:

```
dotnet run
```

En el ejemplo siguiente, puede ver que la clave `Message` tenía anteriormente un valor almacenado en caché, que se estableció mediante la Consola de Redis en Azure Portal. La aplicación actualizó ese valor almacenado en caché. La aplicación también ejecutó los comandos `PING` y `CLIENT LIST`.

![Aplicación de consola parcial](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Trabajar con objetos .NET en la memoria caché

Azure Redis Cache puede almacenar en caché objetos .NET así como tipos de datos primitivos, pero antes de poder almacenar en caché un objeto .NET, se debe serializar. La serialización del objeto .NET es responsabilidad del desarrollador de la aplicación, que tiene total flexibilidad a la hora de elegir el serializador.

Una manera sencilla para serializar objetos es usar los métodos de serialización `JsonConvert` de [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) y serializar a y desde JSON. En esta sección, agregará un objeto .NET a la memoria caché.

Ejecute el siguiente comando para agregar el paquete *Newtonsoft.json* a la aplicación:

```
dotnet add package Newtonsoft.json
```

Agregue la siguiente instrucción `using` al principio del archivo *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Agregue la siguiente definición de clase `Employee` a *Program.cs*:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

En la parte inferior del procedimiento `Main()` de *Program.cs*, antes de llamar a `CloseConnection()`, agregue las siguientes líneas de código para almacenar en caché y recuperar un objeto .NET serializado:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Guarde *Program.cs* y vuelva a compilar la aplicación con el siguiente comando:

```
dotnet build
```

Ejecute la aplicación con el siguiente comando para comprobar la serialización de objetos .NET:

```
dotnet run
```

![Aplicación de consola finalizada](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a seguir con el tutorial siguiente, puede mantener los recursos creados en esta guía de inicio rápido y volverlos a utilizar.

En caso contrario, si ya ha terminado con la aplicación de ejemplo de la guía de inicio rápido, puede eliminar los recursos de Azure creados en este tutorial para evitar cargos. 

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible y el grupo de recursos y todos los recursos que contiene se eliminarán de forma permanente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado los recursos para hospedar este ejemplo en un grupo de recursos existente que contiene recursos que quiere conservar, puede eliminar cada recurso individualmente en la parte izquierda, en lugar de eliminar el grupo de recursos.
>

Inicie sesión en [Azure Portal](https://portal.azure.com) y después seleccione **Grupos de recursos**.

Escriba el nombre del grupo de recursos en el cuadro de texto **Filtrar por nombre...** . En las instrucciones de este artículo se usa un grupo de recursos llamado *TestResources*. En el grupo de recursos de la lista de resultados, seleccione **...** y, después, **Eliminar grupo de recursos**.

![Eliminar](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

Se le pedirá que confirme la eliminación del grupo de recursos. Escriba el nombre del grupo de recursos para confirmar y seleccione **Eliminar**.

Transcurridos unos instantes, el grupo de recursos y todos los recursos que contiene se eliminan.



<a name="next-steps"></a>

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a usar Azure Redis Cache desde una aplicación .NET Core. Continúe con el siguiente inicio rápido para usar Azure Redis Cache con una aplicación web ASP.NET.

> [!div class="nextstepaction"]
> [Creación de una aplicación web ASP.NET que usa Azure Redis Cache.](./cache-web-app-howto.md)

¿Quiere optimizar y ahorrar en el gasto en la nube?

> [!div class="nextstepaction"]
> [Comience a analizar los costos con Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
---
title: Migración de la aplicación para usar el SDK de .NET 3.0 de Azure Cosmos DB (Microsoft.Azure.Cosmos)
description: Aprenda a actualizar la aplicación .NET existente del SDK v2 a la versión más reciente del SDK de .NET v3 (paquete Microsoft.Azure.Cosmos) para Core (SQL) API.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 9ee782734baaf8947aa4e4f930cac874e32a5df6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788199"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Migración de la aplicación para usar el SDK de .NET v3 de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Para obtener información sobre el SDK de .NET v3 de Azure Cosmos DB, consulte las [notas de la versión](sql-api-sdk-dotnet-standard.md), el [repositorio de GitHub de .NET](https://github.com/Azure/azure-cosmos-dotnet-v3), las [sugerencias de rendimiento](performance-tips-dotnet-sdk-v3-sql.md) del SDK de .NET v3 y la [guía de solución de problemas](troubleshoot-dot-net-sdk.md).
>

En este artículo se destacan algunas de las cuestiones relacionadas con la actualización de la aplicación .NET existente al nuevo SDK de .NET v3 de Azure Cosmos DB para Core (SQL) API. El SDK de .NET v3 de Azure Cosmos DB corresponde al espacio de nombres Microsoft.Azure.Cosmos. Puede usar la información de este documento si va a migrar la aplicación desde cualquiera de los siguientes SDK de .NET de Azure Cosmos DB:

* SDK de .NET Framework v2 de Azure Cosmos DB para SQL API
* SDK de .NET Core v2 de Azure Cosmos DB para SQL API

Las instrucciones de este artículo también le ayudarán a migrar las siguientes bibliotecas externas que ahora forman parte del SDK de .NET v3 de Azure Cosmos DB para Core (SQL) API:

* Biblioteca de procesadores de fuente de cambios para .NET 2.0
* Biblioteca Bulk Executor para .NET 1.1 o superior

## <a name="whats-new-in-the-net-v3-sdk"></a>Novedades del SDK de .NET v3

El SDK v3 contiene muchas mejoras de facilidad de uso y rendimiento, entre las que se incluyen:

* Nomenclatura intuitiva del modelo de programación
* .NET Standard 2.0**
* Mayor rendimiento gracias a la compatibilidad con API de transmisión
* Jerarquía fluida que elimina la necesidad de una fábrica de URI
* Compatibilidad integrada con la biblioteca de procesadores de fuente de cambios
* Compatibilidad integrada para operaciones masivas
* API de simulación para pruebas unitarias más sencillas
* Compatibilidad con lotes transaccionales y Blazor
* Serializadores conectables
* Escalado de contenedores sin particiones y con escalabilidad automática

** El SDK tiene como destino .NET Standard 2.0, que unifica los SDK de .NET Framework y .NET Core existentes de Azure Cosmos DB en un solo SDK de .NET. Puede usar el SDK de .NET en cualquier plataforma que implemente .NET Standard 2.0, incluidas las aplicaciones .NET Framework 4.6.1 y versiones posteriores, y .NET Core 2.0 y versiones posteriores.

La mayoría de las funciones de red, la lógica de reintento y los niveles inferiores del SDK continúan en gran medida iguales.

**El SDK de .NET v3 de Azure Cosmos DB es ahora de código abierto.** Agradecemos las solicitudes de incorporación de cambios; registraremos problemas y realizaremos un seguimiento de los comentarios en [GitHub.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Nos centraremos en desarrollar las características que mejoren la experiencia del cliente.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Por qué migrar al SDK de .NET v3

Además de las numerosas mejoras de facilidad de uso y rendimiento, las inversiones en nuevas características realizadas en el último SDK no se trasladarán a las versiones anteriores.

Aunque no hay ningún plan inmediato para [retirar la compatibilidad con los SDK 2.0](sql-api-sdk-dotnet.md), estos se reemplazarán por versiones más recientes en el futuro y el SDK pasará al modo de mantenimiento. Para lograr la mejor experiencia de desarrollo, se recomienda empezar siempre con la versión compatible del SDK más reciente.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Principales cambios de nombre del SDK v2 al SDK v3

Se han aplicado los siguientes cambios de nombre en el SDK de .NET 3.0, para adaptarse a las convenciones de nomenclatura de Core (SQL) API:

* `DocumentClient` ahora se llama `CosmosClient`
* `Collection` ahora se llama `Container`
* `Document` ahora se llama `Item`

Todos los objetos de recursos cambian de nombre con propiedades adicionales, lo que incluye el nombre del recurso para mayor claridad.

A continuación se muestran algunos de los principales cambios en el nombre de clase:

| SDK de .NET v2 | SDK de .NET v3 |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Clases reemplazadas en el SDK de .NET v3

Las siguientes clases se han reemplazado en el SDK 3.0:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

La clase Microsoft.Azure.Documents.UriFactory se ha reemplazado por el diseño fluido. Este diseño crea las direcciones URL internamente y permite pasar un solo objeto `Container` en lugar de `DocumentClient`, `DatabaseName` y `DocumentCollection`.

### <a name="changes-to-item-id-generation"></a>Cambios en la generación del identificador de elemento

El identificador de elemento ya no se rellena automáticamente en el SDK de .NET v3. Por lo tanto, debe incluir específicamente un identificador generado. Consulte el ejemplo siguiente:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Cambio en el comportamiento predeterminado del modo de conexión

El SDK v3 tiene ahora como valor predeterminado los modos de conexión directa y TCP en comparación con el SDK v2 anterior, que tenía como valor predeterminado los modos de conexión de puerta de enlace y HTTPS. Este cambio ofrece un rendimiento y una escalabilidad mejorados.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Cambios en FeedOptions (QueryRequestOptions en el SDK v3.0)

El nombre de la clase `FeedOptions` del SDK v2 ha cambiado a `QueryRequestOptions` en el SDK v3 y, dentro de la clase, se han efectuado cambios en varias propiedades, ya sea en el nombre o en el valor predeterminado, en tanto que otras se han quitado por completo.  

`FeedOptions.MaxDegreeOfParallelism` ahora se llama `QueryRequestOptions.MaxConcurrency`; el valor predeterminado y el comportamiento asociado siguen siendo los mismos, las operaciones que se ejecutan en el cliente durante la ejecución de consultas en paralelo se ejecutarán en serie sin paralelismo.

`FeedOptions.EnableCrossPartitionQuery` se ha quitado; el comportamiento predeterminado en el SDK 3.0 es que las consultas entre particiones se ejecutarán sin necesidad de habilitar la propiedad de forma específica.

La propiedad `FeedOptions.PopulateQueryMetrics` está habilitada de forma predeterminada y los resultados se presentan en la propiedad Diagnostics de la respuesta.

`FeedOptions.RequestContinuation` ahora ha pasado a formar parte de los métodos de consulta.

Se han quitado las propiedades siguientes:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Construcción de un cliente

El SDK de .NET v3 proporciona una clase `CosmosClientBuilder` fluida que elimina la necesidad de la fábrica de URI del SDK v2.

En el ejemplo siguiente se crea una nueva clase `CosmosClientBuilder` con un valor ConsistencyLevel seguro y una lista de ubicaciones preferidas:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="exceptions"></a>Excepciones

Donde el SDK v2 usaba `DocumentClientException` para señalar errores durante las operaciones, el SDK v3 usa `CosmosClientException`, que expone los elementos `StatusCode` y `Diagnostics`, así como otros datos relacionados con la respuesta. Toda la información se serializa cuando se usa `ToString()`:

```csharp
catch (CosmosClientException ex)
{
    HttpStatusCode statusCode = ex.StatusCode;
    CosmosDiagnostics diagnostics = ex.Diagnostics;
    // store diagnostics optionally with diagnostics.ToString();
    // or log the entire error details with ex.ToString();
}
```

### <a name="diagnostics"></a>Diagnóstico

Donde el SDK v2 tenía diagnósticos solo directos disponibles mediante la propiedad `ResponseDiagnosticsString`, el SDK v3 usa el elemento `Diagnostics` disponible en todas las respuestas y excepciones, que son más abundantes y no están restringidas al modo directo. Incluyen no solo el tiempo empleado en el SDK para la operación, sino también las regiones con las que ha contactado la operación:

```csharp
try
{
    ItemResponse<MyItem> response = await container.ReadItemAsync<MyItem>(
                    partitionKey: new PartitionKey("MyPartitionKey"),
                    id: "MyId");
    
    TimeSpan elapsedTime = response.Diagnostics.GetElapsedTime();
    if (elapsedTime > somePreDefinedThreshold)
    {
        // log response.Diagnostics.ToString();
        IReadOnlyList<(string region, Uri uri)> regions = response.Diagnostics.GetContactedRegions();
    }
}
catch (CosmosException cosmosException) {
    string diagnostics = cosmosException.Diagnostics.ToString();
    
    TimeSpan elapsedTime = cosmosException.Diagnostics.GetElapsedTime();
    
    IReadOnlyList<(string region, Uri uri)> regions = cosmosException.Diagnostics.GetContactedRegions();
    
    // log cosmosException.ToString()
}
```

### <a name="connectionpolicy"></a>ConnectionPolicy

Algunas opciones de `ConnectionPolicy` han cambiado de nombre o se han reemplazado:

| SDK de .NET v2 | SDK de .NET v3 |
|-------------|-------------|
|`EnableEndpointRediscovery`|`LimitToEndpoint`: ahora el valor se ha invertido, si `EnableEndpointRediscovery` estaba establecido en `true`, `LimitToEndpoint` debería estar establecido en `false`. Antes de usar esta configuración, debe comprender [cómo afecta al cliente](troubleshoot-sdk-availability.md).|
|`ConnectionProtocol`|Quitado. El protocolo está asociado al modo, ya sea puerta de enlace (HTTPS) o directo (TCP).|
|`MediaRequestTimeout`|Quitado. Ya no se admiten datos adjuntos.|

### <a name="session-token"></a>Token de sesión

Donde el SDK v2 exponía el token de sesión de una respuesta como `ResourceResponse.SessionToken` para los casos en los que se requería capturar el token de sesión, dado que el token de sesión es un encabezado, el SDK v3 expone ese valor en la propiedad `Headers.Session` de cualquier respuesta.

### <a name="timestamp"></a>Timestamp

Donde el SDK v2 exponía la marca de tiempo de un documento mediante la propiedad `Timestamp`, dado que `Document` ya no está disponible, los usuarios pueden asignar la [propiedad del sistema](../account-databases-containers-items.md#properties-of-an-item) `_ts` a una propiedad de su modelo.

### <a name="openasync"></a>OpenAsync

Para los casos de uso en los que se usaba `OpenAsync()` para preparar el cliente del SDK v2, se puede usar `CreateAndInitializeAsync` para [crear y preparar](https://devblogs.microsoft.com/cosmosdb/improve-net-sdk-initialization/) un cliente del SDK v3.

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Uso de las API de procesador de fuente de cambios directamente desde el SDK v3

El SDK v3 tiene compatibilidad integrada con las API de procesador de fuente de cambios, lo que le permite usar el mismo SDK para compilar la aplicación y la implementación del procesador de fuente de cambios. Anteriormente, tenía que usar una biblioteca de procesadores de fuente de cambios independiente.

Para obtener más información, consulte [cómo migrar de la biblioteca de procesadores de fuente de cambios al SDK de .NET v3 de Azure Cosmos DB](how-to-migrate-from-change-feed-library.md).

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Uso de la biblioteca Bulk Executor directamente desde el SDK v3

El SDK v3 tiene compatibilidad integrada con la biblioteca Bulk Executor, lo que permite usar el mismo SDK para compilar la aplicación y realizar operaciones masivas. Anteriormente, era necesario usar una biblioteca Bulk Executor independiente.

Para obtener más información, consulte [cómo migrar de la biblioteca Bulk Executor a la compatibilidad con la ejecución en bloque en el SDK de .NET v3 de Azure Cosmos DB](how-to-migrate-from-bulk-executor-library.md).

## <a name="code-snippet-comparisons"></a>Comparaciones de fragmentos de código

En el fragmento de código siguiente se muestran las diferencias en el modo en que se crean los recursos entre los SDK de .NET v2 y v3:

## <a name="database-operations"></a>Operaciones de la base de datos

### <a name="create-a-database"></a>Crear una base de datos

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Lectura de una base de datos por identificador

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Eliminación de una base de datos

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Operaciones de contenedor

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Creación de un contenedor (escalabilidad automática + período de vida con expiración)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Lectura de las propiedades del contenedor

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Eliminación de un contenedor

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Operaciones con elementos y de consulta

### <a name="create-an-item"></a>Crear un elemento

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Lectura de todos los elementos de un contenedor

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Elementos de consulta

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Eliminación de un elemento

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[SDK de .NET v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Pasos siguientes

* [Compile una aplicación de consola](sql-api-get-started.md) para administrar los datos de SQL API de Azure Cosmos DB mediante el SDK v3.
* Obtenga más información sobre [lo que puede hacer con el SDK v3](sql-api-dotnet-v3sdk-samples.md).
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB?
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).

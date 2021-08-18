---
title: Cargo de unidad de solicitud (RU) de una consulta de SQL en Azure Cosmos DB
description: Aprenda a buscar el cargo de la unidad de solicitud (RU) de las consultas de SQL que se ejecutan en un contenedor de Azure Cosmos. Puede usar los lenguajes de Azure Portal, .NET, Java, Python y Node.js para encontrar el cargo de RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6c11e6804b507fcaa838a83cf9eba102cbca381f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727906"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Búsqueda del cargo de la unidad de solicitud en las operaciones que se ejecutan en la API de SQL de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB admite varias API, como SQL, MongoDB, Cassandra, Gremlin y Table. Cada API tiene su propio conjunto de operaciones de base de datos. Estas abarcan desde sencillas lecturas y escrituras de punto hasta consultas complejas. Cada operación de base de datos consume recursos del sistema en función de la complejidad de la operación.

Azure Cosmos DB se encarga de normalizar el costo de todas las operaciones de base de datos y se expresa en términos de unidades de solicitud (RU en su forma abreviada). La solicitud de cargos son las unidades de solicitud que consumen todas las operaciones de base de datos. Puede considerar que las unidades de solicitud son como una moneda de rendimiento, que resume los recursos del sistema, como CPU, IOPS y memoria, necesarios para realizar las operaciones de base de datos compatibles con Azure Cosmos DB. Con independencia de qué API utilice para interactuar con el contenedor de Azure Cosmos, los costos siempre se miden por RU. Si la operación de base de datos es una escritura, lectura puntual o consulta, los costos siempre se miden en RU. Para más información, consulte el artículo [Unidades de solicitud y sus consideraciones](request-units.md).

En este artículo se presentan las distintas formas de encontrar el consumo de [unidades de solicitud](request-units.md) (RU) de cualquier operación que se ejecuta en un contenedor de la API de SQL de Azure Cosmos DB. Si usa una API diferente, consulte los artículos [API para MongoDB](mongodb/find-request-unit-charge-mongodb.md), [Cassandra API](cassandra/find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md) y [Table API](table/find-request-unit-charge.md) para buscar el cargo de las RU.

Actualmente, puede medir este consumo solo con Azure Portal o por medio de la inspección de la respuesta enviada desde Azure Cosmos DB mediante uno de los SDK. Si usa la API de SQL, tiene varias opciones para buscar el consumo de RU para una operación en un contenedor de Azure Cosmos.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-sql-api-dotnet.md#create-account) y suminístrele datos, o seleccione una cuenta existente de Azure Cosmos que ya contenga datos.

1. Vaya al panel **Data Explorer** y seleccione el contenedor en el que quiere trabajar.

1. Seleccione **Nueva consulta SQL**.

1. Escriba una consulta válida y, luego, seleccione **Ejecutar consulta**.

1. Seleccione **Query Stats** (Estadísticas de consulta) para mostrar el cargo de solicitud real correspondiente a la solicitud que ha ejecutado.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Captura de pantalla del cargo de solicitud de una consulta SQL en Azure Portal":::

## <a name="use-the-net-sdk"></a>Uso del SDK de .NET

# <a name="net-sdk-v2"></a>[SDK de .NET V2](#tab/dotnetv2)

Los objetos devueltos desde el [SDK de .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) exponen una propiedad `RequestCharge`:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[SDK de .NET V3](#tab/dotnetv3)

Los objetos que se devuelven desde el [SDK de .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) exponen una propiedad `RequestCharge`:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Para más información, consulte [Inicio rápido: Cree una aplicación web .NET con una cuenta de SQL API en Azure Cosmos DB](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Uso del SDK de Java

Los objetos devueltos desde el [SDK de Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) exponen un método `getRequestCharge()`:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Para más información, consulte [Inicio rápido: Compilación de una aplicación Java mediante una cuenta de SQL API de Azure Cosmos DB](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Uso del SDK de Node.js

Los objetos devueltos por el [SDK de Node.js](https://www.npmjs.com/package/@azure/cosmos) exponen un subobjeto `headers` que asigna todos los encabezados devueltos por la API HTTP subyacente. El cargo de solicitud está disponible en la clave `x-ms-request-charge`:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Para más información, consulte [Inicio rápido: Compilación de una aplicación Node.js con la cuenta de Azure Cosmos DB SQL API](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>Uso del SDK de Python

El objeto `CosmosClient` del [SDK de Python](https://pypi.org/project/azure-cosmos/) expone un diccionario `last_response_headers` que asigna todos los encabezados devueltos por la API HTTP subyacente de la última operación ejecutada. El cargo de solicitud está disponible en la clave `x-ms-request-charge`:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Para más información, consulte [Inicio rápido: Compilación de una aplicación Python con la cuenta de Azure Cosmos DB SQL API](create-sql-api-python.md). 

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo optimizar el consumo de RU, vea estos artículos:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)
* [Optimización del costo de rendimiento aprovisionado en Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimización de los costos de consulta de Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Escalado global del rendimiento aprovisionado](./request-units.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](set-throughput.md)
* [Aprovisionamiento del rendimiento en un contenedor de Azure Cosmos](how-to-provision-container-throughput.md)
* [Supervisión y depuración con métricas de Azure Cosmos DB](use-metrics.md)

---
title: Aprovisionamiento del rendimiento de contenedor en la API de SQL de Azure Cosmos DB
description: Aprenda a aprovisionar el rendimiento en el nivel de contenedor en la API de SQL de Azure Cosmos DB mediante Azure Portal, la CLI, PowerShell y otros SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: daf169a84268a24fd1cc50cd67386d359f9d726a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097885"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Aprovisionamiento del rendimiento estándar (manual) de un contenedor de Azure Cosmos - API de SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En este artículo se explica cómo aprovisionar el rendimiento estándar (manual) en un contenedor de la API de SQL de Azure Cosmos DB. Puede aprovisionar el rendimiento de un único contenedor o [aprovisionar el de una base de datos](how-to-provision-database-throughput.md) y compartirlo entre los contenedores incluidos en ella. Para aprovisionar el rendimiento de un contenedor, use Azure Portal, la CLI de Azure o los SDK de Azure Cosmos DB.

Si va a usar otra API, vea los artículos [MongoDB API](how-to-provision-throughput-mongodb.md), [Cassandra API](how-to-provision-throughput-cassandra.md) y [Gremlin API](how-to-provision-throughput-gremlin.md) para aprovisionar el rendimiento.

## <a name="azure-portal"></a>Azure portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-sql-api-dotnet.md#create-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione **Nuevo contenedor**. Después, proporcione los detalles siguientes:

   * Indique si va a crear una nueva base de datos o a usar una existente.
   * Escriba un valor en **Container Id** (Id. de contenedor).
   * Escriba un valor en **Partition key** (Clave de partición) (por ejemplo, `/ItemID`).
   * Seleccione un rendimiento de **Autoscale** (Escalabilidad automática) o **Manual** y especifique el valor de **Container throughput** (Rendimiento de contenedor) (por ejemplo, 1000 RU/s). Escriba un rendimiento que quiera aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Captura de pantalla de Data Explorer, con la Nueva colección resaltada":::

## <a name="azure-cli-or-powershell"></a>CLI de Azure o PowerShell

Para crear un contenedor con capacidad de procesamiento dedicada, consulte:

* [Creación de un contenedor mediante la CLI de Azure](manage-with-cli.md#create-a-container)
* [Creación de un contenedor mediante PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Use los SDK de Cosmos de la API de SQL para aprovisionar el rendimiento de todas las API de Cosmos DB, excepto Cassandra API y MongoDB API.

# <a name="net-sdk-v2"></a>[SDK de .NET V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[SDK para .NET V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>SDK de JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para aprender sobre el aprovisionamiento del rendimiento en Azure Cosmos DB:

* [Aprovisionamiento del rendimiento estándar (manual) en una base de datos](how-to-provision-database-throughput.md)
* [Aprovisionamiento del rendimiento de escalabilidad automática en una base de datos](how-to-provision-autoscale-throughput.md)
* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)

---
title: Aprovisionamiento del rendimiento en recursos de Gremlin API de Azure Cosmos DB
description: Obtenga información sobre cómo aprovisionar el rendimiento del contenedor, la base de datos y la escalabilidad automática en los recursos de Gremlin API de Azure Cosmos DB. Usará Azure Portal, la CLI, PowerShell y otros SDK.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
author: manishmsfte
ms.author: mansha
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4b8831812c972415b627c08ccff54227b5318e04
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360620"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Aprovisionamiento el rendimiento de la base de datos, el contenedor o la escalabilidad automática en los recursos de Gremlin API de Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

En este artículo se explica cómo aprovisionar el rendimiento de Gremlin API de Azure Cosmos DB. Puede aprovisionar el rendimiento estándar (manual) o de escalabilidad automática de un contenedor, o de una base de datos y compartirlo entre los contenedores incluidos en ella. Para aprovisionar el rendimiento, use Azure Portal, la CLI de Azure o los SDK de Azure Cosmos DB.

Si usa una API diferente, consulte los artículos [API de SQL](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [API para MongoDB](how-to-provision-throughput-mongodb.md) para aprovisionar el rendimiento.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o seleccione una ya existente.

1. Abra el panel de **Data Explorer** y seleccione **New Graph** (Nuevo grafo). Después, proporcione los detalles siguientes:

   * Indique si va a crear una nueva base de datos o a usar una existente. Seleccione la opción **Provision database throughput** (Aprovisionar rendimiento de base de datos) si desea aprovisionar el rendimiento en el nivel de base de datos.
   * Escriba un identificador de grafo.
   * Escriba un valor de la clave de partición (por ejemplo, `/ItemID`).
   * Escriba un rendimiento que quiera aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Captura de pantalla del Explorador de datos al crear un nuevo grafo con el rendimiento de nivel de base de datos":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Use los SDK de Cosmos de la API de SQL para aprovisionar el rendimiento de todas las API de Azure Cosmos DB, excepto Cassandra API y MongoDB API.

### <a name="provision-container-level-throughput"></a>Aprovisionamiento del rendimiento de nivel de contenedor

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

### <a name="provision-database-level-throughput"></a>Aprovisionamiento del rendimiento de nivel de base de datos

# <a name="net-sdk-v2"></a>[SDK de .NET V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[SDK para .NET V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Las plantillas de Azure Resource Manager se pueden usar para aprovisionar el rendimiento de escalado automático en luna base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Consulte [Plantillas de Azure Resource Manager para Azure Cosmos DB](templates-samples-gremlin.md) para ejemplos.

## <a name="azure-cli"></a>Azure CLI

La CLI de Azure se puede usar para aprovisionar el rendimiento de escalado automático en una base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Para ejemplos, consulte [Ejemplos de la CLI de Azure para Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell se puede usar para aprovisionar el rendimiento de escalado automático en una base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Para ejemplos, consulte [Ejemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para aprender sobre el aprovisionamiento del rendimiento en Azure Cosmos DB:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)
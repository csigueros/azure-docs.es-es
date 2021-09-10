---
title: Aprovisionamiento del rendimiento en recursos de la API de Azure Cosmos DB para MongoDB
description: Aprenda a aprovisionar el rendimiento del contenedor, la base de datos y la escalabilidad automática en los recursos de la API de Azure Cosmos DB para MongoDB. Usará Azure Portal, la CLI, PowerShell y otros SDK.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: b30ac109a8186f39e29ff96ba797d0b8c98ea41c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033371"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Aprovisionamiento del rendimiento de la base de datos, el contenedor o la escalabilidad automática en los recursos de la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En este artículo se explica cómo aprovisionar el rendimiento de la API de Azure Cosmos DB para MongoDB. Puede aprovisionar el rendimiento estándar (manual) o de escalabilidad automática de un contenedor, o de una base de datos y compartirlo entre los contenedores incluidos en ella. Para aprovisionar el rendimiento, use Azure Portal, la CLI de Azure o los SDK de Azure Cosmos DB.

Si usa una API diferente, consulte los artículos [API de SQL](../how-to-provision-container-throughput.md), [Cassandra API](../cassandra/how-to-provision-throughput-cassandra.md), [Gremlin API](../how-to-provision-throughput-gremlin.md) para aprovisionar el rendimiento.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione **Nueva colección**. Después, proporcione los detalles siguientes:

   * Indique si va a crear una nueva base de datos o a usar una existente. Seleccione la opción **Provision database throughput** (Aprovisionar rendimiento de base de datos) si desea aprovisionar el rendimiento en el nivel de base de datos.
   * Escriba el identificador de la colección.
   * Escriba un valor de la clave de partición (por ejemplo, `/ItemID`).
   * Escriba un rendimiento que quiera aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Captura de pantalla del Explorador de datos al crear una nueva recopilación con el rendimiento de nivel de base de datos":::

> [!Note]
> Si va a aprovisionar el rendimiento del contenedor de una cuenta de Azure Cosmos configurada con la API de Azure Cosmos DB para MongoDB, use `/myShardKey` para la ruta de acceso de la clave de partición.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> SDK de .NET

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Las plantillas de Azure Resource Manager se pueden usar para aprovisionar el rendimiento de escalado automático en luna base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Consulte [Plantillas de Azure Resource Manager para Azure Cosmos DB](resource-manager-template-samples.md) para ejemplos.

## <a name="azure-cli"></a>Azure CLI

La CLI de Azure se puede usar para aprovisionar el rendimiento de escalado automático en una base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Para ejemplos, consulte [Ejemplos de la CLI de Azure para Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell se puede usar para aprovisionar el rendimiento de escalado automático en una base de datos o en recursos de nivel de contenedor para todas las API de Azure Cosmos DB. Para ejemplos, consulte [Ejemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para aprender sobre el aprovisionamiento del rendimiento en Azure Cosmos DB:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](../request-units.md)
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).
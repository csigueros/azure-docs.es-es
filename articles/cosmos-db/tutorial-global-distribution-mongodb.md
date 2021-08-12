---
title: Tutorial de configuración de la distribución global con la API de Azure Cosmos DB para MongoDB
description: Aprenda a configurar la distribución global mediante la API de Azure Cosmos DB para MongoDB.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: a68557e02444a7524ee69fc95e9ca66b2aabf7a9
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354284"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Configuración de una base de datos distribuida globalmente mediante la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

En este artículo se muestra cómo usar Azure Portal para configurar una base de datos distribuida globalmente y conectarla mediante la API de Azure Cosmos DB para MongoDB.

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global mediante la [API de Azure Cosmos DB para MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Comprobación de la configuración regional 
Una manera sencilla de comprobar la configuración global con la API de Cosmos DB para MongoDB es ejecutar el comando *isMaster()* desde el Shell de Mongo.

Desde el Shell de Mongo:

   ```
      db.isMaster()
   ```
   
Resultados de ejemplo:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Conexión a una región de preferencia 

La API de Azure Cosmos DB para MongoDB le permite especificar preferencias de lectura de la colección para una base de datos distribuida globalmente. Para lecturas de poca latencia y alta disponibilidad global, se recomienda establecer las preferencias de lectura de la colección en *más cercano*. Una preferencia de lectura de *más cercano* está configurada para leer desde la región más cercana.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Para las aplicaciones con una región de lectura/escritura principal y una secundaria para escenarios de recuperación ante desastres, se recomienda establecer preferencias de lectura de la colección en *secundaria preferida*. Una preferencia de lectura de *secundaria preferida* está configurado para leer desde la región secundaria cuando la región principal no está disponible.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Por último, si quiere hacerlo manualmente, especifique las áreas de lectura. Puede establecer la etiqueta de región dentro de sus preferencias de lectura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

De este modo finaliza este tutorial. Para información sobre cómo administrar la coherencia de la cuenta replicada globalmente, lea [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md). Para más información sobre cómo funciona la replicación global de bases de datos en Azure Cosmos DB, consulte [Distribución global de datos con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global mediante la API de Cosmos DB para MongoDB

Ahora puede continuar en el tutorial siguiente para aprender a desarrollar localmente con el emulador local de Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desarrollo local con el Emulador de Azure Cosmos DB](local-emulator.md)

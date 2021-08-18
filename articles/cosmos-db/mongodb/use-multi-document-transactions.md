---
title: Uso de transacciones de varios documentos en la API de Azure Cosmos DB para MongoDB
description: Aprenda a crear una aplicación de shell de Mongo de ejemplo que pueda ejecutar una transacción de varios documentos (semántica de todo o nada) en una colección fija en la API de Azure Cosmos DB para MongoDB 4.0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: 547b305ed7c6a63b6512fb4d2bbf9d0a7d1b2012
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779910"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Uso de transacciones de varios documentos en la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

En este artículo, creará una aplicación de shell de Mongo que ejecuta una transacción de varios documentos en una colección fija en la API de Azure Cosmos DB para MongoDB con la versión de servidor 4.0.

## <a name="what-are-multi-document-transactions"></a>¿Qué son las transacciones de varios documentos?

En la API de Azure Cosmos DB para MongoDB, las operaciones en un único documento son atómicas. Las transacciones de varios documentos permiten a las aplicaciones ejecutar operaciones atómicas en varios documentos. Ofrece una semántica de "todo o nada" a las operaciones. Al confirmar, se conservan los cambios realizados dentro de las transacciones y, si se produce un error en la transacción, se descartan todos los cambios de la transacción.

Las transacciones de varios documentos siguen la semántica **ACAD**:

* Atomicidad: todas las operaciones se tratan como una
* Coherencia: los datos confirmados son válidos
* Aislamiento: aislado de otras operaciones
* Durabilidad: los datos de las transacciones se conservan cuando el cliente se lo indica

## <a name="requirements"></a>Requisitos

Las transacciones de varios documentos se admiten en una colección no particionada de la versión 4.0 de la API. Las transacciones de varios documentos no se admiten en colecciones ni en colecciones con particiones en la versión 4.0. El tiempo de espera de las transacciones es un valor fijo de 5 segundos.

Todos los controladores que admiten la versión 4.0 o posteriores del protocolo de conexión admitirán las transacciones de varios documentos de la API de Azure Cosmos DB para MongoDB.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Ejecución de transacciones de varios documentos en el shell de MongoDB
> [!Note]
> Este ejemplo no funciona en la versión beta de MongoSH (shell) insertada en MongoDB Compass.

1. Abra un símbolo del sistema, vaya al directorio donde está instalada la versión 4.0 y posteriores del shell de Mongo:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Cree un script de shell de Mongo denominado *connect_friends.js* y agregue el siguiente contenido

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Ejecute el siguiente comando para ejecutar la transacción de varios documentos: El host, el puerto, el usuario y la clave se pueden encontrar en Azure Portal.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Pasos siguientes

Explore las novedades de la [API de Azure Cosmos DB para MongoDB 4.0](feature-support-40.md)

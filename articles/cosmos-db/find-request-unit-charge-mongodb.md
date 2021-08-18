---
title: Búsqueda del cargo de la unidad de solicitud para las operaciones de MongoDB para la API de Azure Cosmos DB
description: Aprenda a buscar el cargo de la unidad de solicitud (RU) de las consultas de MongoDB que se ejecutan en un contenedor de Azure Cosmos. Puede usar los controladores de Azure Portal, MongoDB .NET, Java y Node.js.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7a2ff0e2b2ebc373c0de1dab5003b3c06cc284be
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354706"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Búsqueda del cargo de la unidad de solicitud en las operaciones que se ejecutan en la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB admite varias API, como SQL, MongoDB, Cassandra, Gremlin y Table. Cada API tiene su propio conjunto de operaciones de base de datos. Estas abarcan desde sencillas lecturas y escrituras de punto hasta consultas complejas. Cada operación de base de datos consume recursos del sistema en función de la complejidad de la operación.

Azure Cosmos DB se encarga de normalizar el costo de todas las operaciones de base de datos y se expresa en términos de unidades de solicitud (RU en su forma abreviada). La solicitud de cargos son las unidades de solicitud que consumen todas las operaciones de base de datos. Puede considerar que las unidades de solicitud son como una moneda de rendimiento, que resume los recursos del sistema, como CPU, IOPS y memoria, necesarios para realizar las operaciones de base de datos compatibles con Azure Cosmos DB. Con independencia de qué API utilice para interactuar con el contenedor de Azure Cosmos, los costos siempre se miden por RU. Si la operación de base de datos es una escritura, lectura puntual o consulta, los costos siempre se miden en RU. Para obtener más información, consulte el artículo que contiene las [unidades de solicitud y sus consideraciones](request-units.md).

En este artículo se indican las distintas formas de buscar el consumo de las [unidades de solicitud](request-units.md) (RU) de cualquier operación que se ejecuta en un contenedor de la API de Azure Cosmos DB para MongoDB. Si usa otra API, consulte los artículos referentes a la [API de SQL](find-request-unit-charge.md), [Cassandra API](find-request-unit-charge-cassandra.md), [API de Gremlin](find-request-unit-charge-gremlin.md) y [Table API](find-request-unit-charge-table.md) para averiguar el cargo de las RU.

El cargo de RU se expone mediante un [comando de base de datos](https://docs.mongodb.com/manual/reference/command/) personalizado llamado `getLastRequestStatistics`. El comando devuelve un documento que contiene el nombre de la última operación ejecutada, el cargo de la solicitud y su duración. Si usa la API de Azure Cosmos DB para MongoDB, tiene varias opciones para recuperar el cargo de RU.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) y suminístrele datos, o seleccione una cuenta existente que ya contenga datos.

1. Vaya al panel **Data Explorer** y seleccione el contenedor en el que quiere trabajar.

1. Seleccione **...** junto al nombre del contenedor y, después, **Nueva consulta**.

1. Escriba una consulta válida y, luego, seleccione **Ejecutar consulta**.

1. Seleccione **Query Stats** (Estadísticas de consulta) para mostrar el cargo de solicitud real correspondiente a la solicitud que ha ejecutado. Este editor de consultas permite ejecutar y ver los cargos por unidad de solicitud solo para los predicados de consulta. No puede usar este editor para los comandos de manipulación de datos, como las instrucciones INSERT.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Captura de pantalla del cargo de solicitud de una consulta de MongoDB en Azure Portal":::

1. Para obtener los cargos por solicitud de los comandos de manipulación de datos, ejecute el comando `getLastRequestStatistics` desde una interfaz de usuario basada en Shell, como Mongo Shell, [Robo 3T](mongodb-robomongo.md), [MongoDB Compass](mongodb-compass.md) o una extensión de Visual Studio Code con scripting de Shell.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Uso del controlador de .NET de MongoDB

Al usar el [controlador oficial de .NET para MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/), puede ejecutar comandos mediante una llamada al método `RunCommand` en un objeto `IMongoDatabase`. Este método requiere una implementación de la clase abstracta `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Para más información, consulte [Inicio rápido: Compilación de una aplicación web .NET mediante la API de Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Uso del controlador de .NET de Java


Al usar el [controlador oficial de Java para MongoDB](https://mongodb.github.io/mongo-java-driver/), puede ejecutar comandos mediante una llamada al método `runCommand` en un objeto `MongoDatabase`:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para más información, consulte [Inicio rápido: Compilación de una aplicación web mediante la API de Azure Cosmos DB para MongoDB y el SDK de Java](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Uso del controlador de Node.js de MongoDB

Al usar el [controlador oficial de Node.js para MongoDB](https://mongodb.github.io/node-mongodb-native/), puede ejecutar comandos mediante una llamada al método `command` en un objeto `db`:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para más información, consulte [Inicio rápido: Migración de una aplicación web actual de Node.js en MongoDB a Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo optimizar el consumo de RU, vea estos artículos:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)
* [Optimización del costo de rendimiento aprovisionado en Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimización de los costos de consulta de Azure Cosmos DB](./optimize-cost-reads-writes.md)
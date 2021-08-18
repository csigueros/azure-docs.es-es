---
title: Cargo de unidad de solicitud (RU) de una consulta de Cassandra API de Azure Cosmos DB
description: Obtenga información sobre el cargo de unidad de solicitud (RU) de las consultas de Cassandra ejecutadas en un contenedor de Azure Cosmos. Puede usar Azure Portal y controladores de .NET y de Java para averiguar el cargo de RU.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: a399292ccff0e4d60541a2ca829577ba26b406ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780412"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Averigüe el cargo de unidad de solicitud de las operaciones ejecutadas en Cassandra API de Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB admite varias API, como SQL, MongoDB, Cassandra, Gremlin y Table. Cada API tiene su propio conjunto de operaciones de base de datos. Estas abarcan desde sencillas lecturas y escrituras de punto hasta consultas complejas. Cada operación de base de datos consume recursos del sistema en función de la complejidad de la operación.

Azure Cosmos DB se encarga de normalizar el costo de todas las operaciones de base de datos y se expresa en términos de unidades de solicitud (RU en su forma abreviada). La solicitud de cargos son las unidades de solicitud que consumen todas las operaciones de base de datos. Puede considerar que las unidades de solicitud son como una moneda de rendimiento, que resume los recursos del sistema, como CPU, IOPS y memoria, necesarios para realizar las operaciones de base de datos compatibles con Azure Cosmos DB. Con independencia de qué API utilice para interactuar con el contenedor de Azure Cosmos, los costos siempre se miden por RU. Si la operación de base de datos es una escritura, lectura puntual o consulta, los costos siempre se miden en RU. Para obtener más información, vea el artículo [Unidades de solicitud y sus consideraciones](../request-units.md).

En este artículo se presentan las distintas formas de averiguar el consumo de [unidades de solicitud](../request-units.md) (RU) de cualquier operación ejecutada en un contenedor de Cassandra API de Azure Cosmos DB. Si usa otra API, vea los artículos [MongoDB API](../mongodb/find-request-unit-charge-mongodb.md), [SQL API](../find-request-unit-charge.md), [Gremlin API](../find-request-unit-charge-gremlin.md) y [Table API](../table/find-request-unit-charge.md) para averiguar el cargo de RU/s.

Al realizar operaciones en Cassandra API de Azure Cosmos DB, el cargo de RU se devuelve en la carga de entrada como un campo llamado `RequestCharge`. Tiene varias opciones para recuperar el cargo de RU.

## <a name="use-the-net-sdk"></a>Uso del SDK de .NET

Al usar el [SDK de .NET](https://www.nuget.org/packages/CassandraCSharpDriver/), puede recuperar la carga de entrada en la propiedad `Info` de un objeto `RowSet`:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para más información, consulte [Inicio rápido: Creación de una aplicación de Cassandra con el SDK de .NET y Azure Cosmos DB](manage-data-dotnet.md).

## <a name="use-the-java-sdk"></a>Uso del SDK de Java

Al usar el [SDK de Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), puede recuperar la carga de entrada mediante una llamada al método `getExecutionInfo()` en un objeto `ResultSet`:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para más información, consulte [Inicio rápido: Creación de una aplicación de Cassandra con el SDK de Java y Azure Cosmos DB](manage-data-java.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo optimizar el consumo de RU, vea estos artículos:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](../request-units.md)
* [Optimización del costo de rendimiento aprovisionado en Azure Cosmos DB](../optimize-cost-throughput.md)
* [Optimización de los costos de consulta de Azure Cosmos DB](../optimize-cost-reads-writes.md)
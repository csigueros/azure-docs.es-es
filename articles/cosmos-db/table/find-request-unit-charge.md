---
title: Búsqueda del cargo de la unidad de solicitud (RU) en consultas de la Table API en Azure Cosmos DB
description: Aprenda a buscar el cargo de la unidad de solicitud (RU) de las consultas de la Table API que se ejecutan en un contenedor de Azure Cosmos. Puede usar los lenguajes de Azure Portal, .NET, Java, Python y Node.js para encontrar el cargo de RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: db03c2b0c7926b4c42e8aa3effeaa3cf2ded435d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780198"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Búsqueda del cargo de la unidad de solicitud en las operaciones que se ejecutan en la Table API de Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB admite varias API, como SQL, MongoDB, Cassandra, Gremlin y Table. Cada API tiene su propio conjunto de operaciones de base de datos. Estas abarcan desde sencillas lecturas y escrituras de punto hasta consultas complejas. Cada operación de base de datos consume recursos del sistema en función de la complejidad de la operación.

Azure Cosmos DB se encarga de normalizar el costo de todas las operaciones de base de datos y se expresa en términos de unidades de solicitud (RU en su forma abreviada). La solicitud de cargos son las unidades de solicitud que consumen todas las operaciones de base de datos. Puede considerar que las unidades de solicitud son como una moneda de rendimiento, que resume los recursos del sistema, como CPU, IOPS y memoria, necesarios para realizar las operaciones de base de datos compatibles con Azure Cosmos DB. Con independencia de qué API utilice para interactuar con el contenedor de Azure Cosmos, los costos siempre se miden por RU. Si la operación de base de datos es una escritura, lectura puntual o consulta, los costos siempre se miden en RU. Para obtener más información, consulte el artículo que contiene las [unidades de solicitud y sus consideraciones](../request-units.md).

En este artículo se presentan las distintas formas de buscar el consumo de [unidades de solicitud](../request-units.md) (RU) de cualquier operación que se ejecuta en un contenedor de la Table API de Azure Cosmos DB. Si usa una API diferente, consulte los artículos [API para MongoDB](../mongodb/find-request-unit-charge-mongodb.md), [Cassandra API](../cassandra/find-request-unit-charge-cassandra.md), [API de Gremlin](../find-request-unit-charge-gremlin.md) y [API de SQL](../find-request-unit-charge.md) para buscar el cargo de las RU.

## <a name="use-the-net-sdk"></a>Uso del SDK de .NET

Actualmente, el único SDK que devuelve el cargo de RU para las operaciones de tabla es el [SDK de .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). El objeto `TableResult` expone una propiedad `RequestCharge` que se rellena mediante el SDK cuando se usa con Table API de Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para más información, consulte [Inicio rápido: creación de una aplicación de Table API con el SDK de .NET y Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo optimizar el consumo de RU, vea estos artículos:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](../request-units.md)
* [Optimización del costo de rendimiento aprovisionado en Azure Cosmos DB](../optimize-cost-throughput.md)
* [Optimización de los costos de consulta de Azure Cosmos DB](../optimize-cost-reads-writes.md)
---
title: Búsqueda del cargo de la unidad de solicitud (RU) en consultas de la API de Gremlin en Azure Cosmos DB
description: Aprenda a buscar el cargo de la unidad de solicitud (RU) de las consultas de Gremlin que se ejecutan en un contenedor de Azure Cosmos. Puede usar los controladores de Azure Portal, .NET y Java para buscar el cargo de RU.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
author: manishmsfte
ms.author: mansha
ms.custom: devx-track-js
ms.openlocfilehash: 009a511f93a1f3d4bc8bf85057e4dbaadc114c90
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780951"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Búsqueda del cargo de la unidad de solicitud en las operaciones que se ejecutan en la API de Gremlin de Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB admite varias API, como SQL, MongoDB, Cassandra, Gremlin y Table. Cada API tiene su propio conjunto de operaciones de base de datos. Estas abarcan desde sencillas lecturas y escrituras de punto hasta consultas complejas. Cada operación de base de datos consume recursos del sistema en función de la complejidad de la operación.

Azure Cosmos DB se encarga de normalizar el costo de todas las operaciones de base de datos y se expresa en términos de unidades de solicitud (RU en su forma abreviada). La solicitud de cargos son las unidades de solicitud que consumen todas las operaciones de base de datos. Puede considerar que las unidades de solicitud son como una moneda de rendimiento, que resume los recursos del sistema, como CPU, IOPS y memoria, necesarios para realizar las operaciones de base de datos compatibles con Azure Cosmos DB. Con independencia de qué API utilice para interactuar con el contenedor de Azure Cosmos, los costos siempre se miden por RU. Si la operación de base de datos es una escritura, lectura puntual o consulta, los costos siempre se miden en RU. Para obtener más información, consulte el artículo que contiene las [unidades de solicitud y sus consideraciones](../request-units.md).

En este artículo se presentan las distintas formas de encontrar el consumo de [unidades de solicitud](../request-units.md) (RU) de cualquier operación que se ejecuta en un contenedor del la API de Gremlin de Azure Cosmos DB. Si usa una API diferente, consulte los artículos [API para MongoDB](../mongodb/find-request-unit-charge-mongodb.md), [Cassandra API](../cassandra/find-request-unit-charge-cassandra.md), [API de SQL](../find-request-unit-charge.md) y [Table API](../table/find-request-unit-charge.md) para encontrar el cargo de las RU.

A los encabezados devueltos por la API de Gremlin se les asignan atributos de estado personalizados, que actualmente se exponen mediante el SDK de Java y .NET de Gremlin. El cargo de solicitud está disponible en la clave `x-ms-request-charge`. Si usa la API de Gremlin, tiene varias opciones para buscar el consumo de RU para una operación en un contenedor de Azure Cosmos.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-graph-console.md#create-a-database-account) y suminístrele datos, o seleccione una cuenta existente que ya contenga datos.

1. Vaya al panel **Data Explorer** y seleccione el contenedor en el que quiere trabajar.

1. Escriba una consulta válida y, a continuación, seleccione **Ejecutar consulta de Gremlin**.

1. Seleccione **Query Stats** (Estadísticas de consulta) para mostrar el cargo de solicitud real correspondiente a la solicitud que ha ejecutado.

:::image type="content" source="../media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Captura de pantalla para obtener un cargo de solicitud de consulta de Gremlin en Azure Portal":::

## <a name="use-the-net-sdk-driver"></a>Uso del controlador del SDK de .NET

Si usa el [SDK de .NET para Gremlin](https://www.nuget.org/packages/Gremlin.Net/), los atributos de estado están disponibles en la propiedad `StatusAttributes` del objeto `ResultSet<>`:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para más información, consulte [Inicio rápido: Compilación de una aplicación .NET Framework o Core mediante una cuenta de Gremlin API de Azure Cosmos DB](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Uso del controlador del SDK de Java

Al usar el [SDK de Java para Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), puede recuperar los atributos de estado mediante una llamada al método `statusAttributes()` en el objeto `ResultSet`:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para más información, consulte [Inicio rápido: Creación de una base de datos de grafos en Azure Cosmos DB mediante el SDK de Java](create-graph-java.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo optimizar el consumo de RU, vea estos artículos:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](../request-units.md)
* [Optimización del costo de rendimiento aprovisionado en Azure Cosmos DB](../optimize-cost-throughput.md)
* [Optimización de los costos de consulta de Azure Cosmos DB](../optimize-cost-reads-writes.md)
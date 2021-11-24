---
title: Notas de la versión y recursos del conector OLTP de Azure Cosmos DB Apache Spark 3 para SQL API (versión preliminar)
description: Obtenga información sobre el conector OLTP de Azure Cosmos DB Apache Spark 3 para SQL API, como las fechas de lanzamiento, las fechas de retirada y los cambios realizados entre las diferentes versiones del SDK de Java de Azure Cosmos DB para SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/12/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: abb10485baebc0b7379ec650fd9eeb95ebadf326
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491286"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Conector OLTP de Azure Cosmos DB Apache Spark 3 para Core (SQL) API: notas de la versión y recursos
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [SDK de .NET v2](sql-api-sdk-dotnet.md)
> * [SDK de .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK de fuente de cambios de .NET, versión 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK de Java v4](sql-api-sdk-java-v4.md)
> * [Versión 2 del SDK de Java asincrónico](sql-api-sdk-async-java.md)
> * [SDK de Java v2 sincrónico](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector Spark 3 OLTP](sql-api-sdk-java-spark-v3.md)
> * [Conector Spark 2 OLTP](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Proveedor de recursos de REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor: .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor: Java](sql-api-sdk-bulk-executor-java.md)

El **conector Azure Cosmos DB OLTP Spark** proporciona compatibilidad con Apache Spark en Azure Cosmos DB mediante SQL API. Azure Cosmos DB es un servicio de base de datos de distribución global que permite a los desarrolladores trabajar con datos mediante diversas API estándar, como SQL, MongoDB, Cassandra, Graph y Table.

Si tiene algún comentario o ideas sobre cómo mejorar su experiencia, cree un problema en nuestro [repositorio SDK GitHub](https://github.com/Azure/azure-sdk-for-java/issues/new).

## <a name="documentation-links"></a>Vínculos de documentación

* [Introducción](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
* [API de catálogo](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
* [Referencia de parámetros de configuración](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)

## <a name="version-compatibility"></a>Compatibilidad de versiones

| Conector     | Versión mínima de Spark | Versión mínima de Java | Versiones de Scala admitidas | Runtimes de Databricks compatibles |
| ------------- | --------------------- | -------------------- | -----------------------  | ----------------------------- |
| 4.4.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*, 9.\*                    |
| 4.3.1         | 3.1.1                 | 8                    | 2,12                     | 8.\*, 9.\*                    |
| 4.3.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*, 9.\*                    |
| 4.2.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.1.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0-beta.3  | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0-beta.2  | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0-beta.1  | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |

## <a name="download"></a>Descargar

Puede usar la coordenada de Maven del archivo JAR para instalar automáticamente el conector de Spark en Databricks Runtime 8 desde Maven: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.4.0`.

También puede realizar la integración con el conector Cosmos DB Spark en el proyecto de SBT:

```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.4.0"
```

El conector Azure Cosmos DB Spark está disponible en el [repositorio central de Maven](https://search.maven.org/search?q=g:com.azure.cosmos.spark).

Si encuentra algún error o desea sugerir un cambio de característica, [presenta un problema](https://github.com/Azure/azure-sdk-for-java/issues/new). 

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Más información sobre [Apache Spark](https://spark.apache.org/).
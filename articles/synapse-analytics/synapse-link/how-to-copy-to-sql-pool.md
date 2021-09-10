---
title: Copia de los datos de Synapse Link para Azure Cosmos DB data en un grupo de SQL dedicado mediante Apache Spark
description: Cargue los datos en un dataframe de Spark, manténgalos y cárguelos en una tabla de un grupo de SQL dedicado
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 2a061e5db3e4a6b6aef193449abcd41d0b8eaf76
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097542"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Copia de datos de Azure Cosmos DB en un grupo de SQL dedicado mediante Apache Spark

Azure Synapse Link para Azure Cosmos DB permite a los usuarios ejecutar análisis casi en tiempo real con datos operativos en Azure Cosmos DB. Sin embargo, hay ocasiones en que es necesario agregar y enriquecer algunos datos para servir a los usuarios del almacenamiento de datos. Tanto el mantenimiento como la exportación de datos de Synapse Link se pueden realizar con unas pocas celdas de un cuaderno.

## <a name="prerequisites"></a>Requisitos previos
* [Aprovisione un área de trabajo de Synapse](../quickstart-create-workspace.md)con:
    * [Grupo de Apache Spark sin servidor](../quickstart-create-apache-spark-pool-studio.md)
    * [grupo de SQL dedicado](../quickstart-create-sql-pool-studio.md)
* [Aprovisione una cuenta de Cosmos DB con un contenedor HTAP con datos](../../cosmos-db/configure-synapse-link.md)
* [Conecte el contenedor HTAP de Azure Cosmos DB al área de trabajo](./how-to-connect-synapse-link-cosmos-db.md)
* [Tenga la configuración adecuada para importar datos al grupo de SQL dedicado desde Spark](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Pasos
En este tutorial, se conectará al almacén analítico para que no haya ningún impacto en el almacén de transacciones (no consumirá unidades de solicitud). Siga estos pasos:
1. Leer el contenedor HTAP de Cosmos DB en un dataframe de Spark.
2. Agregar los resultados a un dataframe nuevo.
3. Ingesta de los datos en el grupo de SQL dedicado

[![Pasos de Spark a SQL 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Datos
En ese ejemplo, se usa un contenedor HTAP denominado **RetailSales**. Forma parte de un servicio vinculado denominado **ConnectedData** y tiene el siguiente esquema:
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

Agregaremos las ventas (*quantity*, *revenue* [precio x cantidad] por *productCode* y *weekStarting* para la elaboración de informes. Por último, se exportarán los datos en una tabla del grupo de SQL dedicado denominada **dbo.productsales**.

## <a name="configure-a-spark-notebook"></a>Configuración de un cuaderno de Spark
Cree un cuaderno de Spark con Scala and Spark (Scala) como lenguaje principal. Se usa la configuración predeterminada del cuaderno para la sesión.

## <a name="read-the-data-in-spark"></a>Lectura de datos en Spark
Lea el contenedor HTAP de Cosmos DB con Spark en un dataframe de la primera celda.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Agregación de los resultados a un dataframe nuevo

En la segunda celda, se ejecutan la transformación y los agregados necesarios para el nuevo dataframe antes de cargarlo en una base de datos del grupo de SQL dedicado.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>Carga de los resultados en un grupo de SQL dedicado

En la tercera celda, se cargan los datos en un grupo de SQL dedicado. Se creará automáticamente una tabla externa temporal, un origen de datos externo y un formato de archivo externo que se eliminarán una vez que se haya completado el trabajo.

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Consulta de los resultados con SQL

El resultado se puede mediante una sencilla consulta SQL, como el siguiente script de SQL:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

La consulta presentará los resultados siguientes en modo de gráfico: [![Pasos de Spark a SQL 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes
* [Consulta de un almacén analítico de Azure Cosmos DB con Apache Spark 3](./how-to-query-analytical-store-spark-3.md)
* [Consulta de un almacén analítico de Azure Cosmos DB con Apache Spark 2](./how-to-query-analytical-store-spark.md)

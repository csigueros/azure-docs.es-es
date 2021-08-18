---
title: Acceso a Cassandra API de Azure Cosmos DB desde Azure Databricks
description: En este artículo se explica cómo trabajar con Cassandra API de Azure Cosmos DB desde Azure Databricks.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 1b5f84bfaee7fe98bafbdaa949de783d2e423951
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113355470"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Acceso a datos de Cassandra API de Azure Cosmos DB desde Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

En este artículo se explica cómo trabajar con Cassandra API de Azure Cosmos DB desde Spark en [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks).

## <a name="prerequisites"></a>Prerequisites

* [Aprovisionar una cuenta de Cassandra API de Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Revisar los conceptos básicos de la conexión a Cassandra API de Azure Cosmos DB](cassandra-spark-generic.md)

* [Aprovisionar un clúster de Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)

* [Revisar los ejemplos de código para trabajar con Cassandra API](cassandra-spark-generic.md#next-steps)

* [Utilizar cqlsh para la validación, en caso de preferirse](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuración de la instancia de Cassandra API para el conector Cassandra:**

  El conector para Cassandra API requiere que los detalles de la conexión de Cassandra se inicialicen como parte del contexto de Spark. Cuando inicia un cuaderno de Databricks, el contexto de Spark ya está inicializado y no es aconsejable pararlo y reiniciarlo. Una solución es agregar la configuración de la instancia de Cassandra API en un nivel de clúster, en la configuración de clúster de Spark. Esto se hace una sola vez por cada clúster. Agregue el código siguiente a la configuración de Spark como un par clave-valor separado por espacios:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Incorporación de las dependencias necesarias

* **Conector de Spark de Cassandra:** : para integrar Cassandra API de Azure Cosmos DB con Spark, se debe asociar el conector Cassandra al clúster de Azure Databricks. Para asociar el clúster:

  * Revise la versión del entorno de ejecución de Databricks y la versión de Spark. A continuación, busque las [coordenadas de Maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que son compatibles con el conector de Spark de Cassandra y asócielas al clúster. Consulte el artículo ["Upload a Maven package or Spark package"](https://docs.databricks.com/user-guide/libraries.html) (Carga de un paquete de Maven o Spark) para asociar la biblioteca del conector al clúster. Se recomienda seleccionar el entorno de ejecución de Databricks Runtime versión 7.5, que admite Spark 3.0: Para agregar el conector de Cassandra de Apache Spark, su clúster, seleccione **Bibliotecas** > **Instalar Nueva** > **Maven** y, a continuación, agregue `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` en las coordenadas de Maven. Si usa Spark 2.x, se recomienda utilizar un entorno con la versión de Spark 2.4.5 y el conector de Spark en las coordenadas de Maven `com.datastax.spark:spark-cassandra-connector_2.11:2.4.3`.

* **Biblioteca específica de Cassandra API de Azure Cosmos DB:** si utiliza Spark 2.x, se requiere un generador de conexión personalizado para configurar la directiva de reintentos desde el conector de Spark de Cassandra a Cassandra API de Azure Cosmos DB. Agregue las `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.2.0`[coordenadas de Maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.2.0/jar) para asociar la biblioteca al clúster.

> [!NOTE]
> Si usa Spark 3.0 o posterior, no es necesario instalar la biblioteca específica de Cassandra API de Cosmos DB mencionada anteriormente.

## <a name="sample-notebooks"></a>Cuadernos de ejemplo

Hay disponible una lista de [cuadernos de ejemplo](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/notebooks/scala) de Azure Databricks en el repositorio de GitHub para descarga. Estos ejemplos incluyen cómo conectarse a Cassandra API de Azure Cosmos DB desde Spark y realizar diferentes operaciones CRUD en los datos. También puede [importar todos los cuadernos](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/dbc) en el área de trabajo del clúster de Databricks y ejecutarlos. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Acceso con Cassandra API de Azure Cosmos DB desde los programas de Spark Scala

Los programas que se van a ejecutar como procesos automatizados de Azure Databricks se envían al clúster mediante [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) y están programados para ejecutarse mediante los trabajos de Azure Databricks.

Los siguientes son vínculos que le ayudarán a empezar a compilar programas de Spark Scala para interactuar con Cassandra API de Azure Cosmos DB.
* [Conexión a Cassandra API de Azure Cosmos DB desde un programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Ejecución de un programa de Spark Scala como un trabajo automatizado en Azure Databricks](/azure/databricks/jobs)
* [Lista completa de ejemplos de código para trabajar con Cassandra API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Pasos siguientes

Introducción a la [creación de una cuenta de Cassandra API, una base de datos y una tabla](create-cassandra-api-account-java.md) mediante una aplicación de Java.

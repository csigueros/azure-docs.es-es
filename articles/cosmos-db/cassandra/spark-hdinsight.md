---
title: Acceso a Cassandra API de Azure Cosmos DB desde Spark en YARN con HDInsight
description: En este artículo se explica cómo trabajar con Cassandra API de Azure Cosmos DB desde Spark en YARN con HDInsight
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 2026903bc4db51751b86daa5102c9d846c8dfd51
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780327"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Acceso a Cassandra API de Azure Cosmos DB desde Spark en YARN con HDInsight
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

En este artículo se explica cómo acceder a Cassandra API de Azure Cosmos DB desde Spark en YARN con HDInsight-Spark desde spark-shell. HDInsigt es una PaaS de la plataforma Hadoop de Hortonworks de Microsoft en Azure que aprovecha el almacenamiento de objetos para HDFS y tiene diversas variantes, entre las que se incluye [Spark](../../hdinsight/spark/apache-spark-overview.md).  Aunque el contenido de este documento hace referencia a HDInsight-Spark, se puede aplicar a todas las distribuciones de Hadoop.  

## <a name="prerequisites"></a>Requisitos previos

* [Aprovisionar Cassandra API de Azure Cosmos DB](manage-data-dotnet.md#create-a-database-account)

* [Revisar los conceptos básicos de la conexión a Cassandra API de Azure Cosmos DB](connect-spark-configuration.md)

* [Aprovisionar un clúster de HDInsight-Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Revisar los ejemplos de código para trabajar con Cassandra API](connect-spark-configuration.md#next-steps)

* [Utilizar cqlsh para la validación, en caso de preferirse](connect-spark-configuration.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuración de Cassandra API en Spark2**: el conector de Spark para Cassandra requiere que los detalles de la conexión de Cassandra se inicialicen como parte del contexto de Spark. Al iniciar un cuaderno de Jupyter Notebook, tanto la sesión como el contexto de Spark ya se han inicializado y no se recomienda detener y reinicializar el contexto de Spark, salvo que se haya completado y cada conjunto de configuración forme parte del inicio del cuaderno de Jupyter Notebook predeterminado de HDInsight. Una solución alternativa es agregar los detalles de la instancia de Cassandra directamente a la configuración del servicio Ambari, Spark2. Esta actividad no se realiza más que una vez en cada clúster que requiera que se reinicie el servicio Spark2.
 
  1. Vaya al servicio Ambari, Spark 2, y seleccione las configuraciones.

  2. Luego, vaya a spark2-defaults personalizado y agregue una nueva propiedad con el siguiente y reinicie el servicio Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Acceso a Cassandra API de Azure Cosmos DB desde la shell de Spark

La shell de Spark se usa para realizar pruebas o exploración.

* Inicie spark-shell con las dependencias de maven necesarias compatibles con la versión de Spark de su clúster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Ejecute algunas operaciones DDL y DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Ejecute operaciones CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Acceso a Cassandra API de Azure Cosmos DB desde cuadernos de Jupyter Notebook

HDInsight-Spark incluye los servicios Zeppelin y Jupyter Notebook. Ambos son entornos de cuadernos basados en web que admiten Scala y Python. Los cuadernos son excelentes para la colaboración y el análisis exploratorio interactivo, pero no está pensadas para procesos operativos ni de producción.

Los siguientes cuadernos de Jupyter Notebook se pueden cargar en un clúster de HDInsight Spark y proporcionan ejemplos para trabajar con Cassandra API de Azure Cosmos DB. Asegúrese de consultar el primer cuaderno `1.0-ReadMe.ipynb` para revisar la configuración del servicio de Spark para conectarse a Cassandra API de Azure Cosmos DB.

Descargue estos cuadernos de [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) en su equipo.
  
### <a name="how-to-upload"></a>Cómo se cargan:
Al iniciar Jupyter, vaya a Scala. Cree un directorio y, después, cargue los cuadernos en el directorio. El botón de carga se encuentra en la parte superior derecha.  

### <a name="how-to-run"></a>Cómo se ejecutan:
Ejecute los cuadernos, y cada celda de ellos, de manera secuencial.  Haga clic en el botón de ejecución de la parte superior de cuaderno para ejecutar todas las celdas, o bien Mayús + Entrar para ejecutar cada una de ellas.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Acceso con Cassandra API de Azure Cosmos DB desde el programa Spark Scala

En los procesos automatizados en producción, los programas de Spark se envían al clúster a través de [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un programa Spark Scala en un IDE y envío al clúster de HDInsight Spark mediante Livy para su ejecución](../../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Conexión a Cassandra API de Azure Cosmos DB desde un programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Lista completa de ejemplos de código para trabajar con Cassandra API](connect-spark-configuration.md)

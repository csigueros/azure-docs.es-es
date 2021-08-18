---
title: Operaciones de agregado en tablas de Cassandra API de Azure Cosmos DB de Spark
description: En este artículo se describen operaciones de agregación básicas en tablas de Cassandra API de Azure Cosmos DB de Spark
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 23ec7563c192ae8457e3d888dc4b0982b361afe2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781235"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Operaciones de agregado en tablas de Cassandra API de Azure Cosmos DB de Spark 
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

En este artículo se describen operaciones de agregación básicas en tablas de Cassandra API de Azure Cosmos DB de Spark. 

> [!NOTE]
> Actualmente no se admiten el filtrado ni la agregación del lado del servidor en Cassandra API de Azure Cosmos DB.

## <a name="cassandra-api-configuration"></a>Configuración de Cassandra API

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector
import org.apache.spark.sql.functions._

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> Si usa Spark 3.0 o posterior, no es necesario instalar el asistente de Cosmos DB ni el generador de conexiones. También debe usar `remoteConnectionsPerExecutor` en lugar de `connections_per_executor_max` para el conector de Spark 3 (consulte más arriba). Verá que las propiedades relacionadas con la conexión están definidas en el cuaderno anterior. Con la sintaxis siguiente, las propiedades de conexión se pueden definir de esta manera sin necesidad de definirse en el nivel de clúster (inicialización del contexto de Spark). Sin embargo, cuando se usan operaciones que requieren contexto de Spark (por ejemplo, `CassandraConnector(sc)` para algunas de las operaciones que se muestran a continuación), las propiedades de conexión deben definirse en el nivel de clúster.

## <a name="sample-data-generator"></a>Generador de datos de ejemplo

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Operación de recuento


### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Salida:**
```bash
count: Long = 5
```

### <a name="dataframe-api"></a>Dataframe API

Actualmente no se admite el recuento de tramas de datos.  En el ejemplo siguiente se muestra cómo ejecutar un recuento de tramas de datos después de guardar la trama de datos en la memoria como una solución alternativa.

Elija una de las siguientes [opciones de almacenamiento]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) para no tener problemas de "memoria insuficiente":

* MEMORY_ONLY: se trata de la opción de almacenamiento predeterminada. Almacena RDD como objetos Java deserializados en la máquina virtual Java. Si el RDD no cabe en la memoria, algunas particiones no se almacenarán en caché y se volverán a calcular sobre la marcha cada vez que se necesiten.

* MEMORY_AND_DISK: almacena RDD como objetos Java deserializados en la máquina virtual Java. Si el RDD no cabe en la memoria, almacene las particiones que no entran en el disco y, cuando sea necesario, léalos desde la ubicación en la que estén almacenados.

* MEMORY_ONLY_SER (Java/Scala): almacena RDD como objetos Java serializados: una matriz de un byte por partición. Con esta opción el espacio es más eficiente en comparación con objetos deserializados, especialmente al utilizar un serializador rápido, pero más intensivo en la CPU a la hora de leer.

* MEMORY_AND_DISK_SER (Java/Scala): esta opción de almacenamiento es similar a MEMORY_ONLY_SER, con la única diferencia de que deja desbordar las particiones que no caben en la memoria del disco en lugar de recalcularlas cuando es necesario.

* DISK_ONLY: solo almacena las particiones RDD en el disco.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: igual que los niveles anteriores, pero replica cada partición en dos nodos de clúster.

* OFF_HEAP (experimental): es similar a MEMORY_ONLY_SER, pero almacena los datos en la memoria fuera del montón y requiere que la memoria fuera del montón esté habilitada de antemano. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
%sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Operación de promedio

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Salida:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Salida:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Salida:**
```
16.016000175476073
```

## <a name="min-operation"></a>Operación mínima

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Salida:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Salida:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
%sql
select avg(book_price) from books_vw;
```

**Salida:**
```
11.33
```

## <a name="max-operation"></a>Operación máxima

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Dataframe API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Salida:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
%sql
select max(book_price) from books_vw;
```
**Salida:**
```
22.45
```

## <a name="sum-operation"></a>Operación de suma

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Salida:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Salida:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Salida:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Operación comparable o superior

### <a name="rdd-api"></a>RDD API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Salida:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Dataframe API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Salida:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Pasos siguientes

Para realizar operaciones de copia de tabla, vea:

* [Table copy operations](spark-table-copy-operations.md) (Operaciones de copia en tabla)

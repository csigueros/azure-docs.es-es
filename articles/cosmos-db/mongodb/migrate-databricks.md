---
title: Migración de MongoDB a Azure Cosmos DB API para MongoDB mediante Databricks y Spark
description: Aprenda a usar Databricks Spark para migrar grandes conjuntos de datos desde instancias de MongoDB a Azure Cosmos DB.
author: nayakshweta
ms.author: shwetn
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 437dd6f1b47694d9abb32d4e72ecc23a175d9337
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780551"
---
# <a name="migrate-data-from-mongodb-to-an-azure-cosmos-db-api-for-mongodb-account-by-using-azure-databricks"></a>Migración de datos desde MongoDB a una cuenta de Azure Cosmos DB API para MongoDB mediante Azure Databricks
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Esta guía de migración forma parte de la serie sobre migración de bases de datos de MongoDB a la API Azure CosmosDB para MongoDB. Los pasos críticos de la migración son la [migración previa](pre-migration-steps.md), la migración y la [migración posterior](post-migration-optimization.md), como se muestra a continuación.

:::image type="content" source="./media/pre-migration-steps/overall-migration-steps.png" alt-text="Diagrama de los pasos de migración":::


## <a name="data-migration-using-azure-databricks"></a>Migración de datos con Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) es una oferta de plataforma como servicio (PaaS) para [Apache Spark](https://spark.apache.org/). Ofrece una manera de realizar migraciones sin conexión en un conjunto de datos a gran escala. Puede usar Azure Databricks para realizar una migración sin conexión desde MongoDB a Azure Cosmos DB API para MongoDB.

En este tutorial, aprenderá a:

- Aprovisionar un clúster de Azure Databricks

- Adición de dependencias

- Crear y ejecutar un cuaderno de Scala o Python

- Optimizar el rendimiento de la migración

- Solucionar problemas de errores de limitación de velocidad que se pueden observar durante la migración

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- [Completar los pasos previos a la migración](pre-migration-steps.md), como calcular el rendimiento y elegir una clave compartida.
- [Cree una cuenta de la API de Azure Cosmos DB para MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).

## <a name="provision-an-azure-databricks-cluster"></a>Aprovisionar un clúster de Azure Databricks

Puede seguir las instrucciones para [aprovisionar un clúster de Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Se recomienda seleccionar el entorno de ejecución de Databricks versión 7.6, que admite Spark 3.0.

:::image type="content" source="./media/migrate-databricks/databricks-cluster-creation.png" alt-text="Diagrama de creación del clúster de Databricks.":::


## <a name="add-dependencies"></a>Adición de dependencias

Agregue la biblioteca MongoDB Connector for Spark al clúster para realizar la conexión a puntos de conexión nativos de MongoDB y API de Azure Cosmos DB para MongoDB. En el clúster, seleccione **Libraries** > **Install New** > **Maven** (Bibliotecas > Instalar nueva > Maven) y, después, agregue las coordenadas de Maven `org.mongodb.spark:mongo-spark-connector_2.12:3.0.1`.

:::image type="content" source="./media/migrate-databricks/databricks-cluster-dependencies.png" alt-text="Diagrama de adición de dependencias de clúster de Databricks.":::


Seleccione **Install** (Instalar) y asegúrese de reiniciar el clúster cuando se complete la instalación.

> [!NOTE]
> Asegúrese de reiniciar el clúster de Databricks después de que se haya instalado la biblioteca MongoDB Connector for Spark.

Luego, puede crear un cuaderno de Scala o Python para la migración.


## <a name="create-scala-notebook-for-migration"></a>Creación de un cuaderno de Scala para la migración

Cree un cuaderno de Scala en Databricks. Asegúrese de escribir los valores correctos para las variables antes de ejecutar el código siguiente:


```scala
import com.mongodb.spark._
import com.mongodb.spark.config._
import org.apache.spark._
import org.apache.spark.sql._

var sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
var sourceDb = "<DBNAME>"
var sourceCollection =  "<COLLECTIONNAME>"
var targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
var targetDb = "<DBNAME>"
var targetCollection =  "<COLLECTIONNAME>"

val readConfig = ReadConfig(Map(
  "spark.mongodb.input.uri" -> sourceConnectionString,
  "spark.mongodb.input.database" -> sourceDb,
  "spark.mongodb.input.collection" -> sourceCollection,
))

val writeConfig = WriteConfig(Map(
  "spark.mongodb.output.uri" -> targetConnectionString,
  "spark.mongodb.output.database" -> targetDb,
  "spark.mongodb.output.collection" -> targetCollection,
  "spark.mongodb.output.maxBatchSize" -> "8000"  
))

val sparkSession = SparkSession
  .builder()
  .appName("Data transfer using spark")
  .getOrCreate()

val customRdd = MongoSpark.load(sparkSession, readConfig)

MongoSpark.save(customRdd, writeConfig)
```

## <a name="create-python-notebook-for-migration"></a>Creación de un cuaderno de Python para la migración

Cree un cuaderno de Python en Databricks. Asegúrese de escribir los valores correctos para las variables antes de ejecutar el código siguiente:


```python
from pyspark.sql import SparkSession

sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
sourceDb = "<DBNAME>"
sourceCollection =  "<COLLECTIONNAME>"
targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
targetDb = "<DBNAME>"
targetCollection =  "<COLLECTIONNAME>"

my_spark = SparkSession \
    .builder \
    .appName("myApp") \
    .getOrCreate()

df = my_spark.read.format("com.mongodb.spark.sql.DefaultSource").option("uri", sourceConnectionString).option("database", sourceDb).option("collection", sourceCollection).load()

df.write.format("mongo").mode("append").option("uri", targetConnectionString).option("maxBatchSize",2500).option("database", targetDb).option("collection", targetCollection).save()
```

## <a name="optimize-the-migration-performance"></a>Optimizar el rendimiento de la migración

El rendimiento de la migración se puede ajustar mediante estas configuraciones:

- **Número de trabajos y núcleos en el clúster de Spark**: más trabajos significan más nodos de proceso para ejecutar tareas.

- **maxBatchSize**: el valor `maxBatchSize` controla la velocidad a la que se guardan los datos en la colección de Azure Cosmos DB de destino. Pero si maxBatchSize es demasiado alto para el rendimiento de la colección, puede provocar errores de [limitación de velocidad](prevent-rate-limiting-errors.md).

  Tendría que ajustar el número de trabajadores y maxBatchSize, en función del número de ejecutores en el clúster de Spark, posiblemente el tamaño (y por eso el costo de RU) de cada documento que se escribe y los límites de rendimiento de la colección de destino.

  >[!TIP]
  >maxBatchSize = Rendimiento de la colección / (costo de RU para 1 documento \* número de trabajadores de Spark \* número de núcleos de CPU por trabajo)

- **Partitioner y partitionKey de Spark de MongoDB**: el particionador predeterminado que se usa es MongoDefaultPartitioner y el valor partitionKey predeterminado es _id. El particionador se puede cambiar si se asigna el valor `MongoSamplePartitioner` a la propiedad de configuración de entrada `spark.mongodb.input.partitioner`. De forma similar, partitionKey se puede cambiar si se asigna el nombre de campo adecuado a la propiedad de configuración de entrada `spark.mongodb.input.partitioner.partitionKey`. partitionKey puede ayudar a evitar la asimetría de datos (se escribe un gran número de registros para el mismo valor de clave de partición).

- **Deshabilitar índices durante la transferencia de datos:** para la migración de grandes cantidades de datos, considere la posibilidad de deshabilitar los índices, especialmente los que tienen caracteres comodín en la colección de destino. Los índices aumentan el costo de RU para escribir cada documento. La liberación de estas RU puede ayudar a mejorar la velocidad de transferencia de datos. Puede habilitar los índices una vez que se hayan migrado los datos.



## <a name="troubleshoot"></a>Solucionar problemas

### <a name="timeout-error-error-code-50"></a>Error de tiempo de espera (código de error 50)
Es posible que vea un código de error 50 para las operaciones en la base de datos de Cosmos DB API para MongoDB. Los siguientes escenarios pueden provocar errores de tiempo de espera:

- **El rendimiento asignado a la base de datos es bajo**: asegúrese de que la colección de destino tiene asignado un rendimiento suficiente.
- **Asimetría de datos excesiva con grandes volúmenes de datos**. Si tiene una gran cantidad de datos para migrar a una tabla concreta, pero tiene una asimetría significativa en los datos, es posible que experimente una limitación de velocidad aunque tenga varias [unidades de solicitud](../request-units.md) aprovisionadas en la tabla. Las unidades de solicitud se dividen equitativamente entre las particiones físicas y, una asimetría de datos intensiva puede provocar un cuello de botella de las solicitudes en una única partición. La asimetría de datos significa un gran número de registros para el mismo valor de clave de partición.

### <a name="rate-limiting-error-code-16500"></a>Limitación de velocidad (código de error 16500)

Es posible que vea un código de error 16500 para las operaciones en la base de datos de Cosmos DB API para MongoDB. Se trata de errores de limitación de velocidad y se pueden observar en cuentas antiguas o cuentas en las que la característica de reintento del lado servidor está deshabilitada.
- **Habilitar reintento del lado servidor**: habilite la característica Reintento del lado servidor (SSR) y permita que el servidor vuelva a intentar de forma automática las operaciones con limitación de frecuencia.



## <a name="post-migration-optimization"></a>Optimización posterior a la migración

Después de migrar los datos, puede conectarse a Azure Cosmos DB y administrarlos. También puede seguir otros pasos posteriores a la migración, como optimizar la directiva de indexación, actualizar el nivel de coherencia predeterminado o configurar la distribución global de la cuenta de Azure Cosmos DB. Para más información, consulte el artículo [Optimización posterior a la migración](post-migration-optimization.md).

## <a name="next-steps"></a>Pasos siguientes

* [Administración de la indexación en la API de Azure Cosmos DB para MongoDB](mongodb-indexing.md)

* [Búsqueda del cargo por unidad de solicitud para las operaciones](find-request-unit-charge-mongodb.md)

---
title: API de Hive Warehouse Connector en Azure HDInsight
description: Obtenga información sobre las diferentes API de Hive Warehouse Connector.
author: adesh-rao
ms.author: adrao
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/29/2021
ms.openlocfilehash: 4816830feac9aceb34ecfaa701ea0b7aec9c728b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780867"
---
# <a name="hive-warehouse-connector-apis-in-azure-hdinsight"></a>API de Hive Warehouse Connector en Azure HDInsight

En este artículo se enumeran todas las API que admite Hive Warehouse Connector. Todos los ejemplos que se muestran a continuación se ejecutan usando un shell de Spark y una sesión de Hive Warehouse Connector.

Creación de una sesión de Hive Warehouse Connector:

```scala
import com.hortonworks.hwc.HiveWarehouseSession
val hive = HiveWarehouseSession.session(spark).build()
```

## <a name="prerequisite"></a>Requisito previo

Complete los pasos de [configuración de Hive Warehouse Connector](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).


## <a name="supported-apis"></a>API admitidas

- Establecimiento de la base de datos
    ```scala
    hive.setDatabase("<database-name>")
    ```

- Enumeración de todas las bases de datos
    ```scala
    hive.showDatabases()
    ```

- Enumeración de todas las tablas de la base de datos actual
    ```scala
    hive.showTables()
    ```

- Descripción de una tabla
    
    ```scala
   // Describes the table <table-name> in the current database
    hive.describeTable("<table-name>")
    ```
    
    ```scala
   // Describes the table <table-name> in <database-name>
    hive.describeTable("<database-name>.<table-name>")
    ```

- Quitar una base de datos
    
    ```scala
   // ifExists and cascade are boolean variables
    hive.dropDatabase("<database-name>", ifExists, cascade)
    ```

- Eliminación de una tabla de la base de datos actual
    
    ```scala
    // ifExists and purge are boolean variables
    hive.dropTable("<table-name>", ifExists, purge)
    ```

- Crear una base de datos
    ```scala
   // ifNotExists is boolean variable
    hive.createDatabase("<database-name>", ifNotExists)
    ```

- Creación de una tabla en la base de datos actual
    ```scala
   // Returns a builder to create table
    val createTableBuilder = hive.createTable("<table-name>")
    ```
    
    El generador para crear tablas solo admite las siguientes operaciones: 
    
    ```scala
   // Create only if table does not exists already
    createTableBuilder = createTableBuilder.ifNotExists()
    ```
    
    ```scala
   // Add columns
    createTableBuilder = createTableBuilder.column("<column-name>", "<datatype>")
    ```
    
    ```scala
    // Add partition column
    createTableBuilder = createTableBuilder.partition("<partition-column-name>", "<datatype>")
    ```
    ```scala
   // Add table properties
    createTableBuilder = createTableBuilder.prop("<key>", "<value>")
    ```
    ```scala
    // Creates a bucketed table,
    // Parameters are numOfBuckets (integer) followed by column names for bucketing
    createTableBuilder = createTableBuilder.clusterBy(numOfBuckets, "<column1>", .... , "<columnN>")
    ```
    
    ```scala
    // Creates the table
    createTableBuilder.create()
    ```

    > [!NOTE]
    > Esta API crea una tabla con formato ORC en la ubicación predeterminada. Para conocer otras características u opciones o para crear una tabla usando consultas de Hive, use la API `executeUpdate`.


- Lectura de una tabla

    ```scala
   // Returns a Dataset<Row> that contains data of <table-name> in the current database
    hive.table("<table-name>")
    ```

- Ejecución de comandos DDL en HiveServer2 

    ```scala
    // Executes the <hive-query> against HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>")
    ```
    
    ```scala
    // Executes the <hive-query> against HiveServer2
    // Throws exception, if propagateException is true and query threw excpetion in HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>", propagateException) // propagate exception is boolean value
    ```

- Ejecución de una consulta de Hive y carga del resultado en el conjunto de datos
    
  - Ejecución de una consulta a través de demonios LLAP **(opción recomendada)**
    ```scala
    // <hive-query> should be a hive query 
    hive.executeQuery("<hive-query>")
    ```
  - Ejecución de una consulta a través de HiveServer2 mediante JDBC

    Establecimiento de `spark.datasource.hive.warehouse.smartExecution` en `false` en configuraciones de Spark antes de iniciar una sesión en la plataforma para usar esta API
    ```scala
    hive.execute("<hive-query>")
    ```

- Cierre de una sesión de Hive Warehouse Connector
    ```scala
    // Closes all the open connections and
    // release resources/locks from HiveServer2
    hive.close()
    ```

- Ejecución de una consulta de combinación de Hive
    
    Esta API crea una consulta de combinación de Hive con el siguiente formato:
    
    ```
    MERGE INTO <current-db>.<target-table> AS <targetAlias> USING <source expression/table> AS <sourceAlias>
    ON <onExpr>
    WHEN MATCHED [AND <updateExpr>] THEN UPDATE SET <nameValuePair1> ... <nameValuePairN>
    WHEN MATCHED [AND <deleteExpr>] THEN DELETE
    WHEN NOT MATCHED [AND <insertExpr>] THEN INSERT VALUES <value1> ... <valueN>
    ```

    ```scala
    val mergeBuilder = hive.mergeBuilder() // Returns a builder for merge query
    ```
    El generador admite las siguientes operaciones:
    
    ```scala
    mergeBuilder.mergeInto("<taget-table>", "<targetAlias>")
    ```
    
    ```scala
    mergeBuilder.using("<source-expression/table>", "<sourceAlias>")
    ```
    
    ```scala
    mergeBuilder.on("<onExpr>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenUpdate("<updateExpr>", "<nameValuePair1>", ... , "<nameValuePairN>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenDelete("<deleteExpr>")
    ```
    
    ```scala
    mergeBuilder.whenNotMatchedInsert("<insertExpr>", "<value1>", ... , "<valueN>");
    ```

    ```scala
    // Executes the merge query
    mergeBuilder.merge()
    ```

- Escritura de un conjunto de datos en una tabla de Hive por lotes
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector")
       .option("table", tableName)
       .mode(SaveMode.Type)
       .save()
    ```
   - TableName debe tener el formato `<db>.<table>` o `<table>`. Si no se proporciona ningún nombre de base de datos, la tabla buscará o creará en la base de datos actual.
    
   - Los tipos de SaveMode son los siguientes:
    
     - Append: anexa el conjunto de datos a la tabla dada.
    
     - Overwrite: sobrescribe los datos de la tabla dada con el conjunto de datos.
    
     - Ignore: omite la escritura si la tabla ya existe, y no se produce ningún error.
    
     - ErrorIfExists: se produce un error si ya existe la tabla.


- Escritura de un conjunto de datos en una tabla de Hive usando HiveStreaming
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("metastoreUri", "<HMS_URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    
     // To write to static partition
     df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("partition", partition)
       .option("metastoreUri", "<HMS URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    ```
    > [!NOTE]
    > Al escribir secuencias siempre se anexan datos.


- Escritura de una secuencia de Spark en una tabla de Hive
    ```scala
    stream.writeStream
        .format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource")
        .option("metastoreUri", "<HMS_URI>")
        .option("database", databaseName)
        .option("table", tableName)
      //.option("partition", partition) , add if inserting data in partition
      //.option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
        .start()
    ```

---
title: Las operaciones Apache Spark admitidas por el Hive Warehouse Connector en Azure HDInsight
description: Obtenga información sobre las distintas funcionalidades de Hive Warehouse Connector en Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: b876cd702a2398e8dcb0e1a0be5fffce0615e58d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750819"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Las operaciones Apache Spark admitidas por el Hive Warehouse Connector en Azure HDInsight

En este artículo se muestran las operaciones basadas en Spark que admite Hive Warehouse Connector (HWC). Todos los ejemplos que se muestran a continuación se ejecutarán a través del shell de Apache Spark.

## <a name="prerequisite"></a>Requisito previo

Complete los pasos de [configuración de Hive Warehouse Connector](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).

## <a name="getting-started"></a>Introducción

Para iniciar una sesión de spark-shell, realice los pasos siguientes:

1. Use el [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para conectarse al clúster de Apache Spark. Modifique el comando siguiente: reemplace CLUSTERNAME por el nombre del clúster y, luego, escriba el comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Desde la sesión de ssh, ejecute el comando siguiente para anotar la versión `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Edite el código siguiente con la versión `hive-warehouse-connector-assembly` identificada anteriormente. Ejecute el siguiente comando para iniciar el shell de Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Después de iniciar spark-shell, se puede iniciar una instancia del conector de Hive Warehouse mediante los siguientes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Creación de DataFrames de Spark a partir de consultas de Hive

Los resultados de todas las consultas mediante la biblioteca HWC se devuelven como un elemento DataFrame. En los ejemplos siguientes se muestra cómo crear una consulta de subárbol.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Los resultados de la consulta son DataFrames de Spark, que se pueden usar con bibliotecas de Spark como MLIB y SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Escritura de DataFrames de Spark en tablas de Hive

Spark no admite la escritura en tablas ACID administradas de Hive de forma nativa. Pero con HWC puede escribir cualquier elemento DataFrame en una tabla de Hive. Puede ver como funciona esta funcionalidad en el ejemplo siguiente:

1. Cree una tabla denominada `sampletable_colorado` y especifique sus columnas con el comando siguiente:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtre la tabla `hivesampletable` en que la columna `state` es igual a `Colorado`. Esta consulta de Hive devuelve un elemento DataFrame de Spark y el resultado se guarda en la tabla de Hive `sampletable_colorado` mediante la función `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Vea los resultados con el siguiente comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="visualización de la tabla de Hive del conector de Hive Warehouse" border="true":::


## <a name="structured-streaming-writes"></a>Escrituras de flujos estructurados

Al usar el conector de Hive Warehouse, puede emplear los flujos de Spark para escribir datos en las tablas de Hive.

> [!IMPORTANT]
> No se admiten las escrituras de streaming estructurado en clústeres de Spark 4.0 con ESP habilitado.

En el ejemplo se ingieren datos de una secuencia de Spark en el puerto de localhost 9999 en una tabla de Hive. Hive Warehouse Connector.

1. Desde el shell de Spark abierto, inicie una secuencia de Spark con el siguiente comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Genere datos para el flujo de Spark que creó. Para ello, siga estos pasos:
    1. Abra una segunda sesión de SSH en el mismo clúster de Spark.
    1. En el símbolo del sistema, escriba `nc -lk 9999`. Este comando usa la utilidad `netcat` para enviar datos desde la línea de comandos al puerto especificado.

1. Vuelva a la primera sesión de SSH y cree una nueva tabla de Hive para contener los datos de streaming. En spark-shell, escriba el siguiente comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Después, escriba los datos de streaming en la tabla recién creada mediante el siguiente comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Actualmente, las opciones `metastoreUri` y `database` deben establecerse de forma manual debido a un problema conocido en Apache Spark. Para obtener más información acerca de este problema, consulte  [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Vuelva a la segunda sesión de SSH y escriba los valores siguientes:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Vuelva a la primera sesión de SSH y observe la breve actividad. Use el siguiente comando para ver los datos:

    ```scala
    hive.table("stream_table").show()
    ```

Use **Ctrl + C** para detener `netcat` en la segunda sesión de SSH. Use `:q` para salir de spark-shell en la primera sesión de SSH.

## <a name="next-steps"></a>Pasos siguientes

* [Integración de HWC con Apache Spark y Apache Hive](./apache-hive-warehouse-connector.md)
* [Uso de Interactive Query con HDInsight](./apache-interactive-query-get-started.md)
* [Integración de HWC con Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [API compatibles con HWC](./hive-warehouse-connector-apis.md)

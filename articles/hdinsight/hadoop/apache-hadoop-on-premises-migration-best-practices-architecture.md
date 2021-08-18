---
title: 'Arquitectura: Apache Hadoop local a Azure HDInsight'
description: Obtenga información acerca de los procedimientos recomendados de arquitectura para migrar clústeres locales de Apache Hadoop a Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 91e5effe1cfb948329a784fed06bd2bce0694c3d
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284103"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Procedimientos recomendados de arquitectura para migrar clústeres locales de Apache Hadoop a Azure HDInsight

En este artículo se proporcionan recomendaciones para la arquitectura de sistemas de Azure HDInsight. Forma parte de una serie de artículos que proporcionan prácticas recomendadas para ayudar a migrar sistemas locales de Apache Hadoop a Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Uso de varios clústeres optimizados para cargas de trabajo

Muchas implementaciones de Apache Hadoop locales consisten en un solo clúster grande que admite muchas cargas de trabajo. Este clúster único puede ser complejo y, para que todo funcione correctamente, podría requerir poner en riesgo los servicios individuales. La migración de clústeres locales de Hadoop a Azure HDInsight requiere un cambio de enfoque.

Los clústeres de Azure HDInsight están diseñados para un tipo específico de uso de proceso. Dado que el almacenamiento se puede compartir entre varios clústeres, es posible crear varios clústeres de proceso optimizados para cargas de trabajo a fin de satisfacer las necesidades de los distintos trabajos. Cada tipo de clúster tiene la configuración óptima para esa carga de trabajo específica. En la tabla siguiente se enumeran los tipos de clústeres compatibles de HDInsight y las cargas de trabajo correspondientes.

|Carga de trabajo|Tipo de clúster de HDInsight|
|---|---|
|Procesamiento por lotes (ETL/ELT)|Hadoop, Spark|
|Almacenamiento de datos|Hadoop, Spark, Interactive Query|
|IoT / Streaming|Kafka, Storm, Spark|
|Procesamiento de transacciones de NoSQL|HBase|
|Consultas interactivas y más rápidas con almacenamiento en caché en memoria|Interactive Query|
|Ciencia de datos| Spark|

La tabla siguiente muestra los distintos métodos que se pueden usar para crear un clúster de HDInsight.

|Herramienta|Basado en explorador|Línea de comandos|API DE REST|SDK|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[CLI de Azure (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[SDK de .NET](/dotnet/api/overview/azure/hdinsight)||||X|
|[SDK de Python](/python/api/overview/azure/hdinsight)||||X|
|[SDK de Java](/java/api/overview/azure/hdinsight)||||X|
|[Plantillas del Administrador de recursos de Azure](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Para obtener más información, vea el artículo [Tipos de clúster de HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Uso de clústeres transitorios y a petición

Los clústeres de HDInsight pueden quedarse sin usar durante largos períodos de tiempo. Para ayudar a ahorrar en costos de recursos, HDInsight admite clústeres transitorios y a petición, que se pueden eliminar una vez que la carga de trabajo se ha completado correctamente.

Cuando se elimina un clúster, no se quitan la cuenta de almacenamiento asociada ni los metadatos externos. El clúster se puede volver a crear más adelante utilizando las mismas cuentas de almacenamiento y tiendas de metadatos.

Azure Data Factory puede usarse para programar la creación de clústeres de HDInsight a petición. Para obtener más información, vea el artículo [Creación de clústeres de Apache Hadoop a petición en HDInsight mediante Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Separación del almacenamiento del proceso

Normalmente, las implementaciones locales de Hadoop utilizan el mismo conjunto de máquinas para el almacenamiento y el procesamiento de datos. Como están juntos, el proceso y el almacenamiento deben escalarse a la vez.

En los clústeres de HDInsight, no es necesario ubicar el almacenamiento junto al proceso y pueden encontrarse en Azure Storage, Azure Data Lake Storage o en ambos. La separación del almacenamiento del proceso tiene las siguientes ventajas:

- Uso compartido de datos entre los clústeres.
- Uso de clústeres transitorios, puesto que los datos no dependen del clúster.
- Costo de almacenamiento reducido.
- Escalado del almacenamiento y el proceso por separado.
- Replicación de datos entre regiones.

Los clústeres de proceso se crean cerca de los recursos de la cuenta de almacenamiento en una región de Azure para mitigar el costo de rendimiento derivado de la separación de proceso y almacenamiento. La redes de alta velocidad consiguen que los nodos de proceso puedan acceder de forma eficaz a los datos que están dentro del almacenamiento de Azure.

## <a name="use-external-metadata-stores"></a>Uso de almacenes de metadatos externos

Hay dos tiendas de metadatos principales que funcionan con clústeres de HDInsight: [Apache Hive](https://hive.apache.org/) y [Apache Oozie](https://oozie.apache.org/). Hive Metastore es el repositorio de esquema central que se puede usar en motores de procesamiento de datos como Hadoop, Spark, LLAP, Presto y Apache Pig. La tienda de metadatos de Oozie almacena los detalles sobre la programación y el estado de los trabajos de Hadoop completados y en curso.

HDInsight usa Azure SQL Database para las tiendas de metadatos de Hive y Oozie. Hay dos formas de configurar una tienda de metadatos para los clústeres de HDInsight:

1. Tienda de metadatos predeterminada

    - No implica costes adicionales.
    - La tienda de metadatos se elimina cuando se elimina el clúster.
    - La tienda de metadatos no se puede compartir entre otros clústeres.
    - Usa el nivel básico de Azure SQL DB, que tiene un límite de cinco DTU.

1. Tienda de metadatos externa personalizada.

    - Se especifica una base de datos de Azure SQL externa como almacén de metadatos.
    - Los clústeres se pueden crear y eliminar sin pérdida de metadatos, incluidos los detalles de trabajo de Oozie del esquema de Hive.
    - Una sola base de datos de la tienda de metadatos se puede compartir con otros tipos de clúster.
    - La tienda de metadatos se puede escalar verticalmente según sea necesario.
    - Para más información, consulte [Use external metadata stores in Azure HDInsight](../hdinsight-use-external-metadata-stores.md) (Uso de almacenes externos de metadatos en Azure HDInsight).

## <a name="best-practices-for-hive-metastore"></a>Procedimientos recomendados para Hive Metastore

Estos son algunos procedimientos recomendados para Hive Metastore en HDInsight:

- Utilice una tienda de metadatos externa personalizada para separar recursos de proceso y metadatos.
- Comience con una instancia de Azure SQL de nivel S2, que proporciona 50 DTU y 250 GB de almacenamiento. Si percibe un cuello de botella, puede escalar la base de datos.
- No comparta la tienda de metadatos creada para una versión de clúster de HDInsight con los clústeres de una versión diferente. Diferentes versiones de Hive utilizan diferentes esquemas. Por ejemplo, no se puede compartir una tienda de metadatos con los clústeres de Hive 1.2 y 2.1.
- Realice una copia de la tienda de metadatos personalizada periódicamente.
- Mantenga su tienda de metadatos y el clúster de HDInsight en la misma región.
- Supervise el rendimiento y la disponibilidad de su tienda de metadatos mediante herramientas de supervisión de Azure SQL Database, como Azure Portal o los registros de Azure Monitor.
- Ejecute el comando `ANALYZE TABLE` según sea necesario a fin de generar estadísticas para tablas y columnas. Por ejemplo, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Procedimientos recomendados para las distintas cargas de trabajo

- Considere el uso del clúster de LLAP para consultas de Hive interactivas con tiempo de respuesta mejorado. [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) es una característica nueva en Hive 2.0 que permite el almacenamiento en caché en memoria de las consultas. LLAP hace que las consultas de Hive sean mucho más rápidas, hasta [26 más que Hive 1.x en algunos casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Considere la posibilidad de utilizar trabajos de Spark en lugar de trabajos de Hive.
- Considere la posibilidad de reemplazar las consultas basadas en Impala por consultas LLAP.
- Considere la posibilidad de reemplazar los trabajos de MapReduce por trabajos de Spark.
- Considere la posibilidad de reemplazar los trabajos por lotes de Spark de baja latencia utilizando trabajos de Spark Structured Streaming.
- Considere la posibilidad de usar Azure Data Factory (ADF) 2.0 para la orquestación de datos.
- Considere la posibilidad de utilizar Ambari para la administración de clústeres.
- Cambie el almacenamiento de datos de HDFS local a WASB, ADLS o ADFS para el procesamiento de scripts.
- Considere la posibilidad de usar Ranger RBAC en las tablas y la auditoría de Hive.
- Considere la posibilidad de usar COSMOS DB en lugar de MongoDB o Cassandra.

## <a name="next-steps"></a>Pasos siguientes

Lea el siguiente artículo de esta serie:

- [Infrastructure best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-infrastructure.md) (Procedimientos recomendados de infraestructura para la migración local a Azure HDInsight Hadoop)
---
title: 'Consulta de datos desde Azure Storage compatible con HDFS: Azure HDInsight'
description: Aprenda a consultar datos desde Azure Storage y Azure Data Lake Storage para almacenar los resultados del análisis.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: c4cbce0bce0b495bee9a2f9c717f21de665f29f7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280351"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Uso de Azure Storage con clústeres de Azure HDInsight

Puede almacenar datos en [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) o [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). O puede usar una combinación de estas opciones. Estas opciones de almacenamiento permiten eliminar de forma segura clústeres de HDInsight que se usan para el cálculo sin perder datos del usuario.

Apache Hadoop admite una noción del sistema de archivos predeterminado. El sistema de archivos predeterminado implica una autoridad y un esquema predeterminados. También se puede usar para resolver rutas de acceso relativas. Durante el proceso de creación del clúster de HDInsight, puede especificar un contenedor de blobs en Azure Storage como el sistema de archivos predeterminado. Además, con HDInsight 3.6, puede seleccionar o bien Azure Blob Storage, o Azure Data Lake Storage Gen1 o Azure Data Lake Storage Gen2 como el sistema de archivos predeterminado, con algunas excepciones. Para más información sobre la compatibilidad con el uso de Data Lake Storage Gen1 como almacenamiento predeterminado y como almacenamiento vinculado, consulte [Disponibilidad de los clústeres de HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters).

En este artículo, aprenderá cómo funciona Azure Storage con clústeres de HDInsight. 
* Para más información sobre cómo funciona Data Lake Storage Gen1 con clústeres de HDInsight, consulte el artículo [Uso de Azure Data Lake Storage Gen1 con clústeres de Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen1.md).
* Para más información sobre cómo funciona Data Lake Storage Gen2 con clústeres de HDInsight, consulte el artículo [Uso de Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight](./hdinsight-hadoop-use-data-lake-storage-gen2.md).
* Consulte [Creación de clústeres de Apache Hadoop en HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) para obtener información sobre la creación de un clúster de HDInsight.

> [!IMPORTANT]  
> El tipo de cuenta de almacenamiento **BlobStorage** solo puede usarse como almacenamiento secundario para los clústeres de HDInsight.

| Tipo de cuenta de almacenamiento | Servicios admitidos | Niveles de rendimiento admitidos |Sin niveles de rendimiento admitidos| Niveles de acceso admitidos |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (uso general v2)  | Blob     | Estándar                    |Premium| Frecuente, esporádico, archivo\*   |
| Storage (uso general v1)   | Blob     | Estándar                    |Premium| N/D                    |
| BlobStorage                    | Blob     | Estándar                    |Premium| Frecuente, esporádico, archivo\*   |

No se recomienda usar el contenedor de blobs predeterminado para almacenar datos empresariales. Conviene eliminar el contenedor de blobs predeterminado después de cada uso para reducir los costos de almacenamiento. El contenedor predeterminado contiene los registros del sistema y de la aplicación. Asegúrese de recuperar los registros antes de eliminar el contenedor.

No se permite compartir un contenedor de blobs como sistema de archivos predeterminado entre varios clústeres.

> [!NOTE]  
> El nivel de acceso de archivo es un nivel sin conexión que tiene una latencia de recuperación de varias horas y no se recomienda para su uso con HDInsight. Para más información, consulte [Nivel de acceso de archivo](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Acceso a los archivos desde dentro del clúster

Existen varias maneras de acceder a los archivos de Data Lake Storage desde un clúster de HDInsight. El esquema URI proporciona acceso sin cifrar (con el prefijo *wasb:* ) y acceso cifrado con TLS (con *wasbs*). Se recomienda usar *WASBS* siempre que sea posible, aunque se acceda a datos que se encuentren en la misma región de Azure.

* **Con el nombre completo**. Con este enfoque, proporciona la ruta de acceso completa al archivo al que quiere acceder.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Con el formato abreviado de la ruta de acceso**. Con este enfoque, reemplazará la ruta de acceso a la raíz del clúster por:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Con la ruta de acceso relativa**. Con este enfoque, solo proporciona la ruta de acceso relativa al archivo al que quiere acceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Ejemplos de acceso a datos

Los ejemplos se basan en una [conexión SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo principal del clúster. En los ejemplos se usan los tres esquemas de URI. Reemplace `CONTAINERNAME` y `STORAGEACCOUNT` por los valores pertinentes.

#### <a name="a-few-hdfs-commands"></a>Algunos comandos HDFS

1. Crear un archivo en el almacenamiento local.

    ```bash
    touch testFile.txt
    ```

1. Crear directorios en el almacenamiento de clúster.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiar datos del almacenamiento local al almacenamiento de clúster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Mostrar el contenido del directorio en el almacenamiento de clúster.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Al trabajar con blobs fuera de HDInsight, la mayoría de las utilidades no reconocen el formato WASB y, en su lugar, espera un formato básico de ruta de acceso, como `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Creación de una tabla de Hive

A efectos ilustrativos, se muestran tres ubicaciones de archivos. En la ejecución real, use solo una de las entradas `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Acceso a los archivos desde fuera del clúster

Microsoft ofrece las siguientes herramientas para trabajar con Azure Storage:

| Herramienta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI de Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identificación de la ruta de acceso de almacenamiento de Ambari

* Para identificar la ruta de acceso completa al almacén predeterminado configurado, vaya a:

    **HDFS** > **Configs** (Configuraciones) y escriba `fs.defaultFS` en el cuadro de entrada de filtro.

* Para comprobar si el almacén WASB está configurado como almacenamiento secundario, vaya a:

    **HDFS** > **Configs** (Configuraciones) y escriba `blob.core.windows.net` en el cuadro de entrada de filtro.

Para obtener la ruta de acceso mediante la API REST de Ambari, consulte [Obtención del almacenamiento predeterminado](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Contenedores de blobs

Para usar blobs, debe crear antes una [cuenta de Azure Storage](../storage/common/storage-account-create.md). Como parte de este paso, especifique una región de Azure donde se crea la cuenta de almacenamiento. El clúster y la cuenta de almacenamiento deben ubicarse en la misma región. La base de datos de SQL Server de la metastore de Hive y la base de datos de SQL Server de la metastore de Apache Oozie tienen que encontrarse en la misma región.

Cualquiera que sea su ubicación, todos los blobs que cree pertenecerán a un contenedor de su cuenta de Azure Storage. Este contenedor puede ser un blob existente que se creó fuera de HDInsight. O puede tratarse de un contenedor que se cree para un clúster de HDInsight.

El contenedor de blobs predeterminado almacena información específica del clúster, como los registros y el historial de trabajos. No comparta un contenedor de blobs predeterminado con varios clústeres de HDInsight. Esta acción podría dañar el historial de trabajos. Se recomienda usar un contenedor diferente para cada clúster. Coloque los datos compartidos en una cuenta de almacenamiento vinculada especificada para todos los clústeres apropiados, en lugar de en la cuenta de almacenamiento predeterminada. Para más información sobre cómo configurar cuentas de almacenamiento vinculadas, consulte [Creación de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Aunque puede volver a usar un contenedor de almacenamiento predeterminado después de que se haya eliminado el clúster de HDInsight original. En el caso de los clústeres de HBase, para conservar el esquema y los datos de tabla de HBase se puede crear un nuevo clúster de HBase mediante el contenedor de blobs predeterminado que usaba un clúster de HBase eliminado

[!INCLUDE [secure-transfer-enabled-storage-account](includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Uso de cuentas de almacenamiento adicionales

Al crear un clúster de HDInsight, se especifica la cuenta de Azure Storage a la que quiere asociarlo. Además, puede agregar cuentas de almacenamiento adicionales de la misma suscripción de Azure o de diferentes suscripciones de Azure durante el proceso de creación o después de que se haya creado un clúster. Para obtener instrucciones sobre cómo agregar cuentas de almacenamiento adicionales, consulte [Creación de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> No se admite el uso de una cuenta de almacenamiento adicional en una ubicación diferente a la del clúster de HDInsight.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a usar Azure Storage compatible con HDFS con HDInsight. Este almacenamiento le permite crear soluciones de adquisición de datos de archivado adaptables y a largo plazo, y usar HDInsight para desbloquear la información que hay dentro de los datos estructurados y no estructurados que están almacenados.

Para más información, consulte:

* [Inicio rápido: Creación de un clúster de Apache Hadoop](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Tutorial: Creación de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Uso de Data Lake Storage Gen2 con clústeres de Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Carga de datos en HDInsight](hdinsight-upload-data.md)
* [Tutorial: Extracción, transformación y carga de datos mediante Interactive Query en Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Uso de firmas de acceso compartido de Azure Storage para restringir el acceso a datos con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
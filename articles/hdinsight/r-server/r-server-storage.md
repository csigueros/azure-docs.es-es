---
title: 'Soluciones de Azure Storage para ML Services en HDInsight: Azure'
description: Obtenga información sobre las distintas opciones de almacenamiento disponibles con ML Service en HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/02/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 56b7181cafc9a17c2fdb468e1a47d664499dcdcc
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252921"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluciones de Azure Storage para ML Services en Azure HDInsight

[!INCLUDE [retirement banner](../includes/ml-services-retirement.md)]

ML Services en HDInsight puede usar diversas soluciones para almacenar datos, código u objetos que contienen resultados de análisis. Entre estas soluciones se incluyen las siguientes opciones:

- [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Archivos de Azure](https://azure.microsoft.com/services/storage/files/)

También tiene la opción de acceder a varias cuentas o contenedores de Azure Storage con el clúster de HDInsight. El servicio Azure Files ofrece una manera práctica de almacenar datos para su uso en el nodo perimetral y le permite montar un recurso compartido de archivos de Azure Storage en el sistema de archivos Linux, por ejemplo. Pero los recursos compartidos de archivos de Azure se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux.

Cuando se crea un clúster de Apache Hadoop en HDInsight, se especifica una cuenta de **Azure Blob Storage** o **Data Lake Storage Gen1**. Un contenedor de almacenamiento específico de esa cuenta conserva el sistema de archivos para el clúster creado (por ejemplo, el Sistema de archivos distribuido de Hadoop). Para más información e instrucciones, consulte:

- [Uso de Azure Blob Storage con HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Usar Data Lake Storage Gen1 con clústeres de Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Uso de cuentas de Azure Blob Storage con un clúster de ML Services

Si especificó más de una cuenta de almacenamiento al crear el clúster de ML Services, las instrucciones siguientes explican cómo usar una cuenta secundaria para las operaciones y el acceso a datos en el clúster de ML Services. Supongamos las cuentas de almacenamiento y el contenedor siguientes: **storage1** y un contenedor predeterminado denominado **container1**, y  **storage2** con **container2**.

> [!WARNING]  
> Con vistas al rendimiento, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento principal especificada. No se admite el uso de una cuenta de almacenamiento en una ubicación diferente a la del clúster de HDInsight.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Uso del almacenamiento predeterminado con ML Services en HDInsight

1. Use un cliente SSH para conectarse al nodo perimetral del clúster. Para obtener información sobre cómo utilizar SSH con clústeres de HDInsight, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copie un archivo de muestra, mysamplefile.csv, en el directorio /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Cambie a R Studio u otra consola de R y escriba código R para establecer el nodo de nombre en **default** y la ubicación del archivo al que quiere acceder.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Todas las referencias de archivos y directorios apuntan a la cuenta de almacenamiento `wasbs://container1@storage1.blob.core.windows.net`. Esta es la **cuenta de almacenamiento predeterminada** asociada al clúster de HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Uso de almacenamiento adicional con ML Services en HDInsight

Ahora supongamos que quiere procesar un archivo llamado mysamplefile1.csv que se encuentra en el directorio /private de **container2** en **storage2**.

En el código R, apunte la referencia del nodo de nombres a la cuenta de almacenamiento **storage2** .

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Todas las referencias de archivos y directorios apuntan ahora a la cuenta de almacenamiento `wasbs://container2@storage2.blob.core.windows.net`. Este es el **nodo de nombres** que ha especificado.

Configure el directorio `/user/RevoShare/<SSH username>` en **storage2** de la forma siguiente:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Uso de Azure Data Lake Storage Gen1 con el clúster de ML Services

Para utilizar Data Lake Storage Gen1 con el clúster de HDInsight, tiene que dar al clúster acceso a cada instancia de Azure Data Lake Storage Gen1 que quiera usar. Para obtener instrucciones sobre cómo usar Azure Portal para crear un clúster de HDInsight con una cuenta de Azure Data Lake Storage Gen1 como almacenamiento predeterminado o adicional, consulte [Crear clústeres de HDInsight con Data Lake Storage Gen1 mediante Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

El uso del almacenamiento en el script de R es muy similar al uso de una cuenta de almacenamiento secundaria de Azure (tal como se describe en el procedimiento anterior).

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Incorporación de acceso de clúster a instancias de Azure Data Lake Storage Gen1

El acceso a una instancia de Data Lake Storage Gen1 mediante el uso de una entidad de servicio de Azure Active Directory (Azure AD) asociada a su clúster de HDInsight.

1. Al crear el clúster de HDInsight, seleccione **Identidad de AAD del clúster** en la pestaña **Origen de datos**.

2. En el cuadro de diálogo **Identidad de AAD del clúster**, en **Seleccionar entidad de servicio de AD**, elija **Crear nuevo**.

Después de asignar un nombre a la entidad de servicio y crear una contraseña para ella, haga clic en **Administrar acceso a ADLS** para asociar la entidad de servicio a Data Lake Storage.

También es posible agregar acceso al clúster a una o varias cuentas de Data Lake Storage Gen1 después de la creación del clúster. Abra la entrada de Azure Portal para una instancia de Data Lake Storage Gen1 y vaya a **Explorador de datos > Acceso > Agregar**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Acceso a Data Lake Storage Gen1 desde el clúster ML Services de HDInsight

Una vez que tenga acceso a Data Lake Storage Gen1, puede usar el almacenamiento del clúster ML Services de HDInsight tal y como lo haría con una cuenta de almacenamiento de Azure secundaria. La única diferencia es que el prefijo **wasbs://** cambia a **adl://** , de la forma siguiente:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Los siguientes comandos se usan para configurar Data Lake Storage Gen1 con el directorio RevoShare y agregar el archivo .csv del ejemplo anterior:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-files-with-ml-services-on-hdinsight"></a>Uso de Azure Files con ML Services en HDInsight

También hay una opción de almacenamiento de datos adecuada para su uso en el nodo perimetral llamada [Azure Files](https://azure.microsoft.com/services/storage/files/). Con esta opción podrá montar un recurso compartido de archivos de Azure Storage en el sistema de archivos de Linux. Esta opción puede resultar práctica para almacenar archivos de datos, scripts de R y objetos de resultado que podría necesitar más adelante, en especial cuando sea conveniente usar el sistema de archivos nativo en el nodo perimetral en lugar de HDFS.

Una ventaja importante de Archivos de Azure es que los recursos compartidos de archivos se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. Por ejemplo, puede utilizarse con otro clúster de HDInsight que sea suyo o de alguien de su equipo, con una máquina virtual de Azure, o incluso con un sistema local. Para más información, consulte:

- [Uso de Azure Files con Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Uso de Azure Files en Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Pasos siguientes

- [Información general de clústeres de ML Services en HDInsight](r-server-overview.md)
- [Opciones de contexto de proceso para un clúster de ML Services en HDInsight](r-server-compute-contexts.md)
- [Uso de Data Lake Storage Gen2 con clústeres de Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

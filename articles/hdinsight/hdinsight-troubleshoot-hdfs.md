---
title: Solución de problemas de HDFS en Azure HDInsight
description: Obtenga respuestas a las preguntas comunes sobre cómo trabajar con HDFS y Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: ce9ca7d247498d785b98dcef329a4132b7f07ac8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291068"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Solución de problemas de HDFS de Apache Hadoop con Azure HDInsight

Obtenga información sobre los principales problemas y las soluciones al trabajar con el Sistema de archivos distribuido (HDFS) de Hadoop. Para una lista completa de comandos, consulte la [guía de comandos de HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) y la [guía de comandos shell del sistema de archivos](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>¿Cómo se accede a la instancia de HDFS local desde un clúster?

### <a name="issue"></a>Problema

Acceda a la HDFS local desde la línea de comandos y el código de aplicación, en lugar de mediante Azure Blob Storage o Azure Data Lake Storage desde dentro del clúster de HDInsight.

### <a name="resolution-steps"></a>Pasos de resolución

1. En el símbolo del sistema, use `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` literalmente, como en el siguiente comando:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Desde el código fuente, utilice el identificador URI `hdfs://mycluster/` literalmente, como se muestra en la siguiente aplicación de ejemplo:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Ejecute el archivo .jar compilado (por ejemplo, un archivo denominado `java-unit-tests-1.0.jar`) en el clúster de HDInsight con el siguiente comando:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Excepción de almacenamiento para escritura en blob

### <a name="issue"></a>Problema

al usar los comandos `hadoop` o `hdfs dfs` para escribir archivos de aproximadamente 12 GB o mayores en un clúster de HBase, puede encontrar el siguiente error:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Causa

HBase en clústeres de HDInsight toma como valor predeterminado un tamaño de bloque de 256 KB al escribir en Azure Storage. Si bien esto funciona para API de HBase o API de REST, se produce un error al usar las utilidades de línea de comandos `hadoop` o `hdfs dfs`.

### <a name="resolution"></a>Solución

use `fs.azure.write.request.size` para especificar un tamaño de bloque mayor. Puede realizar esta modificación en función de cada uso mediante el parámetro `-D`. El siguiente comando es un ejemplo de uso de este parámetro con el comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

También puede aumentar el valor de `fs.azure.write.request.size` globalmente mediante Apache Ambari. Los siguientes pasos se pueden usar para cambiar el valor en la IU web de Ambari:

1. En el explorador, vaya a la IU web de Ambari para el clúster. La dirección URL es `https://CLUSTERNAME.azurehdinsight.net`, donde `CLUSTERNAME` es el nombre del clúster. Cuando se le solicite, escriba el nombre de usuario y la contraseña de administrador para el clúster.
2. En el lado izquierdo de la pantalla, seleccione **HDFS** y luego seleccione la pestaña **Configs** (Configuraciones).
3. En el campo **Filter...** (Filtro...), escriba `fs.azure.write.request.size`.
4. Cambie el valor de 262144 (256 KB) al nuevo valor. Por ejemplo, 4194304 (4 MB).

    :::image type="content" source="./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png" alt-text="Imagen de cambiar el valor en la IU web de Ambari" border="false":::

Para más información acerca del uso de Ambari, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

El comando [`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) muestra el tamaño de los archivos y los directorios que contiene el directorio especificado o la longitud de un archivo, si solo fuera uno.

La opción `-s` genera un resumen agregado de las longitudes de los archivos que se muestran.  
La opción `-h` da formato al tamaño de los archivos.

Ejemplo:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

El comando [-rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) elimina los archivos especificados como argumentos.

Ejemplo:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]
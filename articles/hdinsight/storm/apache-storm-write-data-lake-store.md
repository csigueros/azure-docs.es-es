---
title: 'Tutorial: Apache Storm de HDInsight en Storage (Azure o Data Lake)'
description: 'Tutorial: Obtenga más información sobre cómo usar Apache Storm para escribir datos en almacenamiento compatible con HDFS para Azure HDInsight.'
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 48721efe03bde54f2ee766b1c5dae88e6bd5c4d7
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288687"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Escritura en HDFS de Apache Hadoop desde Apache Storm en Azure HDInsight

En este tutorial se muestra cómo usar Apache Storm para escribir datos en el almacenamiento compatible con HDFS que usa Apache Storm en HDInsight. HDInsight puede usar tanto Azure Storage como Azure Data Lake Storage como almacenamiento compatible con HDFS. Storm proporciona un componente [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que permite escribir datos en HDFS. En este documento se proporciona información sobre cómo escribir datos en ambos tipos de almacenamiento desde HdfsBolt.

La topología de ejemplo que se usa en este documento depende de componentes que se incluyen con Storm en HDInsight. Puede que sea necesario realizar alguna modificación para que funcione con Azure Data Lake Storage al usarlo con otros clústeres de Apache Storm.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar el clúster con la acción de script
> * Compilación y empaquetado de la topología
> * Implementación y ejecución de la topología
> * Visualización de datos de salida
> * Detención de la topología

## <a name="prerequisites"></a>Prerrequisitos

* [Kit de desarrolladores de Java (JDK), versión 8](/azure/developer/java/fundamentals/java-support-on-azure)

* [Apache Maven](https://maven.apache.org/download.cgi) correctamente [instalado](https://maven.apache.org/install.html) según Apache.  Maven es un sistema de compilación de proyectos de Java.

* Un cliente SSH. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

* El [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para el almacenamiento principal de clústeres. Sería `wasb://` para Azure Storage, `abfs://` para Azure Data Lake Storage Gen2 o `adl://` para Azure Data Lake Storage Gen1. Si se habilita la transferencia segura para Azure Storage, el identificador URI sería `wasbs://`.  Consulte también el artículo acerca de la [transferencia segura](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Ejemplo de configuración

El siguiente YAML es un fragmento del archivo `resources/writetohdfs.yaml` incluido en el ejemplo. Este archivo define la topología de Storm mediante el marco [Flux](https://storm.apache.org/releases/current/flux.html) para Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

En este YAML se definen los siguientes elementos:

* `syncPolicy`: define cuándo se sincronizan o vacían los archivos en el sistema de archivos. En este ejemplo, cada 1000 tuplas.
* `fileNameFormat`: define la ruta de acceso y el patrón del nombre de archivo que se usarán al escribir archivos. En este ejemplo, la ruta de acceso se proporciona durante el tiempo de ejecución con un filtro, y la extensión de archivo es `.txt`.
* `recordFormat`: define el formato interno de los archivos escritos. En este ejemplo, los campos están delimitados por el carácter `|`.
* `rotationPolicy`: define cuándo se giran los archivos. En este ejemplo no se realiza ningún giro.
* `hdfs-bolt`: usa los componentes anteriores como parámetros de configuración para la clase `HdfsBolt`.

Para obtener más información acerca del marco Flux, consulte [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Configuración del clúster

De manera predeterminada, Storm en HDInsight no incluye los componentes que usa `HdfsBolt` para la comunicación con Azure Storage o Data Lake Storage en el parámetro classpath de Storm. Use la siguiente acción de script para agregar dichos componentes al directorio `extlib` de Storm en su clúster:

| Propiedad | Value |
|---|---|
|Tipo de script |- Personalizado|
|URI de script de Bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Tipos de nodo |Nimbus, Supervisor|
|Parámetros |None|

Para obtener más información sobre cómo usar este script con su clúster, consulte el documento [Personalización de clústeres de HDInsight mediante la acción de scripts](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Compilación y empaquetado de la topología

1. Descargue el proyecto de ejemplo de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) a su entorno de desarrollo.

2. Desde un símbolo del sistema, un terminal o una sesión de shell, cambie los directorios a la raíz del proyecto descargado. Para compilar y empaquetar la topología, use el siguiente comando:

    ```cmd
    mvn compile package
    ```

    Al finalizar la compilación y el empaquetado, habrá un nuevo directorio denominado `target` con el archivo `StormToHdfs-1.0-SNAPSHOT.jar`. Este archivo contiene la topología compilada.

## <a name="deploy-and-run-the-topology"></a>Implementación y ejecución de la topología

1. Use el siguiente comando para copiar la topología en el clúster de HDInsight. Reemplace `CLUSTERNAME` por el nombre del clúster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Una vez finalizada la carga, use lo siguiente para conectarse al clúster de HDInsight con SSH. Reemplace `CLUSTERNAME` por el nombre del clúster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Una vez conectado, use el comando siguiente para crear un archivo denominado `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Use el texto siguiente como contenido del archivo `dev.properties`. Revise según sea necesario en función de su [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Para guardar el archivo, use __Ctrl+X__, después __Y__ y, finalmente, presione __Intro__. Los valores de este archivo establecen la dirección URL de almacenamiento y el nombre del directorio en el que se escriben los datos.

1. Use el comando siguiente para iniciar la topología:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Este comando inicia la topología con el marco de Flux enviándolo al nodo Nimbus del clúster. La topología la define el archivo `writetohdfs.yaml` incluido en el jar. El archivo `dev.properties` se pasa como un filtro y la topología lee los valores contenidos en el archivo.

## <a name="view-output-data"></a>Visualización de datos de salida

Para ver los datos, use el siguiente comando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Se muestra una lista de los archivos que ha creado la topología. La lista siguiente es un ejemplo de los datos devueltos por los comandos anteriores:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Detención de la topología

Las topologías de Storm se ejecutarán hasta que se detengan o se elimine el clúster. Para detener la topología, use el siguiente comando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos creados por este tutorial puede eliminar el grupo de recursos. Al eliminar el grupo de recursos, también se elimina el clúster de HDInsight asociado y otros recursos asociados al grupo.

Para quitar el grupo de recursos mediante Azure Portal:

1. En Azure Portal, expanda el menú en el lado izquierdo para abrir el menú de servicios y elija __Grupos de recursos__ para mostrar la lista de sus grupos de recursos.
2. Busque el grupo de recursos que desea eliminar y haga clic con el botón derecho en __Más__ (...) en el lado derecho de la lista.
3. Seleccione __Eliminar grupo de recursos__ y confirme la elección.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a usar Apache Storm para escribir datos en el almacenamiento compatible con HDFS que usa Apache Storm en HDInsight.

> [!div class="nextstepaction"]
> Vea otros [ejemplos de Apache Storm para HDInsight](apache-storm-example-topology.md)
---
title: MapReduce y conexión de SSH con Apache Hadoop en Azure HDInsight
description: Obtenga más información sobre cómo usar SSH para ejecutar trabajos de MapReduce mediante Apache Hadoop en HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 4a76ff82de55e0f0249ce040b9df2332f32e4461
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112286874"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Uso de MapReduce con Apache Hadoop en HDInsight con SSH

[!INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Aprenda a enviar trabajos de MapReduce desde una conexión Secure Shell (SSH) a HDInsight.

> [!NOTE]
> Si ya está familiarizado con el uso de servidores de Apache Hadoop basados en Linux, pero no conoce HDInsight, consulte [Información sobre el uso de HDInsight en Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Requisitos previos

Un clúster de Apache Hadoop en HDInsight. Consulte [Creación de clústeres de Apache Hadoop mediante Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Uso de comandos de Hadoop

1. Use el [comando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) para conectarse al clúster. Modifique el comando siguiente: reemplace CLUSTERNAME por el nombre del clúster y, luego, escriba el comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Después de conectarse al clúster de HDInsight, use el siguiente comando para iniciar un trabajo de MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Este comando inicia la clase `wordcount`, que está contenido en el archivo `hadoop-mapreduce-examples.jar`. Emplea como entrada el documento `/example/data/gutenberg/davinci.txt` y la salida se almacena en `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para obtener más información sobre este trabajo de MapReduce y los datos de ejemplo, vea [Uso de MapReduce en Apache Hadoop en HDInsight](hdinsight-use-mapreduce.md).

    El trabajo emite detalles a medida que se procesa y devuelve información similar al siguiente texto cuando finaliza el trabajo:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Una vez completado el trabajo, use el siguiente comando para enumerar los archivos de salida:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Este comando muestra dos archivos, `_SUCCESS` y `part-r-00000`. El archivo `part-r-00000` contiene la salida de este trabajo.

    > [!NOTE]  
    > Algunos trabajos de MapReduce pueden dividir los resultados entre varios archivos **part-r-####** . Si es así, utilice el sufijo #### para indicar el orden de los archivos.

1. Para ver la salida, use el comando siguiente:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Este comando muestra una lista de las palabras contenidas en el archivo **wasb://example/data/gutenberg/davinci.txt**, junto con el número de veces que aparecía cada palabra. El texto siguiente es un ejemplo de los datos contenidos en el archivo:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Pasos siguientes

Como se puede ver, los comando Hadoop proporcionan una manera fácil de ejecutar trabajos de MapReduce en un clúster de HDInsight y, a continuación, ver la salida del trabajo. Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de MapReduce en Hadoop de HDInsight](hdinsight-use-mapreduce.md)
* [Uso de Apache Hive con Apache Hadoop en HDInsight](hdinsight-use-hive.md)

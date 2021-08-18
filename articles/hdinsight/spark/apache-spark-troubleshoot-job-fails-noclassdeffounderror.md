---
title: 'NoClassDefFoundError: Apache Spark con datos de Apache Kafka en Azure HDInsight'
description: El trabajo de streaming de Apache Spark que lee datos de un clúster de Apache Kafka devuelve un error con un elemento NoClassDefFoundError en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: fc4eb5aae7da3d036d6fb319c5eb13e41793b4cf
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282284"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>El trabajo de streaming de Apache Spark que lee datos de Apache Kafka devuelve un error con un elemento NoClassDefFoundError en HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

El clúster de Apache Spark ejecuta un trabajo de streaming de Spark que lee los datos de un clúster de Apache Kafka. Se produce un error en el trabajo de streaming de Spark si está activada la compresión de secuencias de Kafka. En este caso, la aplicación Yarn de streaming de Spark application_1525986016285_0193 dejó de funcionar debido a un error:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Causa

Este error puede producirse cuando se especifica una versión del archivo JAR `spark-streaming-kafka` que es diferente a la versión del clúster de Kafka que está ejecutando.

Por ejemplo, si está ejecutando un clúster de Kafka versión 0.10.1, el siguiente comando generará un error:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Resolución

Use el comando Spark-submit con la opción `–packages`, y asegúrese de que la versión del archivo JAR spark-streaming-kafka sea la misma que la versión del clúster de Kafka que está ejecutando.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
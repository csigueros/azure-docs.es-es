---
title: 'Conexiones de Apache Hive con Apache Zookeeper: Azure HDInsight'
description: No se puede acceder a la vista de Apache Hive debido a problemas de Apache Zookeeper en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: cc7ad8d32e4d15b8e0671d162b1796619b4879c7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290726"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Escenario: Apache Hive no puede establecer una conexión con Apache Zookeeper en Azure HDInsight.

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

No se puede tener acceso a la vista de Hive y los registros de `/var/log/hive` muestran un error similar al siguiente:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (<zookeepername1>.cloud.wbmi.com:2181,<zookeepername2>.cloud.wbmi.com:2181,<zookeepername3>.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Causa

Es posible que Hive no pueda establecer una conexión con Zookeeper, lo que impide que se inicie la vista de Hive.

## <a name="resolution"></a>Solución

1. Compruebe que el servicio Zookeeper está en buen estado.

1. Compruebe si el servicio Zookeeper tiene una entrada ZNode para Hive Server2. Falta el valor o no es correcto.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server <zookeepername1>
    [zk: <zookeepername1>(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Para volver a establecer la conectividad, reinicie los nodos de Zookeeper y reinicie HiveServer2.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
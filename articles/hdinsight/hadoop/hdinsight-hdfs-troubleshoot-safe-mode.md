---
title: La instancia de HDFS local está detenida en modo seguro en el clúster de Azure HDInsight
description: Solución de problemas de Apache HDFS local bloqueado en modo seguro en un clúster de Apache en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 6764b2c1b8b2a06f2bd9264a7461f43986f557ad
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299976"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Escenario: La instancia de HDFS local está detenida en modo seguro en el clúster de Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

El Sistema de archivos distribuido Apache Hadoop (HDFS) local está bloqueado en el modo seguro en el clúster de HDInsight. Recibirá un mensaje de error similar al siguiente:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Causa

El clúster de HDInsight se ha reducido verticalmente a muy pocos nodos por debajo o el número de nodos está cerca del factor de replicación de HDFS.

## <a name="resolution"></a>Solución

1. Notifique el estado de HDFS en el clúster de HDInsight con el siguiente comando:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Compruebe la integridad de HDFS en el clúster de HDInsight con el siguiente comando:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Si no falta ningún bloque, no hay bloques dañados o subreplicados, o esos bloques se pueden ignorar, ejecute el comando siguiente para quitar el nodo de nombre del modo seguro:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
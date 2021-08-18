---
title: 'RpcTimeoutException para Thrift de Apache Spark: Azure HDInsight'
description: Al procesar conjuntos de datos grandes mediante el servidor Thrift de Apache Spark verá 502 errores.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 353301b55031e1dde181cac61f47edd68cc33131
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282194"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Escenario: RpcTimeoutException para el servidor Thrift de Apache Spark en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Incidencia

Se produce un error `org.apache.spark.rpc.RpcTimeoutException` en la aplicación Spark con el mensaje `Futures timed out`, como en el ejemplo siguiente:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

Los errores `OutOfMemoryError` y `overhead limit exceeded` también pueden aparecer en `sparkthriftdriver.log`, como en el ejemplo siguiente:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Estos errores se deben a una falta de recursos de memoria durante el procesamiento de datos. Si se inicia el proceso de recolección de elementos no utilizados de Java, la aplicación Spark podría dejar de responder. Las consultas comenzarán a agotar el tiempo de espera y el procesamiento se detendrá. El error `Futures timed out` indica que un clúster está sometido a una sobrecarga severa.

## <a name="resolution"></a>Resolución

Aumente el tamaño del clúster; para hacerlo, agregue más nodos de trabajo o incremente la capacidad de memoria en los nodos de clúster existentes. También puede ajustar la canalización de datos para reducir la cantidad de datos que se procesan de manera simultánea.

`spark.network.timeout` controla el tiempo de espera de todas las conexiones de red. El aumento del tiempo de espera de la red puede conceder más tiempo para que se completen algunas operaciones críticas, pero no resolverá el problema por completo.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
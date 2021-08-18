---
title: 'Agotamiento del tiempo de espera de la vista de Apache Hive desde el resultado de la consulta: Azure HDInsight'
description: Se agota el tiempo de espera de la vista de Apache Hive al capturar el resultado de una consulta en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: a43109a59353fd09ea2f29add07457d324768b16
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290528"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Escenario: se agota el tiempo de espera de la vista de Apache Hive al capturar el resultado de una consulta en Azure HDInsight

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Al ejecutar determinadas consultas desde la vista de Apache Hive, puede aparecer el siguiente error:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Causa

Es posible que el valor de tiempo de espera predeterminado de la vista de Hive no sea adecuado para la consulta que se está ejecutando. El período de tiempo especificado es demasiado corto para que la vista de Hive capture el resultado de la consulta.

## <a name="resolution"></a>Solución

Aumente los tiempos de espera de la vista de Apache Ambari Hive estableciendo las siguientes propiedades en `/etc/ambari-server/conf/ambari.properties`.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

El valor de `HIVE_VIEW_INSTANCE_NAME` está disponible al final de la dirección URL de la vista de Hive.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
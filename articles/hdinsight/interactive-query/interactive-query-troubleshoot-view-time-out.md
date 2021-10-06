---
title: 'Agotamiento del tiempo de espera de la vista de Apache Hive desde el resultado de la consulta: Azure HDInsight'
description: Se agota el tiempo de espera de la vista de Apache Hive al capturar el resultado de una consulta en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 5b1ec7fec182d5b0b6f2d68467d6e3e84fdb5f3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601413"
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

1. Para aumentar los tiempos de espera de la vista de Hive de Apache Ambari, establezca las propiedades de `/etc/ambari-server/conf/ambari.properties` para los **dos nodos principales**.
  ```
  views.ambari.request.read.timeout.millis=300000
  views.request.read.timeout.millis=300000
  views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
  ```
  El valor de `HIVE_VIEW_INSTANCE_NAME` está disponible al final de la dirección URL de la vista de Hive.

2. Ejecute lo siguiente para reiniciar el servidor de Ambari activo. Si recibe un mensaje de error en el que se indica que no es el servidor de Ambari activo, simplemente ejecute ssh en el siguiente nodo principal y repita este paso.
  ```
  sudo ambari-server restart
  ```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]

---
title: Rendimiento deficiente en las consultas Apache Hive LLAP en Azure HDInsight
description: Las consultas de Apache Hive LLAP se ejecutan más lentamente de lo esperado en Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: e21b49d26e8b7570b7f70e30f04b44fd23341f03
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290618"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Escenario: rendimiento deficiente en las consultas LLAP de Apache Hive en Azure HDInsight

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Las configuraciones de clúster predeterminadas no están suficientemente ajustadas para la carga de trabajo. Las consultas de Hive LLAP se ejecutan más lentamente de lo esperado.

## <a name="cause"></a>Causa

Se puede deber a varios motivos.

## <a name="resolution"></a>Solución

LLAP está optimizado para consultas que implican combinaciones y agregados. Las consultas como las siguientes no funcionan bien en un clúster de Hive interactivo:

```
select * from table where column = "columnvalue"
```

Para mejorar el rendimiento de las consultas puntuales en Hive LLAP, establezca las siguientes configuraciones:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

También puede aumentar el uso de la caché de LLAP para mejorar el rendimiento con el siguiente cambio de configuración:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
---
title: Lentitud del reductor en Azure HDInsight
description: Lentitud del reductor en Azure HDInsight debido a una posible asimetría de datos
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 951dd8b4bc9ee68c88e6bae481482e041ae89054
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290600"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Escenario: lentitud del reductor en Azure HDInsight

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Al ejecutar una consulta como `insert into table1 partition(a,b) select a,b,c from table2`, el plan de consulta inicia un grupo de reductores, pero los datos de cada partición van a un único reductor. Esto hace que la consulta sea tan lenta como el tiempo que tarda el reductor de la partición más grande.

## <a name="cause"></a>Causa

Abra [Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) y compruebe el valor del elemento `hive.optimize.sort.dynamic.partition` establecido.

El valor de esta variable está pensado para establecerse en "true" o "false" en función de la naturaleza de los datos.

Si hay menos particiones de la tabla de entrada (por ejemplo, menos de 10), menos particiones de salida y la variable se establece en `true`, los datos se ordenarán y escribirán globalmente con un solo reductor por partición. Aunque el número de reductores disponibles sea mayor, es posible que algunos estén atrasados debido a la asimetría de datos y no sea posible alcanzar el paralelismo máximo. Al cambiar a `false`, más de un reductor puede controlar una sola partición; se escriben varios archivos más pequeños, lo que da lugar a una inserción más rápida. Esto puede afectar a otras consultas debido a la presencia de archivos más pequeños.

Un valor de `true` tiene sentido cuando el número de particiones es mayor y los datos no están sesgados. En tales casos, el resultado de la fase de asignación se escribirá de forma que cada una de las particiones se controle mediante un único reductor, lo que dará como resultado un mejor rendimiento de las consultas.

## <a name="resolution"></a>Solución

1. Intente volver a particionar los datos para normalizarlos en varias particiones.

1. Si no es posible aplicar la primera solución, establezca el valor de la configuración en "false" en la sesión de Beeline y vuelva a intentar la consulta. `set hive.optimize.sort.dynamic.partition=false`. No se recomienda establecer el valor en "false" en un nivel de clúster. El valor de `true` es óptimo y establece el parámetro según sea necesario en función de la naturaleza de los datos y de la consulta.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
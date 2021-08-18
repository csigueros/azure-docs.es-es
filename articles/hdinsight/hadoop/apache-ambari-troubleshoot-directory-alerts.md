---
title: Alertas de directorio de Apache Ambari en Azure HDInsight
description: Debate y análisis de posibles causas y soluciones para las alertas de directorio de Apache Ambari en HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 2e5e74c49b7654c2ade20e78f7098192649f9385
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300136"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Escenario: alertas de directorio de Apache Ambari en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Recibe errores de Apache Ambari que son similares a:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Causa

Faltan los directorios mencionados de la alerta de Ambari en los nodos de trabajo afectados.

## <a name="resolution"></a>Solución

Cree manualmente los directorios que faltan en los nodos de trabajo afectados.

1. SSH en el nodo de trabajo pertinente.

1. Obtenga el usuario raíz: `sudo su`.

1. Cree de forma recursiva los directorios necesarios.

1. Cambie el propietario y el grupo de estos directorios.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. En la interfaz de usuario de Apache Ambari, deshabilite la alerta y, a continuación, habilítela.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
---
title: El nodo de clúster se queda sin espacio en disco en Azure HDInsight.
description: Solución de problemas de espacio en disco del nodo de clúster de Apache Hadoop en Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: 76810596eb8ec3f9880c04036149a068e9acb45f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299778"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Escenario: El nodo de clúster se queda sin espacio en disco en Azure HDInsight.

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Un trabajo puede producir un error con un mensaje similar al siguiente: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

O bien, se puede recibir una alerta de Apache Ambari similar a la siguiente: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Causa

La memoria caché de las aplicaciones de Apache Yarn puede haber consumido todo el espacio disponible en disco. Es probable que la aplicación Spark se ejecute de forma ineficaz.

## <a name="resolution"></a>Solución

1. Utilice la interfaz de usuario de Ambari para determinar qué nodo se está quedando sin espacio en disco.

1. Determine qué carpeta del nodo en cuestión contribuye a la mayor parte del espacio en disco. Primero realice un SSH en el nodo y, a continuación, ejecute `df` para mostrar el uso de disco en todos los montajes. Normalmente es `/mnt`, que es un disco temporal que usa OSS. Puede introducirlo en una carpeta y, después, escribir `sudo du -hs` para mostrar los tamaños de archivo resumidos en una carpeta. Si ve una carpeta similar a `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`, significa que la aplicación todavía se está ejecutando. Esto puede deberse a la persistencia de RDD o a los archivos intermedios aleatorios.

1. Para mitigar el problema, elimine la aplicación; de esta forma, liberará el espacio en disco utilizado por ella.

1. Si el problema se produce con frecuencia en los nodos de trabajo, puede ajustar la configuración de la caché local de YARN en el clúster.

    Abra la interfaz de usuario de Ambari y vaya a YARN --> Configuraciones --> Opciones avanzadas.  
    Agregue las dos propiedades siguientes a la sección yarn-site.xml personalizada y guarde:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Si lo anterior no soluciona el problema definitivamente, optimice la aplicación.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
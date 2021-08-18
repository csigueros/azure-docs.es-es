---
title: 'CPU fija en el clúster de Apache HBase: Azure HDInsight'
description: Solución de problemas de CPU fija en el servidor de regiones del clúster de Apache HBase en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 58eea6b5318b2d7cc71c30b1db7119442ca6a9fc
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291338"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Escenario: CPU fija en el servidor de regiones del clúster de Apache HBase en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

El proceso del servidor de regiones de Apache HBase comienza a ocupar cerca del 200 % de la CPU, lo que provoca que las alertas se activen en el proceso de HBase Master y que el clúster no funcione al máximo de su capacidad.

## <a name="cause"></a>Causa

Si ejecuta un clúster de HBase versión 3.4, es posible que se haya encontrado con un posible error causado por la actualización de JDK a la versión 1.7.0_151. El síntoma visible es que el proceso del servidor de regiones comienza a ocupar cerca del 200 % de CPU (para comprobar esta situación ejecute el comando `top`; si hay un proceso que ocupa cerca del 200 % de CPU, obtenga su PID y confirme que es el proceso del servidor de regiones mediante `ps -aux | grep`).

## <a name="resolution"></a>Solución

1. Instale JDK 1.8 en todos los nodos del clúster como se indica a continuación:

    * Ejecute la acción de script `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`. Asegúrese de seleccionar la opción para ejecutar en todos los nodos.

    * También puede iniciar sesión en cada nodo individual y ejecutar el comando `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`.

1. Vaya a la interfaz de usuario de Ambari: `https://<clusterdnsname>.azurehdinsight.net`.

1. Vaya a **HBase->Configs->Advanced->Advanced** `hbase-env configs` (HBase-> Configuración -> Avanzada -> Avanzada) y cambie la variable `JAVA_HOME` a `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Guarde el cambio de configuración.

1. [Acción opcional pero recomendada] [Vacíe todas las tablas del clúster](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. En la interfaz de usuario de Ambari, reinicie todos los servicios de HBase que necesiten reiniciarse.

1. En función de los datos del clúster, el clúster puede tardar unos minutos o hasta una hora en alcanzar un estado estable. La forma de confirmar que el clúster alcanza el estado estable es comprobar la interfaz de usuario de HMaster (todos los servidores de regiones deben estar activos) desde Ambari (actualice) o, desde el nodo principal, ejecutar el shell de HBase y, a continuación, ejecutar el comando de estado.

Para comprobar que la actualización se realizó correctamente, compruebe que se han iniciado los procesos de HBase correspondientes con la versión de Java adecuada. Por ejemplo, compruebe el servidor de regiones como se muestra a continuación:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
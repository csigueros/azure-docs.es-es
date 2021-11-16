---
title: Problemas del recopilador de métricas de Apache Ambari en Azure HDInsight
description: Solución de problemas del recopilador de métricas de Apache Ambari en Azure HDInsight
ms.service: hdinsight
ms.author: linjzhu
author: jacky-hdi
ms.topic: troubleshooting
ms.date: 11/03/2021
ms.openlocfilehash: 6fbfc4f47cb14966a7d03d9ef378bb03f519183c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063924"
---
# <a name="apache-ambari-metrics-collector-issues-in-azure-hdinsight"></a>Problemas del recopilador de métricas de Apache Ambari en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="scenario-outofmemoryerror-or-unresponsive-apache-ambari-metrics-collector"></a>Escenario: OutOfMemoryError o recopilador de métricas de Apache Ambari sin respuesta

### <a name="issue"></a>Incidencia

* Podría recibir una alerta crítica **"Proceso del recopilador de métricas"** en la interfaz de usuario de Ambari y mostrar un mensaje similar al siguiente.
    `Connection failed: timed out to <headnode fqdn>:6188`
* Es posible que el recopilador de métricas de Ambari se reinicie con frecuencia en el nodo principal
* Es posible que algunas métricas de Apache Ambari no se muestren. Por ejemplo, **NAMENODE** muestra estado **Iniciado** en lugar de **Activo/En espera** 


### <a name="cause"></a>Causa

Los siguientes escenarios son posibles causas de estos problemas:

#### <a name="an-out-of-memory-exception-happens-frequently"></a>Excepción de falta de memoria que se produce con frecuencia

Comprobación del registro del recopilador de métricas de Apache Ambari `/var/log/ambari-metrics-collector/ambari-metrics-collector.log*`.

```
19:59:45,457 ERROR [325874797@qtp-2095573052-22] log:87 - handle failed
java.lang.OutOfMemoryError: Java heap space

19:59:45,457 FATAL [MdsLoggerSenderThread] YarnUncaughtExceptionHandler:51 - Thread Thread[MdsLoggerSenderThread,5,main] threw an Error.  Shutting down now...
java.lang.OutOfMemoryError: Java heap space
```

#### <a name="busy-garbage-collection"></a>Recolección de elementos no utilizados ocupada

1. El recopilador de métricas de Apache Ambari no escucha en **6188** en el registro de hbase-ams `/var/log/ambari-metrics-collector/hbase-ams-master-hn*.log`

   ```
   2021-04-13 05:57:37,546 INFO  [timeline] timeline.HadoopTimelineMetricsSink: No live collector to send metrics to. Metrics to be sent will be discarded. This message will be skipped for the next 20 times.
   ```
   
2. Obtener el pid del recopilador de métricas de Apache Ambari y comprobar el rendimiento de GC

   ```
   ps -fu ams | grep 'org.apache.ambari.metrics.AMSApplicationServer'
   ```
       
3. Comprobación del estado de la recolección de elementos no utilizados mediante `jstat -gcutil <pid> 1000 100`. Si ve que el **FGCT** aumenta mucho en cuestión de segundos, indica que el recopilador de métricas de Apache Ambari está ocupado con una GC completa y no puede procesar las otras solicitudes.

### <a name="resolution"></a>Solución

Para evitar estos problemas, considere la posibilidad de usar alguna de las siguientes opciones:

1. Aumente la memoria del montón del recopilador de métricas de Apache Ambari desde **Ambari** > **Recopilador de métricas de Ambari** > **Configuración** > **Tamaño del montón del recopilador de métricas**

   :::image type="content" source="./media/apache-ambari-troubleshoot-ams-issues/editing-ams-configuration-ambari.png" alt-text="Captura de pantalla de la edición de las propiedades de configuración del servicio de métricas de Ambari." border="true":::

2. Siga estos pasos para limpiar los datos del servicio de métricas de Ambari (AMS).

   > [!NOTE]
   > Al limpiar los datos de AMS, se quitan todos los datos históricos de AMS disponibles. Si necesita el historial, puede que esta no sea la mejor opción.

   1.  Inicie sesión en el portal de Ambari  
    1.  Establezca AMS en mantenimiento  
    2.  Detenga AMS desde Ambari  
    3.  Identifique lo siguiente en la pantalla **Configs AMS**  
                1.  `hbase.rootdir` (El valor predeterminado es `file:///mnt/data/ambari-metrics-collector/hbase`)  
                2.  `hbase.tmp.dir`(El valor predeterminado es `/var/lib/ambari-metrics-collector/hbase-tmp`)  
   2. SSH en el nodo principal donde existe el recopilador de métricas de Apache Ambari. Como superusuario:
    1. Quite los datos de Zookeeper de AMS; para ello, haga una **copia de seguridad** y quite el contenido de  `'hbase.tmp.dir'/zookeeper`
    2. Quite los archivos de la cola de Phoenix de la carpeta `<hbase.tmp.dir>/phoenix-spool`. 
    3. ***(Merece la pena omitir este paso inicialmente e intentar reiniciar AMS para ver si se resuelve el problema. Si AMS sigue produciendo error, pruebe este paso)***  
            Los datos de AMS se almacenarían en `hbase.rootdir`, tal y como se indicó anteriormente. Use los comandos del sistema operativo habituales para hacer las copias de seguridad y quitar los archivos. Ejemplo:     
            `tar czf /mnt/backupof-ambari-metrics-collector-hbase-$(date +%Y%m%d-%H%M%S).tar.gz /mnt/data/ambari-metrics-collector/hbase`  
   3.  Reinicio de AMS con Ambari.


## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]

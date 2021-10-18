---
title: Un agente no se puede iniciar debido a un disco lleno en Azure HDInsight
description: Pasos de solución de problemas del proceso de un agente de Apache Kafka, que no se puede iniciar debido a un error de disco lleno.
ms.service: hdinsight
ms.topic: troubleshooting
author: Jacky-hdi
ms.author: linjzhu
ms.date: 10/11/2021
ms.openlocfilehash: 3e7d113da1c086c0a95916dd29c9149beb897862
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859597"
---
# <a name="scenario-brokers-are-unhealthy-or-cant-restart-due-to-disk-space-full-issue"></a>Escenario: el estado de los agentes es incorrecto o estos no se pueden reiniciar debido a un problema de espacio del disco, que está lleno

En este artículo se indican los pasos de solución de problemas y las posibles soluciones de los problemas de uso de Apache Kafka en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Los discos de datos usados por los agentes de Apache Kafka en los clústeres de Azure HDInsight pueden llenarse. Cuando sucede esto, el proceso del agente de Apache Kafka no se puede iniciar y se produce un error debido a que el disco está lleno. Si ha realizado cambios recientes en la configuración, no se aplican porque el proceso del agente de Kafka no se inicia.

## <a name="cause"></a>Causa

El problema puede deberse a uno o a varios de los siguientes escenarios:

- No es posible aumentar el número de discos ni su tamaño después de crear el clúster de Kafka.
- Normalmente, las alertas de disco se configuran en la interfaz de usuario de Apache Ambari. Cada vez que el uso del disco aumenta por encima del 60 %, una alerta avisa de que es necesario escalar horizontalmente o reducir el número de registros existentes en el clúster de Kafka.
- Este problema solo se produce si se omiten las alertas de disco. Se puede escalar el clúster horizontalmente para que haya más espacio disponible para responder a las alertas de espacio en el disco.
- Los mensajes no se eliminan inmediatamente, aunque pase el tiempo de retención. Los mensajes que se van a eliminar se marcan para su eliminación. Más adelante, un proceso de limpieza en segundo plano elimina los mensajes. Solo se eliminan los mensajes de los segmentos pasivos.

> [!IMPORTANT]
> Puede usar una configuración para mejorar el rendimiento del limpiador de registros, pero *tenga cuidado* al aplicar esta mejora, ya que podría afectar a la generación o el consumo de mensajes.

## <a name="mitigation"></a>Mitigación

Para mitigar el problema:

1. Compruebe los archivos `server.properties` para buscar el tiempo de retención de cada tema. A veces, la directiva de retención de registros está establecida en el nivel de tema. Para buscar el tiempo de retención configurado en el nivel de tema, ejecute el siguiente comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper <zookeeper-list>
    ```

2. Con esa información, compruebe qué particiones pesadas ocupan el espacio en disco máximo.

    ```bash
    # Command to sort the directories based on size:
    du -hs * | sort -rh | head -5 
    ```

3. Haga una copia de seguridad de los archivos anteriores al nuevo tiempo de retención.

4. Si hay algún espacio disponible, puede reiniciar los agentes para usar la nueva configuración de tiempo de retención. Al reiniciar los agentes, se limpian los registros más antiguos y se libera algún espacio en el disco.

   > [!IMPORTANT]
   > A veces, una copia de seguridad puede no ser una opción, ya que el disco del sistema operativo podría llenarse o sobrecargar otros discos de Kafka. En este escenario, es posible que tenga que eliminar archivos que sean anteriores al tiempo de retención.

## <a name="resolution"></a>Solución

Aunque se puede reducir el tiempo de retención, la configuración no es escalable si se quieren agregar más temas en el clúster o si aumenta la carga o la cantidad de datos que se ingiere.

Para evitar estos escenarios, considere la posibilidad de usar alguna de las siguientes opciones:

- Si las particiones son demasiado grandes, aumente el número de particiones de los temas más pesados.

  > [!NOTE]
  > El aumento de las particiones de un tema existente no reorganiza los datos del tema. En primer lugar, debe copiar manualmente los datos de un tema antiguo de pocas particiones en un nuevo tema de más particiones. 

- Escale horizontalmente el clúster y vuelva a equilibrar todas las particiones entre varios discos.

- Cree un clúster con una SKU más grande y más discos conectados.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]

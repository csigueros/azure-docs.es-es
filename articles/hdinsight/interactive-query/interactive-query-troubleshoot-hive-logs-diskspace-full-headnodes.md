---
title: 'Solución de problemas: los registros de Hive llenan el espacio en disco en Azure HDInsight'
description: En este artículo se proporcionan los pasos para la solución de problemas que se deben seguir cuando los registros de Apache Hive llenan el espacio en disco de los nodos principales de Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: kcheeeung
ms.author: kecheung
ms.date: 05/21/2021
ms.openlocfilehash: f6e2deac6c5f5807618225f4afc208e091e7e004
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578695"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Escenario: Los registros de Apache Hive llenan el espacio en disco de los nodos principales de Azure HDInsight.

En este artículo se describen los pasos para la solución de problemas y las posibles opciones para problemas relacionados con el espacio en disco insuficiente en los nodos principales de los clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

En un clúster de Apache Hive/LLAP HDI 4.0, los registros no deseados ocupan todo el espacio en disco de los nodos principales. Esta condición podría provocar los problemas siguientes:

- No se puede acceder a SSH porque no queda espacio en el nodo principal.
- HiveServer2 Interactive no se puede reiniciar.

## <a name="cause"></a>Causa

La eliminación automática de registros de Hive no está configurada en las opciones avanzadas de hive-log4j2. El límite de tamaño predeterminado de 60 GB ocupa demasiado espacio para el patrón de uso del cliente.

## <a name="resolution"></a>Solución

1. Vaya al resumen de componentes de Hive en el portal de Ambari y seleccione la pestaña **Configs** (Configuraciones).

2. Vaya a la sección `Advanced hive-log4j2` en **Advanced settings** (Configuración avanzada).

3. Asegúrese de que tiene estos valores: Si no ve ninguna configuración relacionada, anexe estos valores:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfFileName
    appender.RFA.strategy.action.condition.regex = hive*.*log.*
    appender.RFA.strategy.action.condition.nested_condition.type = IfAny
    # Deletes logs based on total accumulated size, keeping the most recent
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

4. Recorreremos tres opciones básicas con eliminación basadas en:
- **Tamaño total**
    - Cambie `appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds` por un límite de tamaño de su elección.

- **Fecha**
    - También puede quitar la marca de comentario y cambiar las condiciones. Luego, cambie `appender.RFA.strategy.action.condition.nested_condition.lastMod.age` por la edad que prefiera.

    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

- **Combinación de tamaño total y fecha**
    - Puede combinar ambas opciones quitando la marca de comentario, como aquí. log4j2 se comportará entonces así: comienza a eliminar registros cuando se cumple cualquiera de las condiciones.
    
    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```
5. Guarde las configuraciones y reinicie los componentes necesarios.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]

---
title: Solución de problemas de YARN en Azure HDInsight
description: Obtenga respuestas a las preguntas más comunes sobre cómo trabajar con Apache Hadoop YARN y Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 2b4066251db4f8fd95efa259599f2c4d50783c29
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291050"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Solucione problemas de YARN de Apache Hadoop con Azure HDInsight.

Obtenga información sobre los principales problemas y sus soluciones al trabajar con cargas útiles de Apache Hadoop YARN en Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>¿Cómo se crea una nueva cola de YARN en un clúster?

### <a name="resolution-steps"></a>Pasos de resolución

Para crear una nueva cola de YARN y equilibrar la asignación de capacidad en todas las colas, siga estos pasos en Ambari.

En este ejemplo, se cambia la capacidad de las dos colas existentes (**default** (predeterminado) y **thriftsvr**) del 50% al 25%, lo que proporciona una capacidad del 50% a la nueva cola (spark).

| Cola | Capacity | Capacidad máxima |
| --- | --- | --- |
| default | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| spark | 50 % | 50 % |

1. Seleccione el icono **Ambari Views** (Vistas de Ambari) y, a continuación, seleccione el patrón de cuadrícula. A continuación, seleccione **YARN Queue Manager** (Administrador de colas de YARN).

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png" alt-text="Administrador de colas de YARN del panel de Apache Ambari" border="false":::
2. Seleccione la cola **default**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png" alt-text="YARN de Apache Ambari: seleccionar cola predeterminada" border="false":::
3. Para la cola **default**, cambie la **capacidad** del 50% al 25%. Para la cola **thriftsvr**, cambie la **capacidad** al 25%.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png" alt-text="Cambio de la capacidad al 25 % para las colas default y thriftsvr" border="false":::
4. Seleccione **Add Queue** (Agregar cola) para crear una nueva cola.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png" alt-text="Panel de YARN de Apache Ambari: agregar cola" border="false":::

5. Asigne un nombre a la cola nueva.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png" alt-text="Panel de YARN de Apache Ambari: nombre de la cola" border="false":::  

6. Deje los valores de **Capacity** (Capacidad) en el 50 % y seleccione el botón **Actions** (Acciones).

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png" alt-text="YARN de Apache Ambari: seleccionar acción" border="false":::  
7. Seleccione **Save and Refresh Queues** (Guardar y actualizar colas).

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png" alt-text="Selección de Save and Refresh Queues (Guardar y actualizar colas)" border="false":::  

Estos cambios están visibles inmediatamente en la interfaz de usuario de YARN Scheduler.

### <a name="additional-reading"></a>Lecturas adicionales

- [YARN CapacityScheduler de Apache Hadoop](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>¿Cómo se descargan registros de YARN desde un clúster?

### <a name="resolution-steps"></a>Pasos de resolución

1. Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Para más información, consulte [Lecturas adicionales](#additional-reading-2).

1. Enumere todos los identificadores de aplicación de las aplicaciones Yarn que se están ejecutando con el siguiente comando:

    ```apache
    yarn top
    ```

    Los identificadores aparecen en la columna **APPLICATIONID**. Puede descargar los registros desde la columna **APPLICATIONID**.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Descargue los registros de contenedor de YARN para todos los maestros de aplicación con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Este comando crea un archivo de registro denominado amlogs.txt.

1. Descargue los registros de contenedor de YARN solo para el maestro de aplicación más reciente con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando crea un archivo de registro denominado latestamlogs.txt.

1. Descargue los registros de contenedor de YARN para los dos primeros maestros de aplicación con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Este comando crea un archivo de registro denominado first2amlogs.txt.

1. Descargue todos los registros de contenedor de YARN con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Este comando crea un archivo de registro denominado logs.txt.

1. Descargue el registro de YARN de un contenedor determinado con el siguiente comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Este comando crea un archivo de registro denominado containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Lecturas adicionales

- [Conexión a HDInsight (Apache Hadoop) con SSH](./hdinsight-hadoop-linux-use-ssh-unix.md)
- [Apache Hadoop Yarn concepts and applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow) (Conceptos y aplicaciones de YARN en Apache Hadoop)

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]
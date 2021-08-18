---
title: Solución de problemas de Apache Spark en Azure HDInsight
description: Obtenga respuestas a las preguntas comunes sobre cómo trabajar con Apache Spark y Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 6841a6965646afbf4192c502c0a9eba260531f5e
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369081"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Solución de problemas de Apache Spark mediante Azure HDInsight

Conozca los principales problemas y sus soluciones al trabajar con cargas útiles de Apache Spark en [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante Apache Ambari en clústeres?

Los valores de configuración de Spark se pueden ajustar para ayudar a evitar una excepción `OutofMemoryError` de aplicación de Apache Spark. Los pasos siguientes muestran los valores de configuración predeterminados de Spark en Azure HDInsight:

1. Inicie sesión en Ambari en `https://CLUSTERNAME.azurehdidnsight.net` con las credenciales del clúster. La pantalla inicial muestra un panel de información general. Hay ligeras diferencias cosméticas entre HDInsight 3.6 y 4.0.

1. Vaya a **Spark2** > **Configs** (Configuraciones).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Selección de la pestaña Configs (Configuraciones)" border="true":::

1. En la lista de configuraciones, seleccione y expanda **Custom-spark2-defaults**.

1. Busque el valor de configuración que tiene que ajustar, por ejemplo **spark.executor.memory**. En este caso, el valor de **9728m** es demasiado alto.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Selección de custom-spark-defaults" border="true":::

1. Establezca el valor en la configuración recomendada. Se recomienda el valor de **2048m** para esta configuración.

1. Guarde el valor y, después, guarde la configuración. Seleccione **Guardar**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Cambiar el valor a 2048m" border="true":::

    Escriba una nota sobre los cambios de configuración y, después, seleccione **Guardar**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Escribir una nota sobre los cambios realizados" border="true":::

    Se le notificará si hay configuraciones que necesiten atención. Tenga en cuenta los elementos y, a continuación, seleccione **Proceed Anyway** (Continuar de todos modos).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Selección de Proceed Anyway (Continuar de todos modos)" border="true":::

1. Cada vez que se guarda una configuración, se le solicitará que reinicie el servicio. Seleccione **Reiniciar**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Selección de Reiniciar" border="true":::

    Confirme el reinicio.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Selección de Confirm Restart All (Confirmar reinicio de todo)" border="true":::

    Puede revisar los procesos en ejecución.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Revisar los procesos en ejecución" border="true":::

1. Puede agregar configuraciones. En la lista de configuraciones, seleccione **Custom-spark2-defaults** y, a continuación, seleccione **Agregar propiedad**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Selección de Agregar propiedad" border="true":::

1. Defina una nueva propiedad. Puede definir una propiedad única mediante un cuadro de diálogo para valores específicos como el tipo de datos. O bien, puede definir varias propiedades mediante una definición por línea.

    En este ejemplo, se define la propiedad **spark.driver.memory** con un valor de **4g**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Definir la nueva propiedad" border="true":::

1. Guarde la configuración y, a continuación, reinicie el servicio, como se describe en los pasos 6 y 7.

Estos cambios son para todo el clúster, pero se pueden invalidar al enviar el trabajo de Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante un cuaderno de Jupyter Notebook en clústeres?

En la primera celda del cuaderno de Jupyter Notebook, después de la directiva **%%configure**, especifique las configuraciones de Spark en formato JSON válido. Cambie los valores reales según sea necesario:

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Agregar una configuración" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante Apache Livy en clústeres?

Envíe la aplicación Spark a Livy mediante un cliente REST como cURL. Use un comando similar al siguiente. Cambie los valores reales según sea necesario:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante spark-submit en clústeres?

Inicie spark-shell mediante un comando similar al siguiente. Cambie el valor real de las configuraciones según sea necesario:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Lecturas adicionales

[Apache Spark job submission on HDInsight clusters](/archive/blogs/azuredatalake/spark-job-submission-on-hdinsight-101) (Envío de trabajos de Apache Spark en clústeres de HDInsight)

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* [Información general de la administración de memoria en Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depuración de aplicaciones Spark en clústeres de HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. Esta cuenta pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).

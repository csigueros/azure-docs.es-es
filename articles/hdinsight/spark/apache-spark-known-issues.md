---
title: Solución de problemas con el clúster de Apache Spark en Azure HDInsight
description: Obtenga información sobre problemas relacionados con los clústeres de Apache Spark en Azure HDInsight y cómo evitarlos.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 020ef7750d0ee93931d44b8781fd170f5f34984f
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112370246"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conocidos de clústeres de Apache Spark en HDInsight

En este documento se hace un seguimiento de todos los problemas conocidos de la versión preliminar pública de HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Sesión interactiva con pérdidas de Apache Livy
Cuando se reinicia [Apache Livy](https://livy.incubator.apache.org/) (desde [Apache Ambari](https://ambari.apache.org/) o debido al reinicio de la máquina virtual del nodo principal 0) con una sesión interactiva aún activa, se pierde una sesión de trabajo interactiva. Como resultado, los nuevos trabajos se pueden detener en el estado Aceptado.

**Mitigación:**

Utilice el procedimiento siguiente para encontrar una solución para el problema:

1. SSH en el nodo principal. Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ejecute el siguiente comando para buscar los identificadores de aplicación de los trabajos interactivos iniciados mediante Livy.

   ```bash
   yarn application –list
   ```

    Los nombres de trabajo predeterminados serán Livy si se iniciaron con una sesión interactiva de Livy sin especificar ningún nombre. Para la sesión de Livy que inició [Jupyter Notebook](https://jupyter.org/), el nombre del trabajo empieza por `remotesparkmagics_*`.

3. Ejecute el comando siguiente para terminar esos trabajos.

   ```bash
   yarn application –kill <Application ID>
   ```

Los nuevos trabajos empezarán a ejecutarse.

## <a name="spark-history-server-not-started"></a>No se ha iniciado el servidor de historial de Spark
El servidor de historial de Spark no se inicia automáticamente después de crear un clúster.  

**Mitigación:**

Inicie el servidor de historial manualmente desde Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problemas con permisos en el directorio de registros de Spark
hdiuser obtiene el siguiente error al enviar un trabajo mediante spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

Y no se escribe ningún registro del controlador.

**Mitigación:**

1. Agregue hdiuser al grupo de Hadoop.
2. Proporcione 777 permisos en /var/log/spark después de crear el clúster.
3. Actualice la ubicación del registro spark con Ambari para que sea un directorio con 777 permisos.  
4. Ejecute spark-submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>No se admite el conector Spark-Phoenix

Los clústeres de HDInsight Spark no son compatibles con el conector Spark-Phoenix.

**Mitigación:**

Debe usar el conector Spark-HBase en su lugar. Para instrucciones, consulte el artículo sobre el [uso del conector Spark-HBase](/archive/blogs/azuredatalake/hdinsight-how-to-use-spark-hbase-connector).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados con los cuadernos de Jupyter Notebook

A continuación, se muestran algunos problemas conocidos relacionados con los cuadernos de Jupyter Notebook.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Cuadernos que tienen caracteres no ASCII en los nombres de archivo

No utilice caracteres que no sean ASCII en los nombres de archivo de los cuadernos de Jupyter. Si intenta cargar un archivo a través de la interfaz de usuario de Jupyter, que tiene un nombre de archivo que no es ASCII, se produce un error sin ningún mensaje. Jupyter no permite cargar el archivo, pero tampoco produce un error visible.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Error al cargar cuadernos con tamaños mayores

Es posible que aparezca un error **`Error loading notebook`** al cargar cuadernos de mayor tamaño.  

**Mitigación:**

El hecho de recibir este error no implica que los datos estén dañados o perdidos.  Los cuadernos siguen aún en el disco en `/var/lib/jupyter`y se puede conectar mediante SSH al clúster para acceder ellos. Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Cuando se haya conectado al clúster mediante SSH, puede copiar sus cuadernos desde el clúster en la máquina local (mediante SCP o WinSCP) como copia de seguridad para impedir la pérdida de datos importantes en el cuaderno. A continuación, puede aplicar túneles SSH al nodo principal del puerto 8001 para tener acceso a Jupyter sin pasar por la puerta de enlace.  Desde ahí, puede borrar la salida del cuaderno y volver a guardarla para minimizar el tamaño de este.

Para evitar que este error ocurra en el futuro, debe seguir algunos procedimientos recomendados:

* Es importante mantener reducido el tamaño del bloc de notas. Las salidas de los trabajos de Spark que se envían a Jupyter de vuelta se conservan en el bloc de notas.  En general, con Jupyter no se recomienda ejecutar `.collect()` en RDD o tramas de datos de gran tamaño; en su lugar, si desea ojear el contenido de un RDD, considere la posibilidad de ejecutar `.take()` o `.sample()` para que la salida no sea demasiado grande.
* Además, cuando guarde un bloc de notas, desactive todas las celdas de la salida para reducir el tamaño.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>El primer inicio del cuaderno tarda más de lo esperado

La primera instrucción del cuaderno de Jupyter con la instrucción mágica de Spark podría tardar más de un minuto.  

**Explicación:**

Esto sucede cuando se ejecuta la primera celda de código. En segundo plano se inicia la configuración de la sesión y se establecen los contextos de Spark, SQL y Hive. Una vez establecidos estos contextos, se ejecuta la primera instrucción y esto da la impresión de que la instrucción tardó mucho tiempo en completarse.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tiempo de espera del cuaderno de Jupyter en la creación de la sesión

Cuando el clúster de Spark se queda sin recursos, el tiempo de espera de los kernels de Spark y Pyspark del cuaderno de Jupyter se agotan al intentar crear la sesión.

**Mitigaciones:**

1. Libere algunos recursos del clúster Spark de la siguiente forma:

   * Detenga otros cuadernos de Spark en el menú Cerrar y detener o haga clic en Apagar en el explorador del cuaderno.
   * Detenga otras aplicaciones de Spark desde YARN.

2. Reinicie el cuaderno que trataba de iniciar. Debe haber suficientes recursos para crear una sesión ahora.

## <a name="see-also"></a>Consulte también

* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Apache Spark con BI: Análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: Uso de Spark en HDInsight para analizar la temperatura de un edificio mediante datos de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análisis de registros de un sitio web mediante Apache Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecución de trabajos de forma remota en un clúster de Apache Spark mediante Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Apache Spark applications remotely](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Apache Spark de forma remota)
* [Uso de cuadernos de Apache Zeppelin con un clúster de Apache Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponible para Jupyter Notebook en clústeres Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

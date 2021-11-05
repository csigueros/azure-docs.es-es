---
title: 'Depuración de trabajos de Spark con Azure Toolkit para IntelliJ (versión preliminar): HDInsight'
description: Guía para usar las herramientas de HDInsight en Azure Toolkit for IntelliJ con el fin de depurar aplicaciones
keywords: depurar remotamente intellij, depuración remota intellij, ssh, intellij, hdinsight, depurar intellij, depuración
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: c57abd00282067b66be0da55bf33324fc55dc435
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020072"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Error de depuración de trabajos de Spark con Azure Toolkit for IntelliJ (versión preliminar)

En este artículo se proporcionan instrucciones paso a paso sobre cómo usar las herramientas de HDInsight de [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) para ejecutar aplicaciones de **depuración de errores de Spark**.

## <a name="prerequisites"></a>Requisitos previos

* [Kit de desarrollo de Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). En este tutorial se usa la versión 8.0.202 de Java.
  
* IntelliJ IDEA. En este artículo se usa [IntelliJ IDEA Community 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Consulte [Instalación de Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* Conéctese al clúster de HDInsight. Vea [Conexión al clúster de HDInsight](apache-spark-intellij-tool-plugin.md).

* Explorador de Azure Storage. Vea [Descargar el Explorador de Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Creación de un proyecto con la plantilla de depuración

Cree un proyecto de Spark 2.3.2 para continuar con la depuración de errores; use el archivo de ejemplo de depuración de tareas de este documento.

1. Abra IntelliJ IDEA. Abra la ventana **New Project** (Nuevo proyecto).

   a. Seleccione **Azure Spark/HDInsight** en el panel izquierdo.

   b. Seleccione **Spark Project with Failure Task Debugging Samples (Preview) (Scala)** (Proyecto de Spark con ejemplos de depuración de tareas de error [versión preliminar] [Scala]) en la ventana principal.

     :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png" alt-text="Creación de un proyecto de depuración en IntelliJ" border="true":::

   c. Seleccione **Next** (Siguiente).

2. En la ventana **Nuevo proyecto**, siga estos pasos:

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png" alt-text="Selección de versión de Spark en nuevo proyecto de IntelliJ" border="true":::

   a. Escriba un nombre y una ubicación de proyecto.

   b. En la lista desplegable **Project SDK** (SDK del proyecto), seleccione **Java 1.8** para el clúster de **Spark 2.3.2**.

   c. En la lista desplegable **Spark Version** (Versión de Spark), seleccione **Spark 2.3.2(Scala 2.11.8)** .

   d. Seleccione **Finalizar**.

3. Seleccione **src** > **main** > **scala** para abrir el código en el proyecto. En este ejemplo se usa el script **AgeMean_Div()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Ejecución de una aplicación Scala o Java de Spark en un clúster de HDInsight Spark

Cree una aplicación Scala o Java de Spark, y después ejecútela en un clúster de Spark mediante estos pasos:

1. Haga clic en **Add Configuration** (Agregar configuración) para abrir la ventana **Run/Debug Configurations** (Ejecutar/depurar configuraciones).

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png" alt-text="Adición de nueva configuración para IntelliJ en HDI" border="true":::

2. En el cuadro de diálogo **Run/Debug Configurations** (Ejecutar/depurar configuraciones), seleccione el signo más ( **+** ). Después, seleccione la opción **Apache Spark on HDInsight** (Apache Spark en HDInsight).

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png" alt-text="Adición de nueva configuración para IntelliJ" border="true":::

3. Cambie a la pestaña **Remotely Run in Cluster** (Ejecutar de forma remota en clúster). Escriba la información en los campos **Name** (Nombre), **Spark cluster** (Clúster de Spark) y **Main class name** (Nombre de clase principal). Nuestras herramientas admiten la depuración con **ejecutores**. El valor predeterminado de **numExectors** es 5, y se recomienda no establecerlo por encima de 3. Para reducir el tiempo de ejecución, puede agregar **spark.yarn.maxAppAttempts** a **job Configurations** y establecer el valor en 1. Haga clic en el botón **Aceptar** para guardar la configuración.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png" alt-text="Nueva configuración de ejecución o depuración para IntelliJ" border="true":::

4. La configuración se guarda ahora con el nombre especificado. Para ver los detalles de configuración, seleccione el nombre de configuración. Para realizar cambios, seleccione **Edit Configurations** (Editar configuraciones).

5. Después de completar la configuración, puede ejecutar el proyecto en el clúster remoto.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png" alt-text="Botón de ejecución remota del trabajo de Spark de depuración remota para IntelliJ" border="true":::

6. Puede comprobar el identificador de la aplicación en la ventana de salida.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png" alt-text="Resultado de la ejecución remota del trabajo de Spark de depuración remota para IntelliJ" border="true":::

## <a name="download-failed-job-profile"></a>Error en la descarga del perfil de trabajo

Si se produce un error al enviar el trabajo, puede descargar el perfil de trabajo con errores en el equipo local para depurarlo más.

1. Abra el **Explorador de Microsoft Azure Storage**, busque la cuenta de HDInsight del clúster del trabajo con errores, descargue los recursos del trabajo con errores desde la ubicación correspondiente: **\hdp\spark2-events\\.spark-failures\\\<application ID>** en una carpeta local. En la ventana **activities** (actividades) se mostrará el progreso de la descarga.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png" alt-text="Fallo de descarga del Explorador de Azure Storage" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png" alt-text="Descarga correcta del Explorador de Azure Storage" border="true":::

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configuración del entorno de depuración local y depuración en caso de error

1. Abra el proyecto original o cree uno y asócielo con el código fuente original. Actualmente solo se admite la versión Spark 2.3.2 para la depuración de errores.

1. En IntelliJ IDEA, cree un archivo de configuración **Spark Failure Debug** (Depuración de errores de Spark) y seleccione el archivo FTD de los recursos de trabajo con errores que ha descargado antes para el campo **Spark Job Failure Context location** (Ubicación del contexto de error del trabajo de Spark).

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png" alt-text="crear configuración de error" border="true":::

1. Haga clic en el botón de ejecución local en la barra de herramientas; el error se mostrará en la ventana de ejecución.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png" alt-text="ejecutar-configuración-de-error1" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png" alt-text="ejecutar-configuración-de-error2" border="true":::

1. Establezca el punto de interrupción como se indica en el registro y, después, haga clic en el botón depuración local para realizar la depuración local como en los proyectos normales de Scala o Java en IntelliJ.

1. Después de la depuración, si el proyecto se completa de forma correcta, puede volver a enviar el trabajo con error al clúster de Spark en HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Pasos siguientes

* [Información general: Depuración de aplicaciones de Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="scenarios"></a>Escenarios

* [Apache Spark con BI: Análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: uso de Apache Spark en HDInsight para analizar la temperatura de edificios con los datos del sistema de acondicionamiento de aire](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análisis de registros de un sitio web mediante Apache Spark en HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](./apache-spark-create-standalone-application.md)
* [Ejecución de trabajos de forma remota en un clúster de Apache Spark mediante Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Uso de Azure Toolkit for IntelliJ con el fin de crear aplicaciones Apache Spark para un clúster de HDInsight](apache-spark-intellij-tool-plugin.md)
* [Uso de Azure Toolkit for IntelliJ para depurar de forma remota aplicaciones de Apache Spark mediante VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Uso de las herramientas de HDInsight de Azure Toolkit for Eclipse con el fin de crear aplicaciones Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Apache Zeppelin con un clúster de Apache Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels para Jupyter Notebook en clústeres Azure Spark en Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)
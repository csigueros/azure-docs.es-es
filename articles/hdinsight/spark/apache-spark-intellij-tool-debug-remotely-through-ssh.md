---
title: 'Azure Toolkit for IntelliJ: Depuración de aplicaciones de Spark con SSH: HDInsight'
description: Instrucciones paso a paso para el uso de las herramientas de HDInsight del Kit de herramientas de Azure para IntelliJ para depurar aplicaciones de forma remota en clústeres de HDInsight mediante SSH
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 0bd31aea95506f64f2e9b787882a81c7a0799ec2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016214"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depuración de aplicaciones de Apache Spark en un clúster de HDInsight con Azure Toolkit for IntelliJ mediante SSH

En este artículo se ofrecen instrucciones paso a paso para el empleo de las herramientas de HDInsight de [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) para depurar aplicaciones de forma remota en un clúster de HDInsight.

## <a name="prerequisites"></a>Prerrequisitos

* Un clúster de Apache Spark en HDInsight. Vea [Creación de un clúster de Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Para usuarios de Windows: Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede producirse una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta excepción se produce porque falta WinUtils.exe en Windows.

    Para solucionar este error, descargue [Winutils.exe](https://github.com/steveloughran/winutils) en una ubicación como **C:\WinUtils\bin**. Después, agregue una variable de entorno **HADOOP_HOME** y establezca el valor de la variable en **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (la edición Community es gratuita).

* [Kit de herramientas de Azure para IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Complemento de Scala para IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Un cliente SSH. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Creación de una aplicación de Scala en Spark

1. Inicie IntelliJ IDEA y seleccione **Create New Project** (Crear proyecto) para abrir la ventana **New Project** (Nuevo proyecto).

1. Seleccione **Azure Spark/HDInsight** en el panel izquierdo.

1. Seleccione **Spark Project with Samples (Scala)** (Proyecto de Spark con ejemplos [Scala]) en la ventana principal.

1. En la lista desplegable **Build tool** (Herramienta de compilación), seleccione una de las siguientes:

    * **Maven**: para agregar compatibilidad con el asistente para la creación de proyectos de Scala.
    * **SBT** para administrar las dependencias y compilar el proyecto de Scala.

     :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png" alt-text="Creación de nuevo proyecto de Spark en IntelliJ" border="true":::

1. Seleccione **Next** (Siguiente).

1. En la siguiente ventana **New Project** (Nuevo proyecto), proporcione la siguiente información:

    |Propiedad |Descripción |
    |---|---|
    |Nombre de proyecto|Escriba un nombre. Este tutorial usa `myApp`.|
    |Ubicación del proyecto|Escriba la ubicación deseada para guardar el proyecto.|
    |Project SDK (SDK del proyecto)|Si está en blanco, seleccione **New...** (Nuevo...) y vaya a su JDK.|
    |Versión de Spark|El asistente de creación integra la versión adecuada de los SDK de Spark y Scala. Si la versión del clúster de Spark es anterior a 2.0, seleccione **Spark 1.x**. De lo contrario, seleccione **Spark 2.x**. En este ejemplo se usa **Spark 2.3.0 (Scala 2.11.8)** .|

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png" alt-text="Selección de versión de Spark en nuevo proyecto de IntelliJ" border="true":::

1. Seleccione **Finalizar**. El proyecto puede tardar unos minutos en estar disponible. Fíjese en la esquina inferior derecha para ver el progreso.

1. Expanda el proyecto y vaya a **src** > **main** > **scala** > **sample**. Haga doble clic en **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Realizar una ejecución local

1. En el script **SparkCore_WasbIOTest**, haga clic con el botón derecho en el editor de scripts y luego seleccione la opción **Run “SparkCore_WasbIOTest”** (Ejecutar “SparkCore_WasbIOTest”) para realizar la ejecución local.

1. Una vez completada la ejecución local, puede ver el archivo de salida guardado en el Explorador de proyectos actual **datos** >  **__default__** .

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png" alt-text="Resultado de la ejecución local del proyecto de IntelliJ" border="true":::

1. Nuestras herramientas han establecido automáticamente la configuración de la ejecución local predeterminada al realizar la ejecución local y la depuración local. Abra el  **XXX [Spark en HDInsight]** de configuración en la esquina superior derecha; verá que ya se ha creado el **XXX [Spark en HDInsight]** en **Apache Spark on HDInsight** (Apache Spark en HDInsight). Cambie a la pestaña **Ejecutar localmente**.

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png" alt-text="Ejecución local de las configuraciones de ejecución y depuración de IntelliJ" border="true":::

    - [Variables de entorno](#prerequisites): si ya ha establecido la variable de entorno del sistema **HADOOP_HOME** en **C:\WinUtils**, detecta automáticamente que no es necesario agregarla manualmente.
    - [Ubicación de WinUtils.exe](#prerequisites): si no se ha establecido la variable de entorno del sistema, haga clic en el botón correspondiente para encontrar la ubicación.
    - Solo tiene que elegir cualquiera de las dos opciones. No se necesitan en MacOS y Linux.

1. También puede establecer la configuración manualmente antes de realizar la depuración local y la ejecución local. En la captura de pantalla anterior, seleccione el signo más ( **+** ). Después, seleccione la opción **Apache Spark on HDInsight** (Apache Spark en HDInsight). Escriba la información en **Name** (Nombre), **Main class name** (Nombre de clase principal) y haga clic en el botón de ejecución local.

## <a name="perform-local-debugging"></a>Realizar una depuración local

1. Abra el script **SparkCore_wasbloTest** y establezca los puntos de interrupción.

1. Haga clic con el botón derecho en el editor de scripts y seleccione la opción **Debug '[Spark Job]XXX'** (Depurar "[Trabajo de Spark]XXX") para realizar la depuración local.

## <a name="perform-remote-run"></a>Realizar la ejecución remota

1. Seleccione **Ejecutar** > **Editar configuraciones...** . Desde este menú, puede crear o modificar las configuraciones para la depuración remota.

1. En el cuadro de diálogo **Run/Debug Configurations** (Ejecutar/depurar configuraciones), seleccione el signo más ( **+** ). Después, seleccione la opción **Apache Spark on HDInsight** (Apache Spark en HDInsight).

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png" alt-text="Adición de nueva configuración para IntelliJ" border="true":::

1. Cambie a la pestaña **Remotely Run in Cluster** (Ejecutar de forma remota en clúster). Escriba la información en los campos **Name** (Nombre), **Spark cluster** (Clúster de Spark) y **Main class name** (Nombre de clase principal). A continuación, haga clic en **Advanced configuration (Remote Debugging)** (Configuración avanzada [depuración remota]). Nuestras herramientas admiten la depuración con **ejecutores**. Elv alor predeterminado de **numExectors** es 5. Es mejor no establecer más de 3.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png" alt-text="Configuraciones de ejecución y depuración para IntelliJ" border="true":::

1. En la parte **Advanced Configuration (Remote Debugging)** (Configuración avanzada [depuración remota]), seleccione **Enable Spark remote debug** (Habilitar la depuración remota de Spark). Escriba el nombre de usuario SSH y luego especifique una contraseña o use un archivo de clave privada. Si desea realizar la depuración remota, debe establecerla. No es necesario establecerla si solo desea usar la ejecución remota.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png" alt-text="Habilitación de depuración remota de Spark en la configuración avanzada para IntelliJ" border="true":::

1. La configuración se guarda ahora con el nombre especificado. Para ver los detalles de configuración, seleccione el nombre de configuración. Para realizar cambios, seleccione **Edit Configurations** (Editar configuraciones).

1. Después de completar la configuración, puede ejecutar el proyecto en el clúster remoto o realizar la depuración remota.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png" alt-text="Botón de ejecución remota del trabajo de Spark de depuración remota para IntelliJ" border="true":::

1. Haga clic en el botón **Desconectar** si los registros de envío no aparecen en el panel izquierdo. Sin embargo, continúa en ejecución en el back-end.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png" alt-text="Resultado de la ejecución remota del trabajo de Spark de depuración remota para IntelliJ" border="true":::

## <a name="perform-remote-debugging"></a>Realizar una depuración remota

1. Configure los puntos de interrupción y luego seleccione el icono **Remote debug** (Depuración remota). La diferencia con el envío remoto es que hay que configurar el nombre de usuario y la contraseña de SSH.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png" alt-text="Icono de depuración para trabajo de depuración remota de Spark para IntelliJ" border="true":::

1. Cuando la ejecución del programa alcanza el punto de interrupción, aparecen una pestaña **Controlador** y dos pestañas **Ejecutor** en el panel **Depurador**. Seleccione el icono **Resume Program** (Continuar programa) para seguir ejecutando el código, que luego alcanza el siguiente punto de interrupción. Debe cambiar a la pestaña **Executor** (Ejecutor) correcta para buscar el ejecutor de destino que se va a depurar. Puede ver los registros de ejecución en la pestaña **Console** (Consola) correspondiente.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png" alt-text="Pestaña de depuración para trabajo de depuración remota de Spark para IntelliJ" border="true":::

### <a name="perform-remote-debugging-and-bug-fixing"></a>Realizar la depuración remota y la corrección de errores

1. Configure dos puntos de interrupción y luego seleccione el icono **Depurar** para iniciar el proceso de depuración remota.

1. El código se detiene en el primer punto de interrupción y se muestra la información de parámetros y variables en el panel **Variables**.

1. Seleccione el icono **Resume Program** (Continuar programa) para continuar. El código se detiene en el segundo punto. La excepción se detecta según lo previsto.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png" alt-text="Error producido en trabajo de depuración remota de Spark para IntelliJ" border="true":::

1. Vuelva a seleccionar el icono **Resume Program** (Continuar programa). La ventana **HDInsight Spark Subsmission**  (Envío de HDInsight Spark) muestra un error de ejecución de trabajo.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png" alt-text="Error de envío de trabajo de depuración remota de Spark para IntelliJ" border="true":::

1. Para actualizar de forma dinámica el valor de variable mediante la funcionalidad de depuración de IntelliJ, vuelva a seleccionar **Depurar**. El panel **Variables** aparece de nuevo.

1. Haga clic con el botón derecho en el destino en el pestaña **Depurar** y, a continuación, seleccione **Establecer valor**. Luego escriba un nuevo valor para la variable. A continuación, seleccione **Entrar** para guardar el valor.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png" alt-text="Establecimiento de valor para trabajo de depuración remota de Spark para IntelliJ" border="true":::

1. Seleccione el icono **Resume Program** (Continuar programa) para seguir ejecutando el programa. Esta vez no se detecta ninguna excepción. Puede ver que el proyecto se ejecuta correctamente sin ninguna excepción.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png" alt-text="Trabajo de depuración remota de Spark para IntelliJ sin excepción" border="true":::

## <a name="next-steps"></a>Pasos siguientes

* [Información general: Apache Spark en Azure HDInsight](apache-spark-overview.md)

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
* [Uso de las herramientas de HDInsight de Azure Toolkit for Eclipse con el fin de crear aplicaciones Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Apache Zeppelin con un clúster de Apache Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels para Jupyter Notebook en clústeres Azure Spark en Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)
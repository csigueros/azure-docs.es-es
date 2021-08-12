---
title: 'Tutorial: Azure Toolkit for IntelliJ (aplicación Spark)'
description: 'Tutorial: Uso de Azure Toolkit for IntelliJ para desarrollar aplicaciones Spark escritas en Scala y enviarlas a un grupo de Apache Spark sin servidor.'
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 4ccc36bb9f940fd018fe6d96b5338a7178657aa2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469388"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Tutorial: Creación de una aplicación Apache Spark con IntelliJ mediante un área de trabajo de Synapse

En este tutorial se muestra cómo usar el complemento de Azure Toolkit for IntelliJ para desarrollar aplicaciones Apache Spark escritas en [Scala](https://www.scala-lang.org/) y enviarlas a continuación a un grupo de Apache Spark sin servidor directamente desde el entorno de desarrollo integrado (IDE) de IntelliJ. Puede usar el complemento de varias maneras:

- Desarrollar y enviar una aplicación Spark en Scala a un grupo de Spark.
- Acceder a los recursos de los grupos de Spark.
- Desarrollar y ejecutar localmente una aplicación Spark en Scala.

En este tutorial, aprenderá a:
> [!div class="checklist"]
>
> - Usar el complemento Azure Toolkit for IntelliJ
> - Desarrollar aplicaciones de Apache Spark
> - Envío de una aplicación a grupos de Spark

## <a name="prerequisites"></a>Requisitos previos

- [IntelliJ IDEA Community Version](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Complemento de Azure Toolkit 3.27.0-2019.2: instalación desde el [Repositorio de complementos de IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation)
- [JDK (Versión 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Complemento de Scala: instalación desde el [Repositorio de complementos de IntelliJ](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).
- El requisito previo siguiente es solo para los usuarios de Windows:

  Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede producirse una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta excepción se produce porque falta WinUtils.exe en Windows.
  Para solucionar este error, descargue el [archivo ejecutable WinUtils](https://github.com/steveloughran/winutils/releases/download/tag_2017-08-29-hadoop-2.8.1-native/hadoop-2.8.1.zip) y guárdelo en una ubicación como **C:\WinUtils\bin**. Después, agregue una variable de entorno **HADOOP_HOME** y establezca el valor de la variable en **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Creación de una aplicación Spark en Scala para un grupo de Spark

1. Inicie IntelliJ IDEA y seleccione **Create New Project** (Crear proyecto) para abrir la ventana **New Project** (Nuevo proyecto).
2. Seleccione **Azure Spark/HDInsight** en el panel izquierdo.
3. Seleccione **Spark Project with Samples (Scala)** (Proyecto de Spark con ejemplos [Scala]) en la ventana principal.
4. En la lista desplegable **Build tool** (Herramienta de compilación), seleccione uno de los siguientes tipos:

   - **Maven**: para agregar compatibilidad con el asistente para la creación de proyectos de Scala.
   - **SBT** para administrar las dependencias y compilar el proyecto de Scala.

    ![IDEA de IntelliJ: cuadro de diálogo New Project (Nuevo proyecto)](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Seleccione **Next** (Siguiente).
6. En la ventana **New Project** (Nuevo proyecto), proporcione la siguiente información:

    | Propiedad | Descripción |
    | ----- | ----- |
    |Nombre de proyecto| Escriba un nombre. En este tutorial se usa `myApp`.|
    |Project&nbsp;location (Ubicación del proyecto)| Escriba la ubicación deseada para guardar el proyecto.|
    |Project SDK (SDK del proyecto)| Podría quedarse en blanco la primera vez que se usa IDEA. Seleccione **New...** (Nuevo...) y vaya a su JDK.|
    |Versión de Spark|El asistente de creación integra la versión adecuada de los SDK de Spark y Scala. Synapse solo admite **Spark 2.4.0**.|
    |||

    ![Selección del SDK de Apache Spark](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Seleccione **Finalizar**. El proyecto puede tardar unos minutos en estar disponible.
8. El proyecto de Spark crea automáticamente un artefacto. Para ver el artefacto, haga lo siguiente:

   a. En la barra de menús, vaya a **Archivo** > **Estructura del proyecto...** .

   b. En la ventana **Estructura del proyecto**, seleccione **Artefactos**.

   c. Seleccione **Cancelar** después de ver el artefacto.

    ![Información del artefacto en el cuadro de diálogo](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Busque **LogQuery** en **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery**. En este tutorial se usa **LogQuery** para la ejecución.

   ![Comandos para crear una clase Scala desde Proyecto](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Conexión a los grupos de Spark

Inicie sesión en la suscripción de Azure para conectarse a los grupos de Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure

1. En la barra de menús, vaya a **Ver** > **Ventanas de herramientas** > **Azure Explorer**.

   ![IntelliJ IDEA muestra Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. En Azure Explorer, haga clic con el botón derecho en el nodo **Azure** y después seleccione **Iniciar sesión**.

   ![IntelliJ IDEA: clic con el botón derecho en Azure Explorer](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. En el cuadro de diálogo de **inicio de sesión en Azure**, seleccione **Inicio de sesión del dispositivo** y, a continuación, **Iniciar sesión**.

    ![IntelliJ IDEA: Inicio de sesión en Azure](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. En el cuadro de diálogo **Inicio de sesión del dispositivo de Azure**, seleccione **Copiar y abrir**.

   ![IntelliJ IDEA: inicio de sesión del dispositivo de Azure](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. En la interfaz del explorador, pegue el código y, a continuación, seleccione **Siguiente**.

   ![Cuadro de diálogo del código de entradas de Microsoft para HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Escriba sus credenciales de Azure y, a continuación, cierre el explorador.

   ![Cuadro de diálogo del correo electrónico de entradas de Microsoft para HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Seleccione la suscripción y, a continuación, elija **Seleccionar**.

    ![Cuadro de diálogo Seleccionar suscripciones](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. En **Azure Explorer**, expanda **Apache Spark en Synapse** para ver las áreas de trabajo de las suscripciones.

    ![IDEA de IntelliJ: vista principal de Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Para ver los grupos de Spark, puede expandir un área de trabajo.

    ![Azure Explorer: cuentas de almacenamiento](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Ejecución remota de una aplicación Spark en Scala en un grupo de Spark

Después de crear una aplicación en Scala, puede ejecutarla de forma remota.

1. Seleccione el icono para abrir la ventana **Configuraciones de ejecución/depuración**.

    ![Envío de aplicación Spark a comando HDInsight 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. En el ventana de diálogo **Configuraciones de ejecución/depuración**, seleccione **+** y, a continuación, elija **Apache Spark en Synapse**.

    ![Envío de aplicación Spark a comando HDInsight 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. En la ventana **Configuraciones de ejecución/depuración**, proporcione los valores siguientes y, a continuación, seleccione **Aceptar**:

    |Propiedad |Value |
    |----|----|
    |Grupos de Spark|Seleccione los grupos de Spark en los que quiere ejecutar la aplicación.|
    |Seleccione un artefacto para enviarlo|Deje la configuración predeterminada.|
    |Nombre de clase principal|el valor predeterminado es la clase principal del archivo seleccionado. Puede cambiar la clase si selecciona los puntos suspensivos ( **...** ) y elige otra clase.|
    |Configuraciones del trabajo|Puede cambiar las claves y valores predeterminados. Para más información, consulte [API de REST de Apache Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumentos de la línea de comandos|Puede especificar los argumentos divididos por un espacio para la clase principal, si es necesario.|
    |Archivos jar a los que se hace referencia y archivos a los que se hace referencia|puede escribir las rutas de acceso de los archivos y los Jar a los que se hace referencia, si los hubiera. También puede examinar archivos en el sistema de archivos virtual de Azure, que actualmente solo admite el clúster de ADLS Gen2. Para más información: [Configuración de Apache Spark]https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) y [Carga de recursos en el clúster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Almacenamiento de carga del trabajo|Expanda para mostrar opciones adicionales.|
    |Tipo de almacenamiento|Seleccione **Use Azure Blob to upload** (Usar Azure Blob para cargar) o **Use cluster default storage account to upload** (Usar la cuenta de almacenamiento predeterminada del clúster para cargar).|
    |Cuenta de almacenamiento|Escriba su cuenta de Storage.|
    |Clave de almacenamiento|Escriba su clave de almacenamiento.|
    |Contenedor de almacenamiento|Seleccione su contenedor de almacenamiento en la lista desplegable una vez que se hayan escrito **Cuenta de Storage** y **Clave de almacenamiento**.|

    ![Cuadro de diálogo de envío de Spark](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Seleccione el icono **SparkJobRun** para enviar el proyecto al grupo de Spark seleccionado. La pestaña **Remote Spark Job in Cluster** (Trabajo de Spark remoto en clúster) muestra el progreso de la ejecución del trabajo en la parte inferior. Puede detener la aplicación si selecciona el botón rojo.

    ![Ventana de envío de Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Cuadro de diálogo de envío de Spark 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Ejecución/depuración local de aplicaciones Apache Spark

Puede seguir las instrucciones siguientes para configurar la ejecución local y la depuración local para el trabajo de Apache Spark.

### <a name="scenario-1-do-local-run"></a>Escenario 1: Ejecución local

1. Abra el cuadro de diálogo **Configuraciones de ejecución/depuración** y seleccione el signo más ( **+** ). A continuación, seleccione la opción **Apache Spark en Synapse**. Escriba la información de **Nombre** y **Nombre de clase principal** para guardar.

    ![Ejecución local de las configuraciones de ejecución y depuración de IntelliJ 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Las variables de entorno y la ubicación de WinUtils.exe son solo para los usuarios de Windows.
    - Variables de entorno: la variable de entorno del sistema se puede detectar automáticamente si se ha establecido antes y no es necesario agregarla manualmente.
    - [Ubicación de WinUtils.exe](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): para especificar la ubicación de WinUtils, seleccione el icono de carpeta de la derecha.

2. A continuación, seleccione el botón de reproducción local.

    ![Ejecución local de las configuraciones de ejecución y depuración de IntelliJ 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Una vez completada la ejecución local, si el script incluye una salida, puede comprobar el archivo de salida desde **data** > **__default__**.

    ![Resultado de la ejecución local del proyecto de IntelliJ 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Escenario 2: Depuración local

1. Abra el script **LogQuery** y establezca puntos de interrupción.
2. Seleccione el icono **Depuración local** para realizar la depuración local.

    ![Resultado de la ejecución local del proyecto de IntelliJ 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Acceso y administración del área de trabajo de Synapse

Puede realizar diferentes operaciones en Azure Explorer dentro de Azure Toolkit for IntelliJ. En la barra de menús, vaya a **Ver** > **Ventanas de herramientas** > **Azure Explorer**.

### <a name="launch-workspace"></a>Inicio del área de trabajo

1. En Azure Explorer, vaya a **Apache Spark en Synapse** y, a continuación, expándalo.

    ![IDEA de IntelliJ: vista principal de Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Haga clic con el botón derecho en un área de trabajo y, a continuación, seleccione **Iniciar área de trabajo**, se abrirá el sitio web.

    ![Detalles de aplicación en vista de trabajos de Spark 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Detalles de aplicación en vista de trabajos de Spark 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Consola de Spark

Puede ejecutar la consola local de Spark (Scala) o la consola de sesión interactiva de Spark Livy (Scala).

### <a name="spark-local-console-scala"></a>Consola local de Spark (Scala)

Asegúrese de que cumple el requisito previo de WINUTILS.EXE.

1. En la barra de menús, vaya a **Ejecutar** > **Editar configuraciones...**
2. En la ventana **Configuraciones de ejecución/depuración**, en el panel izquierdo, vaya a **Apache Spark en Synapse** >  **[Spark en Synapse] myApp**.
3. En la ventana principal, seleccione la pestaña **Ejecutar de forma local**.
4. Proporcione los valores siguientes y seleccione **Aceptar**:

    |Propiedad |Value |
    |----|----|
    |Variables de entorno|Asegúrese de que el valor de HADOOP_HOME sea correcto.|
    |Ubicación de WINUTILS.exe|Asegúrese de que la ruta de acceso sea correcta.|

    ![Configuración de conjunto de consola local](./media/intellij-tool-synapse/local-console-synapse01.png)

5. En el proyecto, vaya a **myApp** > **src** > **main** > **scala** > **myApp**.
6. En la barra de menús, vaya a **Herramientas** > **Consola de Spark** > **Ejecutar consola local de Spark (Scala)** .
7. Pueden aparecer dos cuadros de diálogo para preguntarle si quiere corregir automáticamente las dependencias. Si es así, seleccione **Autocorrección**.

    ![IDEA de IntelliJ: cuadro de diálogo 1 de corrección automática de Spark](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IDEA de IntelliJ: cuadro de diálogo 2 de corrección automática de Spark](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. El aspecto de la consola debería ser similar al de la siguiente imagen. En la ventana de la consola, escriba `sc.appName` y presione CTRL + Entrar. Se muestra el resultado. Para detener la consola local, seleccione el botón rojo.

    ![IDEA de IntelliJ: resultado de la consola local](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Consola de sesión interactiva de Spark Livy (Scala)

Solo se admite en IntelliJ 2018.2 y 2018.3.

1. En la barra de menús, vaya a **Ejecutar** > **Editar configuraciones...**

2. En la ventana **Configuraciones de ejecución/depuración**, en el panel izquierdo, vaya a **Apache Spark en Synapse** >  **[Spark en Synapse] myApp**.

3. En la ventana principal, seleccione la pestaña **Remotely Run in Cluster** (Ejecutar en clúster de forma remota).

4. Proporcione los valores siguientes y seleccione **Aceptar**:

    |Propiedad |Value |
    |----|----|
    |Nombre de clase principal| Seleccione el nombre de clase Main.| 
    |Grupos de Spark|Seleccione los grupos de Spark en los que quiere ejecutar la aplicación.|
    ||

    ![Configuración de conjunto de consola interactiva](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. En el proyecto, vaya a **myApp** > **src** > **main** > **scala** > **myApp**.

6. En la barra de menús, vaya a **Herramientas** > **Consola de Spark** > **Ejecutar consola de sesión interactiva de Spark Livy (Scala)** .
7. El aspecto de la consola debería ser similar al de la siguiente imagen. En la ventana de la consola, escriba `sc.appName` y presione CTRL + Entrar. Se muestra el resultado. Para detener la consola local, seleccione el botón rojo.

    ![IDEA de IntelliJ: resultado de la consola interactiva](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Envío de la selección a la consola de Spark

Puede ver el resultado del script si envía algún código a la consola local o la consola de sesión interactiva de Livy (Scala). Para hacerlo, puede resaltar parte del código en el archivo de Scala y hacer clic con el botón derecho en **Enviar selección a consola de Spark**. El código seleccionado se envía a la consola y se ejecuta. El resultado se muestra después del código en la consola. La consola comprobará si hay errores.

   ![Envío de la selección a la consola de Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Pasos siguientes

- [Azure Synapse Analytics](../overview-what-is.md)
- [Creación de un grupo de Apache Spark para un área de trabajo de Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
---
title: 'Azure Toolkit for IntelliJ: Aplicación Spark en HDInsight'
description: Uso del kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: aeb6c9e266bdb663b9a664c8509e9cafab8a5128
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743018"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Uso de Azure Toolkit for IntelliJ para crear aplicaciones de Apache Spark para un clúster de HDInsight

En este artículo se muestra cómo desarrollar aplicaciones de Apache Spark en Azure HDInsight con el complemento **Azure Toolkit** para el IDE de IntelliJ. [Azure HDInsight](../hdinsight-overview.md) es un servicio de análisis administrado de código abierto en la nube. El servicio le permite usar marcos de código abierto como Hadoop, Apache Spark, Apache Hive y Apache Kafka.

Puede usar el complemento **Azure Toolkit** de varias maneras:

* Desarrollar y enviar una aplicación Spark con Scala en un clúster de Spark en HDInsight.
* Tener acceso a los recursos del clúster de Azure HDInsight Spark.
* Desarrollar y ejecutar localmente una aplicación Spark en Scala.

En este artículo aprenderá a:
> [!div class="checklist"]
> * Usar el complemento Azure Toolkit for IntelliJ
> * Desarrollar aplicaciones de Apache Spark
> * Enviar una aplicación al clúster de Azure HDInsight

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md). Solo se admiten clústeres de HDInsight en la nube pública, mientras que otros tipos de nube segura (por ejemplo, nubes gubernamentales) no se admiten.

* [Kit de desarrollo de Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  En este artículo se usa la versión 8.0.202 de Java.

* IntelliJ IDEA. En este artículo se usa [IntelliJ IDEA Community  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Consulte [Instalación de Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instale el complemento de Scala para IntelliJ IDEA.

Pasos de instalación del complemento Scala:

1. Abra IntelliJ IDEA.

2. En la pantalla de bienvenida, vaya a **Configure** > **Plugins** (Configurar > Complementos) para abrir la ventana **Plugins** (Complementos).

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png" alt-text="IDEA de IntelliJ habilita el complemento Scala" border="true":::

3. Seleccione **Install** (Instalar) para el complemento de Scala que se presenta en la nueva ventana.  

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png" alt-text="IDEA de IntelliJ instala el complemento Scala" border="true":::

4. Después de que el complemento se instale correctamente, debe reiniciar el IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Creación de una aplicación Spark en Scala para un clúster de HDInsight Spark

1. Inicie IntelliJ IDEA y seleccione **Create New Project** (Crear proyecto) para abrir la ventana **New Project** (Nuevo proyecto).

2. Seleccione **Azure Spark/HDInsight** en el panel izquierdo.

3. Seleccione **Spark Project (Scala)** (Proyecto de Spark [Scala]) en la ventana principal.

4. En la lista desplegable **Build tool** (Herramienta de compilación), seleccione una de las siguientes opciones:
   * **Maven**: para agregar compatibilidad con el asistente para la creación de proyectos de Scala.
   * **SBT** para administrar las dependencias y compilar el proyecto de Scala.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png" alt-text="IDEA de IntelliJ: cuadro de diálogo New Project (Nuevo proyecto)" border="true":::

5. Seleccione **Next** (Siguiente).

6. En la ventana **New Project** (Nuevo proyecto), proporcione la siguiente información:  

    |  Propiedad   | Descripción   |  
    | ----- | ----- |  
    |Nombre de proyecto| Escriba un nombre.  En este artículo se usa `myApp`.|  
    |Project&nbsp;location (Ubicación del proyecto)| Escriba la ubicación para guardar el proyecto.|
    |Project SDK (SDK del proyecto)| Este campo puede estar en blanco la primera vez que se usa IDEA.  Seleccione **New...** (Nuevo...) y vaya a su JDK.|
    |Versión de Spark|El asistente de creación integra la versión adecuada de los SDK de Spark y Scala. Si la versión del clúster de Spark es anterior a 2.0, seleccione **Spark 1.x**. De lo contrario, seleccione **Spark2.x**. En este ejemplo se usa **Spark 2.3.0 (Scala 2.11.8)** .|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-new-project.png" alt-text="Selección del SDK de Apache Spark" border="true":::

7. Seleccione **Finalizar**.  El proyecto puede tardar unos minutos en estar disponible.

8. El proyecto de Spark crea automáticamente un artefacto. Para ver el artefacto, siga estos pasos:

   a. En la barra de menús, vaya a **Archivo** > **Estructura del proyecto...** .

   b. En la ventana **Estructura del proyecto**, seleccione **Artefactos**.  

   c. Seleccione **Cancelar** después de ver el artefacto.

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png" alt-text="Información del artefacto en el cuadro de diálogo" border="true":::

9. Agregue el código fuente de aplicación mediante los siguientes pasos:

    a. En Proyecto, vaya a **myApp** > **src** > **main** > **scala**.  

    b. Haga clic con el botón derecho en **scala**, y, a continuación, vaya a **Nuevo** > **Clase Scala**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png" alt-text="Comandos para crear una clase Scala desde Proyecto" border="true":::

   c. En el cuadro de diálogo **Crear nueva clase Scala**, proporcione un nombre, seleccione **Objeto** en la lista desplegable **Variante** y seleccione **Aceptar**.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png" alt-text="Creación de un cuadro de diálogo de nueva clase de Scala" border="true":::

   d. A continuación, el archivo **myApp.scala** se abre en la vista principal. Reemplace el código predeterminado por el código encontrado a continuación:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    El código lee los datos de HVAC.csv (disponible en todos los clústeres de HDInsight Spark), recupera las filas que solo tienen un dígito en la séptima columna del archivo CSV y escribe la salida en `/HVACOut` en el contenedor de almacenamiento predeterminado para el clúster.

## <a name="connect-to-your-hdinsight-cluster"></a>Conéctese a su clúster de HDInsight

El usuario puede [iniciar sesión en la suscripción a Azure](#sign-in-to-your-azure-subscription) o [vincular un clúster de HDInsight](#link-a-cluster). Use el nombre de usuario y la contraseña de Ambari o credenciales unidas a un dominio para conectarse al clúster de HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure

1. En la barra de menús, vaya a **Ver** > **Ventanas de herramientas** > **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png" alt-text="IntelliJ IDEA muestra Azure Explorer" border="true":::

2. En Azure Explorer, haga clic con el botón derecho en el nodo **Azure** y después seleccione **Iniciar sesión**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png" alt-text="IntelliJ IDEA: clic con el botón derecho en Azure Explorer" border="true":::

3. En el cuadro de diálogo de **inicio de sesión en Azure**, seleccione **Inicio de sesión del dispositivo** y, a continuación, **Iniciar sesión**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png" alt-text="`IntelliJ IDEA azure sign-in device login`" border="true":::

4. En el cuadro de diálogo **Inicio de sesión del dispositivo de Azure**, haga clic en **Copiar y abrir**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png" alt-text="IntelliJ IDEA: inicio de sesión del dispositivo de Azure" border="true":::

5. En la interfaz del explorador, pegue el código y, a continuación, haga clic en **Siguiente**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png" alt-text="`Microsoft enter code dialog for HDI`" border="true":::

6. Escriba sus credenciales de Azure y, a continuación, cierre el explorador.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png" alt-text="`Microsoft enter e-mail dialog for HDI`" border="true":::

7. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Seleccione su suscripción y luego seleccione el botón **Seleccionar**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png" alt-text="Cuadro de diálogo Seleccionar suscripciones" border="true":::

8. En **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight Spark de sus suscripciones.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png" alt-text="IDEA de IntelliJ: vista principal de Azure Explorer" border="true":::

9. Para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados al clúster, puede expandir un nodo de nombre de clúster.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png" alt-text="Azure Explorer: cuentas de almacenamiento" border="true":::

### <a name="link-a-cluster"></a>Vinculación de un clúster

Puede vincular un clúster de HDInsight mediante el nombre de usuario administrado de Apache Ambari. De forma similar, para un clúster de HDInsight unido a un dominio, puede vincular con el dominio y el nombre de usuario, como `user1@contoso.com`. También puede vincular el clúster del servicio de Livy.

1. En la barra de menús, vaya a **Ver** > **Ventanas de herramientas** > **Azure Explorer**.

1. En Azure Explorer, haga clic con el botón derecho en el nodo **HDInsight** y, a continuación, seleccione **Vincular un clúster**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png" alt-text="Azure Explorer: menú contextual de vinculación de clúster" border="true":::

1. Las opciones disponibles en la ventana **Vincular un clúster** variarán dependiendo del valor que seleccione en la lista desplegable **Tipo de recurso de vínculo**.  Escriba estos valores y, a continuación, seleccione **Aceptar**.

    * **Clúster de HDInsight**  
  
        |Propiedad |Value |
        |----|----|
        |Tipo de recurso de vínculo|Seleccione **Clúster de HDInsight** en la lista desplegable.|
        |Nombre o dirección URL del clúster| Escriba el nombre del clúster.|
        |Tipo de autenticación| Dejar como **Autenticación básica**|
        |Nombre de usuario| Escriba el nombre de usuario del clúster, el valor predeterminado es admin.|
        |Contraseña| Escriba la contraseña del nombre de usuario.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png" alt-text="IDEA de IntelliJ: cuadro de diálogo de vinculación de clúster" border="true":::

    * **Livy Service**  
  
        |Propiedad |Value |
        |----|----|
        |Tipo de recurso de vínculo|Seleccione **Livy Service** en la lista desplegable.|
        |Punto de conexión de Livy| Escribir punto de conexión de Livy|
        |Cluster Name| Escriba el nombre del clúster.|
        |Punto de conexión de Yarn|Opcional.|
        |Tipo de autenticación| Dejar como **Autenticación básica**|
        |Nombre de usuario| Escriba el nombre de usuario del clúster, el valor predeterminado es admin.|
        |Contraseña| Escriba la contraseña del nombre de usuario.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png" alt-text="IDEA de IntelliJ: cuadro de diálogo de vinculación de clúster de Livy" border="true":::

1. Puede ver su clúster vinculado en el nodo **HDInsight**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png" alt-text="Azure Explorer: clúster 1 vinculado" border="true":::

1. También puede desvincular un clúster de **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer: clúster desvinculado" border="true":::

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark

Después de crear una aplicación de Scala, puede enviarla al clúster.

1. En el proyecto, vaya a **myApp** > **src** > **main** > **scala** > **myApp**.  Haga clic con el botón derecho en **myApp** y seleccione **Enviar aplicación Spark** (probablemente estará en la parte inferior de la lista).

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png" alt-text="Envío de aplicación Spark a comando HDInsight" border="true":::

2. En el cuadro de diálogo **Enviar aplicación Spark**, seleccione **1. Spark en HDInsight**.

3. En la ventana **Editar configuración**, proporcione los valores siguientes y, a continuación, seleccione **Aceptar**:

    |Propiedad |Value |
    |----|----|
    |Clústeres de Spark (solo Linux)|Seleccione el clúster de HDInsight Spark en el que quiere ejecutar la aplicación.|
    |Seleccione un artefacto para enviarlo|Deje la configuración predeterminada.|
    |Nombre de clase principal|el valor predeterminado es la clase principal del archivo seleccionado. Puede cambiar la clase si selecciona los puntos suspensivos ( **...** ) y elige otra clase.|
    |Configuraciones del trabajo|Puede cambiar las claves o valores predeterminados. Para más información, consulte [API de REST de Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumentos de la línea de comandos|Puede especificar los argumentos divididos por un espacio para la clase principal, si es necesario.|
    |Archivos jar a los que se hace referencia y archivos a los que se hace referencia|puede escribir las rutas de acceso de los archivos y los Jar a los que se hace referencia, si los hubiera. También puede examinar archivos en el sistema de archivos virtual de Azure, que actualmente solo admite el clúster de ADLS Gen 2. Para obtener más información: [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) (Configuración de Apache Spark).  Consulte también [cómo cargar recursos en un clúster](../../storage/blobs/quickstart-storage-explorer.md).|
    |Almacenamiento de carga del trabajo|Expanda para mostrar opciones adicionales.|
    |Tipo de almacenamiento|Seleccione la opción para **usar Azure Blob para cargar** en la lista desplegable.|
    |Cuenta de almacenamiento|Escriba su cuenta de Storage.|
    |Clave de almacenamiento|Escriba su clave de almacenamiento.|
    |Contenedor de almacenamiento|Seleccione su contenedor de almacenamiento en la lista desplegable una vez que se hayan escrito **Cuenta de Storage** y **Clave de almacenamiento**.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Cuadro de diálogo de envío de Spark" border="true":::

4. Seleccione **SparkJobRun** para enviar el proyecto para el clúster seleccionado. La pestaña **Remote Spark Job in Cluster** (Trabajo de Spark remoto en clúster) muestra el progreso de la ejecución del trabajo en la parte inferior. Puede detener la aplicación si hace clic en el botón rojo.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png" alt-text="Ventana de envío de Apache Spark" border="true":::

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depuración de aplicaciones Apache Spark de forma local o remota en un clúster de HDInsight

También se recomienda otra manera de enviar la aplicación Spark al clúster. Puede hacerlo estableciendo los parámetros del IDE de **configuraciones de ejecución o depuración**. Consulte [Depuración de aplicaciones de Apache Spark de forma local o remota en un clúster de HDInsight con Azure Toolkit for IntelliJ mediante SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ

Puede realizar varias operaciones mediante Azure Toolkit for IntelliJ.  La mayoría de las operaciones se inician desde **Azure Explorer**.  En la barra de menús, vaya a **Ver** > **Ventanas de herramientas** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Acceso a la vista de trabajo

1. En Azure Explorer, vaya a **HDInsight** > \<Your Cluster> > **Trabajos**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png" alt-text="Azure Explorer de IntelliJ: nodo de vista de trabajos" border="true":::

2. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Seleccione el nombre de la aplicación para la que desea ver más detalles.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png" alt-text="Detalles de aplicación en vista de trabajos de Spark" border="true":::

3. Para mostrar información básica del trabajo de ejecución, mantenga el mouse sobre el gráfico del trabajo. Para ver el gráfico de fases y la información que todo trabajo genera, seleccione un nodo del gráfico del trabajo.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png" alt-text="Detalles de fases de trabajo en vista de trabajos de Spark" border="true":::

4. Para ver registros usados con frecuencia, como *Driver Stderr*, *Driver Stdout* y *Directory Info*, seleccione la pestaña **Registro**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png" alt-text="Detalles de registro en vista de trabajos de Spark" border="true":::

5. Puede ver la interfaz de usuario del historial de Spark y la interfaz de usuario de YARN (en el nivel de aplicación). Seleccione un enlace en la parte superior de la ventana.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark

1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Abrir IU de historial de Spark**.  
2. Cuando se le solicite, escriba las credenciales de administrador del clúster, que especificó al configurar el clúster.

3. En el panel del servidor de historial de Spark, puede usar el nombre de aplicación para buscar la aplicación que acaba de terminar de ejecutar. En el código anterior, se establece el nombre de la aplicación mediante `val conf = new SparkConf().setAppName("myApp")`. El nombre de la aplicación de Spark es **myApp**.

### <a name="start-the-ambari-portal"></a>Inicio del portal de Ambari

1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Abrir el portal de administración de clústeres (Ambari)** .  

2. Cuando se le pida, escriba las credenciales de administrador para el clúster. Estas son las credenciales que especificó durante el proceso de configuración de clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure

De forma predeterminada, el kit de herramientas de Azure para IntelliJ enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones a las que desea tener acceso.  

1. En Azure Explorer, haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Seleccionar suscripciones**.  

2. En la ventana **Seleccionar suscripciones**, desactive las casillas situadas junto a las suscripciones a las que no desea tener acceso y seleccione **Cerrar**.

## <a name="spark-console"></a>Consola de Spark

Puede ejecutar la consola local de Spark (Scala) o la consola de sesión interactiva de Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Consola local de Spark (Scala)

Asegúrese de que cumple el requisito previo de WINUTILS.EXE.

1. En la barra de menús, vaya a **Ejecutar** > **Editar configuraciones...**

2. En la ventana **Ejecutar/depurar configuraciones**, en el panel izquierdo, vaya a **Apache Spark en HDInsight** >  **[Spark en HDInsight] myApp**.

3. En la ventana principal, seleccione la pestaña **`Locally Run`** .

4. Proporcione los valores siguientes y seleccione **Aceptar**:

    |Propiedad |Value |
    |----|----|
    |Clase principal del trabajo|el valor predeterminado es la clase principal del archivo seleccionado. Puede cambiar la clase si selecciona los puntos suspensivos ( **...** ) y elige otra clase.|
    |Variables de entorno|Asegúrese de que el valor de HADOOP_HOME sea correcto.|
    |Ubicación de WINUTILS.exe|Asegúrese de que la ruta de acceso sea correcta.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/console-set-configuration.png" alt-text="Configuración de conjunto de consola local" border="true":::

5. En el proyecto, vaya a **myApp** > **src** > **main** > **scala** > **myApp**.  

6. En la barra de menús, vaya a **Herramientas** > **Consola de Spark** > **Run Spark Local Console(Scala)** (Ejecutar consola local de Spark (Scala)).

7. Pueden aparecer dos cuadros de diálogo para preguntarle si quiere corregir automáticamente las dependencias. Si es así, seleccione **Autocorrección**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png" alt-text="IDEA de IntelliJ: cuadro de diálogo 1 de corrección automática de Spark" border="true":::

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png" alt-text="IDEA de IntelliJ: cuadro de diálogo 2 de corrección automática de Spark" border="true":::

8. El aspecto de la consola debería ser similar al de la siguiente imagen. En la ventana de la consola, escriba `sc.appName` y presione CTRL + Entrar.  Se muestra el resultado. Para detener la consola local, haga clic en el botón rojo.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/local-console-result.png" alt-text="IDEA de IntelliJ: resultado de la consola local" border="true":::

### <a name="spark-livy-interactive-session-consolescala"></a>Consola de sesión interactiva de Spark Livy (Scala)

1. En la barra de menús, vaya a **Ejecutar** > **Editar configuraciones...**

2. En la ventana **Ejecutar/depurar configuraciones**, en el panel izquierdo, vaya a **Apache Spark en HDInsight** >  **[Spark en HDInsight] myApp**.

3. En la ventana principal, seleccione la pestaña **`Remotely Run in Cluster`** .

4. Proporcione los valores siguientes y seleccione **Aceptar**:

    |Propiedad |Value |
    |----|----|
    |Clústeres de Spark (solo Linux)|Seleccione el clúster de HDInsight Spark en el que quiere ejecutar la aplicación.|
    |Nombre de clase principal|el valor predeterminado es la clase principal del archivo seleccionado. Puede cambiar la clase si selecciona los puntos suspensivos ( **...** ) y elige otra clase.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png" alt-text="Configuración de conjunto de consola interactiva" border="true":::

5. En el proyecto, vaya a **myApp** > **src** > **main** > **scala** > **myApp**.  

6. En la barra de menús, vaya a **Herramientas** > **Consola de Spark** > **Run Spark Livy Interactive Session Console(Scala)** (Ejecutar consola interactiva de Spark Livy (Scala)).

7. El aspecto de la consola debería ser similar al de la siguiente imagen. En la ventana de la consola, escriba `sc.appName` y presione CTRL + Entrar.  Se muestra el resultado. Para detener la consola local, haga clic en el botón rojo.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-result.png" alt-text="IDEA de IntelliJ: resultado de la consola interactiva" border="true":::

### <a name="send-selection-to-spark-console"></a>Envío de la selección a la consola de Spark

Resulta cómodo predecir el resultado del script mediante el envío de código a la consola local o a la consola de sesión interactiva de Livy (Scala). Puede resaltar parte del código en el archivo de Scala y hacer clic con el botón derecho en **Send Selection To Spark Console** (Enviar selección a consola de Spark). El código seleccionado se envía a la consola. El resultado se muestra después del código en la consola. La consola comprueba si hay errores.  

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png" alt-text="Envío de la selección a la consola de Spark" border="true":::

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integración con HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Conexión al clúster de HDInsight ESP con el agente de HDInsight Identity Broker (HIB)

Puede seguir los pasos normales para iniciar sesión en la suscripción de Azure para conectarse a su clúster de HDInsight ESP con el agente de HDInsight Identity Broker (HIB). Después de iniciar sesión, verá la lista de clústeres en Azure Explorer. Para conocer más instrucciones, consulte [Conexión al clúster de HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark con el agente de HDInsight Identity Broker (HIB)

Puede seguir los pasos normales para enviar el trabajo a un clúster de HDInsight ESP con el agente de HDInsight Identity Broker (HIB). Consulte [Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) para más instrucciones.

Cargaremos los archivos necesarios en una carpeta denominada con su cuenta de inicio de sesión y podrá ver la ruta de acceso de carga en el archivo de configuración.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png" alt-text="Ruta de acceso de carga en la configuración" border="true":::

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Consola de Spark en un clúster de HDInsight ESP con el agente de HDInsight Identity Broker (HIB)

Puede ejecutar Spark Local Console (Scala) o ejecutar Spark Livy Interactive Session Console (Scala) en un clúster de HDInsight ESP con el agente de HDInsight Identity Broker (HIB). Consulte [Consola de Spark](#spark-console) para obtener más instrucciones.

   > [!NOTE]  
   > En el caso del clúster de HDInsight ESP con HDInsight Identity Broker (HIB), las opciones para [vincular un clúster](#link-a-cluster) y [depurar las aplicaciones de Apache Spark de forma remota](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) no se admiten actualmente.

## <a name="reader-only-role"></a>Rol de solo lectura

Cuando los usuarios envían trabajos a un clúster con el permiso de rol de solo lectura, se requieren credenciales de Ambari.

### <a name="link-cluster-from-context-menu"></a>Vínculo de clúster desde menú contextual

1. Inicie sesión con la cuenta del rol de solo lectura.

2. En **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight de su suscripción. Los clústeres marcados como **"Role:Reader"** tienen únicamente permiso de solo lectura.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png" alt-text="`IntelliJ Azure Explorer Role:Reader`" border="true":::

3. Haga clic derecho en el clúster con el permiso de rol de solo lectura. Seleccione **Link this cluster** (Vincular este clúster) en el menú contextual para vincular el clúster. Escriba el nombre de usuario y la contraseña de Ambari.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png" alt-text="Azure Explorer de IntelliJ: vínculo de este clúster" border="true":::

4. Si el clúster se vinculó correctamente, se actualizará HDInsight.
   La fase del clúster quedará vinculada.
  
    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png" alt-text="Azure Explorer de IntelliJ: cuadro de diálogo de vínculo realizado" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Vínculo de clúster mediante la expansión del nodo de trabajos

1. Haga clic en el nodo **Trabajos** y aparecerá la ventana emergente **Cluster Job Access Denied** (Se denegó el acceso al trabajo del clúster).

2. Haga clic en **Link this cluster** (Vincular este clúster) para vincular el clúster.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png" alt-text="Cuadro de diálogo de denegación de acceso al trabajo del clúster" border="true":::

### <a name="link-cluster-from-rundebug-configurations-window"></a>Vínculo de clúster desde la ventana de configuraciones de ejecución o depuración

1. Cree una configuración de HDInsight. Luego, seleccione **Remotely Run in Cluster** (Ejecutar de forma remota en clúster).

2. Seleccione un clúster, que tiene permiso de rol de solo lectura para **clústeres de Spark (solo Linux)** . Aparece un mensaje de advertencia. Puede hacer clic en **Link this cluster** (Vincular este clúster) para vincular el clúster.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-configuration.png" alt-text="IDEA de IntelliJ: creación de configuración de ejecución o depuración" border="true":::

### <a name="view-storage-accounts"></a>Visualización de cuentas de almacenamiento

* Para los clústeres con permiso de rol de solo lectura, haga clic en el nodo **Cuentas de almacenamiento** y aparecerá la ventana emergente **Storage Access Denied** (Se denegó el acceso al almacenamiento). Seleccione **Abrir Explorador de Azure Storage** para abrir el explorador de Storage.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png" alt-text="`IntelliJ IDEA Storage Access Denied`" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png" alt-text="IDEA de IntelliJ: botón de acceso denegado a almacenamiento" border="true":::

* Para los clústeres vinculados, haga clic en el nodo **Cuentas de almacenamiento** y aparecerá la ventana emergente **Storage Access Denied** (Se denegó el acceso al almacenamiento). Puede hacer clic en **Open Azure Storage** (Abrir Azure Storage) para abrir el Explorador de Storage.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png" alt-text="`IntelliJ IDEA Storage Access Denied2`" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png" alt-text="IDEA de IntelliJ: botón de acceso denegado a almacenamiento 2" border="true":::

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Conversión de las aplicaciones IntelliJ IDEA existentes para usar el kit de herramientas de Azure para IntelliJ

Puede convertir las aplicaciones Spark en Scala existentes creadas en IntelliJ IDEA para que sean compatibles con el kit de herramientas de Azure para IntelliJ. Esto le permitirá utilizar el complemento para enviar las aplicaciones a un clúster de HDInsight Spark.

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo `.iml` asociado.

2. En el nivel raíz, hay un elemento de **module** similar al siguiente texto:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Edite el elemento para agregar `UniqueKey="HDInsightTool"` de forma que el elemento de **module** sea similar al siguiente texto:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Guarde los cambios. La aplicación ahora debe ser compatible con el kit de herramientas de Azure para IntelliJ. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en Proyecto. El menú emergente ahora debería tener la opción **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine el clúster que creó mediante los siguientes pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En el cuadro **Búsqueda** en la parte superior, escriba **HDInsight**.

1. Seleccione **Clústeres de HDInsight** en **Servicios**.

1. En la lista de clústeres de HDInsight que aparece, seleccione el botón **...** situado junto al clúster que ha creado en este artículo.

1. Seleccione **Eliminar**. Seleccione **Sí**.

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal elimina un clúster de HDInsight" border="true":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a usar el complemento Azure Toolkit for IntelliJ para desarrollar aplicaciones de Apache Spark escritas en [Scala](https://www.scala-lang.org/). Después, las ha enviado a un clúster de HDInsight Spark directamente desde el entorno de desarrollo integrado (IDE) de IntelliJ. En el siguiente artículo podrá ver cómo extraer en una herramienta de análisis de BI, como Power BI, los datos que registró en Apache Spark.

> [!div class="nextstepaction"]
> [Análisis de datos de Apache Spark mediante Power BI](apache-spark-use-bi-tools.md)

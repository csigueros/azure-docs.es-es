---
title: Envío de trabajos desde Herramientas de R para Visual Studio en Azure HDInsight
description: Envío de trabajos de R desde la máquina local de Visual Studio hasta un clúster de HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/19/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8cc2cdfc123d0a02008859d2c6ecf8d20b763495
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112295988"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Envío de trabajos desde Herramientas de R para Visual Studio

[!INCLUDE [retirement banner](../includes/ml-services-retirement.md)]

[Herramientas de R para Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) es una extensión gratuita de código abierto de las ediciones Community (gratuita), Professional y Enterprise de [Visual Studio 2017](https://www.visualstudio.com/downloads/) y [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) o superior. RTVS no está disponible para [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019).

RTVS mejora el flujo de trabajo de R al ofrecer herramientas como la [ventana R interactivo](/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (finalización de código), [visualización de trazados](/visualstudio/rtvs/visualizing-data) mediante bibliotecas de R como ggplot2 y ggviz, [depuración de código de R](/visualstudio/rtvs/debugging) y muchas más.

## <a name="set-up-your-environment"></a>Configurar el entorno

1. Instale [Herramientas de R para Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png" alt-text="Instalación de RTVS en Visual Studio de 2017" border="true":::

2. Seleccione la carga de trabajo *Aplicaciones de ciencia de datos y de análisis* y luego seleccione las opciones **Compatibilidad con el lenguaje R**, **Compatibilidad de runtime con las herramientas de desarrollo de R** y **Microsoft R Client**.

3. Debe tener las claves públicas y privadas para la autenticación de SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale [ML Server](/previous-versions/machine-learning-server/install/r-server-install-windows) en su máquina. ML Server proporciona las funciones [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) y `RxSpark`.

5. Instale [PuTTY](https://www.putty.org/) para proporcionar un contexto de proceso para ejecutar funciones de `RevoScaleR` desde el cliente local hasta el clúster de HDInsight.

6. Tiene la opción de aplicar la Configuración de ciencia de datos al entorno de Visual Studio, lo que proporciona un nuevo diseño para el área de trabajo de las herramientas de R.
   1. Para guardar la configuración actual de Visual Studio, use el comando **Herramientas > Importar y exportar configuraciones** y, luego, seleccione **Exportar la configuración de entorno seleccionada** y especifique un nombre de archivo. Para restaurar esa configuración, use el mismo comando y seleccione **Importar la configuración de entorno seleccionada**.

   2. Vaya al elemento de menú **Herramientas de R** y seleccione **Configuración de ciencia de datos...**.

       :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png" alt-text="Configuración de ciencia de datos de Visual Studio" border="true":::

      > [!NOTE]  
      > Mediante el enfoque del paso 1, también puede guardar y restaurar su diseño de ciencia de datos personalizado en lugar de repetir el comando **Configuración de ciencia de datos**.

## <a name="execute-local-r-methods"></a>Ejecución de métodos de R locales

1. Cree el clúster de ML Services de HDInsight.
2. Instale la [extensión RTVS](/visualstudio/rtvs/installation).
3. Descargue el [archivo ZIP de ejemplos](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Abra `examples/Examples.sln` para iniciar la solución en Visual Studio.
5. Abra el archivo `1-Getting Started with R.R` de la carpeta de la solución `A first look at R`.
6. Desde la parte superior del archivo, presione Ctrl + Entrar para enviar cada línea, de una en una, a la ventana de R interactivo. Puede que algunas líneas tarden un rato mientras instalan los paquetes.
    * Existe la alternativa de seleccionar todas las líneas del archivo de R (Ctrl+A) y luego ejecutar todo (Ctrl+Entrar) o seleccionar el icono Execute Interactive (Ejecutar Interactivo) en la barra de herramientas.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png" alt-text="Ejecución interactiva de Visual Studio" border="true":::

7. Después de ejecutar todas las líneas del script, debería ver una salida similar a la siguiente:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png" alt-text="Herramientas de R del área de trabajo de Visual Studio" border="true":::

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Envío de trabajos a un clúster de HDInsight ML Services

Mediante una instancia de Microsoft ML Server/Microsoft R Client desde un equipo Windows equipado con PuTTY, puede crear un contexto de proceso que ejecute funciones `RevoScaleR` distribuidas desde el cliente local hasta el clúster de HDInsight. Use `RxSpark` para crear el contexto de proceso y especifique el nombre de usuario, el nodo perimetral del clúster de Apache Hadoop, los modificadores de SSH, etc.

1. La dirección del nodo perimetral de ML Services en HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net`, donde `CLUSTERNAME` es el nombre del clúster de ML Services.

1. Pegue el código siguiente en la ventana de R interactivo en Visual Studio y modifique los valores de las variables de configuración para que coincidan con el entorno.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png" alt-text="Configuración de Apache Spark: el contexto" border="true":::

1. Ejecute los siguientes comandos en la ventana de R interactivo:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Debería ver una salida similar a la siguiente:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png" alt-text="Ejecución correcta de comandos de rx" border="true":::
a
1. Compruebe que el comando `rxHadoopCopy` copió correctamente el archivo `people.json` de la carpeta de datos de ejemplo en la carpeta `/user/RevoShare/newUser` recién creada:

    1. En el panel del clúster de HDInsight ML Services en Azure, seleccione **Cuentas de almacenamiento** en el menú izquierdo.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png" alt-text="Cuentas de almacenamiento para Azure HDInsight" border="true":::

    2. Seleccione la cuenta de almacenamiento predeterminada del clúster y anote el nombre del contenedor o directorio.

    3. Seleccione **Contenedores** en el menú izquierdo del panel de la cuenta de almacenamiento.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png" alt-text="Contenedores de almacenamiento para Azure HDInsight" border="true":::

    4. Seleccione el nombre del contenedor del clúster, vaya a la carpeta **user** (puede que tenga que hacer clic en *Cargar más* en la parte inferior de la lista), luego seleccione *RevoShare* y, a continuación, **newUser**. El archivo `people.json` se debe mostrar en la carpeta `newUser`.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png" alt-text="Ubicación de la carpeta de archivos copiada de HDInsight" border="true":::

1. Cuando haya terminado de usar el contexto de Apache Spark actual, debe detenerlo. No puede ejecutar varios contextos a la vez.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Opciones de contexto de proceso para ML Services en HDInsight](r-server-compute-contexts.md)
* La [combinación de ScaleR y SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) proporciona un ejemplo de predicciones de retrasos en los vuelos.
---
title: 'Hue con Hadoop en clústeres de HDInsight basados en Linux: Azure'
description: Aprenda a instalar Hue en clústeres de HDInsight Linux y a usar tunelización para enrutar las solicitudes a Hue. Use Hue para examinar el almacenamiento y ejecutar Pig o Hive.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 8f0346a3e9a2d21dc5959df51d894daef8a5a23f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284210"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalación y uso de Hue en clústeres de Hadoop para HDInsight

Aprenda a instalar Hue en clústeres de HDInsight Linux y a usar tunelización para enrutar las solicitudes a Hue.

## <a name="what-is-hue"></a>¿Qué es Hue?

Hue es un conjunto de aplicaciones web que se usan para interactuar con un clúster de Apache Hadoop. Puede usar Hue para examinar el almacenamiento asociado a un clúster de Hadoop (WASB, en el caso de clústeres de HDInsight), ejecutar trabajos de Hive y scripts de Pig, etc. Los siguientes componentes son compatibles con la instalación de Hue en un clúster de Hadoop para HDInsight.

* Editor Beeswax de Hive
* Apache Pig
* Administrador de la tienda de metadatos
* Apache Oozie
* FileBrowser (que se comunica con el contenedor predeterminado WASB)
* Explorador web

> [!WARNING]  
> Los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [Página de preguntas y respuestas de Microsoft sobre HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com). Los proyectos de Apache también tienen sitios de proyecto en [https://apache.org](https://apache.org), como por ejemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalación de Hue mediante acciones de script

Utilice la información de la tabla siguiente para la acción de script. Para obtener instrucciones específicas sobre el uso de las acciones de script, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

> [!NOTE]  
> Para instalar Hue en clústeres de HDInsight, el tamaño recomendado de nodo principal es como mínimo A4 (8 núcleos, 14 gigabytes de memoria).

|Propiedad |Value |
|---|---|
|Tipo de script:|- Personalizado|
|Nombre|Instalación de Hue|
|URI de script de Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Tipos de nodo:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Use Hue con clústeres de HDInsight

Solo puede tener una cuenta de usuario con matiz en clústeres normales. Para el acceso de varios usuarios, habilite [Enterprise Security Package](./domain-joined/hdinsight-security-overview.md) en el clúster. La tunelización de SSH es la única forma de obtener acceso a la matiz en el clúster cuando se está ejecutando. La tunelización a través de SSH permite al tráfico ir directamente al nodo principal del clúster en el que se ejecuta Hue. Cuando termine de aprovisionar el clúster, siga estos pasos para usar Hue en un clúster de HDInsight.

> [!NOTE]  
> Se recomienda utilizar el explorador web de Firefox para seguir las instrucciones siguientes.

1. Consulte la información de [Uso de la tunelización SSH para acceder a la interfaz de usuario web de Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md) para crear un túnel SSH desde el sistema cliente al clúster de HDInsight y luego configurar el explorador web para usar el túnel SSH como proxy.

1. Use el [comando SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) para conectarse al clúster. Modifique el comando siguiente: reemplace CLUSTERNAME por el nombre del clúster y, luego, escriba el comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Una vez conectado, use el comando siguiente para obtener el nombre de dominio completo del nodo principal primario:

    ```bash
    hostname -f
    ```

    Devolverá un nombre similar al siguiente:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Este es el nombre de host del nodo principal primario donde se encuentra el sitio web Hue.

1. Use el explorador para abrir el portal de Hue en `http://HOSTNAME:8888`. Reemplace HOSTNAME por el nombre obtenido en el paso anterior.

   > [!NOTE]  
   > Al iniciar sesión por primera vez, se le pedirá que cree una cuenta para iniciar sesión en el portal de Hue. Las credenciales que especifique aquí se limitarán al portal y no están relacionadas con las credenciales de administrador o de usuario SSH que especificó al aprovisionar el clúster.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png" alt-text="Ventana de inicio de sesión del portal de Hue en HDInsight":::

### <a name="run-a-hive-query"></a>Ejecución de una consulta de Hive

1. En el portal de Hue, seleccione **Query Editors** (Editores de consultas), y, a continuación, seleccione **Hive** para abrir el editor de Hive.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png" alt-text="Uso del editor de Hive del portal de Hue de HDInsight":::

2. En la pestaña **Assist** (Asistencia), en **Database** (Base de datos), debería de ver **hivesampletable**. Se trata de una tabla de ejemplo que se incluye con todos los clústeres de Hadoop en HDInsight. Escriba una consulta de ejemplo en el panel derecho y vea el resultado en la pestaña **Results** (resultados) en el panel debajo, como se muestra en la captura de pantalla.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png" alt-text="Consulta de Hive del portal de Hue de HDInsight":::

    También puede usar la pestaña **Chart** para ver una representación visual del resultado.

### <a name="browse-the-cluster-storage"></a>Examinar el almacenamiento del clúster

1. En el portal de Hue, seleccione **File Browser** (explorador de archivos) en la esquina superior derecha de la barra de menús.
2. De forma predeterminada se abre el explorador de archivos en el directorio **/user/myuser** . Seleccione la barra oblicua que se encuentra antes del directorio del usuario en la ruta de acceso para ir a la raíz del contenedor de Azure Storage asociado con el clúster.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png" alt-text="Explorador de archivos del portal de Hue en HDInsight":::

3. Haga clic con el botón derecho en un archivo o carpeta para ver las operaciones disponibles. Use el botón **Cargar** situado en la esquina derecha para cargar archivos en el directorio actual. Use el botón **Nuevo** para crear nuevos archivos o directorios.

> [!NOTE]  
> El explorador de archivos de Hue solo puede mostrar el contenido del contenedor predeterminado asociado con el clúster de HDInsight. Cualquier cuenta o contenedor de almacenamiento adicional asociados con el clúster no serán accesibles mediante el explorador de archivos. Sin embargo, los contenedores adicionales asociados con el clúster siempre serán accesibles para los trabajos de Hive. Por ejemplo, si escribe el comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` en el editor de Hive, también podrá ver el contenido de los contenedores adicionales. En este comando, **newcontainer** no es el contenedor predeterminado asociado a un clúster.

## <a name="important-considerations"></a>Consideraciones importantes

1. El script que se usó para instalar Hue lo instala solo en el nodo principal primario del clúster.

1. Durante la instalación, se reinician varios servicios de Hadoop (HDFS, YARN, MR2, Oozie) para actualizar la configuración. Cuando el script finaliza la instalación de Hue, puede tardar algún tiempo hasta que otros servicios de Hadoop se inicien. Esto podría afectar inicialmente al rendimiento de Hue. Una vez que todos los servicios se inician, la funcionalidad de Hue será total.

1. La matiz no entiende los trabajos de Apache Tez, que es el valor predeterminado actual de Hive. Si desea usar MapReduce como el motor de ejecución de Hive, actualice el script para usar el comando siguiente en el script:

   `set hive.execution.engine=mr;`

1. Con los clústeres de Linux, se puede dar el caso de que los servicios se ejecutan en el nodo principal primario mientras Resource Manager se ejecuta en el secundario. Este escenario podría producir errores (que se muestra a continuación) cuando se usa Hue para ver detalles de trabajos de ejecución en el clúster. De todas formas, puede ver los detalles del trabajo una vez que el trabajo se complete.

   :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png" alt-text="Mensaje de error de ejemplo del portal de Hue":::

   Este es un problema conocido. Como alternativa, modifique Ambari para que la instancia de Resource Manager que está activa también se ejecute en el nodo principal primario.

1. Hue entiende WebHDFS, mientras que los clústeres de HDInsight usan Azure Storage mediante `wasbs://`. Por lo tanto, el script personalizado que se usa con la acción de script instala WebWasb, que es un servicio compatible con WebHDFS para hablar con WASB. Así que aunque el portal de Hue dice HDFS en lugares (como cuando se mueve el mouse sobre el **Explorador de archivos**), se debe interpretar como WASB.

## <a name="next-steps"></a>Pasos siguientes

[Personalización de clústeres de HDInsight con acciones de script](hdinsight-hadoop-customize-cluster-linux.md) 
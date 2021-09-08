---
title: 'Administración de clústeres de Machine Learning Services en HDInsight: Azure'
description: Aprenda a administrar varias tareas en un clúster de ML Services en Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/19/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 476d108b7edcec30d38587ba128080c99ae04fd9
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299328"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Administración de clústeres de ML Services en Azure HDInsight

[!INCLUDE [retirement banner](../includes/ml-services-retirement.md)]

En este artículo, aprenderá a administrar un clúster de ML Services en Azure HDInsight para realizar tareas como agregar varios usuarios simultáneos, conectarse de forma remota a un cliente o un clúster de ML Services, cambiar el contexto de proceso, etc.

## <a name="prerequisites"></a>Prerrequisitos

* Un clúster de ML Services en HDInsight. Consulte el artículo sobre la [Creación de clústeres de Apache Hadoop mediante Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) y seleccione **ML Services** como **Tipo de clúster**.

* Un cliente de Secure Shell (SSH): el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para obtener más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Habilitación de varios usuarios simultáneos

Para habilitar varios usuarios simultáneos para un clúster de ML Services en HDInsight agregando más usuarios al nodo perimetral en el que se ejecuta la versión de comunidad de RStudio. Al crear un clúster de HDInsight, es preciso especificar dos usuarios, un usuario HTTP y un usuario SSH:

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users1.png" alt-text="Parámetros de inicio de sesión de HDI en Azure Portal" border="true":::

- **Nombre de usuario de inicio de sesión del clúster**: un usuario HTTP para la autenticación a través de la puerta de enlace de HDInsight que se usa para proteger los clústeres de HDInsight que ha creado. Este usuario HTTP se utiliza para acceder a la interfaz de usuario de Apache Ambari, a la interfaz de usuario de Apache Hadoop YARN y a otros componentes de la interfaz de usuario.
- **Nombre de usuario de Secure Shell (SSH)** : un usuario SSH para acceder al clúster a través de Secure Shell. Este es un usuario del sistema Linux para todos los nodos principales, nodos de trabajo y nodos perimetrales. Por consiguiente, puede usar Secure Shell para acceder a cualquiera de los nodos de un clúster remoto.

La versión de comunidad de R Studio Server que se usa en el clúster de ML Services en HDInsight acepta únicamente el nombre de usuario y la contraseña de Linux como mecanismo de inicio de sesión. No admite tokens de paso. Por lo tanto, al tratar de obtener acceso a R Studio por primera vez en un clúster de ML Services, debe iniciar sesión dos veces.

- Primero inicie sesión con las credenciales de usuario HTTP a través de la puerta de enlace de HDInsight.

- Luego, use las credenciales de usuario de SSH para iniciar sesión en RStudio.
  
Actualmente, una cuenta de usuario SSH solo se puede crear al aprovisionar un clúster de HDInsight. Por tanto, para que varios usuarios puedan acceder al clúster de ML Services en HDInsight, debe crear usuarios adicionales en el sistema Linux.

Dado que RStudio se ejecuta en el nodo perimetral del clúster, hay que dar varios pasos:

1. Uso del usuario de SSH creado para iniciar sesión en el nodo perimetral
2. Agregar más usuarios de Linux al nodo perimetral.
3. Utilizar la versión de comunidad de RStudio con el usuario creado.

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Paso 1: Uso del usuario de SSH creado para iniciar sesión en el nodo perimetral

Siga las instrucciones que se proporcionan en [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) para acceder al nodo perimetral. La dirección del nodo perimetral para el clúster de ML Services en HDInsight es `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Paso 2: Adición de más usuarios de Linux al nodo perimetral

Para agregar un usuario al nodo perimetral, ejecute los comandos:

```bash
# Add a user 
sudo useradd <yournewusername> -m

# Set password for the new user
sudo passwd <yournewusername>
```

En la captura de pantalla siguiente se muestra el resultado.

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users2.png" alt-text="Captura de pantalla de salida de usuarios simultáneos" border="true":::

Cuando se le pida la "contraseña actual de Kerberos", presione **Entrar** para ignorarlo. La opción `-m` del comando `useradd` indica que el sistema creará una carpeta particular para el usuario, que se requiere para la versión de comunidad de RStudio.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Paso 3: Uso de la versión de comunidad de RStudio con el usuario creado

Acceda a RStudio desde `https://CLUSTERNAME.azurehdinsight.net/rstudio/`. La primera vez que inicie sesión después de crear el clúster, escriba las credenciales de administrador del clúster seguidas de las credenciales del usuario SSH que ha creado. Si no se trata del primer inicio de sesión, escriba solo las credenciales del usuario SSH que ha creado.

También puede iniciar sesión con las credenciales originales (de forma predeterminada es *sshuser*) simultáneamente desde otra ventana del explorador.

Tenga en cuenta también que los usuarios recién agregados no tienen privilegios raíz en el sistema Linux, pero tienen el mismo acceso a todos los archivos en el almacenamiento HDFS y WASB remoto.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Conexión de forma remota a Microsoft ML Services

Puede configurar el acceso al contexto de proceso de HDInsight Spark desde una instancia remota de ML Client que se ejecute en un equipo de escritorio. Para ello, debe especificar las opciones (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches y sshProfileScript) al definir el contexto de proceso RxSpark en el equipo de escritorio. Por ejemplo:

```r
myNameNode <- "default"
myPort <- 0

mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
mySshUsername  <- '<sshuser>'# HDI SSH username
mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

mySparkCluster <- RxSpark(
    hdfsShareDir = myhdfsShareDir,
    shareDir     = myShareDir,
    sshUsername  = mySshUsername,
    sshHostname  = mySshHostname,
    sshSwitches  = mySshSwitches,
    sshProfileScript = '/etc/profile',
    nameNode     = myNameNode,
    port         = myPort,
    consoleOutput= TRUE
)
```

Para más información, consulte la sección "Using Microsoft Machine Learning Server as a Apache Hadoop Client" (Uso de Microsoft Machine Learning Server como cliente de Apache Hadoop) de [How to use RevoScaleR in a Apache Spark compute context](/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Uso de RevoScaleR en un contexto de proceso de Apache Spark)

## <a name="use-a-compute-context"></a>Usar un contexto de proceso

Un contexto de proceso le permite controlar si un cálculo se realizará de forma local en el nodo perimetral o se distribuirá en los nodos del clúster de HDInsight.  Para ver un ejemplo de cómo configurar un contexto de proceso con RStudio Server, consulte [Execute an R script on an ML Services cluster in Azure HDInsight using RStudio Server](machine-learning-services-quickstart-job-rstudio.md) (Ejecutar un script de R en un clúster de servicios de ML en Azure HDInsight mediante RStudio Server).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir el código de R a varios nodos

Con ML Services en HDInsight puede seleccionar el código de R existente y ejecutarlo en varios nodos del clúster mediante `rxExec`. Esta función es útil cuando se realiza un barrido de parámetros o simulaciones. El siguiente código es un ejemplo de cómo usar `rxExec`:

```r
rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
```

Si aún usa el contexto de Spark, este comando devuelve el valor nodename de los nodos de trabajo en los que se ejecuta el código `(Sys.info()["nodename"])`. Por ejemplo, en un clúster de cuatro nodos, lo esperable es recibir una salida similar al siguiente fragmento de código:

```r
$rxElem1
    nodename
"wn3-mymlser"

$rxElem2
    nodename
"wn0-mymlser"

$rxElem3
    nodename
"wn3-mymlser"

$rxElem4
    nodename
"wn3-mymlser"
```

## <a name="access-data-in-apache-hive-and-parquet"></a>Acceso a datos en Apache Hive y Parquet

ML Services de HDInsight permite el acceso directo a los datos en Hive y Parquet para usarlos en funciones ScaleR en el contexto de proceso de Spark. Estas funcionalidades están disponibles por medio de las nuevas funciones de origen de datos ScaleR llamadas RxHiveData y RxParquetData. Estas emplean Spark SQL para cargar datos directamente en un elemento DataFrame de Spark para analizarlos con ScaleR.

El siguiente código proporciona código de ejemplos relativo al uso de las nuevas funciones:

```r
#Create a Spark compute context:
myHadoopCluster <- rxSparkConnect(reset = TRUE)

#Retrieve some sample data from Hive and run a model:
hiveData <- RxHiveData("select * from hivesampletable",
                       colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)

#Retrieve some sample data from Parquet and run a model:
rxHadoopMakeDir('/share')
rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
pqData <- RxParquetData('/share/claimsParquet',
                        colInfo = list(
                            age    = list(type = "factor"),
                            car.age = list(type = "factor"),
                            type = list(type = "factor")
                        ) )
rxGetInfo(pqData, getVarInfo = TRUE)

rxNaiveBayes(type ~ age + cost, data = pqData)

#Check on Spark data objects, cleanup, and close the Spark session:
lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Para más información acerca del uso de estas nuevas funciones, consulte la ayuda en línea de ML Services mediante los comandos `?RxHivedata` y `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalación de paquetes de R adicionales en el clúster

### <a name="to-install-r-packages-on-the-edge-node"></a>Procedimiento para instalar paquetes de R adicionales en el nodo perimetral

Si quiere instalar paquetes de R adicionales en el nodo perimetral, puede usar `install.packages()` directamente desde la consola de R cuando se conecte al nodo perimetral a través de SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Procedimiento para instalar paquetes de R en el nodo de trabajo

Para instalar paquetes de R en los nodos de trabajo del clúster, debe usar una acción de script. Las acciones de script son scripts de Bash que se usan para realizar cambios en la configuración del clúster de HDInsight o para instalar software adicional, como paquetes de R adicionales. 

> [!IMPORTANT]  
> Usar Acciones de script para instalar paquetes de R adicionales solo se permite una vez creado el clúster. No use este procedimiento durante la creación del clúster, ya que el script cuenta con que ML Services esté completamente configurado.

1. Siga los pasos de [Personalización de clústeres mediante la acción de script](../hdinsight-hadoop-customize-cluster-linux.md).

3. En **Enviar acción de script**, proporcione la siguiente información:

   * En **Tipo de script**, seleccione **Personalizada**.

   * En **Nombre**, especifique un nombre para la acción de script.

     * En **URI de script de Bash**, escriba `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Se trata del script que instala los paquetes de R adicionales en el nodo de trabajo.

   * Active solo la casilla de verificación de **Trabajo**.

   * **Parámetros**: paquetes de R que se van a instalar. Por ejemplo: `bitops stringr arules`

   * Active la casilla de verificación de **Persist this script action** (Continuar con esta acción de script).  

   > [!NOTE]
   > 1. De forma predeterminada, todos los paquetes de R se instalan desde una instantánea del repositorio Microsoft MRAN coherente con la versión de ML Server que se ha instalado. Si desea instalar las versiones más recientes de los paquetes, corre el riesgo de que se produzca incompatibilidad. Sin embargo, para realizar este tipo de instalación es preciso especificar `useCRAN` como primer elemento de la lista de paquetes, por ejemplo `useCRAN bitops, stringr, arules`.  
   > 2. Algunos paquetes de R requieren otras bibliotecas de sistema de Linux. Por comodidad, ML Services de HDInsight está preinstalado con las dependencias que necesitan los 100 paquetes de R más populares. Sin embargo, si los paquetes de R que instale necesitan otras bibliotecas, debe descargar el script base usado aquí y realizar los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado a un contenedor de blobs público en Azure Storage y usar el script modificado para instalar los paquetes.
   >    Para más información sobre cómo desarrollar acciones de script, consulte [Desarrollo de acciones de script](../hdinsight-hadoop-script-actions-linux.md).

   :::image type="content" source="./media/r-server-hdinsight-manage/submit-script-action.png" alt-text="Envío de acción de script en Azure Portal" border="true":::

4. Seleccione **Crear** para ejecutar el script. Una vez que finalice el script, los paquetes de R estarán disponibles en todos los nodos de trabajo.

## <a name="next-steps"></a>Pasos siguientes

* [Operacionalización de clústeres de Machine Learning Services en HDInsight](r-server-operationalize.md)
* [Opciones de contexto de proceso para un clúster de ML Services en HDInsight](r-server-compute-contexts.md)
* [Opciones de Azure Storage para un clúster de ML Services en HDInsight](r-server-storage.md)
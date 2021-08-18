---
title: 'Puesta en funcionamiento de ML Services en HDInsight: Azure'
description: Aprenda a poner en funcionamiento un modelo de datos para realizar predicciones con ML Services en Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/27/2018
ROBOTS: NOINDEX
ms.openlocfilehash: eb8114da913347309e6e8ad263c0bb7fcf56790f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299292"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Puesta en funcionamiento de clústeres de ML Services en Azure HDInsight

[!INCLUDE [retirement banner](../includes/ml-services-retirement.md)]

Después de haber usado el clúster de ML Services en HDInsight para completar el modelado de datos, puede usar el modelo para realizar predicciones. En este artículo se ofrecen instrucciones sobre realizar esta tarea.

## <a name="prerequisites"></a>Prerrequisitos

* Un clúster de ML Services en HDInsight. Consulte el artículo sobre la [Creación de clústeres de Apache Hadoop mediante Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) y seleccione **ML Services** como **Tipo de clúster**.

* Un cliente de Secure Shell (SSH): el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Poner en funcionamiento el clúster de ML Services con la configuración de un solo equipo

> [!NOTE]  
> Los pasos siguientes se pueden aplicar a R Server 9.0 y ML Server 9.1. Para ML Server 9.3, consulte [Use the administration tool to manage the operationalization configuration](/machine-learning-server/operationalize/configure-admin-cli-launch) (Usar la herramienta de administración para administrar la configuración de puesta en marcha).

1. Uso de SSH en el nodo perimetral.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Para obtener instrucciones sobre cómo utilizar SSH con Azure HDInsight, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Cambie el directorio para la versión pertinente y use sudo en el archivo DLL de dotnet: 

    - Para Microsoft ML Server 9.1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Para Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. Se muestran las diferentes opciones que tiene. Elija la primera opción, tal como se muestra en la siguiente captura de pantalla, con el fin de **configurar ML Server para la puesta en marcha**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-1.png" alt-text="Selección en la utilidad de administración de R Server" border="true":::

1. Ahora verá la opción para elegir cómo desea poner en marcha ML Server. Entre las opciones, elija la primera de ellas escribiendo **A**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-2.png" alt-text="Puesta en marcha en la utilidad de administración de R Server" border="true":::

1. Cuando se le solicite, escriba dos veces la contraseña de un usuario administrador local.

1. Debería ver resultados que indican que la operación se realizó correctamente. También se le pedirá que seleccione otra opción del menú. Seleccione E para volver al menú principal.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-3.png" alt-text="Operación correcta en la utilidad de administración de R Server" border="true":::

1. Como paso opcional, puede realizar comprobaciones de diagnóstico mediante la ejecución de una prueba de diagnóstico tal y como se muestra a continuación:

    a. En el menú principal, seleccione **6** para ejecutar pruebas de diagnóstico.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic1.png" alt-text="Diagnóstico en la utilidad de administración de R Server" border="true":::

    b. En el menú de pruebas de diagnóstico, seleccione **A**. Cuando se le solicite, escriba la contraseña que ha proporcionado para el usuario administrador local.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic2.png" alt-text="Prueba en la utilidad de administración de R Server" border="true":::

    c. Compruebe que el resultado muestra que el estado general es Pass (Aprobado).

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic3.png" alt-text="Superación de prueba en la utilidad de administración de R Server" border="true":::

    d. En las opciones de menú que se muestran, escriba **E** para volver al menú principal y, luego, escriba **8** para salir de la utilidad de administración.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Retrasos prolongados al consumir el servicio web en Apache Spark

Si se producen retrasos prolongados al intentar consumir un servicio web creado con funciones de mrsdeploy en un contexto de proceso de Apache Spark, puede que necesite agregar algunas carpetas que falten. La aplicación de Spark pertenece a un usuario llamado "*rserve2*" cada vez que se invoque desde un servicio web mediante las funciones de mrsdeploy. Para evitar este problema:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

En esta fase, la configuración de la puesta en operación está completa. Ahora puede usar el paquete `mrsdeploy` en RClient para conectarse a la operacionalización en el nodo perimetral y empezar a usar sus características, como la [ejecución remota](/machine-learning-server/r/how-to-execute-code-remotely) y los [servicios web](/machine-learning-server/operationalize/concept-what-are-web-services). Dependiendo de si el clúster se configura en una red virtual o no, puede que necesite configurar la tunelización de reenvío del puerto mediante el inicio de sesión SSH. En las secciones siguientes se explica cómo configurar este túnel.

### <a name="ml-services-cluster-on-virtual-network"></a>Clúster de ML Services en una red virtual

Asegúrese de que permite que el tráfico pase a través del puerto 12800 al nodo perimetral. De esa forma, puede usar el nodo perimetral para conectarse a la característica de operacionalización.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Si `remoteLogin()` no se puede conectarse al nodo perimetral, pero se posible usar SSH en dicho nodo, tendrá que comprobar si se ha configurado correctamente la regla para permitir el tráfico en el puerto 12800. Si el problema no desaparece, puede solucionarlo mediante la configuración de la tunelización de reenvío del puerto a través de SSH. Para obtener instrucciones, consulte la siguiente sección:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>El clúster de ML Services no se configura en una red virtual

Si el clúster no está configurado en la red virtual o si tiene problemas con la conectividad a través de la red virtual, puede utilizar la tunelización de reenvío del puerto de SSH:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Cuando la sesión SSH esté activa, el tráfico del puerto 12800 de la máquina local se reenviará al puerto 12800 del nodo perimetral mediante la sesión de SSH. Asegúrese de utilizar `127.0.0.1:12800` en el método `remoteLogin()`. Con esta acción, se iniciará sesión en la operacionalización del nodo perimetral mediante el enrutamiento de puerto.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Escala de nodos de proceso operacionalizados en nodos de trabajo de HDInsight

Para escalar los nodos de proceso, retire primero los nodos de trabajo y, luego, configure los nodos de proceso en los nodos de trabajo retirados.

### <a name="step-1-decommission-the-worker-nodes"></a>Paso 1: Retirada de los nodos de trabajo

El clúster de ML Services no se administra a través de [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Si los nodos de trabajo no se retiran, el Administrador de recursos de YARN no funcionará según lo previsto, ya que no reconocerá los recursos que utiliza el servidor. Para evitar esto, se recomienda la retirada de los nodos de trabajo antes de escalar horizontalmente los nodos de proceso.

Siga estos pasos para retirar nodos de trabajo:

1. Inicie sesión en la consola de Ambari del clúster y haga clic en la pestaña **Hosts**.

1. Seleccione los nodos de trabajo (para retirarlos).

1. Haga clic en **Acciones** > **Hosts seleccionados** > **Hosts** > **Activar modo de mantenimiento**. Por ejemplo, en la siguiente imagen se han seleccionado wn3 y wn4 para su retirada.  

   :::image type="content" source="./media/r-server-operationalize/get-started-operationalization.png" alt-text="Activación de modo de mantenimiento en Apache Ambari" border="true":::  

* Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > haga clic en **Retirar**.
* Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > haga clic en **Retirar**.
* Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > haga clic en **Detener**.
* Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > haga clic en **Detener**.
* Seleccione **Acciones** > **Hosts seleccionados** > **Hosts** > haga clic en **Detener todos los componentes**.
* Anule la selección de los nodos de trabajo y selecciones los nodos principales.
* Seleccione **Acciones** > **Hosts seleccionados** > "**Hosts** > **Reiniciar todos los componentes**.

###    <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Paso 2: Configuración de los nodos de proceso en cada nodo de trabajo retirado

1. SSH en cada nodo de trabajo retirado.

1. Ejecute la utilidad de administración con el archivo DLL correspondiente para el clúster de ML Services que tenga. Para ML Server 9.1, ejecute lo siguiente:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Escriba **1** para seleccionar la opción **Configure ML Server for Operationalization** (Configurar R Server para operacionalización).

1. Escriba **c** para seleccionar la opción `C. Compute node`. Esto configurará el nodo de proceso en el nodo de trabajo.

1. Salga de la utilidad de administración.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Paso 3: Incorporación de detalles de nodos de proceso en el nodo web

Una vez que todos los nodos de trabajo retirados se hayan configurado para ejecutar nodos de proceso, vuelva al nodo perimetral y agregue las direcciones IP de los nodos de trabajo retirados a la configuración del nodo web de ML Server:

1. Uso de SSH en el nodo perimetral.

1. Ejecute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Busque la sección de identificadores URI y agregue la dirección IP del nodo de trabajo y los detalles del puerto.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", &quot;http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Administración de clústeres de Machine Learning Services en HDInsight](r-server-hdinsight-manage.md)
* [Opciones de contexto de proceso para un clúster de ML Services en HDInsight](r-server-compute-contexts.md)
* [Opciones de Azure Storage para un clúster de ML Services en HDInsight](r-server-storage.md)
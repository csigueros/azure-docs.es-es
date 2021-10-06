---
title: Transformación de datos con Hive en Azure Virtual Network mediante Azure Portal
description: Este tutorial proporciona instrucciones detalladas para transformar los datos mediante el uso de la actividad de Hive en Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: f937d03691c21430f0055916b81e3a8963cbceba
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769852"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Transformación de datos en Azure Virtual Network mediante la actividad de Hive en Azure Data Factory con Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este tutorial, se usa Azure Portal para crear una canalización de Data Factory que transforma los datos mediante la actividad de Hive en un clúster de HDInsight que se encuentra en una instancia de Azure Virtual Network (VNet). En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos. 
> * Creación de una instancia de Integration Runtime autohospedada
> * Creación de servicios vinculados con Azure Storage y Azure HDInsight
> * Creación de una canalización con la actividad de Hive
> * Desencadenamiento de una ejecución de la canalización
> * Supervisión de la ejecución de la canalización 
> * Comprobación del resultado

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Cuenta de Azure Storage**. Cree un script de Hive y cárguelo en Azure Storage. La salida desde el script de Hive se almacena en esta cuenta de almacenamiento. En este ejemplo, el clúster de HDInsight usa esta cuenta de Azure Storage como el almacenamiento principal. 
- **Azure Virtual Network.** Si no tiene ninguna instancia de Azure Virtual Network, cree una siguiendo [estas instrucciones](../virtual-network/quick-create-portal.md). En este ejemplo, HDInsight se encuentra en una instancia de Azure Virtual Network. A continuación, puede ver una configuración de ejemplo de Azure Virtual Network. 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png" alt-text="Creación de una red virtual":::
- **Clúster de HDInsight.** Consulte el artículo siguiente para crear un clúster de HDInsight y unirlo a la red virtual que creó en el paso anterior: [Extender Azure HDInsight mediante una instancia de Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md). A continuación, puede ver una configuración de ejemplo de HDInsight en una red virtual. 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png" alt-text="HDInsight en una red virtual":::
- **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-Az-ps).
- **Una máquina virtual**. Cree una máquina virtual de Azure y únala a la misma red virtual que contiene el clúster de HDInsight. Para obtener más información, consulte [Creación de máquinas virtuales](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Carga del script de Hive en la cuenta de Blob Storage

1. Cree un archivo SQL de Hive denominado **hivescript.hql** con el siguiente contenido:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. En Azure Blob Storage, cree un contenedor denominado **adftutorial** si no existe.
3. Cree una carpeta llamada **hivescripts**.
4. Cargue el archivo **hivescript.hql** en la subcarpeta **hivescripts**.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
1. Inicie sesión en [Azure Portal](https://portal.azure.com/).    
2. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png" alt-text="Nuevo->DataFactory":::
3. En la página **Nueva factoría de datos**, escriba **ADFTutorialHiveFactory** como **nombre**. 
      
     :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png" alt-text="Página New data factory (Nueva factoría de datos)":::
 
   El nombre de la instancia de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameMyAzureSsisDataFactory) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
    *El nombre "MyAzureSsisDataFactory" de factoría de datos no está disponible.*
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable. 
   - Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos.   
         
     Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).  
4. Seleccione **V2** para la **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista solo se muestran las ubicaciones que se admiten para la creación de factorías de datos.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Crear**.
8. En el panel, verá el icono siguiente con el estado: **Deploying data factory** (Implementación de la factoría de datos). 

     :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png" alt-text="icono implementando factoría de datos":::
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png" alt-text="Página principal Factoría de datos":::
10. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente.
11. En la página principal, cambie a la pestaña **Administrar** del panel de la izquierda como se muestra en la siguiente imagen: 

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="Captura de pantalla en la que se muestra la pestaña Administrar.":::

## <a name="create-a-self-hosted-integration-runtime"></a>Creación de una instancia de Integration Runtime autohospedada
Como el clúster de Hadoop está dentro de una red virtual, debe instalar un entorno de ejecución de integración (IR) autohospedado en la misma red virtual. En esta sección, va a crear una nueva máquina virtual, se unirá a la misma red virtual e instalará un entorno de ejecución de integración autohospedado en ella. El entorno de ejecución de integración autohospedado permite al servicio Data Factory enviar solicitudes de proceso a un servicio de proceso como HDInsight en una red virtual. También permite mover datos hacia los almacenes de datos de una red virtual y, desde estos, a Azure. Puede usar un entorno de ejecución de integración autohospedado cuando el almacén de datos o de procesos está también en un entorno local. 

1. En la interfaz de usuario de Azure Data Factory, haga clic en **Connections** (Conexiones) en la parte inferior de la ventana, vaya a la pestaña **Integration Runtimes** y haga clic en el botón **+ New** (+Nuevo) en la barra de herramientas. 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png" alt-text="Menú Nuevo entorno de ejecución de integración":::
2. En la ventana **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione la opción **Perform data movement and dispatch activities to external computes** (Realizar movimientos de datos y enviar actividades a procesos externos), y haga clic en **Next** (Siguiente). 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png" alt-text="Seleccionar la opción Realizar movimientos de datos y enviar actividades":::
3. Seleccione **Private Network** (Red privada) y haga clic en **Next** (Siguiente).
    
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png" alt-text="Seleccionar red privada":::
4. Escriba **MySelfHostedIR** como **nombre** y haga clic en **Next** (Siguiente). 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png" alt-text="Especificar nombre de entorno de ejecución de integración"::: 
5. Copie la **clave de autenticación** del entorno de ejecución de integración haciendo clic en el botón de copia y guárdela. Mantenga la ventana abierta. Use esta clave para registrar el entorno de ejecución de integración instalado en una máquina virtual. 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png" alt-text="Copiar clave de autenticación":::

### <a name="install-ir-on-a-virtual-machine"></a>Instalación de un entorno de ejecución de integración en una máquina virtual

1. En la máquina virtual de Azure, descargue la [instancia de Integration Runtime autohospedada](https://www.microsoft.com/download/details.aspx?id=39717). Use la **clave de autenticación** que obtuvo en el paso anterior para registrar manualmente el entorno de ejecución de integración autohospedado. 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png" alt-text="Registro de Integration Runtime":::

2. Verá el siguiente mensaje cuando se haya registrado correctamente el entorno de ejecución de integración autohospedado. 
   
    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png" alt-text="Se registró correctamente":::
3. Haga clic en **Iniciar Configuration Manager**. Verá la siguiente página cuando el nodo esté conectado al servicio en la nube: 
   
    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png" alt-text="El nodo está conectado":::

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Entorno de ejecución de integración autohospedado en la interfaz de usuario de Azure Data Factory

1. En la **interfaz de usuario de Azure Data Factory**, debería ver el nombre de la máquina virtual autohospedada y su estado.

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png" alt-text="Nodos autohospedados existentes":::
2. Haga clic en **Finish** (Finalizar) para cerrar la ventana **Integration Runtime Setup** (Configuración de Integration Runtime). Puede ver que el entorno de ejecución de integración autohospedado aparece en la lista de entornos.

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png" alt-text="Entorno de ejecución de integración autohospedado en la lista":::


## <a name="create-linked-services"></a>Crear servicios vinculados

En esta sección, deberá crear e implementar dos servicios vinculados:
- Un **servicio vinculado a Azure Storage** que vincule una cuenta de Azure Storage con la factoría de datos. Este es el almacenamiento principal que usa el clúster de HDInsight. En este caso, puede usar esta cuenta de Azure Storage para almacenar el script de Hive y la salida del script.
- Un **servicio vinculado a HDInsight**. Azure Data Factory envía el script de Hive a este clúster de HDInsight para su ejecución.

### <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage

1. Vaya a la pestaña **Servicios vinculados** y haga clic en **Nuevo**.

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png" alt-text="Botón New linked service (Nuevo servicio vinculado)":::    
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure Blob Storage** y haga clic en **Continue** (Continuar). 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png" alt-text="Seleccionar Azure Blob Storage":::
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureStorageLinkedService** en **Name** (Nombre).
    2. Seleccione **MySelfHostedIR** en la opción **Connect via integration runtime** (Conectar mediante IR).
    3. Seleccione la cuenta de Azure Storage de **Storage account name** (Nombre de la cuenta de Storage). 
    4. Haga clic en **Prueba de conexión** para probar la conexión con la cuenta de almacenamiento.
    5. Haga clic en **Save**(Guardar).
   
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png" alt-text="Especificar una cuenta de Azure Blob Storage":::

### <a name="create-hdinsight-linked-service"></a>Creación del servicio vinculado de HDInsight

1. Haga clic en **New** (Nuevo) una vez más para crear otro servicio vinculado. 
    
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png" alt-text="Botón New linked service (Nuevo servicio vinculado)":::    
2. Vaya a la pestaña **Compute**, seleccione **Azure HDInsight** y haga clic en **Continuar**.

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png" alt-text="Seleccionar Azure HDInsight":::
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureHDInsightLinkedService** como **nombre**.
    2. Seleccione **Bring your own HDInsight** (Traer su propio HDInsight). 
    3. Seleccione el clúster de HDInsight como **clúster de HDI**. 
    4. Escriba el **nombre de usuario** del clúster de HDInsight.
    5. Escriba la **contraseña** del usuario. 
    
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png" alt-text="Configuración de Azure HDInsight":::

En este artículo, se supone que tiene acceso al clúster a través de Internet. Por ejemplo, que se puede conectar al clúster en `https://clustername.azurehdinsight.net`. Esta dirección usa la puerta de enlace pública, que no está disponible si ha usado grupos de seguridad de red (NSG) o rutas definidas por el usuario (UDR) para restringir el acceso desde Internet. Para que Data Factory pueda enviar trabajos al clúster de HDInsight en Azure Virtual Network, debe configurar su instancia de Azure Virtual Network de modo que la URL pueda resolverse en la dirección IP privada de la puerta de enlace que usa HDInsight.

1. Desde Azure Portal, abra la instancia de Virtual Network en que se encuentra HDInsight. Abra la interfaz de red con nombres que empiecen por `nic-gateway-0`. Anote su dirección IP privada. Por ejemplo: 10.6.0.15. 
2. Si su instancia de Azure Virtual Network tiene un servidor DNS, actualice el registro DNS para que la dirección URL del clúster de HDInsight `https://<clustername>.azurehdinsight.net` puede resolverse en `10.6.0.15`. Si no tiene ningún servidor DNS en su instancia de Azure Virtual Network, puede evitar este problema de forma temporal. Para ello, edite el archivo de hosts (C:\Windows\System32\drivers\etc) de todas las máquinas virtuales que se registran como nodos del entorno de ejecución de integración autohospedado mediante la adición de una entrada similar a la siguiente: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Crear una canalización 
En este paso, se crea una canalización con una actividad de Hive. La actividad ejecuta el script de Hive para devolver datos de una tabla de ejemplo y guardarlos en una ruta de acceso que haya definido.

Tenga en cuenta los siguientes puntos:

- **scriptPath** apunta a la ruta de acceso al script de Hive de la cuenta de Azure Storage que usó para MyStorageLinkedService. La ruta de acceso distingue mayúsculas de minúsculas.
- **Output** es un argumento que se usa en el script de Hive. Use el formato de `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` para que apunte a una carpeta existente de su instancia de Azure Storage. La ruta de acceso distingue mayúsculas de minúsculas. 

1. En la interfaz de usuario de Data Factory, haga clic en **+ (signo más)** en el panel izquierdo y haga clic en **Pipeline** (Canalización). 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png" alt-text="Menú New pipeline (Nueva canalización)":::
2. En el cuadro de herramientas **Activities** (Actividades), expanda **HDInsight**, arrastre la actividad **Hive** y colóquela en la superficie del diseñador de canalizaciones. 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png" alt-text="Arrastrar y colocar la actividad Hive":::
3. En la ventana de propiedades, vaya a la pestaña **HDI Cluster** (Clúster de HDI) y seleccione **AzureHDInsightLinkedService** como **servicio vinculado a HDInsight**.

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png" alt-text="Seleccionar servicio vinculado a HDInsight":::
4. Cambie a la pestaña **Scripts** y realice los pasos siguientes: 

    1. Seleccione **AzureStorageLinkedService** como **servicio vinculado de script**. 
    2. En **File Path** (Ruta de archivo), haga clic en **Browse Storage** (Examinar almacenamiento). 
 
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png" alt-text="Examinar almacenamiento":::
    3. En la ventana **Choose a file or folder** (Elegir un archivo o carpeta), vaya a la carpeta **hivescripts** del contenedor **adftutorial**, seleccione **hivescript.hql** y haga clic en **Finish** (Finalizar).  
        
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png" alt-text="Elegir un archivo o carpeta"::: 
    4. Confirme que ve **adftutorial/hivescripts/hivescript.hql** en **File Path** (Ruta de archivo).

        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png" alt-text="Configuración del script":::
    5. En la **pestaña Script**, expanda la sección **Advanced** (Avanzadas). 
    6. Haga clic en **Auto-fill from script** (Rellenado automático a partir de script) en **Parameters** (Parámetros). 
    7. Escriba el valor del parámetro **Output** en el siguiente formato: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Por ejemplo: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png" alt-text="Argumentos de script":::
1. Para publicar artefactos en Data Factory, haga clic en **Publish** (Publicar).

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png" alt-text="La captura de pantalla muestra la opción para publicar en una factoría de datos.":::

## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización

1. En primer lugar, valide la canalización haciendo clic en el botón **Validate** (Comprobar) en la barra de herramientas. Cierre la ventana **Pipeline Validation Output** (Salida de comprobación de canalización) haciendo clic en **flecha derecha (>>)** . 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png" alt-text="Comprobar la canalización"::: 
2. Para desencadenar una ejecución de la canalización, haga clic en Trigger (Desencadenar) en la barra de herramientas y en Trigger Now (Desencadenar ahora). 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png" alt-text="Trigger now (Desencadenar ahora)":::

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Puede ver una ejecución de canalización en la lista **Pipeline Runs** (Ejecuciones de canalización). 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png" alt-text="Supervisión de las ejecuciones de canalización":::
2. Haga clic en **Refresh** (Actualizar) para actualizar la lista.
4. Para ver las ejecuciones de actividad asociadas a la de la canalización, primero haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Action** (Acción). Otros vínculos de acción se usan para detener y volver a ejecutar la canalización. 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png" alt-text="Ver ejecuciones de actividad":::
5. Verá solo una actividad de ejecución ya que solo hay una actividad en la canalización que sea del tipo **HDInsightHive**. Para volver a la vista anterior, haga clic en el vínculo **Pipelines** (Canalizaciones) de la parte superior.

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png" alt-text="Ejecuciones de actividad":::
6. Confirme que ve un archivo de salida en la carpeta **outputfolder** del contenedor **adftutorial**. 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png" alt-text="Archivo de salida":::

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, realizó los pasos siguientes: 

> [!div class="checklist"]
> * Creación de una factoría de datos. 
> * Creación de una instancia de Integration Runtime autohospedada
> * Creación de servicios vinculados con Azure Storage y Azure HDInsight
> * Creación de una canalización con la actividad de Hive
> * Desencadenamiento de una ejecución de la canalización
> * Supervisión de la ejecución de la canalización 
> * Comprobación del resultado

Pase al tutorial siguiente para obtener información acerca de la transformación de datos mediante el uso de un clúster de Spark en Azure:

> [!div class="nextstepaction"]
>[Bifurcación y encadenamiento del flujo de control de Data Factory](tutorial-control-flow-portal.md)
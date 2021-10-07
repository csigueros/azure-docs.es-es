---
title: 'Transformación de datos mediante Spark en Azure Data Factory '
description: Este tutorial proporciona instrucciones detalladas para transformar los datos mediante el uso de una actividad de Spark en Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
author: nabhishek
ms.author: abnarain
ms.date: 06/07/2021
ms.openlocfilehash: 22b6aad6d57fd2dc6e53313ea65a1813781ead9f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769681"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Transformación de datos en la nube mediante una actividad de Spark en Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este tutorial, usará Azure Portal para crear una canalización de Azure Data Factory. Esta canalización permite transformar datos mediante una actividad de Spark y un servicio vinculado de Azure HDInsight a petición. 

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos. 
> * Creación de una canalización que use una actividad de Spark.
> * Desencadenamiento de una ejecución de la canalización
> * Supervisión de la ejecución de la canalización

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Cuenta de Azure Storage**. Debe crear un script de Python y un archivo de entrada y cargarlos en Azure Storage. La salida del programa Spark se almacena en esta cuenta de almacenamiento. El clúster de Spark a petición usa la misma cuenta de almacenamiento que el almacenamiento principal.  

> [!NOTE]
> HdInsight admite solo cuentas de almacenamiento de uso general con nivel estándar. Asegúrese de que la cuenta no sea una cuenta de almacenamiento solo Premium o de blobs.

* **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Carga del script de Python en la cuenta de Blob Storage
1. Cree un archivo de Python denominado **WordCount_Spark.py** con el siguiente contenido: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Reemplace *&lt;storageAccountName&gt;* por el nombre de la cuenta de Azure Storage. A continuación, guarde el archivo. 
1. En Azure Blob Storage, cree un contenedor denominado **adftutorial** si no existe. 
1. Cree una carpeta llamada **spark**.
1. Cree una subcarpeta denominada **script** en la carpeta **spark**. 
1. Cargue el archivo **WordCount_Spark.py** a la subcarpeta **script**. 


### <a name="upload-the-input-file"></a>Carga del archivo de entrada
1. Cree un archivo denominado **minecraftstory.txt** con algo de texto. El programa Spark contará el número de palabras de este texto. 
1. Cree una subcarpeta denominada **inputfiles** en la carpeta **spark**. 
1. Cargue el archivo **minecraftstory.txt** en la subcarpeta **inputfiles**. 

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
1. Seleccione **Nuevo** en el menú de la izquierda, seleccione **Datos y análisis** y, después, seleccione **Data Factory**. 
   
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png" alt-text="Selección de Data Factory en el "::: panel &quot;Nuevo&quot;.
1. En el panel **Nueva factoría de datos**, escriba **ADFTutorialDataFactory** en **Nombre**. 
      
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png" alt-text="Panel de la &quot;nueva factoría de datos&quot;":::
 
   El nombre de la instancia de Azure Data Factory debe ser *único de forma global*. Si ve el siguiente error, cambie el nombre de la factoría de datos. (Por ejemplo, utilice **&lt;SuNombre&gt;ADFTutorialDataFactory**). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/name-not-available-error.png" alt-text="Mensaje de error cuando un nombre no está disponible":::
1. En **Suscripción**, seleccione la suscripción de Azure donde desea crear la factoría de datos. 
1. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable. 
   - Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos.   
         
   En algunos de los pasos de esta guía de inicio rápido se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).  
1. En **Versión**, seleccione **V2**.
1. En **Ubicación**, seleccione la ubicación de la factoría de datos. 

   Para una lista de las regiones de Azure en las que Data Factory está disponible actualmente, seleccione las regiones que le interesen en la página siguiente y expanda **Análisis** para poder encontrar **Data Factory**: [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Los almacenes de datos (como Azure Storage y Azure SQL Database) y los procesos (como HDInsight) que usan Data Factory pueden encontrarse en otras regiones.

1. Seleccione **Crear**.

1. Una vez completada la creación, verá la página **Data Factory**. Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente.

    :::image type="content" source="./media/tutorial-transform-data-spark-portal/data-factory-home-page.png" alt-text="Página principal de la factoría de datos, con el icono &quot;Crear y supervisar&quot;":::

## <a name="create-linked-services"></a>Crear servicios vinculados
En esta sección, deberá crear dos servicios vinculados: 
    
- Un **servicio vinculado a Azure Storage** que vincule una cuenta de Azure Storage con la factoría de datos. Este almacenamiento lo usa el clúster HDInsight a petición. También contiene el script de Spark que se ejecutará. 
- Un **servicio vinculado de HDInsight a petición**. Azure Data Factory permite crear automáticamente un clúster de HDInsight y ejecutar el programa de Spark. A continuación, elimina el clúster de HDInsight si el clúster está inactivo durante un tiempo configurado previamente. 

### <a name="create-an-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage

1. En la página principal, cambie a la pestaña **Administrar** del panel de la izquierda. 

   :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="Captura de pantalla en la que se muestra la pestaña Administrar.":::

1. Seleccione **Connections** (Conexiones) en la parte inferior de la ventana y seleccione **+ New** (+ Nuevo). 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-connection.png" alt-text="Botones para crear una conexión nueva":::
1. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Data Store** > **Azure Blob Storage** y después **Continue** (Continuar). 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/select-azure-storage.png" alt-text="Selección del icono &quot;Azure Blob Storage&quot;":::
1. En **Storage account name** (Nombre de la cuenta de almacenamiento), seleccione el nombre de la lista y, a continuación, seleccione **Save** (Guardar). 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png" alt-text="Cuadro para especificar el nombre de la cuenta de almacenamiento":::


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Creación de un servicio vinculado de HDInsight a petición

1. Seleccione el botón **+ New** (+ Nuevo) una vez más para crear otro servicio vinculado. 
1. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Compute** > **Azure HDInsight** y seleccione **Continue** (Continuar). 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png" alt-text="Selección del icono de &quot;Azure HDInsight&quot;":::
1. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes: 

   a. Escriba **AzureHDInsightLinkedService** como **nombre**.
   
   b. Confirme que **On-demand HDInsight** (HDInsight a petición) está seleccionado en **tipo**.
   
   c. Seleccione **AzureBlobStorage1** como **Servicio vinculado de Azure Storage**. Este servicio vinculado lo creó anteriormente. Si usó un nombre diferente, especifique el nombre correcto aquí. 
   
   d. Seleccione **spark** como **tipo de clúster**.
   
   e. Escriba el **identificador de la entidad de servicio** que tenga permiso para crear un clúster de HDInsight. 
   
      Esta entidad de servicio debe ser miembro del rol de colaborador de la suscripción o del grupo de recursos en el que se crea el clúster. Para más información, consulte [Creación de una aplicación de Azure Active Directory y una entidad de servicio](../active-directory/develop/howto-create-service-principal-portal.md). El **Id. de entidad de servicio** es equivalente al *Id. de aplicación* y una **Clave de entidad de servicio** es equivalente al valor de un *Secreto de cliente*.
   
   f. Escriba la **clave de la entidad de servicio**. 
   
   g. Seleccione el mismo grupo de recursos que utilizó al crear la factoría de datos en **Grupo de recursos**. El clúster de Spark se crea en este grupo de recursos. 
   
   h. Expanda **OS type** (Tipo de SO).
   
   i. Escriba un nombre para **Cluster user name** (Nombre de usuario del clúster). 
   
   j. Escriba el valor de **Cluster password** (Contraseña del clúster) para el usuario. 
   
   k. Seleccione **Finalizar**. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png" alt-text="Configuración del servicio vinculado de HDInsight":::

> [!NOTE]
> Azure HDInsight limita el número total de núcleos que se pueden utilizar en cada región de Azure que admite. Para el servicio vinculado de HDInsight a petición, el clúster de HDInsight se creará en la misma ubicación de Azure Storage que se usó como almacenamiento principal. Asegúrese de que dispone de suficientes cuotas de núcleo para que el clúster se cree correctamente. Para obtener más información, consulte [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Crear una canalización

1. Seleccione el botón **+** (Más) y seleccione **Pipeline** (Canalización) en el menú.

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png" alt-text="Botones para crear una canalización":::
1. En el cuadro de herramientas **Activities** (Actividades), expanda **HDInsight**. Arrastre la actividad de **Spark** del cuadro de herramientas **Activities** (Actividades) a la superficie del diseñador de canalizaciones. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png" alt-text="Arrastrar la actividad de Spark":::
1. En las propiedades de la ventana de la actividad de **Spark** de la parte inferior, realice los pasos siguientes: 

   a. Vaya a la pestaña **HDI Cluster** (Clúster de HDI).
   
   b. Seleccione el servicio **AzureHDInsightLinkedService** que creó en el procedimiento anterior. 
        
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png" alt-text="Especificar el servicio vinculado de HDInsight":::
1. Cambie a la pestaña **Script/Jar** y complete los pasos siguientes: 

   a. Seleccione **AzureBlobStorage1** como **servicio vinculado de trabajo**.
   
   b. Seleccione **Browse Storage** (Examinar almacenamiento).

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/specify-spark-script.png" alt-text="Especificar el script de Spark en la pestaña &quot;Script/Jar&quot;":::
   
   c. Vaya a la carpeta **adftutorial/spark/script**, seleccione **WordCount_Spark.py** y seleccione **Finalizar**.      

1. Para comprobar la canalización, seleccione el botón **Validate** (Comprobar) en la barra de herramientas. Seleccione el botón **>>** (flecha derecha) para cerrar la ventana de comprobación. 
    
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/validate-button.png" alt-text="Botón &quot;Validate&quot; (Comprobar)":::
1. Seleccione **Publish All** (Publicar todo). La interfaz de usuario de Data Factory permite publicar entidades (servicios vinculados y canalizaciones) en el servicio Azure Data Factory. 
    
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/publish-button.png" alt-text="Botón &quot;Publish All&quot; (Publicar todo)":::


## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización
Seleccione **Add Trigger** (Agregar desencadenador) en la barra de herramientas y, después, seleccione **Trigger Now** (Desencadenar ahora). 

:::image type="content" source="./media/tutorial-transform-data-spark-portal/trigger-now-menu.png" alt-text="Botones &quot;Trigger&quot; (Desencadenar) y &quot;Trigger Now&quot; (Desencadenar ahora)":::

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Vaya a la pestaña **Monitor** (Supervisar). Confirme que ve una ejecución de canalización. Se tardan veinte minutos aproximadamente en crear un clúster de Spark. 
   
1. Seleccione **Actualizar** periódicamente para comprobar el estado de la ejecución de canalización. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/monitor-tab.png" alt-text="Pestaña para supervisar las ejecuciones de la canalización, con el botón &quot;Actualizar&quot;":::

1. Para ver las ejecuciones de actividad asociadas con la ejecución de esta canalización, seleccione **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones).

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png" alt-text="Estados de la ejecución de canalización"::: 

   Puede volver a la vista de ejecuciones de canalización. Para ello seleccione el vínculo **All Pipeline Runs** (Todas las ejecuciones de canalizaciones) en la parte superior.

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/activity-runs.png" alt-text="Ver &quot;ejecuciones de actividad&quot;":::

## <a name="verify-the-output"></a>Comprobación del resultado
Compruebe que se crea el archivo de salida en la carpeta spark/otuputfiles/wordcount del contenedor adftutorial. 

:::image type="content" source="./media/tutorial-transform-data-spark-portal/verity-output.png" alt-text="Ubicación del archivo de salida":::

El archivo debe incluir todas las palabras del archivo de texto de entrada y el número de veces que cada palabra aparecía en el archivo. Por ejemplo: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo permite transformar datos mediante una actividad de Spark y un servicio vinculado de HDInsight a petición. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos. 
> * Creación de una canalización que use una actividad de Spark.
> * Desencadenamiento de una ejecución de la canalización
> * Supervisión de la ejecución de la canalización

Pase al siguiente tutorial para obtener información sobre cómo puede transformar datos mediante la ejecución del script de Hive en un clúster de Azure HDInsight que se encuentra en una red virtual: 

> [!div class="nextstepaction"]
> [Tutorial: transformación de datos con Hive en Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).






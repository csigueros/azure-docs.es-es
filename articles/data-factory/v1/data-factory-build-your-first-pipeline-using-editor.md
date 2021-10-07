---
title: Compilación de la primera factoría de datos (Azure Portal)
description: En este tutorial, se crea una canalización de Azure Data Factory de ejemplo mediante Data Factory Editor en Azure Portal.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: a13309631d2fbd971021c3307f8ebe129e6a3a3e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592420"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Tutorial: Compilación de la primera instancia de Data Factory mediante Azure Portal
> [!div class="op_single_selector"]
> * [Introducción y requisitos previos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Plantilla de Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artículo se aplica a la versión 1 de Azure Data Factory, que está disponible con carácter general. Si utiliza la versión actual del servicio Data Factory, consulte el artículo [Inicio rápido: Creación de una factoría de datos con Data Factory](../quickstart-create-data-factory-dot-net.md).

> [!WARNING]
> El editor de JSON de Azure Portal para crear e implementar canalizaciones de ADF v1 se desactivará el 31 de julio de 2019. Después del 31 de julio de 2019, puede seguir usando los [cmdlets de PowerShell de ADF v1](/powershell/module/az.datafactory/), el [SDK de .Net para ADF v1](/dotnet/api/microsoft.azure.management.datafactories.models), las [API REST de ADF v1](/rest/api/datafactory/) para crear las canalizaciones de ADF v1.

En este artículo, aprenderá a usar [Azure Portal](https://portal.azure.com/) para crear su primera factoría de datos. Si desea realizar el tutorial con otras herramientas o SDK, seleccione una de las opciones de la lista desplegable. 

La canalización de este tutorial tiene una sola actividad: una actividad de Azure HDInsight Hive. Dicha actividad ejecuta un script de Hive en un clúster de HDInsight que transforma los datos de entrada para generar datos de salida. La canalización está programada para ejecutarse una vez al mes entre las horas de inicio y finalización especificadas. 

> [!NOTE]
> En este tutorial, la canalización de datos transforma los datos de entrada para generar datos de salida. Para ver un tutorial acerca de cómo copiar datos mediante Data Factory, consulte [Tutorial: Copia de datos de Azure Blob Storage a Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> pero cualquier canalización puede tener más de una actividad. También puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Para más información, consulte [Programación y ejecución en Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Requisitos previos
Lea el [tutorial introductorio](data-factory-build-your-first-pipeline.md) y siga los pasos de la sección de requisitos previos.

En este artículo no se ofrece información general conceptual sobre el servicio Data Factory. Para más información acerca del servicio, consulte [Introducción a Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Crear una factoría de datos
Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Un ejemplo es una actividad de copia que copie datos de un almacén de datos de origen a uno de destino. Otro ejemplo es una actividad de Hive de HDInsight que ejecuta un script de Hive para transformar los datos de entrada en datos de salida del producto. 

Para crear una factoría de datos, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **Nuevo** > **Data + Analytics** > **Data Factory**.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png" alt-text="Hoja Creación":::

1. En la hoja **Nueva factoría de datos**, en **Nombre**, escriba **GetStartedDF**.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png" alt-text="Hoja Nueva Factoría de datos":::

   > [!IMPORTANT]
   > El nombre de la factoría de datos debe ser globalmente único. Si aparece el error "El nombre GetStartedDF de factoría de datos no está disponible", cambie dicho nombre. Por ejemplo, use yournameGetStartedDF y vuelva a crear la factoría de datos. Para más información acerca de las reglas de nomenclatura, consulte [Azure Data Factory: Reglas de nomenclatura](data-factory-naming-rules.md).
   >
   > El nombre de la factoría de datos se puede registrar como nombre DNS en el futuro y puede convertirse en visible públicamente.
   >
   >
1. En **Suscripción**, seleccione la suscripción de Azure en la que desea que se cree la factoría de datos.

1. Seleccione un grupo de recursos existente o cree uno nuevo. Para este tutorial, cree un grupo de recursos llamado **ADFGetStartedRG**.

1. En **Ubicación**, seleccione la ubicación de la factoría de datos. La lista desplegable solo muestra las regiones que admite el servicio Data Factory.

1. Seleccione la casilla **Anclar al panel**.

1. Seleccione **Crear**.

   > [!IMPORTANT]
   > Para crear instancias de Data Factory, es preciso ser miembro del rol [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) en el nivel de grupo de recursos o suscripción.
   >
   >
1. En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory):    

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png" alt-text="Estado Deploying Data Factory (Implementando Data Factory)":::

1. Tras crear la factoría de datos, se ve la página de la **factoría de datos**, que muestra su contenido.     

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png" alt-text="Hoja Factoría de datos":::

Antes de crear una canalización en la factoría de datos, es necesario crear varias entidades de factoría de datos. En primer lugar, cree servicios vinculados para vincular almacenes de datos y procesos a su almacén de datos. Luego, defina los conjuntos de datos de entrada y salida para representar los datos de entrada y salida en almacenes de datos vinculados. Por último, cree la canalización con una actividad que utilice dichos conjuntos de datos.

## <a name="create-linked-services"></a>Crear servicios vinculados
En este paso, vinculará su cuenta de Azure Storage y un clúster de HDInsight a petición con su factoría de datos. La cuenta de almacenamiento contiene los datos de entrada y salida de la canalización de este ejemplo. El servicio vinculado de HDInsight se usa para ejecutar un script de Hive especificado en la actividad de la canalización en este ejemplo. Identificar las actividades que [almacén de datos](data-factory-data-movement-activities.md)/[servicios de proceso](data-factory-compute-linked-services.md) se usan en el escenario. Luego, vincule dichos servicios a la factoría de datos mediante la creación de servicios vinculados.  

### <a name="create-a-storage-linked-service"></a>Creación de un servicio vinculado de Storage
En este paso, vinculará su cuenta de almacenamiento con su factoría de datos. En este tutorial, usará la misma cuenta de almacenamiento para almacenar los datos de entrada y salida, y el archivo de script de HQL.

1. En la hoja de la **factoría de datos** de **GetStartedDF**, seleccione **Crear e implementar**. Aparecerá Data Factory Editor.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png" alt-text="Icono Crear e implementar":::

1. Seleccione **Nuevo almacén de datos** y elija **Azure Storage**.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png" alt-text="Hoja Nuevo almacén de datos":::

1. Vera el script JSON para crear un servicio vinculado de Storage en el editor.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png" alt-text="Servicio vinculado de Storage":::

1. Reemplace **account name** por el nombre de su cuenta de almacenamiento. Reemplace **account key** por la clave de acceso de la cuenta de almacenamiento. Para aprender a obtener la clave de acceso de almacenamiento, consulte [Administración de claves de acceso de la cuenta de almacenamiento](../../storage/common/storage-account-keys-manage.md).

1. Seleccione **Implementar** en la barra de comandos para implementar el servicio vinculado.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png" alt-text="Botón Implementar":::

   Después de que el servicio vinculado se haya implementado correctamente, la ventana Borrador-1 desaparece. **AzureStorageLinkedService** aparece en la vista de árbol de la izquierda.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png" alt-text="AzureStorageLinkedService":::    

### <a name="create-an-hdinsight-linked-service"></a>Creación de un servicio vinculado de HDInsight
En este paso, vinculará un clúster de HDInsight a petición con la factoría de datos. El clúster de HDInsight se crea automáticamente en el runtime. Después de realizar el procesamiento y permanecer inactivo durante el período de tiempo especificado se elimina.

1. En Data Factory Editor, seleccione **Más** > **Nuevo proceso** > **Clúster de HDInsight a petición**.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png" alt-text="Nuevo proceso":::

1. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1. El fragmento de código JSON describe las propiedades que se usan para crear el clúster de HDInsight a petición.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código.

   | Propiedad | Descripción |
   |:--- |:--- |
   | clusterSize |Especifica el tamaño del clúster de HDInsight. |
   | timeToLive | Especifica el tiempo de inactividad del clúster de HDInsight antes de que se elimine. |
   | linkedServiceName | Especifica la cuenta de almacenamiento que se usa para almacenar los registros que genera HDInsight. |

    Tenga en cuenta los siguientes puntos:

     a. La factoría de datos crea un clúster de HDInsight basado en Linux con las propiedades de JSON. Para más información, consulte [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Puede usar su propio clúster de HDInsight, en lugar de usar un clúster de HDInsight a petición. Para más información, consulte [Servicio vinculado a petición de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. El clúster de HDInsight crea un contenedor predeterminado en el almacenamiento de blobs que especificó en la propiedad JSON (**linkedServiceName**). HDInsight no elimina este contenedor cuando se elimina el clúster. Este comportamiento es así por diseño. Con el servicio vinculado de HDInsight a petición se crea un clúster de HDInsight cada vez que se procesa un segmento, a menos que haya un clúster existente activo (**timeToLive**). El clúster se elimina automáticamente cuando finaliza el procesamiento.

     A medida que se procesen más segmentos, verá numerosos contenedores en su almacenamiento de blobs. Si no los necesita para solucionar los problemas de los trabajos, puede eliminarlos para reducir el costo de almacenamiento. Los nombres de estos contenedores siguen este patrón: "adf **nombreDeFactoríaDeDatos**-**nombreDeServicioVinculado**-marcaDeFechayHora". Use herramientas como el [Explorador de Azure Storage](https://storageexplorer.com/) para eliminar los contenedores del almacenamiento de blobs.

     Para más información, consulte [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Seleccione **Implementar** en la barra de comandos para implementar el servicio vinculado.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png" alt-text="Opción Implementar":::

1. Asegúrese de que puede ver tanto **AzureStorageLinkedService** como **HDInsightOnDemandLinkedService** en la vista de árbol de la izquierda.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png" alt-text="Captura de pantalla que muestra que AzureStorageLinkedService y HDInsightOnDemandLinkedService están vinculados entre sí.":::

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos que representan los datos de entrada y salida para el procesamiento de Hive. Estos conjuntos de datos hacen referencia al servicio AzureStorageLinkedService1 que creó anteriormente en este tutorial. Los puntos de servicio vinculados a una cuenta de almacenamiento. Los conjuntos de datos especifican el contenedor, la carpeta y el nombre de archivo del almacenamiento que contiene los datos de entrada y salida.   

### <a name="create-the-input-dataset"></a>Creación del conjunto de datos de entrada
1. En Data Factory Editor, seleccione **More** > **New dataset** > **Azure Blob Storage** (Más > Nuevo conjunto de datos > Azure Blob Storage).

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png" alt-text="Nuevo conjunto de datos":::

1. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1. En el fragmento de código JSON, cree un conjunto de datos llamado **AzureBlobInput** que represente los datos de entrada para una actividad de la canalización. Además, especifique que los datos de entrada están en el contenedor de blobs llamado **adfgetstarted** y en la carpeta llamada **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código.

   | Propiedad | Anidada en | Descripción |
   |:--- |:--- |:--- |
   | type | properties |La propiedad type se establece en **AzureBlob**, ya que los datos residen en el almacenamiento de blobs. |
   | linkedServiceName | format |Hace referencia al servicio AzureStorageLinkedService que creó anteriormente. |
   | folderPath | typeProperties | Especifica el contenedor de blobs y la carpeta que contiene los blobs de entrada. | 
   | fileName | typeProperties |Esta propiedad es opcional. Si omite esta propiedad, se seleccionan todos los archivos de folderPath. En este tutorial, solo se procesa el archivo input.log. |
   | type | format |Los archivos de registro están en formato de texto, así que use **TextFormat**. |
   | columnDelimiter | format |Las columnas de los archivos de registro están delimitadas por una coma (`,`). |
   | frequency/interval | availability |La frecuencia se establece en **Mes** y el intervalo es **1**, lo que significa que los segmentos de entrada estarán disponibles cada mes. |
   | external | properties | Esta propiedad se establece en **true** si esta canalización no ha generado los datos de entrada. En este tutorial, esta canalización no genera el archivo input.log, por lo que la propiedad se establece en **true**. |

    Para más información acerca de estas propiedades de JSON, consulte el artículo acerca del [conector de blobs de Azure](data-factory-azure-blob-connector.md#dataset-properties).

1. Seleccione **Implementar** en la barra de comandos para implementar el conjunto de datos recién creado. Vera el conjunto de datos en la vista de árbol de la izquierda.

### <a name="create-the-output-dataset"></a>Creación del conjunto de datos de salida
Ahora, cree el conjunto de datos de salida que representa los datos de salida almacenados en el almacenamiento de blobs.

1. En Data Factory Editor, seleccione **More** > **New dataset** > **Azure Blob Storage** (Más > Nuevo conjunto de datos > Azure Blob Storage).

1. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1. En el fragmento de código JSON, cree un conjunto de datos llamado **AzureBlobOutput** para especificar la estructura de los datos que genera el script de Hive. Especifique también que los resultados se almacenen en el contenedor de blobs **adfgetstarted** y en la carpeta **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Para ver las descripciones de estas propiedades, consulte la sección "Create the input dataset" (Creación del conjunto de datos de entrada). No establezca la propiedad externa en un conjunto de datos de salida, ya que este lo genera el servicio Data Factory.

1. Seleccione **Implementar** en la barra de comandos para implementar el conjunto de datos recién creado.

1. Compruebe que el conjunto de datos se creó correctamente.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png" alt-text="Vista de árbol con servicios vinculados":::

## <a name="create-a-pipeline"></a>Crear una canalización
En este paso, creará la primera canalización con una actividad de Hive de HDInsight. El segmento de entrada está disponible de forma mensual (la frecuencia es Mes y el intervalo es 1). El segmento de salida se genera mensualmente. La propiedad del programador de la actividad también se establece en mensual. La configuración del conjunto de datos de salida y la del programador de la actividad deben coincidir. Actualmente, el conjunto de datos de salida es lo que controla la programación, por lo que debe crear un conjunto de datos de salida aunque la actividad no genere ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. Al final de esta sección se explican las propiedades que se usan en el siguiente fragmento de código JSON.

1. En Data Factory Editor, seleccione **More** > **New pipeline** (Nueva canalización).

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png" alt-text="Opción Nueva canalización":::

1. Copie y pegue el fragmento de código siguiente en la ventana Borrador-1.

   > [!IMPORTANT]
   > Reemplace **storageaccountname** por el nombre de su cuenta de almacenamiento en el fragmento de código JSON.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    En el fragmento de código JSON, cree una canalización que conste de una sola actividad que use Hive para procesar los datos de un clúster de HDInsight.

    El archivo de script de Hive, **partitionweblogs.hql**, se guarda en la cuenta de almacenamiento, que especifica scriptLinkedService y que se llama **AzureStorageLinkedService**. Puede encontrarlo en la carpeta **script** del contenedor **adfgetstarted**.

    La sección **defines** se usa para especificar la configuración del runtime que se pasa al script de Hive como valores de configuración de Hive. Algunos ejemplos son ${hiveconf: inputtable} y ${hiveconf:partitionedtable}.

    Las propiedades **start** y **end** de la canalización especifican su período activo.

    En el código JSON de la actividad, especifique que el script de Hive se ejecuta en el proceso especificado por **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Para más información acerca de las propiedades de JSON que se usan en el ejemplo, consulte la sección "JSON de canalización" en [Canalizaciones y actividades en Azure Data Factory](data-factory-create-pipelines.md).
   >
   >
1. Confirme lo siguiente:

   a. El archivo **input.log** se encuentra en la carpeta **inputdata** del contenedor **adfgetstarted** del almacenamiento de blobs.

   b. El archivo **partitionweblogs.hql** se encuentra en la carpeta **script** del contenedor **adfgetstarted** del almacenamiento de blobs. Si no ve estos archivos, siga los pasos de la sección de requisitos previos del [Tutorial introductorio](data-factory-build-your-first-pipeline.md).

   c. Ha reemplazado **storageaccountname** por el nombre de su cuenta de almacenamiento en el archivo JSON de la canalización.

1. Seleccione **Implementar** en la barra de comandos para implementar la canalización. Dado que las horas de **inicio** y **finalización** están establecidas en el pasado e **isPaused** está establecido en **false**, la canalización (la actividad de la canalización) se ejecuta inmediatamente después de realizar la implementación.

1. Confirme que la canalización aparece en la vista de árbol.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png" alt-text="Vista de árbol con canalización":::



## <a name="monitor-a-pipeline"></a>Supervisión de una canalización
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Supervisión de una canalización mediante la vista Diagrama
1. En la hoja de la **factoría de datos**, seleccione **Diagrama**.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png" alt-text="Icono Diagrama":::

1. En la vista **Diagrama**, se puede encontrar información general acerca de las canalizaciones y los conjuntos de datos que se usan en este tutorial.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png" alt-text="Vista Diagrama":::

1. Para ver todas las actividades de la canalización, haga clic con el botón derecho en la canalización en el diagrama y seleccione **Abrir canalización**.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png" alt-text="Menú Abrir canalización":::

1. Confirme que ve **Actividad de Hive** en la canalización.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png" alt-text="Vista Abrir canalización":::

    Para volver a la vista anterior, seleccione **Data Factory** en el menú superior.

1. En la vista **Diagrama**, haga doble clic en el conjunto de datos **AzureBlobInput**. Confirme que el estado del segmento es **Listo**. Es posible que el segmento tarde un par de minutos en aparecer con ese estado **.** Si no aparece después de un tiempo, compruebe si el archivo de entrada (**input.log**) está en el contenedor (**adfgetstarted**) y en la carpeta (**inputdata**) correctos.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png" alt-text="Segmento de entrada en estado Listo":::

1. Cierre la hoja **AzureBlobInput**.

1. En la vista **Diagrama**, haga doble clic en el conjunto de datos **AzureBlobOutput**. Se ve el segmento que se está procesando.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png" alt-text="Procesamiento de conjunto de datos en curso":::

1. Cuando finalice el procesamiento, verá que el segmento está en estado **Listo**.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png" alt-text="Conjunto de datos en estado Listo":::  

   > [!IMPORTANT]
   > La creación de un clúster de HDInsight a petición normalmente tarda algún aproximadamente 20 minutos. Cabe esperar que la canalización tarde aproximadamente 30 minutos en procesar el segmento.
   >
   >

1. Cuando el segmento esté en estado **Listo**, busque los datos de salida en la carpeta **partitioneddata** del contenedor **adfgetstarted** del almacenamiento de blobs.  

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png" alt-text="Datos de salida":::

1. Seleccione el segmento para ver más información del mismo en una hoja **Segmento de datos**.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png" alt-text="Información de Segmento de datos":::

1. En la lista **Ejecuciones de actividad**, seleccione una ejecución de actividad para obtener más información sobre ella (en este escenario, es una actividad de Hive). La información aparece en la hoja **Detalles de la ejecución de actividad**.   

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png" alt-text="Ventana Detalles de la ejecución de actividad":::    

   En los archivos de registro, puede ver la consulta de Hive que se ejecutó y la información acerca de su estado. Estos registros son útiles para solucionar cualquier problema.
   Para más información, consulte [Monitor and manage pipelines by using Azure portal blades](data-factory-monitor-manage-pipelines.md) (Supervisión y administración de canalizaciones mediante las hojas de Azure Portal).

> [!IMPORTANT]
> El archivo de entrada se elimina cuando el segmento se procesa correctamente. Por consiguiente, si desea volver a ejecutar el segmento o volver a realizar el tutorial, cargue el archivo de entrada (**input.log**) en la carpeta **inputdata** del contenedor **adfgetstarted**.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Supervisión de una canalización mediante la aplicación Supervisión y administración
Para supervisar las canalizaciones también se puede usar la aplicación Supervisión y administración. Para más información acerca de cómo usar esta aplicación, consulte [Monitor and manage Azure Data Factory pipelines by using the Monitoring and Management app](data-factory-monitor-manage-app.md) (Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación Supervisión y administración).

1. Seleccione el icono **Supervisión y administración** en la página principal de la factoría de datos.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png" alt-text="Icono Supervisión y administración":::

1. En la aplicación Supervisión y administración, cambie el valor de **Hora de inicio** y **Hora de finalización** para que coincidan con las horas de inicio y finalización de la canalización. Seleccione **Aplicar**.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png" alt-text="Aplicación Supervisión y administración":::

1. Seleccione una ventana de actividad en la lista **Ventanas de actividad** para ver información acerca de ella.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png" alt-text="Lista Activity Windows (Ventanas de actividad)":::

## <a name="summary"></a>Resumen
En este tutorial, ha creado una factoría de datos para procesar datos mediante la ejecución de un script de Hive en un clúster de Hadoop de HDInsight. Ha usado Data Factory Editor en Azure Portal para realizar las siguientes tareas:  

* Creación de una factoría de datos.
* Crear dos servicios vinculados:
   * Un servicio vinculado de Storage para vincular el almacenamiento de blobs que contiene los archivos de entrada y salida a la factoría de datos.
   * Un servicio vinculado de HDInsight a petición para vincular un clúster de Hadoop de HDInsight a petición a la factoría de datos. Data Factory crea un clúster de Hadoop en HDInsight justo a tiempo para procesar los datos de entrada y generar datos de salida.
* Crear dos conjuntos de datos, que describen los datos de entrada y salida de una actividad de Hive de HDInsight en la canalización.
* Crear una canalización con una actividad de Hive de HDInsight.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha creado una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Para ver cómo usar una actividad de copia para copiar datos de Blob Storage a Azure Database SQL, consulte [Tutorial: Copia de datos de Blob Storage en SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte también
| Tema | Descripción |
|:--- |:--- |
| [Canalizaciones](data-factory-create-pipelines.md) |Este artículo le ayuda a conocer las canalizaciones y actividades de Data Factory y cómo usarlas para construir flujos de trabajo controlados por datos de un extremo a otro para su escenario o negocio. |
| [Conjuntos de datos](data-factory-create-datasets.md) |Este artículo le ayuda a conocer los conjuntos de datos de Data Factory. |
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) |En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Data Factory. |
| [Supervisión y administración de canalizaciones mediante la aplicación Supervisión y administración](data-factory-monitor-manage-app.md) |En este artículo se describe cómo supervisar, administrar y depurar canalizaciones mediante la aplicación Supervisión y administración. |
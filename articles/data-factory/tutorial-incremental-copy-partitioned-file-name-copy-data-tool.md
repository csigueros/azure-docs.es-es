---
title: Copia incremental de nuevos archivos por el nombre de archivo con particiones de tiempo
description: Cree una instancia de Azure Data Factory y, luego, use la herramienta Copiar datos para cargar incrementalmente archivos nuevos solo por el nombre de archivo con particiones de tiempo.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2021
ms.openlocfilehash: f2fe1e854027cb6fd15b6d0e2e659cc62528a2e2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757864"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copia incremental de nuevos archivos por el nombre de archivo con particiones de tiempo mediante la herramienta Copiar datos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial, usará Azure Portal para crear una factoría de datos. Luego, use la herramienta Copiar datos para crear una canalización que copie incrementalmente los archivos nuevos por el nombre de archivo con particiones de tiempo de una instancia de Azure Blob Storage a otra.

> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>Requisitos previos

* **Suscripción de Azure**: Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de Azure Storage**: Use Blob Storage como almacén de datos de _origen_ y _receptor_. Si no dispone de una cuenta de almacenamiento de Azure, consulte las instrucciones de [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Crear dos contenedores en Azure Blob Storage

Haga lo siguiente para preparar su instancia de Blob Storage para el tutorial.

1. Cree un contenedor denominado **source**.  Cree una ruta de acceso de carpeta, como **2021/07/15/06**, en el contenedor. Cree un archivo de texto vacío y asígnele el nombre **file1.txt**. Cargue el archivo file1.txt en la ruta de acceso de carpeta **source/2021/07/15/06** en su cuenta de almacenamiento.  Puede usar varias herramientas para realizar estas tareas, como el [Explorador de Azure Storage](https://storageexplorer.com/).

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png" alt-text="cargar archivos":::

    > [!NOTE]
    > Ajuste el nombre de carpeta con la hora UTC correspondiente.  Por ejemplo, si la hora UTC actual es 6:10 a. m. del 15 de julio de 2021, puede crear la ruta de acceso de carpeta como **source/2021/07/15/06/** por la regla de **source/{año}/{mes}/{día}/{hora}/** .

2. Cree un contenedor denominado **destination**. Puede usar varias herramientas para realizar estas tareas, como el [Explorador de Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, seleccione **Crear un recurso** > **Integración** > **Data Factory**:

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Selección de Data Factory en el panel &quot;Nuevo&quot;":::

2. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**.

    El nombre de la factoría de datos debe ser _globalmente único_. Puede aparecer el siguiente mensaje de error:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nuevo mensaje de error de factoría de datos por nombre duplicado.":::

   Si recibe un mensaje de error sobre el valor de nombre, escriba un nombre diferente para la factoría de datos. Por ejemplo, utilice _**suNombre**_ **ADFTutorialDataFactory**. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Data Factory: reglas de nomenclatura](naming-rules.md).
3. Seleccione la **suscripción** de Azure en la que quiere crear la nueva factoría de datos.
4. Para **Grupo de recursos**, realice uno de los siguientes pasos:

    a. Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable.

    b. Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos. 
         
    Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).

5. En **Versión**, seleccione **V2** como versión.
6. En **Ubicación**, seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas se muestran en la lista desplegable. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa la factoría de datos pueden estar en otras ubicaciones o regiones.
7. Seleccione **Crear**.
8. Una vez finalizada la creación, se muestra la página principal de **Data Factory**.
9. Para iniciar la aplicación de interfaz de usuario (IU) de Azure Data Factory en una pestaña independiente del explorador, seleccione **Abrir** en el icono **Abrir Azure Data Factory Studio**.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página principal de Azure Data Factory, seleccione el título **Ingerir** para iniciar la herramienta Copiar datos.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Captura de pantalla que muestra la página principal de ADF.":::

2. En la página **Propiedades**, realice los pasos siguientes:
    1. En **Tipo de tarea**, elija **Built-in copy task** (Tarea de copia integrada).

    1. En **Task cadence or task schedule** (Cadencia de tareas o programación de tareas), seleccione **Ventana de saltos de tamaño constante**.

    1. En **Periodicidad**, establezca **1 hora**.

    1. Seleccione **Next** (Siguiente).

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png" alt-text="Página de propiedades":::
3. En la página **Almacén de datos de origen**, realice los pasos siguientes:

    a. Seleccione **+ Nueva conexión** para agregar una conexión.
    
    b. Seleccione **Azure Blob Storage** en la galería y, a continuación, seleccione **Continue** (Continuar).
    
    c. En la página **Nueva conexión (Azure Blob Storage)** , escriba un nombre para la conexión. Seleccione la suscripción a Azure y, a continuación, su cuenta de almacenamiento de la lista **Nombre de la cuenta de almacenamiento**. Pruebe la conexión y, después, seleccione **Create** (Crear).

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-connection.png" alt-text="Página Source data store (Almacén de datos de origen)":::

    d. En la página **Almacén de datos de origen**, seleccione la conexión recién creada en la sección **Conexión**.

    e. En la sección **Archivo o carpeta**, busque y seleccione el contenedor **source** y, luego, seleccione **Aceptar**.

    f. En **File loading behavior** (Comportamiento de carga de archivos), seleccione **Incremental load: time-partitioned folder/file names** (Carga Incremental: nombres de archivo/carpeta con particiones de tiempo).

    g. Escriba la ruta de acceso de carpeta dinámica como **source/{año}/{mes}/{día}/{hora}/** y cambie el formato según se muestra en la captura de pantalla siguiente. 
    
    h. Seleccione **Binary copy** (Copia binaria) y seleccione **Next** (Siguiente).

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png" alt-text="Captura de pantalla que muestra la configuración de la página Almacén de datos de origen.":::


4. En la página **Almacén de datos de destino**, realice los pasos siguientes:
    1. Seleccione el elemento **AzureBlobStorage**, que es la misma cuenta de almacenamiento que el almacén de origen de datos.

    1. Busque y seleccione la carpeta **destination** y, después, seleccione **Aceptar**.

    1. Escriba la ruta de acceso de carpeta dinámica como **destination/{año}/{mes}/{día}/{hora}/** y cambie el formato según se muestra en la captura de pantalla siguiente.

    1. Seleccione **Siguiente**.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store.png" alt-text="Captura de pantalla que muestra la configuración de la página Almacén de datos de destino.":::

5. En la página **Configuración**, en **Nombre de tarea**, escriba **DeltaCopyFromBlobPipeline** y, a continuación, seleccione **Siguiente**. La interfaz de usuario de Data Factory crea una canalización con el nombre de la tarea especificado.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png" alt-text="Captura de pantalla que muestra la configuración de la página Configuración.":::

6. En la página **Summary** (Resumen), revise la configuración y seleccione **Next** (Siguiente).

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png" alt-text="Página de resumen":::

7. En la página **Implementación**, seleccione **Monitor** para supervisar la canalización (tarea).
    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png" alt-text="Página Deployment (Implementación)":::

8. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente.  Debe esperar a que la canalización se ejecute cuando se desencadene automáticamente (después de una hora aproximadamente). Cuando se ejecute, seleccione el vínculo del nombre de canalización **DeltaCopyFromBlobPipeline** para ver los detalles de la ejecución de la actividad o vuelva a ejecutar la canalización. Seleccione **Refresh** (Actualizar) para actualizar la lista.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png" alt-text="Captura de pantalla que muestra la pestaña Pipeline runs (Ejecuciones de canalizaciones).":::
9. Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Ajuste el ancho de columna de la columnas de **origen** y **destino** (si es necesario) para mostrar más detalles. Puede ver que el archivo de origen (file1.txt) se ha copiado de *source/2021/07/15/06/* a *destination/2021/07/15/06/* con el mismo nombre de archivo. 

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png" alt-text="Captura de pantalla que muestra los detalles de ejecución de las canalizaciones.":::

    Esto también se puede confirmar usando el Explorador de Azure Storage(https://storageexplorer.com/) ) para examinar los archivos.

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png" alt-text="Captura de pantalla que muestra los detalles de ejecución de la canalización respecto al destino.":::

10. Cree otro archivo de texto vacío con el nuevo nombre **file2.txt**. Cargue el archivo file2.txt en la ruta de acceso de carpeta **source/2021/07/15/07** de su cuenta de almacenamiento. Puede usar varias herramientas para realizar estas tareas, como el [Explorador de Azure Storage](https://storageexplorer.com/).

    > [!NOTE]
    > Es posible que ya sepa que debe crear una nueva ruta de acceso de carpeta. Ajuste el nombre de carpeta con la hora UTC correspondiente.  Por ejemplo, si la hora UTC actual es las 7:30 a. m. del 15 de julio de 2021, puede crear la ruta de acceso de carpeta como **source/2021/07/15/07/** por la regla de **{año}/{mes}/{día}/{hora}/** .

11. Para volver a la vista **Ejecuciones de canalización**, seleccione **Todas las ejecuciones de la canalización**, y espere a que la misma canalización se desencadene de nuevo automáticamente al cabo de una hora.  

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png" alt-text="Captura de pantalla que muestra el vínculo All pipeline runs (Todas las ejecuciones de la canalización) para volver a esa página.":::

12. Seleccione el nuevo vínculo **DeltaCopyFromBlobPipeline** para la segunda ejecución de canalización cuando se realice y repita el proceso para revisar los detalles. Puede ver que el archivo de origen (file2.txt) se ha copiado de **source/2021/07/15/07/** a **destination/2021/07/15/07/** con el mismo nombre de archivo. Esto también se puede confirmar usando el Explorador de Azure Storage (https://storageexplorer.com/) ) para examinar los archivos en el contenedor **destination**.


## <a name="next-steps"></a>Pasos siguientes
Pase al tutorial siguiente para obtener información acerca de la transformación de datos mediante el uso de un clúster de Spark en Azure:

> [!div class="nextstepaction"]
>[Transformar datos con un clúster de Spark en la nube](tutorial-transform-data-spark-portal.md)

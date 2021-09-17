---
title: Herramienta de datos para copiar archivos nuevos y actualizados de forma incremental
description: Cree una instancia de Azure Data Factory y, luego, use la herramienta Copiar datos para cargar incrementalmente archivos nuevos según LastModifiedDate.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/12/2021
ms.openlocfilehash: 0c2cfe51e84931b90ab80588b16fe126aadaf4c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638812"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copia incremental de archivos nuevos y modificados según LastModifiedDate con la herramienta Copiar datos

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial, usará Azure Portal para crear una instancia de Data Factory. Luego, usará la herramienta Copiar datos para crear una canalización que copie incrementalmente solo los archivos nuevos y modificados desde una instancia de Azure Blob Storage a otro. Usa `LastModifiedDate` para determinar qué archivos se van a copiar.

Después de completar los pasos que se indican aquí, Azure Data Factory examinará todos los archivos del almacén de origen, aplicará el filtro de archivos por `LastModifiedDate` y copiará en el almacén de destino solo los archivos nuevos o que se han actualizado desde la última vez. Tenga en cuenta que si Data Factory examina un gran número de archivos, debe esperar largos períodos. El análisis de archivos tarda mucho tiempo, incluso cuando se reduce la cantidad de datos copiados.

> [!NOTE]
> Si no está familiarizado con Data Factory, consulte [Introducción a Azure Data Factory](introduction.md).

En este tutorial va a completar estas tareas:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>Requisitos previos

* **Suscripción de Azure**: Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* **Cuenta de Azure Storage**: Use Blob Storage para los almacenes de datos de origen y receptor. Si no dispone de una cuenta de Azure Storage, siga las instrucciones que aparecen en [Creación de una cuenta de Azure Storage](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Crear dos contenedores en Azure Blob Storage

Complete estos pasos para preparar su instancia de Blob Storage para el tutorial:

1. Cree un contenedor denominado **source**. Puede usar varias herramientas para realizar esta tarea, como el [Explorador de Azure Storage](https://storageexplorer.com/).

2. Cree un contenedor denominado **destination**.

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el panel izquierdo, seleccione **Crear un recurso**. Seleccione **Integración** > **Data Factory**:

   ![Selección de Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**.

   El nombre de la factoría de datos debe ser globalmente único. Es posible que reciba este mensaje de error:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nuevo mensaje de error de factoría de datos por nombre duplicado.":::

   Si recibe un mensaje de error sobre el valor de nombre, escriba un nombre diferente para la factoría de datos. Por ejemplo, utilice _**suNombre**_**ADFTutorialDataFactory**. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Data Factory: reglas de nomenclatura](naming-rules.md).
3. En **Suscripción**, seleccione la suscripción de Azure en la que creará la factoría de datos nueva.
4. En **Grupo de recursos**, complete uno de estos pasos:

    * Seleccione **Usar existente** y, luego, seleccione un grupo de recursos existente de la lista.

    * Seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos.
         
    Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).

5. En **Versión**, seleccione **V2**.
6. En **Ubicación**, seleccione la ubicación de la factoría de datos. Solo las ubicaciones admitidas aparecen en la lista. Los almacenes de datos (por ejemplo, Azure Storage y Azure SQL Database) y los procesos (por ejemplo, Azure HDInsight) que Data Factory usa pueden estar en otras ubicaciones o regiones.
8. Seleccione **Crear**.
9. Una vez creada la factoría de datos, aparece la página principal de Data Factory.
10. Para iniciar la aplicación de interfaz de usuario (IU) de Azure Data Factory en una pestaña independiente del explorador, seleccione **Abrir** en el icono para **abrir Azure Data Factory Studio**:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página principal de Azure Data Factory, seleccione el icono **Ingerir** para abrir la herramienta Copiar datos:

   ![Captura de pantalla que muestra la página principal de ADF.](./media/doc-common-process/get-started-page.png)

2. En la página **Propiedades**, realice los pasos siguientes:

    1. En **Tipo de tarea**, seleccione **Built-in copy task** (Tarea de copia integrada).

    1. En **Task cadence or task schedule** (Cadencia de tareas o programación de tareas), seleccione **Tumbling window** (Desencadenador periódico).

    1. En **Recurrence** (Periodicidad), establezca **15 minutos**.

    1. Seleccione **Siguiente**.

    ![Página de propiedades de Copy data (Copiar datos)](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. En la página **Almacén de datos de origen**, complete estos pasos:

    1. Seleccione **+ New connection** (+ Nueva conexión) para agregar una conexión.

    1. Seleccione **Azure Blob Storage** en la galería y, a continuación, seleccione **Continuar**:

        ![Seleccionar Azure Blob Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    1. En la página **New connection (Azure Blob Storage)** (Nueva conexión [Azure Blob Storage]), seleccione la suscripción a Azure de la lista **Azure subscription** (Suscripción a Azure) y la cuenta de almacenamiento de la lista **Storage account name** (Nombre de la cuenta de almacenamiento). Pruebe la conexión y seleccione **Crear**.

    1. Seleccione la conexión recién creada en el bloque **Conexión**.

    1. En la sección **Archivo o carpeta**, **busque** y seleccione la carpeta **source** y, luego, seleccione **Aceptar**.

    1. En **File loading behavior** (Comportamiento de la carga de archivos), seleccione **Incremental load: LastModifiedDate** (Carga incremental: fecha de última modificación) y, luego, **Binary copy** (Copia binaria).
    
    1. Seleccione **Siguiente**.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png" alt-text="Captura de pantalla que muestra la página del almacén de datos de origen":::

4. En la página **Destination data store** (Almacén de datos de destino), haga lo siguiente:
    1. Seleccione la conexión **AzureBlobStorage** que creó. Se trata de la misma cuenta de almacenamiento que el almacén de datos de origen.

    1. En la sección **Ruta de la carpeta**, busque y seleccione la carpeta **destination** y, luego, seleccione **Aceptar**.

    1. Seleccione **Siguiente**.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page.png" alt-text="Captura de pantalla que muestra la página del almacén de datos de destino":::

5. En la página **Configuración**, en **Nombre de tarea**, escriba **DeltaCopyFromBlobPipeline** y, luego, seleccione **Siguiente**. La interfaz de usuario de Data Factory crea una canalización con el nombre de la tarea especificado.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png" alt-text="Captura de pantalla que muestra la página de configuración":::

6. En la página **Summary** (Resumen), revise la configuración y seleccione **Next** (Siguiente).

    ![Página de resumen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

7. En la página **Implementación**, seleccione **Monitor** para supervisar la canalización (tarea).

    ![Página Deployment (Implementación)](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

8. Observe que la pestaña **Monitor** (Supervisión) de la izquierda se selecciona automáticamente. La aplicación cambia a la pestaña **Monitor** (Supervisión). Verá el estado de la canalización. Seleccione **Refresh** (Actualizar) para actualizar la lista. Seleccione el vínculo bajo **Nombre de canalización** para ver los detalles de la ejecución de actividad o para volver a ejecutar la canalización.

    ![Actualizar la lista y ver los detalles de la ejecución de actividad](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

9. Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para ver detalles sobre la operación de copia, en la página **Ejecuciones de actividad**, seleccione el vínculo **Detalles** (el icono de gafas) en la columna **Nombre de actividad**. Para detalles sobre las propiedades, consulte [Introducción a la actividad de copia](copy-activity-overview.md).

    ![Actividad de copia en la canalización](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Como no hay archivos en el contenedor de origen de la cuenta de Blob Storage, no verá ningún archivo copiado en el contenedor de destino en la cuenta:

    ![No hay archivos en los contenedores source o destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

10. Cree un archivo de texto vacío y asígnele el nombre **file1.txt**. Cargue este archivo de texto en el contenedor de origen en su cuenta de almacenamiento. Puede usar varias herramientas para realizar estas tareas, como el [Explorador de Azure Storage](https://storageexplorer.com/).

    ![Crear file1.txt y cargar en el contenedor de origen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

11. Para volver a la vista **Ejecuciones de canalizaciones**, seleccione el vínculo **Todas las ejecuciones de la canalización** en el menú de la ruta de navegación de la página **Ejecuciones de actividad** y espere a que la misma canalización se desencadene de nuevo automáticamente.  

12. Cuando se complete la segunda ejecución de canalización, siga los mismos pasos mencionados anteriormente para revisar los detalles de la ejecución de actividad.  

    Verá que un archivo (file1.txt) se copió del contenedor de origen al contenedor de destino de la cuenta de Blob Storage:

    ![file1.txt se copió del contenedor de origen al contenedor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

13. Cree otro archivo de texto vacío y asígnele el nombre **file2.txt**. Cargue este archivo de texto en el contenedor de origen en su cuenta de Blob Storage.

14. Repita los pasos 11 y 12 con el segundo archivo de texto. Verá que solamente un archivo (file2.txt) se copió del contenedor de origen al contenedor de destino de la cuenta de almacenamiento durante la ejecución de canalización.  

    También puede comprobar que solo se copió un archivo mediante el uso del [Explorador de Azure Storage](https://storageexplorer.com/) para examinar los archivos:

    ![Examen de archivos mediante el Explorador de Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

## <a name="next-steps"></a>Pasos siguientes
Vaya al tutorial siguiente para aprender a transformar los datos mediante el uso de un clúster de Apache Spark en Azure:

> [!div class="nextstepaction"]
>[Transformación de datos en la nube mediante una actividad de Spark en Azure Data Factory](tutorial-transform-data-spark-portal.md)

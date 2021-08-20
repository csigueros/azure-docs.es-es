---
title: Copia incremental de datos con Change Tracking en Azure Portal
description: En este tutorial, creará una instancia de Azure Data Factory con una canalización que carga los datos diferenciales según la información de control de cambios de la base de datos de origen de Azure SQL Database a una instancia de Azure Blob Storage.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 9dbeeaa10fc755b978a1819a0af181da47149921
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113436845"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-the-azure-portal"></a>Carga incremental de datos de Azure SQL Database a Azure Blob Storage mediante la información de control de cambios en Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial, creará una instancia de Azure Data Factory con una canalización que carga los datos diferenciales según la información de **control de cambios** de la base de datos de origen de Azure SQL Database a una instancia de Azure Blob Storage.  

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación del almacenamiento de datos de origen
> * Creación de una factoría de datos.
> * Cree servicios vinculados.
> * Cree los conjuntos de datos de control de cambios, el origen y el receptor.
> * Creación, ejecución y supervisión de la canalización de copia completa
> * Adición o actualización de datos en la tabla de origen
> * Creación, ejecución y supervisión de la canalización de copia incremental

## <a name="overview"></a>Información general
En una solución de integración de datos, la carga incremental de los datos después de cargas completas iniciales es un método ampliamente usado. En algunos casos, los datos modificados en un período en el almacén de datos de origen pueden ser fácilmente segmentados (por ejemplo, LastModifyTime o CreationTime). En algunos casos, no hay ninguna manera explícita de identificar las diferencias de datos desde la última vez que se procesaron. Para identificar los datos diferenciales, puede usarse la tecnología de control de cambios, admitida por almacenes de datos como Azure SQL Database y SQL Server.  Este tutorial describe cómo usar Azure Data Factory con la tecnología de control de cambios de SQL Server para cargar incrementalmente los datos diferenciales desde Azure SQL Database a Azure Blob Storage.  Para obtener información más concreta sobre la tecnología de control de cambios de SQL, consulte [Acerca del control de cambios (SQL Server)](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

## <a name="end-to-end-workflow"></a>Flujo de trabajo de un extremo a otro
Estos son los pasos del flujo de trabajo completo típico para cargar incrementalmente los datos mediante la tecnología de control de cambios.

> [!NOTE]
> Tanto Azure SQL Database como SQL Server admiten la tecnología de control de cambios. Este tutorial utiliza Azure SQL Database como almacén de datos de origen. También puede usar una instancia de SQL Server.

1. **Carga inicial de datos históricos** (ejecutar una vez):
    1. Habilite la tecnología Change Tracking en la base de datos de origen de Azure SQL Database.
    2. Obtenga el valor inicial de SYS_CHANGE_VERSION en la base de datos como base de referencia para capturar los datos que han cambiado.
    3. Cargue todos los datos de la base de datos de origen en una instancia de Azure Blob Storage.
2. **Carga incremental de los datos diferenciales según una programación** (ejecutar periódicamente después de la carga inicial de datos):
    1. Obtenga los valores SYS_CHANGE_VERSION antiguos y nuevos.
    3. Cargue los datos diferenciales combinando las claves principales de las filas modificadas (entre dos valores SYS_CHANGE_VERSION) desde **sys.change_tracking_tables** con los datos de la **tabla de origen** y, a continuación, muévalos al destino.
    4. Actualice el valor SYS_CHANGE_VERSION para la carga diferencial la próxima vez.

## <a name="high-level-solution"></a>Solución de alto nivel
En este tutorial, creará dos canalizaciones que llevan a cabo las dos operaciones siguientes:  

1. **Carga inicial:** creará una canalización con la actividad de copia que copia todos los datos desde el almacén de datos de origen (Azure SQL Database) al almacén de datos de destino (Azure Blob Storage).

    ![Carga completa de datos](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Carga incremental:** creará una canalización con las siguientes actividades y la ejecutará con regularidad.
    1. Cree **dos actividades de búsqueda** para obtener los valores SYS_CHANGE_VERSION antiguo y nuevo desde Azure SQL Database y pasarlos a la actividad de copia.
    2. Cree **una actividad de copia** para copiar los datos insertados, actualizados o eliminados entre los dos valores SYS_CHANGE_VERSION de Azure SQL Database a Azure Blob Storage.
    3. Cree **una actividad de procedimiento almacenado** para actualizar el valor SYS_CHANGE_VERSION para la ejecución de la siguiente canalización.

    ![Diagrama de flujo de incremento de carga](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
* **Azure SQL Database**. La base de datos se usa como almacén de datos de **origen**. Si no tiene ninguna base de datos en Azure SQL Database, consulte el artículo [Creación de una base de datos en Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) para ver los pasos y crear una.
* **Cuenta de Azure Storage**. Blob Storage se usa como almacén de datos **receptor**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md) para ver los pasos para su creación. Cree un contenedor denominado **adftutorial**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Creación de una tabla de origen de datos en Azure SQL Database

1. Abra **SQL Server Management Studio** y conéctese a SQL Database.
2. En el **Explorador de servidores**, haga clic con el botón derecho en la **base de datos** y elija la **Nueva consulta**.
3. Ejecute el siguiente comando SQL en su base de datos para crear una tabla denominada `data_source_table` como almacén de origen de datos.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```

4. Habilite el mecanismo de **control de cambios** en la base de datos y la tabla de origen (data_source_table) ejecutando la siguiente consulta SQL:

    > [!NOTE]
    > - Reemplace el &lt;nombre de la base de datos&gt; por el nombre de la base de datos de Azure SQL Database que tiene la tabla data_source_table.
    > - Los datos modificados se mantienen durante dos días en el ejemplo actual. Si carga los datos cambiados para cada tres días o más, no se incluyen algunos que han cambiado.  Tiene que cambiar el valor de CHANGE_RETENTION por un número mayor. También puede asegurarse de que el período para cargar los datos cambiados es dentro de dos días. Para más información, vea [Habilitar el control de cambios para una base de datos](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Cree una tabla y almacene el valor predeterminado ChangeTracking_version ejecutando la consulta siguiente:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > Si los datos no cambian después de haber habilitado el control de cambios para SQL Database, el valor de la versión de control de cambios es 0.
6. Ejecute la siguiente consulta para crear un procedimiento almacenado en su base de datos. La canalización invoca este procedimiento almacenado para actualizar la versión de control de cambios en la tabla que creó en el paso anterior.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

    UPDATE table_store_ChangeTracking_version
    SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
1. En el menú de la izquierda, seleccione **Crear un recurso** > **Datos y análisis** > **Data Factory**:

   ![Selección de la factoría de datos en el panel Nuevo](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre).

     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)

   El nombre de la instancia de Azure Data Factory debe ser **único globalmente**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.

   *El nombre "ADFTutorialDataFactory" de factoría de datos no está disponible.*
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos.
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:

      - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable.
      - Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos.   
         
        Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Crear**.      
8. En el panel, verá el icono siguiente con el estado: **Deploying data factory** (Implementación de la factoría de datos).

    ![icono implementando factoría de datos](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

10. Seleccione **Abrir** en el icono **Abrir Azure Data Factory Studio** para iniciar la aplicación de interfaz de usuario (IU) de Azure Data Factory en una pestaña independiente.
11. En la página principal, cambie a la pestaña **Administrar** del panel de la izquierda como se muestra en la siguiente imagen:

    ![Captura de pantalla en la que se muestra el botón Administrar.](media/doc-common-process/get-started-page-manage-button.png)

## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta sección, creará servicios vinculados en su cuenta de Azure Storage y en la base de datos de Azure SQL Database.

### <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage
En este paso, vincula su cuenta de Azure Storage a la factoría de datos.

1. Haga clic en **Connections** (Conexiones) y en **+ New** (+ Nuevo).

   ![Botón para nueva conexión](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure Blob Storage** y haga clic en **Continue** (Continuar).

   ![Seleccionar Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureStorageLinkedService** en **Name** (Nombre).
    2. Seleccione la cuenta de Azure Storage en **Storage account name** (Nombre de la cuenta de Storage).
    3. Haga clic en **Save**(Guardar).

   ![Configuración de la cuenta de Azure Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Creación de un servicio vinculado de Azure SQL Database
En este paso, vinculará la base de datos a la factoría de datos.

1. Haga clic en **Connections** (Conexiones) y en **+ New** (+ Nuevo).
2. En la ventana **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure SQL Database** y haga clic en **Continue** (Continuar).
3. En la ventana **New Linked Service** (Nuevo servicio vinculado), realice los pasos siguientes:

    1. Escriba **AzureSqlDatabaseLinkedService** en el campo **Name** (Nombre).
    2. Seleccione su servidor en el campo **Server name** (Nombre del servidor).
    3. Seleccione la base de datos en el campo **Database name** (Nombre de la base de datos).
    4. Escriba el nombre del usuario en el campo **User name** (Nombre de usuario).
    5. Escriba la contraseña del usuario en el campo **Password** (Contraseña).
    6. Haga clic en **Test connection** (Prueba de conexión) para probar la conexión.
    7. Haga clic en **Save** (Guardar) para guardar el servicio vinculado.

       ![Configuración del servicio vinculado de Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos para representar el origen de datos, el destino de datos. y el lugar donde almacenar el valor SYS_CHANGE_VERSION.

### <a name="create-a-dataset-to-represent-source-data"></a>Creación de un conjunto de datos que represente datos de origen
En este paso, creará conjuntos de datos para representar el origen de datos.

1. En la vista de árbol, haga clic en el **signo + (más)** y en **Dataset** (Conjunto de datos).

   ![Menú New Dataset (Nuevo conjunto de datos)](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar).

   ![Tipo de conjunto de datos de origen: Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Verá una nueva pestaña para configurar el conjunto de datos. También verá el conjunto de datos en la vista de árbol. En la ventana **Properties** (Propiedades), cambie el nombre del conjunto de datos a **SourceDataset**.

   ![Nombre del conjunto de datos de origen](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Cambie a la pestaña **Connection** (Conexión) y realice los pasos siguientes:

    1. Seleccione **AzureSqlDatabaseLinkedService** como **Linked service** (Servicio vinculado).
    2. Seleccione **[dbo].[data_source_table]** en **Table** (Tabla).

   ![Conexión de origen](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Creación de un conjunto de datos que represente los datos copiados en el almacén de datos receptor
En este paso, creará un conjunto de datos para representar los datos que se copian desde el almacén de datos de origen. Ha creado el contenedor adftutorial en la instancia de Azure Blob Storage como parte de los requisitos previos. Cree el contenedor si no existe (o) asígnele el nombre de uno existente. En este tutorial, el nombre de archivo de salida se genera dinámicamente mediante la expresión `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. En la vista de árbol, haga clic en el **signo + (más)** y en **Dataset** (Conjunto de datos).

   ![Menú New Dataset (Nuevo conjunto de datos)](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Seleccione **Azure Blob Storage** y haga clic en **Finish** (Finalizar).

   ![Tipo de conjunto de datos receptor: Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Verá una nueva pestaña para configurar el conjunto de datos. También verá el conjunto de datos en la vista de árbol. En la ventana **Properties** (Propiedades), cambie el nombre del conjunto de datos a **SinkDataset**.

   ![Conjunto de datos receptor: nombre](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Cambie a la pestaña **Connection** (Conexión) de la ventana Properties (Propiedades) y realice los pasos siguientes:

    1. Seleccione **AzureStorageLinkedService** en **Linked service** (Servicio vinculado).
    2. Escriba **adftutorial/incchgtracking** en la parte de **carpeta** de la **ruta de acceso de archivo**.
    3. Escriba **\@CONCAT('Incremental-', pipeline().RunId, '.txt')** en la parte de **archivo** de la **ruta de acceso de archivo**.  

       ![Conjunto de datos receptor: conexión](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Creación de un conjunto de datos que represente datos de seguimiento de cambios
En este paso, creará un conjunto de datos para almacenar la versión de control de cambios.  Ha creado la tabla table_store_ChangeTracking_version como parte de los requisitos previos.

1. En la vista de árbol, haga clic en el **signo + (más)** y en **Dataset** (Conjunto de datos).
2. Seleccione **Azure SQL Database** y haga clic en **Finish** (Finalizar).
3. Verá una nueva pestaña para configurar el conjunto de datos. También verá el conjunto de datos en la vista de árbol. En la ventana **Properties** (Propiedades), cambie el nombre del conjunto de datos a **ChangeTrackingDataset**.
4. Cambie a la pestaña **Connection** (Conexión) y realice los pasos siguientes:

    1. Seleccione **AzureSqlDatabaseLinkedService** como **Linked service** (Servicio vinculado).
    2. Seleccione **[dbo].[table_store_ChangeTracking_version]** en **Table** (Table).

## <a name="create-a-pipeline-for-the-full-copy"></a>Creación de una canalización para la copia completa
En este paso, va a crear una canalización con la actividad de copia que copia todos los datos desde el almacén de datos de origen (Azure SQL Database) al almacén de datos de destino (Azure Blob Storage).

1. Haga clic en el **signo + (más)** en el panel izquierdo y en **Pipeline** (Canalización).

    ![La captura de pantalla muestra la opción de canalización para una factoría de datos.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Verá una nueva pestaña para configurar la canalización. También verá la canalización en la vista de árbol. En la ventana **Properties** (Propiedades), cambie el nombre de la canalización a **FullCopyPipeline**.

    ![La captura de pantalla muestra una canalización con un nombre especificado.](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. En el cuadro de herramientas **Activities** (Actividades), expanda **Data Flow** (Flujo de datos), arrastre la actividad **Copy** (Copiar) y colóquela en la superficie del diseñador de canalizaciones; después, establezca el nombre en **FullCopyActivity**.

    ![Nombre de la actividad de copia completa](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Cambie a la pestaña **Source** (Origen) y seleccione **SourceDataset** en el campo **Source Dataset** (Conjunto de datos de origen).

    ![Actividad de copia: origen](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Cambie a la pestaña **Sink** (Receptor) y seleccione **SinkDataset** en el campo **Sink Dataset** (Conjunto de datos receptor).

    ![Actividad de copia: receptor](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Para comprobar la definición de la canalización, haga clic en **Validate** (Comprobar) en la barra de herramientas. Confirme que no haya errores de comprobación. Para cerrar **Pipeline Validation Report** (Informe de comprobación de la canalización), haga clic en **>>** .

    ![Comprobación de la canalización](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Para publicar entidades (servicios vinculados, conjuntos de datos y canalizaciones), haga clic en **Publish** (Publicar). Espere hasta que la publicación se realice correctamente.

    ![La captura de pantalla muestra la factoría de datos, con el botón para publicar todo seleccionado.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Espere a que aparezca el mensaje **Successfully published** (Publicado correctamente).

    ![Publicación correcta](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Otra manera de ver las notificaciones es hacer clic en el botón **Show Notifications** (Mostrar notificaciones) de la izquierda. Haga clic en la **X** para cerrar la ventana de notificaciones.

    ![Show Notifications (Mostrar notificaciones)](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Ejecución de la canalización de copia completa
Haga clic en **Trigger** (Desencadenar) en la barra de herramientas de la canalización y en **Trigger Now** (Desencadenar ahora).

![La captura de pantalla muestra la opción para desencadenar ahora seleccionada en el menú correspondiente.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Supervisión de la canalización de copia completa

1. Haga clic en la pestaña **Monitor** (Supervisar) de la izquierda. Verá la ejecución de la canalización en la lista y su estado. Haga clic en **Refresh** (Actualizar) para actualizar la lista. Los vínculos de la columna Actions (Acciones) permiten ver las ejecuciones de actividad asociadas a la de la canalización y volver a ejecutar la canalización.

    ![La captura de pantalla muestra las ejecuciones de la canalización para una factoría de datos.](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Para ver las ejecuciones de actividad asociadas a la de la canalización, haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Como solo hay una actividad en la canalización, solo verá una entrada en la lista. Para volver a la vista de ejecuciones de canalización, haga clic en el vínculo **Pipelines** (Canalizaciones) de la parte superior.

    ![La captura de pantalla muestra las ejecuciones de actividad de una factoría de datos, con el vínculo de canalizaciones seleccionado.](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Revisión del resultado
Verá un archivo denominado `incremental-<GUID>.txt` en la carpeta `incchgtracking` del contenedor `adftutorial`.

![Archivo de salida de una copia completa](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

El archivo debe tener los datos de la base de datos:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Adición de más datos a la tabla de origen

Ejecute la siguiente consulta en la base de datos para agregar una fila y actualizarla.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Creación de una canalización para la copia diferencial
En este paso, creará una canalización con las siguientes actividades y la ejecutará con regularidad. Las **actividades de búsqueda** obtienen los valores SYS_CHANGE_VERSION antiguo y nuevo desde Azure SQL Database y los pasan a la actividad de copia. La **actividad de copia** copia los datos insertados, actualizados o eliminados entre los dos valores SYS_CHANGE_VERSION de Azure SQL Database a Azure Blob Storage. La **actividad de procedimiento almacenado** actualiza el valor SYS_CHANGE_VERSION para la ejecución de la siguiente canalización.

1. En la interfaz de usuario de Data Factory, cambie a la pestaña **Edit** (Editar). Haga clic en el **signo + (más)** en el panel izquierdo y en **Pipeline** (Canalización).

    ![La captura de pantalla muestra cómo crear una canalización en una factoría de datos.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Verá una nueva pestaña para configurar la canalización. También verá la canalización en la vista de árbol. En la ventana **Properties** (Propiedades), cambie el nombre de la canalización a **IncrementalCopyPipeline**.

    ![Nombre de la canalización](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. En el cuadro de herramientas **Activities** (Actividades), expanda **General** (General), arrastre la actividad **Lookup** (Búsqueda) y colóquela en la superficie del diseñador de canalizaciones. Establezca el nombre de la actividad en **LookupLastChangeTrackingVersionActivity**. Esta actividad obtiene la versión de seguimiento de cambios de la última operación de copia que se almacenó en la tabla **table_store_ChangeTracking_version**.

    ![La captura de pantalla muestra una canalización con una actividad de búsqueda.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Cambie a **Settings** (Configuración) en la ventana **Properties** (Propiedades) y seleccione **ChangeTrackingDataset** en el campo **Source Dataset** (Conjunto de datos de origen).

    ![La captura de pantalla muestra la pestaña de configuración en la ventana de propiedades.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Arrastre la actividad **Lookup** (Búsqueda) del cuadro de herramientas **Activities** (Actividades) y colóquela en la superficie del diseñador de canalizaciones. Establezca el nombre de la actividad en **LookupCurrentChangeTrackingVersionActivity**. Esta actividad obtiene la versión del seguimiento de cambios actual.

    ![La captura de pantalla muestra una canalización con dos actividades de búsqueda.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Cambie a la pestaña **Settings** (Configuración) de la ventana **Properties** (Propiedades) y realice los pasos siguientes:

   1. Seleccione **SourceDataset** en el campo **Source Dataset** (Conjunto de datos de origen).
   2. Seleccione **Query** (Consulta) en **Use Query** (Usar consulta).
   3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta).

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![La captura de pantalla muestra una consulta agregada a la pestaña de configuración en la ventana de propiedades.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. En el cuadro de herramientas **Activities** (Actividades), expanda **Data Flow** (Flujo de datos), arrastre la actividad **Copy** (Copiar) y colóquela en la superficie del diseñador de canalizaciones. Establezca el nombre de la actividad en **IncrementalCopyActivity**. Esta actividad copia los datos de la última versión del seguimiento de cambios y la actual en el almacén de datos de destino.

    ![Actividad de copia: nombre](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Cambie a la pestaña **Source** (Origen) de la ventana **Properties** (Propiedades) y realice los pasos siguientes:

   1. Seleccione **SourceDataset** como **Source Dataset** (Conjunto de datos de origen).
   2. Seleccione **Query** (Consulta) en **Use Query** (Usar consulta).
   3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta).

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```

      ![Actividad de copia: configuración del origen](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Cambie a la pestaña **Sink** (Receptor) y seleccione **SinkDataset** en el campo **Sink Dataset** (Conjunto de datos receptor).

    ![Actividad de copia: configuración del receptor](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. **Conecte las dos actividades de búsqueda con la actividad de copia** una a una. Arrastre el botón **verde** asociado a la actividad **Lookup** (Búsqueda) a la actividad **Copy** (Copia).

    ![Conexión de las actividades de búsqueda y de copia](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Arrastre la actividad **Stored Procedure** (procedimiento almacenado) del cuadro de herramientas **Activities** (Actividades) y colóquela en la superficie del diseñador de canalizaciones. Establezca el nombre de la actividad en **StoredProceduretoUpdateChangeTrackingActivity**. Esta actividad actualiza la versión de seguimiento de cambios en la tabla **table_store_ChangeTracking_version**.

    ![Actividad de procedimiento almacenado: nombre](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Cambie a la pestaña *SQL Account** (Cuenta de SQL) y seleccione **AzureSqlDatabaseLinkedService** en **Linked service** (Servicio vinculado).

    ![Actividad de procedimiento almacenado: cuenta SQL](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Cambie a la pestaña **Stored Procedure** (Procedimiento almacenado) y realice los pasos siguientes:

    1. Como **Stored procedure name** (Nombre de procedimiento almacenado), seleccione **Update_ChangeTracking_Version**.  
    2. Seleccione **Import parameter** (Importar parámetro).
    3. En la sección **Stored procedure parameters** (Parámetros de procedimiento almacenado), especifique estos valores:

        | Nombre | Tipo | Value |
        | ---- | ---- | ----- |
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} |
        | TableName | String | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} |

        ![Actividad de procedimiento almacenado: parámetros](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Conecte la actividad de copia a la de procedimiento almacenado**. Arrastre el botón **verde** asociado a la actividad Copy (Copiar) y colóquelo en la actividad Stored Procedure (Procedimiento almacenado).

    ![Conexión de las actividades de copia y de procedimiento almacenado](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Haga clic en **Validate** (Comprobar) en la barra de herramientas. Confirme que no haya errores de comprobación. Para cerrar la ventana **Pipeline Validation Report** (Informe de comprobación de la canalización), haga clic en **>>** .

    ![Botón Validate (Comprobar)](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. Para publicar entidades (servicios vinculados, conjuntos de datos y canalizaciones) en el servicio Data Factory, haga clic en el botón **Publish All** (Publicar todo). Espere hasta ver el mensaje **Publishing succeeded** (Publicación correcta).

       ![La captura de pantalla muestra el botón para publicar todo de una factoría de datos.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Ejecución de la canalización de la copia incremental
1. Haga clic en **Trigger** (Desencadenar) en la barra de herramientas de la canalización y en **Trigger Now** (Desencadenar ahora).

    ![La captura de pantalla muestra una canalización con las actividades y la opción para desencadenar ahora seleccionada en el menú correspondiente.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. En la ventana **Pipeline Run** (Ejecución de canalización), seleccione **Finish** (Finalizar).

### <a name="monitor-the-incremental-copy-pipeline"></a>Supervisión de la canalización de la copia incremental
1. Haga clic en la pestaña **Monitor** (Supervisar) de la izquierda. Verá la ejecución de la canalización en la lista y su estado. Haga clic en **Refresh** (Actualizar) para actualizar la lista. Los vínculos de la columna **Actions** (Acciones) permiten ver las ejecuciones de actividad asociadas a la de la canalización y volver a ejecutar la canalización.

    ![La captura de pantalla muestra ejecuciones de la canalización para una factoría de datos, incluida su canalización.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Para ver las ejecuciones de actividad asociadas a la de la canalización, haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Como solo hay una actividad en la canalización, solo verá una entrada en la lista. Para volver a la vista de ejecuciones de canalización, haga clic en el vínculo **Pipelines** (Canalizaciones) de la parte superior.

    ![La captura de pantalla muestra ejecuciones de la canalización para una factoría de datos, con varias de ellas marcadas como correctas.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Revisión del resultado
Verá el segundo archivo `incchgtracking` en la carpeta `adftutorial` del contenedor.

![Archivo de salida de la copia incremental](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

El archivo debe tener solo los datos diferenciales de la base de datos. El registro con `U` es la fila actualizada en la base de datos y `I` es la fila que se agrega.

```
1,update,10,2,U
6,new,50,1,I
```
Las tres primeras columnas son datos que han cambiado de data_source_table. Las dos últimas columnas son los metadatos de la tabla del sistema de control de cambios. La cuarta columna es el valor SYS_CHANGE_VERSION de cada fila modificada. La quinta columna es la operación:  U = actualización, I = inserción.  Para obtener más información acerca de la información de control de cambios, consulte [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10            2                                 U
6        new     50            1                                 I
```

## <a name="next-steps"></a>Pasos siguientes
Pase al tutorial siguiente para obtener información acerca de cómo copiar archivos nuevos y modificados solo según el valor de LastModifiedDate:

> [!div class="nextstepaction"]
> [Copia de archivos nuevos por LastModifiedDate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

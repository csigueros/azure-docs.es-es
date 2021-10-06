---
title: Copia incremental de una tabla mediante Azure Portal
description: En este tutorial, creará una instancia de Azure Data Factory con una canalización que carga los datos diferenciales de una tabla de Azure SQL Database a una instancia de Azure Blob Storage.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 07/05/2021
ms.openlocfilehash: 738c60663f80fd036f50c7bd354ca0e3b1d9284e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757827"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Carga de datos incremental de Azure SQL Database a Azure Blob Storage mediante Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial, creará una instancia de Azure Data Factory con una canalización que carga los datos diferenciales de una tabla de Azure SQL Database a una instancia de Azure Blob Storage.

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación del almacén de datos para almacenar el valor de marca de agua
> * Creación de una factoría de datos.
> * Cree servicios vinculados.
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización
> * Revisión de los resultados
> * Adición de más datos al origen
> * Repetición de la ejecución de la canalización
> * Supervisión de la segunda ejecución de la canalización
> * Revisión de los resultados de la segunda ejecución


## <a name="overview"></a>Información general
Este es el diagrama de solución de alto nivel:

:::image type="content" source="media/tutorial-Incremental-copy-portal/incrementally-load.png" alt-text="Cargar datos de forma incremental":::

Estos son los pasos importantes para crear esta solución:

1. **Seleccione la columna de marca de agua**.
    Seleccione una columna en el almacén de datos de origen, que pueda usarse para segmentar los registros nuevos o actualizados para cada ejecución. Normalmente, los datos de esta columna seleccionada (por ejemplo, last_modify_time o id.) siguen aumentando cuando se crean o se actualizan las filas. El valor máximo de esta columna se utiliza como una marca de agua.

2. **Prepare el almacén de datos para almacenar el valor de marca de agua**. En este tutorial, el valor de marca de agua se almacena en una base de datos SQL.

3. **Cree una canalización con el siguiente flujo de trabajo**:

    La canalización de esta solución consta de las siguientes actividades:

    * Cree dos actividades de búsqueda. Use la primera actividad de búsqueda para recuperar el último valor de marca de agua. y, la segunda actividad, para recuperar el nuevo valor de marca de agua. Estos valores de marca de agua se pasan a la actividad de copia.
    * Cree una actividad de copia que copie filas del almacén de datos de origen con el valor de la columna de marca de agua que sea mayor que el valor anterior y menor que el nuevo. A continuación, copia los datos diferenciales del almacén de datos de origen en Blob Storage como un archivo nuevo.
    * Cree un procedimiento almacenado que actualice el valor de marca de agua de la canalización que se ejecute la próxima vez.


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
* **Azure SQL Database**. La base de datos se usa como almacén de datos de origen. Si no tiene ninguna base de datos de Azure SQL Database, consulte el artículo [Creación de una base de datos en Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) para ver los pasos y crear una.
* **Azure Storage**. Blob Storage se usa como almacén de datos receptor. Si no tiene una cuenta de almacenamiento, consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md) para ver los pasos para su creación. Cree un contenedor denominado adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Creación de una tabla de origen de datos en la base de datos SQL
1. Abra SQL Server Management Studio. En el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

2. Ejecute el siguiente comando SQL en la base de datos SQL para crear una tabla denominada `data_source_table` como el almacén de origen de datos:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    En este tutorial, utilizará LastModifytime como columna de marca de agua. Los datos del almacén de origen de datos se muestran en la tabla siguiente:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Creación de otra tabla en la base de datos SQL para almacenar el valor de límite máximo

1. Ejecute el siguiente comando SQL en la base de datos SQL para crear una tabla denominada `watermarktable` y almacenar el valor de marca de agua:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Establezca el valor predeterminado de límite máximo con el nombre de la tabla del almacén de datos de origen. En este tutorial, el nombre de tabla es data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Revise los datos de la tabla `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Salida:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Creación de un procedimiento almacenado en la base de datos SQL

Ejecute el siguiente comando para crear un procedimiento almacenado en la base de datos SQL:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. En el menú de la izquierda, seleccione **Crear un recurso** > **Integración** > **Data Factory**:

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Selección de Data Factory en el "::: panel &quot;Nuevo&quot;

3. En la página **Nueva factoría de datos**, escriba **ADFIncCopyTutorialDF** para el **nombre**.

   El nombre de la instancia de Azure Data Factory debe ser **único globalmente**. Si ve un signo de exclamación rojo con el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFIncCopyTutorialDF) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.

    *El nombre "ADFIncCopyTutorialDF" de factoría de datos no está disponible.*
4. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos.
5. Para el **grupo de recursos**, realice uno de los siguientes pasos:

      - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable.
      - Seleccione **Crear nuevo** y escriba el nombre de un grupo de recursos.   
         
        Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).  
6. Seleccione **V2** para la **versión**.
7. Seleccione la **ubicación** de Data Factory. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (Azure Storage, Azure SQL Database, Instancia administrada de Azure SQL, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.
8. Haga clic en **Crear**.      
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página principal de Azure Data Factory, con el icono Abrir Azure Data Factory Studio.":::

10. Seleccione **Abrir** en el icono **Abrir Azure Data Factory Studio** para iniciar la aplicación de interfaz de usuario (IU) de Azure Data Factory en una pestaña independiente.

## <a name="create-a-pipeline"></a>Crear una canalización
En este tutorial, creará una canalización con dos actividades de búsqueda, una actividad de copia y un procedimiento almacenado encadenada en una canalización.

1. En la página principal de la interfaz de usuario de Data Factory, haga clic en el icono **Orquestar.**

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Captura de pantalla en la que se muestra la página principal de la interfaz de usuario de Data Factory.":::    
3. En el panel General, en **Propiedades**, especifique **IncrementalCopyPipeline** en **Nombre**. A continuación, contraiga el panel; para ello, haga clic en el icono Propiedades en la esquina superior derecha.

4. Agreguemos la primera actividad de búsqueda para recuperar el valor de marca de agua anterior. En el cuadro de herramientas **Activities** (Actividades), expanda **General** (General), arrastre la actividad **Lookup** (Búsqueda) y colóquela en la superficie del diseñador de canalizaciones. Cambie el nombre de la actividad a **LookupOldWaterMarkActivity**.

   :::image type="content" source="./media/tutorial-incremental-copy-portal/first-lookup-name.png" alt-text="Nombre de la primera actividad de búsqueda":::
5. Cambie a la pestaña **Settings** (Configuración) y haga clic en **+ New** (+ Nuevo) para **Source Dataset** (Conjunto de datos de origen). En este paso, creará conjuntos de datos que representarán los datos de **watermarktable**. Esta tabla contiene la marca de agua que se utilizó anteriormente en la operación de copia anterior.

6. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Continue** (Continuar). Verá que se abre una nueva ventana para el conjunto de datos.

7. En la ventana **Set properties** (Establecer propiedades) para el conjunto de datos, escriba **WatermarkDataset** en **Name** (Nombre).

8. En **Linked Service** (Servicio vinculado), seleccione **New** (Nuevo) y siga estos pasos:

    1. Escriba **AzureSqlDatabaseLinkedService** en **Name** (Nombre).
    2. Seleccione su servidor en **Server name** (Nombre del servidor).
    3. Seleccione el **nombre de la base de datos** en el menú desplegable.
    4. Escriba su **nombre de usuario** &  y **contraseña**.
    5. Para probar la conexión a la base de datos de SQL, haga clic en **Test connection** (Prueba de conexión).
    6. Haga clic en **Finalizar**
    7. Confirme que **AzureSqlDatabaseLinkedService** está seleccionado en **Linked service** (Servicio vinculado).

        :::image type="content" source="./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png" alt-text="Ventana New Linked Service (Nuevo servicio vinculado)":::
    8. Seleccione **Finalizar**.
9. En la pestaña **Connection** (Conexión), seleccione **[dbo].[watermarktable]** en **Table** (Tabla). Si desea una vista previa de los datos de la tabla, haga clic en **Preview data** (Vista previa de los datos).

    :::image type="content" source="./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png" alt-text="Conjunto de datos de marca de agua: configuración de la conexión":::
10. Cambie al editor de canalización; para ello, haga clic en la pestaña de la canalización de la parte superior o en el nombre de esta de la vista de árbol de la izquierda. En la ventana de propiedades de la actividad de **búsqueda**, confirme que **WatermarkDataset** está seleccionado en el campo **Source Dataset** (Conjunto de datos de origen).

11. En el cuadro de herramientas **Activities** (Actividades), expanda **General** (General), y arrastre otra actividad **Lookup** (Búsqueda) y colóquela en la superficie del diseñador de canalizaciones; después, establezca el nombre en **LookupNewWaterMarkActivity** en la pestaña **General** (General) de la ventana de propiedades. Esta actividad de búsqueda obtiene el nuevo valor de marca de agua de la tabla y copia los datos de origen en el destino.

12. En la ventana de propiedades de la segunda actividad de **búsqueda**, cambie a la pestaña **Settings** (Configuración) y haga clic en **New** (Nuevo). Creará un conjunto de datos que apuntará a la tabla de origen con el nuevo valor de marca de agua (valor máximo de LastModifyTime).

13. En la ventana **New Dataset** (Nuevo conjunto de datos), seleccione **Azure SQL Database** y haga clic en **Continue** (Continuar).
14. En la ventana **Set Properties** (Establecer propiedades), escriba **SourceDataset** (Conjunto de datos de origen) en **Name** (Nombre). Seleccione **AzureSqlDatabaseLinkedService** como **Linked service** (Servicio vinculado).
15. Seleccione **[dbo].[data_source_table]** en Table (Tabla). Más adelante en este tutorial especificará una consulta en este conjunto de datos. La consulta tiene prioridad sobre la tabla que se especifica en este paso.
16. Seleccione **Finalizar**.
17. Cambie al editor de canalización; para ello, haga clic en la pestaña de la canalización de la parte superior o en el nombre de esta de la vista de árbol de la izquierda. En la ventana de propiedades de la actividad de **búsqueda**, confirme que **SourceDataset** está seleccionado en el campo **Source Dataset** (Conjunto de datos de origen).
18. Seleccione **Query** (Consulta) en el campo **Use Query** (Usar consulta) y escriba la siguiente consulta: solo se selecciona el valor máximo de **LastModifytime** de **data_source_table**. Asegúrese de haber activado también **First row only** (Solo la primera fila).

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    :::image type="content" source="./media/tutorial-incremental-copy-portal/query-for-new-watermark.png" alt-text="Segunda actividad de búsqueda: consulta":::
19. En el cuadro de herramientas **Activities** (Actividades), expanda **Move & Transform** (Mover y transformar) y arrastre la actividad **Copy** (Copiar) del cuadro de herramientas Activities (Actividades); después, establezca el nombre en  **IncrementalCopyActivity**.

20. **Conecte las dos actividades Lookup (Búsqueda) a la actividad Copy (Copiar)** ; para ello, arrastre el **botón verde** de las actividades de búsqueda a la actividad de copia. Suelte el botón del mouse cuando vea el color del borde de la actividad de copia cambiar a azul.

    :::image type="content" source="./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png" alt-text="Conexión de las actividades de búsqueda a la actividad de copia":::
21. Seleccione la **actividad de copia** y confirme que ve sus propiedades en la ventana **Properties** (Propiedades).

22. Cambie a la pestaña **Source** (Origen) de la ventana **Properties** (Propiedades) y realice los pasos siguientes:

    1. Seleccione **SourceDataset** en el campo **Source Dataset** (Conjunto de datos de origen).
    2. Seleccione **Query** (Consulta) en el campo **Use Query** (Usar consulta).
    3. Escriba la siguiente consulta SQL en el campo **Query** (Consulta).

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        :::image type="content" source="./media/tutorial-incremental-copy-portal/copy-activity-source.png" alt-text="Actividad de copia: origen":::
23. Cambie a la pestaña **Sink** (Receptor) y haga clic en **+ New** (+ Nuevo) en el campo **Sink Dataset** (Conjunto de datos receptor).

24. En este tutorial, el almacén de datos receptor es de tipo Azure Blob Storage. Por lo tanto, seleccione **Azure Blob Storage** y haga clic en **Continue** (Continuar) en la ventana **New Dataset** (Nuevo conjunto de datos).
25. En la página **Select Format** (Seleccionar formato), elija el tipo de formato de los datos y, después, seleccione **Continue** (Continuar).
25. En la ventana **Set Properties** (Establecer propiedades), escriba **SinkDataset** (Conjunto de datos de receptor) en **Name** (Nombre). En **Linked Service** (Servicio vinculado), seleccione **+ New** (+ Nuevo). En este paso se crea una conexión (servicio vinculado) y su instancia de **Azure Blob Storage**.
26. En la ventana **New Linked Service (Azure Blob Storage)** [Nuevo servicio vinculado (Azure Blob Storage)], realice los siguientes pasos:

    1. Escriba **AzureStorageLinkedService** en **Name** (Nombre).
    2. Seleccione la cuenta de Azure Storage en **Storage account name** (Nombre de la cuenta de Storage).
    3. Pruebe la conexión y, a continuación, haga clic en **Finalizar**.

27. En la ventana **Set Properties** (Establecer propiedades), confirme que **AzureStorageLinkedService** está seleccionado para **Linked service** (Servicio vinculado). Después, seleccione **Finalizar**.
28. Vaya a la pestaña **Connection** (Conexión) de SinkDataset y realice los pasos siguientes:
    1. En el campo **File path** (Ruta de acceso de archivo), escriba **adftutorial/incrementalcopy**. **adftutorial** es el nombre del contenedor de blobs; **incrementalcopy**, el de la carpeta. Con este fragmento de código se da por supuesto que tiene un contenedor de blob denominado adftutorial en Blob Storage. Cree el contenedor si no existe o asígnele el nombre de uno existente. Si no existe, Azure Data Factory crea automáticamente la carpeta de salida **incrementalcopy**. También puede usar el botón **Browse** (Examinar) para ir a una carpeta del contenedor de blobs mediante la **ruta de acceso del archivo**.
    2. En la parte **File** (Archivo) del campo **File path** (Ruta de acceso de archivo), seleccione **Add dynamic content [ALT+P]** y, después, escriba `@CONCAT('Incremental-', pipeline().RunId, '.txt')` en la ventana abierta. Después, seleccione **Finalizar**. El nombre de archivo se genera dinámicamente mediante la expresión. Cada ejecución de canalización tiene un identificador único. La actividad de copia usa el identificador de ejecución para generar el nombre de archivo.

28. Cambie al editor de **canalización**; para ello, haga clic en la pestaña de la canalización de la parte superior o en el nombre de esta de la vista de árbol de la izquierda.
29. En el cuadro de herramientas **Activities** (Actividades), expanda **General** (General), arrastre la actividad **Stored Procedure** (Procedimiento almacenado) del cuadro de herramientas **Actividades** para colocarla en la superficie del diseñador de canalizaciones. **Conecte** el resultado verde (correcto) de la actividad **Copy** (Copiar) con la actividad **Stored Procedure** (Procedimiento almacenado).

24. Seleccione **Stored Procedure Activity** (Actividad Procedimiento almacenado) en el diseñador de canalizaciones y cambie el nombre a **StoredProceduretoWriteWatermarkActivity**.

25. Cambie a la pestaña **SQL Account** (Cuenta de SQL) y seleccione **AzureSqlDatabaseLinkedService** en **Linked service** (Servicio vinculado).

26. Cambie a la pestaña **Stored Procedure** (Procedimiento almacenado) y realice los pasos siguientes:

    1. En **Stored procedure name** (Nombre del procedimiento almacenado), seleccione **usp_write_watermark**.
    2. Para especificar valores para los parámetros del procedimiento almacenado, haga clic en **Import parameter** (Importar parámetro) y escriba los valores siguientes:

        | Nombre | Tipo | Value |
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

        :::image type="content" source="./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png" alt-text="Actividad de procedimiento almacenado: configuración del procedimiento almacenado":::
27. Para comprobar la configuración de canalización, haga clic en **Validate** (Comprobar) en la barra de herramientas. Confirme que no haya errores de comprobación. Para cerrar la ventana **Pipeline Validation Report** (Informe de comprobación de la canalización), haga clic en >>.   

28. Para publicar entidades (servicios vinculados, conjuntos de datos y canalizaciones) en el servicio Azure Data Factory, seleccione el botón **Publish All** (Publicar todo). Espere hasta que vea un mensaje de que la publicación se completó correctamente.


## <a name="trigger-a-pipeline-run"></a>Desencadenamiento de una ejecución de la canalización
1. Haga clic en **Add Trigger** (Agregar desencadenar) en la barra de herramientas y en **Trigger Now** (Desencadenar ahora).

2. En la ventana **Pipeline Run** (Ejecución de canalización), seleccione **Finish** (Finalizar).

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Verá el estado de ejecución de la canalización desencadenada por un desencadenador manual. Puede usar los vínculos de la columna **NOMBRE DE CANALIZACIÓN** para ver los detalles de la ejecución y volver a ejecutar la canalización.

2. Para ver las ejecuciones de actividad asociadas a la ejecución de la canalización, seleccione el vínculo en la columna **NOMBRE DE CANALIZACIÓN**. Para más información sobre las ejecuciones de actividad, seleccione el vínculo **Detalles** (icono de gafas) en la columna **NOMBRE DE ACTIVIDAD**. Para volver a la vista Ejecuciones de canalización, seleccione **All pipeline runs** (Todas las ejecuciones de canalización) en la parte superior. Para actualizar la vista, seleccione **Refresh** (Actualizar).


## <a name="review-the-results"></a>Revisión del resultado
1. Puede conectarse a su cuenta de Azure Storage mediante herramientas como el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Compruebe que se crea un archivo de salida en la carpeta **incrementalcopy** del contenedor **adftutorial**.

    :::image type="content" source="./media/tutorial-incremental-copy-portal/first-output-file.png" alt-text="Primer archivo de salida":::
2. Abra el archivo de salida y observe que todos los datos se hayan copiado de **data_source_table** en el archivo de blobs.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Compruebe el valor más reciente de `watermarktable`. Verá que se ha actualizado el valor de marca de agua.

    ```sql
    Select * from watermarktable
    ```

    Este es el resultado:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>Incorporación de más datos al origen

Inserte nuevos datos en la base de datos (almacén de origen de datos).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

Los datos actualizados de la base de datos son los siguientes:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```

## <a name="trigger-another-pipeline-run"></a>Desencadenamiento de otra ejecución de canalización

1. Cambie a la pestaña **Edit** (Editar). Si no está abierta en el diseñador, haga clic en la canalización en la vista de árbol.

2. Haga clic en **Add Trigger** (Agregar desencadenar) en la barra de herramientas y en **Trigger Now** (Desencadenar ahora).


## <a name="monitor-the-second-pipeline-run"></a>Supervisión de la segunda ejecución de la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Verá el estado de ejecución de la canalización desencadenada por un desencadenador manual. Puede usar los vínculos de la columna **PIPELINE NAME** (Nombre de la canalización) para ver los detalles de la actividad y volver a ejecutar la canalización.

2. Para ver las ejecuciones de actividad asociadas a la ejecución de la canalización, seleccione el vínculo en la columna **NOMBRE DE CANALIZACIÓN**. Para más información sobre las ejecuciones de actividad, seleccione el vínculo **Detalles** (icono de gafas) en la columna **NOMBRE DE ACTIVIDAD**. Para volver a la vista Ejecuciones de canalización, seleccione **All pipeline runs** (Todas las ejecuciones de canalización) en la parte superior. Para actualizar la vista, seleccione **Refresh** (Actualizar).


## <a name="verify-the-second-output"></a>Comprobación de la segunda salida

1. En la instancia de Blob Storage, verá que se ha creado otro archivo. En este tutorial, el nombre de archivo nuevo es `Incremental-<GUID>.txt`. Abra ese archivo y verá que contiene dos filas de registros.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Compruebe el valor más reciente de `watermarktable`. Verá que se ha vuelto a actualizar el valor de marca de agua.

    ```sql
    Select * from watermarktable
    ```
    salida de ejemplo:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Pasos siguientes
En este tutorial, realizó los pasos siguientes:

> [!div class="checklist"]
> * Preparación del almacén de datos para almacenar el valor de marca de agua
> * Creación de una factoría de datos.
> * Cree servicios vinculados.
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización
> * Revisión de los resultados
> * Adición de más datos al origen
> * Repetición de la ejecución de la canalización
> * Supervisión de la segunda ejecución de la canalización
> * Revisión de los resultados de la segunda ejecución

En este tutorial, la canalización copió datos de una única tabla de SQL Database a Blob Storage. Avance al tutorial siguiente para aprender a copiar datos de varias tablas de una base de datos de SQL Server en SQL Database.

> [!div class="nextstepaction"]
>[Carga incremental de datos de varias tablas de SQL Server a Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)

---
title: Actividad de procedimiento almacenado de SQL Server
description: Obtenga información sobre cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una base de datos de Azure SQL o una instancia de Azure Synapse Analytics desde una canalización de Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 88a4281d564b7061e831a66b35e768e6377a0115
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397311"
---
# <a name="sql-server-stored-procedure-activity"></a>Actividad de procedimiento almacenado de SQL Server
> [!div class="op_single_selector" title1="Actividades de transformación"]
> * [Actividad de Hive](data-factory-hive-activity.md)
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución por lotes de ML Studio (clásico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad de actualización de recurso de ML Studio (clásico)](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Azure Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de la [transformación de datos mediante la actividad de procedimientos almacenados en Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Información general
Las actividades de transformación en una [canalización](data-factory-create-pipelines.md) de Data Factory transforman y procesan sus datos sin procesar para convertirlos en predicciones y perspectivas. La actividad de procedimiento almacenado es una de las actividades de transformación que admite Data Factory. Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md), que presenta información general de la transformación de datos y las actividades de transformación admitidas en Data Factory.

Puede usar la actividad de procedimiento almacenado para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos de la empresa o en una máquina virtual (VM) de Azure:

- Azure SQL Database
- Azure Synapse Analytics
- Base de datos de SQL Server. Si se usa SQL Server, se debe instalar la puerta de enlace de administración de datos en el mismo equipo que hospeda la base de datos o en un equipo independiente que tenga acceso a la base de datos. La puerta de enlace de administración de datos es un componente que conecta orígenes de datos locales o en la máquina virtual de Azure con servicios en la nube de forma segura y administrada. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para obtener detalles.

> [!IMPORTANT]
> Al copiar datos en Azure SQL Database o SQL Server, se puede configurar **SqlSink** en la actividad de copia para invocar un procedimiento almacenado mediante la propiedad **sqlWriterStoredProcedureName**. Para más información, consulte [Invocación del procedimiento almacenado desde la actividad de copia en Azure Data Factory](data-factory-invoke-stored-procedure-from-copy-activity.md). Para más información sobre la propiedad, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). No se admite la invocación de un procedimiento almacenado al copiar datos en Azure Synapse Analytics mediante una actividad de copia. Pero se puede usar la actividad de procedimiento almacenado para invocar un procedimiento almacenado de Azure Synapse Analytics.
>
> Al copiar datos de Azure SQL Database, SQL Server o Azure Synapse Analytics, se puede configurar **SqlSource** en la actividad de copia para invocar un procedimiento almacenado de lectura de datos desde la base de datos de origen mediante la propiedad **sqlReaderStoredProcedureName**. Para más información, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

El siguiente procedimiento usa la actividad del procedimiento almacenado en una canalización para invocar un procedimiento almacenado en Azure SQL Database.

## <a name="walkthrough"></a>Tutorial
### <a name="sample-table-and-stored-procedure"></a>Procedimiento almacenado y tabla de ejemplo

1. Cree la siguiente **tabla** en su base de datos de Azure SQL con SQL Server Management Studio o cualquier otra herramienta que le resulte cómoda. La columna datetimestamp indica la fecha y la hora en que se generó el identificador correspondiente.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    `Id` es el identificador único y, la columna `datetimestamp` indica la fecha y la hora en que se generó el identificador correspondiente.
    
    ![Datos de muestra](./media/data-factory-stored-proc-activity/sample-data.png)

    En este ejemplo, el procedimiento almacenado está en una base de datos de Azure SQL Database. Si el procedimiento almacenado está en Azure Synapse Analytics y una base de datos de SQL Server, el enfoque es similar. Para una base de datos de SQL Server, debe instalar una [Puerta de enlace de administración de datos](data-factory-data-management-gateway.md).
    
2. Cree el siguiente **procedimiento almacenado**, que inserta datos en **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > El **nombre** y el **uso de mayúsculas y minúsculas** en el parámetro (DateTime en este ejemplo) tienen que coincidir con los del parámetro especificado en el código JSON de la canalización o actividad. En la definición del procedimiento almacenado, asegúrese de que se usa **\@** como prefijo del parámetro.

### <a name="create-a-data-factory"></a>Crear una factoría de datos
1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **NUEVO**, en **Inteligencia y análisis** y en **Data Factory**.

    ![Nueva factoría de datos 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. En la hoja **Nueva factoría de datos**, escriba **SProcDF** para el nombre. Los nombres de Azure Data Factory son **únicos globalmente**. Es necesario agregar su nombre como prefijo al nombre de la factoría de datos para permitir la creación correcta de la factoría.

   ![Nueva factoría de datos 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Seleccione la **suscripción de Azure**.
5. Para el **grupo de recursos**, realice uno de los siguientes pasos:
   1. Haga clic en **Crear nuevo** y escriba un nombre para el grupo de recursos.
   2. Haga clic en **Usar existente** y seleccione un grupo de recursos existente.
6. Seleccione la **ubicación** de Data Factory.
7. Seleccione **Anclar al panel** de manera que pueda ver la factoría de datos en el panel la próxima vez que inicie sesión.
8. Haga clic en **Crear** en la hoja **Nueva fábrica de datos**.
9. Verá la factoría de datos que se crea en el **panel** de Azure Portal. Tras crear correctamente la factoría de datos, se ve la página de la factoría de datos, que muestra el contenido de la misma.

   ![Página principal de Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Crear un servicio vinculado SQL de Azure.
Después de crear la factoría de datos, cree un servicio vinculado de Azure SQL que conecte su base de datos de Azure SQL Database (que contiene la tabla sampletable y el procedimiento almacenado usp_sample) con la factoría de datos.

1. En la hoja **Crear e implementar**, haga clic en la hoja **Data Factory** para que **SProcDF** inicie Data Factory Editor.
2. Haga clic en **Nuevo almacén de datos** en la barra de comandos y elija **Azure SQL Database**. Debería ver el script JSON para crear un servicio vinculado SQL de Azure en el editor.

   ![Nuevo almacén de datos 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. Realice los siguientes cambios en el script JSON:

   1. Reemplace `<servername>` por el nombre del servidor.
   2. Reemplace `<databasename>` por la base de datos en la que creó la tabla y el procedimiento almacenado.
   3. Reemplace `<username@servername>` por la cuenta de usuario que tiene acceso a la base de datos.
   4. Reemplace `<password>` por la contraseña de la cuenta de usuario.

      ![Nuevo almacén de datos 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Para implementar el servicio vinculado, haga clic en **Implementar** en la barra de comandos. Confirme que AzureSqlLinkedService aparece en la vista de árbol a la izquierda.

    ![vista de árbol con servicios vinculados 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida
Debe especificar un conjunto de datos de salida para una actividad de procedimiento almacenado aunque el procedimiento almacenado no genere ningún dato. Esto se debe a que se trata del conjunto de datos de salida que determina la programación de la actividad (frecuencia con que se ejecuta la actividad: cada hora, diariamente, etc.). El conjunto de datos de salida debe usar un **servicio vinculado** que haga referencia a una base de datos de Azure SQL, una instancia de Azure Synapse Analytics o una base de datos de SQL Server en la que quiera que el procedimiento almacenado se ejecute. El conjunto de datos de salida puede usarse como una forma de pasar el resultado del procedimiento almacenado para su posterior procesamiento por otra actividad ([encadenamiento de actividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) en la canalización. Sin embargo, Data Factory no escribe automáticamente la salida de un procedimiento almacenado en este conjunto de datos. Es el procedimiento almacenado el que escribe en una tabla SQL a la que apunta el conjunto de datos de salida. En algunos casos, el conjunto de datos de salida puede ser un **conjunto de datos ficticio** (un conjunto de datos que apunta a una tabla que no contiene realmente la salida del procedimiento almacenado). Este conjunto de datos ficticio solo se usa para especificar la programación de la ejecución de la actividad de procedimiento almacenado.

1. Haga clic en **... Más** en la barra de herramientas, haga clic en **Nuevo conjunto de datos** y en **Azure SQL**. Haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Azure SQL**.

    ![vista de árbol con servicios vinculados 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie y pegue el siguiente script JSON en el editor de JSON.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Para implementar el conjunto de datos, haga clic en **Implementar** en la barra de comandos. Confirme que el conjunto de datos aparece en la vista de árbol.

    ![vista de árbol con servicios vinculados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Crear una canalización con una actividad SqlServerStoredProcedure
Ahora, vamos a crear una canalización con una actividad de procedimiento almacenado.

Tenga en cuenta las siguientes propiedades:

- La propiedad **type** se establece en **SqlServerStoredProcedure**.
- El valor de **storedProcedureName** de las propiedades de tipo se establece en **usp_sample** (nombre del procedimiento almacenado).
- La sección **storedProcedureParameters** contiene un parámetro llamado **DateTime**. El nombre y el uso de mayúsculas y minúsculas en el parámetro JSON deben coincidir con los del parámetro de la definición del procedimiento almacenado. Si necesita pasar null para un parámetro, use la sintaxis: `"param1": null` null (todo en minúsculas).

1. Haga clic en **... Más** en la barra de comandos y en **Nueva canalización**.
2. Copie y pegue el siguiente fragmento de código JSON:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Para implementar la canalización, haga clic en **Implementar** en la barra de herramientas.

### <a name="monitor-the-pipeline"></a>Supervisar la canalización
1. Haga clic en el botón **X** para cerrar las hojas del Editor de Data Factory y volver a la hoja de Data Factory. A continuación, haga clic en **Diagrama**.

    ![Icono Diagrama 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. En la **Vista de diagrama**, se ve información general de las canalizaciones y los conjuntos de datos empleados en este tutorial.

    ![Icono Diagrama 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. En la Vista de diagrama, haga doble clic en el conjunto de datos `sprocsampleout`. Verá los segmentos con estado Listo. Debería haber cinco segmentos, porque se genera un segmento para cada hora entre la hora de inicio y de finalización del código JSON.

    ![Icono Diagrama 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Cuando un segmento tiene el estado **Listo**, ejecute una consulta `select * from sampletable` en la base de datos para comprobar que el procedimiento almacenado insertó los datos en la tabla.

   ![Datos de salida](./media/data-factory-stored-proc-activity/output.png)

   Vea [Supervisar la canalización](data-factory-monitor-manage-pipelines.md) para obtener información detallada sobre cómo supervisar las canalizaciones de Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Especificación de un conjunto de datos de entrada
En el tutorial, la actividad de procedimiento almacenado no tiene ningún conjunto de datos de entrada. Si especifica un conjunto de datos de entrada, la actividad de procedimiento almacenado no se ejecuta hasta que el segmento del conjunto de datos de entrada esté disponible (en estado Listo). El conjunto de datos puede ser externo (no producido por otra actividad de la misma canalización) o interno que produce una actividad del canal de subida (la actividad que se ejecuta antes de esta). Puede especificar varios conjuntos de datos de entrada para la actividad de procedimiento almacenado. Si lo hace, la actividad de procedimiento almacenado solo se ejecuta cuando están disponibles todos los sectores de conjunto de datos de entrada (en estado Listo). El conjunto de datos de entrada no se puede usar en el procedimiento almacenado como parámetro. Solo se utiliza para comprobar la dependencia antes de iniciar la actividad de procedimiento almacenado.

## <a name="chaining-with-other-activities"></a>Encadenamiento con otras actividades
Si quiere encadenar una actividad del canal de subida a esta actividad, especifique la salida de la actividad del canal de subida como entrada de esta actividad. Al hacerlo, la actividad de procedimiento almacenado no se ejecuta hasta que la actividad del canal de subida finalice y el conjunto de datos de salida de la actividad del canal de subida esté disponible (en estado Listo). Puede especificar conjuntos de datos de salida de varias actividades del canal de subida como conjuntos de datos de entrada de la actividad de procedimiento almacenado. Si lo hace, la actividad de procedimiento almacenado solo se ejecuta cuando están disponibles todos los sectores de conjunto de datos de entrada.

En el ejemplo siguiente, la salida de la actividad de copia es: OutputDataset, que es una entrada de la actividad de procedimiento almacenado. Por consiguiente, la actividad de procedimiento almacenado no se ejecuta hasta que la actividad de copia finalice y el segmento OutputDataset esté disponible (en estado Listo). Si especifica varios conjuntos de datos de entrada, la actividad de procedimiento almacenado no se ejecuta hasta que todos los segmentos del conjunto de datos de entrada estén disponibles (en estado Listo). Los conjuntos de datos de entrada no se pueden usar directamente como parámetros en la actividad de procedimiento almacenado.

Para obtener más información sobre cómo encadenar actividades, vea [Varias actividades en una canalización](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline).

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

De manera similar, para vincular la actividad de procedimiento almacenado con **actividades de bajada** (las actividades que se ejecutan después de que finalice la actividad de procedimiento almacenado), especifique el conjunto de datos de salida de la actividad de procedimiento almacenado como entrada de la actividad de bajada en la canalización.

> [!IMPORTANT]
> Al copiar datos en Azure SQL Database o SQL Server, se puede configurar **SqlSink** en la actividad de copia para invocar un procedimiento almacenado mediante la propiedad **sqlWriterStoredProcedureName**. Para más información, consulte [Invocación del procedimiento almacenado desde la actividad de copia en Azure Data Factory](data-factory-invoke-stored-procedure-from-copy-activity.md). Para más información sobre la propiedad, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Al copiar datos de Azure SQL Database, SQL Server o Azure Synapse Analytics, se puede configurar **SqlSource** en la actividad de copia para invocar un procedimiento almacenado de lectura de datos desde la base de datos de origen mediante la propiedad **sqlReaderStoredProcedureName**. Para más información, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formato JSON
Este es el formato JSON para definir una actividad de procedimiento almacenado:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

En la tabla siguiente se describen estas propiedades JSON:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| name | Nombre de la actividad |Sí |
| description |Texto que describe para qué se usa la actividad. |No |
| type | Se debe establecer en: **SqlServerStoredProcedure** | Sí |
| inputs | Opcional. Si especifica un conjunto de datos de entrada, debe estar disponible (en estado "Listo") para que se ejecute la actividad de procedimiento almacenado. El conjunto de datos de entrada no se puede usar en el procedimiento almacenado como parámetro. Solo se utiliza para comprobar la dependencia antes de iniciar la actividad de procedimiento almacenado. |No |
| outputs | Debe especificar un conjunto de datos para una actividad de procedimiento almacenado. El conjunto de datos de salida especifica la **programación** para la actividad de procedimiento almacenada (por hora, semanal, mensual, etc.). <br/><br/>El conjunto de datos de salida debe usar un **servicio vinculado** que haga referencia a una base de datos de Azure SQL, una instancia de Azure Synapse Analytics o una base de datos de SQL Server en la que quiera que el procedimiento almacenado se ejecute. <br/><br/>El conjunto de datos de salida puede usarse como una forma de pasar el resultado del procedimiento almacenado para su posterior procesamiento por otra actividad ([encadenamiento de actividades](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) en la canalización. Sin embargo, Data Factory no escribe automáticamente la salida de un procedimiento almacenado en este conjunto de datos. Es el procedimiento almacenado el que escribe en una tabla SQL a la que apunta el conjunto de datos de salida. <br/><br/>En algunos casos, el conjunto de datos de salida puede ser un **conjunto de datos ficticio**, que solo se utilice para especificar la programación para ejecutar la actividad de procedimiento almacenado. |Sí |
| storedProcedureName |Especifique el nombre del procedimiento almacenado en Azure SQL Database, Azure Synapse Analytics o SQL Server que se representa mediante el servicio vinculado que usa la tabla de salida. |Sí |
| storedProcedureParameters |Especifique valores para los parámetros del procedimiento almacenado. Si necesita pasar null para un parámetro, use la sintaxis: "param1": null (todo en minúsculas). Vea el ejemplo siguiente para aprender el uso de esta propiedad. |No |

## <a name="passing-a-static-value"></a>Pasar un valor estático
Ahora, consideremos la opción de agregar otra columna denominada "Escenario" en la tabla que contiene un valor estático denominado "Ejemplo de documento".

![Datos de ejemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabla:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedimiento almacenado:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Ahora, pase el parámetro **Escenario** y el valor desde la actividad de procedimiento almacenado. La sección **typeProperties** del ejemplo anterior es similar al siguiente fragmento de código:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Conjuntos de datos de Data Factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Canalización de Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```

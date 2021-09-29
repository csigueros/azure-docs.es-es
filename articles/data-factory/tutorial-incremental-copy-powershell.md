---
title: Copia incremental de una tabla mediante PowerShell
description: En este tutorial, va a crear una canalización de Azure Data Factory que copia datos de forma incremental de una base de datos de Azure SQL a Azure Blob Storage.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: 9b1592f76917975e06e7d8a36cb618cbe3abadcf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757788"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-powershell"></a>Carga de datos incremental de Azure SQL Database a Azure Blob Storage mediante PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este tutorial, usará una instancia de Azure Data Factory para crear una canalización que carga los datos diferenciales de una tabla de Azure SQL Database a una instancia de Azure Blob Storage.

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación del almacén de datos para almacenar el valor de marca de agua
> * Creación de una factoría de datos.
> * Cree servicios vinculados.
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización

## <a name="overview"></a>Información general
Este es el diagrama de solución de alto nivel:

:::image type="content" source="media/tutorial-Incrementally-copy-powershell/incrementally-load.png" alt-text="Cargar datos de forma incremental":::

Estos son los pasos importantes para crear esta solución:

1. **Seleccione la columna de marca de agua**.
    Seleccione una columna en el almacén de datos de origen, que pueda usarse para segmentar los registros nuevos o actualizados para cada ejecución. Normalmente, los datos de esta columna seleccionada (por ejemplo, last_modify_time o id.) siguen aumentando cuando se crean o se actualizan las filas. El valor máximo de esta columna se utiliza como una marca de agua.

2. **Prepare el almacén de datos para almacenar el valor de marca de agua**.   
    En este tutorial, el valor de marca de agua se almacena en una base de datos SQL.

3. **Cree una canalización con el siguiente flujo de trabajo**:

    La canalización de esta solución consta de las siguientes actividades:

    * Cree dos actividades de búsqueda. Use la primera actividad de búsqueda para recuperar el último valor de marca de agua. y, la segunda actividad, para recuperar el nuevo valor de marca de agua. Estos valores de marca de agua se pasan a la actividad de copia.
    * Cree una actividad de copia que copie filas del almacén de datos de origen con el valor de la columna de marca de agua que sea mayor que el valor anterior y menor que el nuevo. A continuación, copia los datos diferenciales del almacén de datos de origen en Blob Storage como un archivo nuevo.
    * Cree un procedimiento almacenado que actualice el valor de marca de agua de la canalización que se ejecute la próxima vez.


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL Database**. La base de datos se usa como almacén de datos de origen. Si no tiene ninguna base de datos de Azure SQL Database, consulte el artículo [Creación de una base de datos en Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) para ver los pasos y crear una.
* **Azure Storage**. Blob Storage se usa como almacén de datos receptor. Si no tiene una cuenta de almacenamiento, consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-account-create.md) para ver los pasos para su creación. Cree un contenedor denominado adftutorial. 
* **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-Az-ps).

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
1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/management/overview.md) entre comillas dobles y ejecute el comando. Un ejemplo es `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y vuelva a ejecutar el comando.

2. Defina una variable para la ubicación de la factoría de datos.

    ```powershell
    $location = "East US"
    ```
3. Para crear el grupo de recursos de Azure, ejecute el comando siguiente:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y vuelva a ejecutar el comando.

4. Defina una variable para el nombre de la factoría de datos.

    > [!IMPORTANT]
    >  Actualice el nombre de la factoría de datos para que sea globalmente único. Un ejemplo es ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Para crear la factoría de datos, ejecute el siguiente cmdlet, **Set-AzDataFactoryV2**:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

Tenga en cuenta los siguientes puntos:

* El nombre de la factoría de datos debe ser globalmente único. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles colaborador o propietario, o de administrador de la suscripción de Azure.
* Para una lista de las regiones de Azure en las que Data Factory está disponible actualmente, seleccione las regiones que le interesen en la página siguiente y expanda **Análisis** para poder encontrar **Data Factory**: [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Los almacenes de datos (Storage, SQL Database, Instancia administrada de Azure SQL, etc.) y los procesos (Azure HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.


## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta sección, creará servicios vinculados a la cuenta de almacenamiento y a SQL Database.

### <a name="create-a-storage-linked-service"></a>Creación de un servicio vinculado de Storage
1. Cree un archivo JSON llamado AzureStorageLinkedService.json en la carpeta C:\ADF con el siguiente contenido. (Cree la carpeta ADF si no existe). Reemplace `<accountName>` y `<accountKey>` con el nombre y la clave de la cuenta de almacenamiento antes de guardar el archivo.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. En PowerShell, cambie a la carpeta ADF.

3. Ejecute el cmdlet **Set-AzDataFactoryV2LinkedService** para crear el servicio vinculado AzureStorageLinkedService. En el ejemplo siguiente, debe pasar los valores de los parámetros *ResourceGroupName* y *DataFactoryName*:

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-a-sql-database-linked-service"></a>Creación de un servicio vinculado a SQL Database
1. Cree un archivo JSON llamado AzureSQLDatabaseLinkedService.json en la carpeta C:\ADF con el siguiente contenido. (Cree la carpeta ADF si no existe). Antes de guardar el archivo, reemplace &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; y &lt;password&gt; con el nombre del servidor, base de datos, identificador de usuario y contraseña.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. En PowerShell, cambie a la carpeta ADF.

3. Ejecute el cmdlet **Set-AzDataFactoryV2LinkedService** para crear el servicio vinculado AzureSQLDatabaseLinkedService.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, debe crear conjuntos de datos que representan los datos de origen y del receptor.

### <a name="create-a-source-dataset"></a>Creación de un conjunto de datos de origen

1. Cree un archivo JSON llamado SourceDataset.json en la misma carpeta con el siguiente contenido:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }

    ```
    En este tutorial, usará el nombre de tabla data_source_table. Reemplácelo si utiliza una tabla con otro nombre.

2. Ejecute el cmdlet **Set-AzDataFactoryV2Dataset** para crear el conjunto de datos SourceDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Creación de un conjunto de datos receptor

1. Cree un archivo JSON llamado SinkDataset.json en la misma carpeta con el siguiente contenido:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Este fragmento de código da por supuesto que tiene un contenedor de blobs denominado `adftutorial` en Blob Storage. Cree el contenedor si no existe o asígnele el nombre de uno existente. La carpeta de salida `incrementalcopy` se crea automáticamente si no existe en el contenedor. En este tutorial, el nombre de archivo se genera dinámicamente mediante la expresión `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Ejecute el cmdlet **Set-AzDataFactoryV2Dataset** para crear el conjunto de datos SinkDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Creación de un conjunto de datos para una marca de agua
En este paso, creará un conjunto de datos para almacenar un valor de límite máximo.

1. Cree un archivo JSON llamado WatermarkDataset.json en la misma carpeta con el siguiente contenido:

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Ejecute el cmdlet **Set-AzDataFactoryV2Dataset** para crear el conjunto de datos WatermarkDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:

    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Crear una canalización
En este tutorial, creará una canalización con dos actividades de búsqueda, una actividad de copia y un procedimiento almacenado encadenada en una canalización.


1. Cree un archivo JSON llamado IncrementalCopyPipeline.json en la misma carpeta con el siguiente contenido:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },

                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },

                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },

                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],

                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },

                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {

                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },

                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },

                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]

        }
    }
    ```


2. Ejecute el cmdlet **Set-AzDataFactoryV2Pipeline** para crear la canalización IncrementalCopyPipeline.

   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Este es la salida de ejemplo:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```

## <a name="run-the-pipeline"></a>Ejecución de la canalización

1. Ejecute la canalización IncrementalCopyPipeline mediante el cmdlet **Invoke-AzDataFactoryV2Pipeline**. Reemplace los marcadores de posición por su propio grupo de recursos y el nombre de la factoría de datos.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
2. Compruebe el estado de la canalización ejecutando el cmdlet **Get-AzDataFactoryV2ActivityRun** hasta que vea que todas las actividades se ejecutan correctamente. Reemplace los marcadores de posición con la hora que le resulte adecuada para los parámetros *RunStartedAfter* y *RunStartedBefore*. En este tutorial, se usará  *-RunStartedAfter "2017/09/14"* y *-RunStartedBefore "2017/09/15"* .

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Este es la salida de ejemplo:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Revisión del resultado

1. En la instancia de Blob Storage (almacén de receptor), verá que los datos se han copiado en el archivo definido en SinkDataset. En el tutorial actual, el nombre de archivo es `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Abra el archivo y podrá ver que los registros del archivo son los mismos que los datos de la base de datos SQL.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
2. Compruebe el valor más reciente de `watermarktable`. Verá que se ha actualizado el valor de marca de agua.

    ```sql
    Select * from watermarktable
    ```

    Este es la salida de ejemplo:

    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Inserción de datos en el almacén de origen de datos para comprobar la carga de datos diferencial

1. Inserte nuevos datos en la base de datos SQL (almacén de origen de datos).

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ```

    Los datos actualizados en la base de datos SQL son los siguientes:

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
2. Ejecute de nuevo la canalización IncrementalCopyPipeline mediante el cmdlet **Invoke-AzDataFactoryV2Pipeline**. Reemplace los marcadores de posición por su propio grupo de recursos y el nombre de la factoría de datos.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Compruebe el estado de la canalización ejecutando el cmdlet **Get-AzDataFactoryV2ActivityRun** hasta que vea que todas las actividades se ejecutan correctamente. Reemplace los marcadores de posición con la hora que le resulte adecuada para los parámetros *RunStartedAfter* y *RunStartedBefore*. En este tutorial, se usará  *-RunStartedAfter "2017/09/14"* y *-RunStartedBefore "2017/09/15"* .

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Este es la salida de ejemplo:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. En la instancia de Blob Storage, verá que se ha creado otro archivo. En este tutorial, el nombre de archivo nuevo es `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Abra ese archivo y verá que contiene dos filas de registros.

5. Compruebe el valor más reciente de `watermarktable`. Verá que se ha vuelto a actualizar el valor de marca de agua.

    ```sql
    Select * from watermarktable
    ```
    salida de ejemplo:

    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000


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

En este tutorial, la canalización copió datos de una única tabla de Azure SQL Database a Blob Storage. Avance al tutorial siguiente para aprender a copiar datos de varias tablas de una base de datos de SQL Server en SQL Database.

> [!div class="nextstepaction"]
>[Carga incremental de datos de varias tablas de SQL Server a Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

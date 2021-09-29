---
title: 'Invocación del paquete de SSIS mediante Azure Data Factory: actividad de procedimiento almacenado'
description: En este artículo se describe cómo invocar un paquete de SQL Server Integration Services (SSIS) desde una canalización de Azure Data Factory mediante la actividad de procedimiento almacenado.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 718ca020577a33e5225d46579ee62d8a9859b18a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641396"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Invocación de un paquete de SSIS mediante una actividad de procedimiento almacenado de Azure Data Factory
En este artículo se describe cómo invocar un paquete de SSIS desde una canalización de Azure Data Factory mediante una actividad de procedimiento almacenado. 

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte [Invocar paquetes SSIS mediante la actividad de procedimiento almacenado](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-sql-database"></a>Azure SQL Database 
En el tutorial de este artículo se usa Azure SQL Database. También puede usar una Instancia administrada de Azure SQL.

### <a name="create-an-azure-ssis-integration-runtime"></a>Creación de un entorno de ejecución de integración de SSIS para Azure
Cree una instancia de Integration Runtime de SSIS de Azure si no tiene ninguna. Para ello, siga las instrucciones paso a paso del [tutorial: Implementación de paquetes de SSIS](../tutorial-deploy-ssis-packages-azure.md). Debe usar Data Factory versión 1 para crear un entorno de ejecución de integración SSIS de Azure. 

## <a name="azure-powershell"></a>Azure PowerShell
En esta sección, se usa Azure PowerShell para crear una canalización de Data Factory con una actividad de procedimiento almacenado que invoca un paquete SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Crear una factoría de datos
El siguiente procedimiento detalla los pasos para crear una factoría de datos. Debe crear una canalización con una actividad de procedimiento almacenado en esta factoría de datos. La actividad de procedimiento almacenado ejecuta un procedimiento almacenado en la base de datos SSISDB para ejecutar el paquete de SSIS.

1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../../azure-resource-manager/management/overview.md) entre comillas dobles y ejecute el comando. Por ejemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$ResourceGroupName` y vuelva a ejecutar el comando
2. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$ResourceGroupName` y ejecute el comando de nuevo. 
3. Defina una variable para el nombre de la factoría de datos. 

    > [!IMPORTANT]
    >  Actualice el nombre de la factoría de datos para que sea globalmente único. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Para crear la factoría de datos, ejecute el siguiente cmdlet **New-AzDataFactory** con las propiedades ResourceGroupName y Location de la variable $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Tenga en cuenta los siguientes puntos:

* El nombre de la instancia de Azure Data Factory debe ser único de forma global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Creación de un servicio vinculado de Azure SQL Database
Cree un servicio vinculado para vincular su base de datos de Azure SQL Database que hospeda el catálogo de SSIS con la factoría de datos. Data Factory usa la información de este servicio vinculado para conectarse a la base de datos SSISDB y ejecuta un procedimiento almacenado para ejecutar un paquete de SSIS. 

1. Cree un archivo JSON denominado **AzureSQLDatabaseLinkedService.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido: 

    > [!IMPORTANT]
    > Reemplace &lt;servername&gt;, &lt;username&gt;, @&lt;servername&gt; y &lt;password&gt; por los nombres de su base de datos de Azure SQL antes de guardar el archivo.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. En **Azure PowerShell**, cambie a la carpeta **C:\ADF\RunSSISPackage**.
3. Ejecute el cmdlet **New-AzDataFactoryLinkedService** para crear el servicio vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida
Este conjunto de datos de salida es un conjunto de datos ficticio que controla la programación de la canalización. Observe que el valor frequency está establecido en Hour y que interval está establecido en 1. Por lo tanto, la canalización se ejecuta una vez por hora entre las horas de inicio y fin de la canalización. 

1. Cree un archivo OutputDataset.json con el siguiente contenido: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Ejecute el cmdlet **New-AzDataFactoryDataset** para crear un conjunto de datos. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Crear una canalización con una actividad de procedimiento almacenado 
En este paso, debe crear una canalización con una actividad de procedimiento almacenado. La actividad invoca el procedimiento almacenado sp_executesql para ejecutar su paquete de SSIS. 

1. Cree un archivo JSON con el nombre **MyPipeline.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido:

    > [!IMPORTANT]
    > Reemplace &lt;folder name&gt;, &lt;project name&gt; y &lt;package name&gt; por los nombres de carpeta, proyecto y paquete del catálogo de SSIS antes de guardar el archivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Para crear la canalización: **RunSSISPackagePipeline**, ejecute el cmdlet **New-AzDataFactoryPipeline**.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Ejecute **Get-AzDataFactorySlice** para obtener detalles sobre todos los segmentos del conjunto de datos de salida**, que es la tabla de salida de la canalización.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observe que la StartDateTime que especifique aquí es la misma hora de inicio especificada en el JSON de la canalización. 
1. Ejecute **Get-AzDataFactoryRun** para más información de las ejecuciones de actividad para un segmento específico.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Puede seguir ejecutando este cmdlet hasta que vea que el segmento se encuentra en el estado **Listo** o **Con error**. 

    Puede ejecutar la consulta siguiente en la base de datos de SSISDB en el servidor para comprobar la ejecución del paquete. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de la actividad de procedimiento almacenado, consulte el artículo [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md).
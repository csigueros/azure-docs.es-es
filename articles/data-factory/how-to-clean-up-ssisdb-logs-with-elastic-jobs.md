---
title: Procedimiento de limpieza automática de registros de SSISDB
description: En este artículo se describe cómo limpiar los registros de implementación de proyectos de SSIS y de ejecución de paquetes almacenados en SSISDB mediante la invocación automática del procedimiento almacenado de SSISDB pertinente a través de Azure Data Factory, el agente de Azure SQL Managed Instance o los trabajos de Base de datos elástica.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 08/28/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e235bd04b0693a0fabc9f4432aff01c85b5c67e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824705"
---
# <a name="how-to-clean-up-ssisdb-logs-automatically"></a>Procedimiento de limpieza automática de registros de SSISDB

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Una vez aprovisionado un entorno de ejecución de integración (IR) de Azure SQL Server Integration Services (SSIS) en Azure Data Factory (ADF), puede usarlo para ejecutar paquetes SSIS implementados en:

- El catálogo de SSIS (SSISDB) hospedado por un servidor de Azure SQL Database o Instancia administrada (modelo de implementación de proyectos)
- El sistema de archivos, en Azure Files o en una base de datos de SQL Server (MSDB) hospedados por Azure SQL Managed Instance (modelo de implementación de paquetes)

En el modelo de implementación de proyectos, su instancia de Azure-SSIS IR implementará proyectos de SSIS en SSISDB, capturará paquetes SSIS para que se ejecuten desde SSISDB y volverá a escribir los registros de ejecución de paquetes en SSISDB. SSISDB también se usa para almacenar los registros de trabajos de SSIS y de operaciones de IR. Para administrar los registros acumulados, hemos proporcionado propiedades y procedimientos almacenados de SSISDB pertinentes que se pueden invocar automáticamente según la programación a través de ADF, el agente de Azure SQL Managed Instance o los trabajos de Base de datos elástica.

## <a name="ssisdb-log-clean-up-properties-and-stored-procedures"></a>Propiedades y procedimientos almacenados de limpieza de registros de SSISDB
Para administrar los registros de ejecución de paquetes SSIS, puede configurar las propiedades de limpieza de registros de SSISDB mediante la conexión a la base de datos SSISDB hospedada por el servidor de Azure SQL Database o Instancia administrada mediante SQL Server Management Studio (SSMS), consulte [Conexión a SSISDB](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial?view=sql-server-ver15&preserve-view=true#connect-to-the-ssisdb-database). Una vez establecida la conexión, en la ventana **Explorador de objetos** de SSMS, puede expandir el nodo **Catálogos de Integration Services**, hacer clic con el botón derecho en el subnodo **SSISDB** y seleccionar el elemento de menú **Propiedades** para abrir el cuadro de diálogo **Propiedades del catálogo**. En el cuadro de diálogo **Propiedades del catálogo**, puede encontrar las siguientes propiedades de limpieza de registros de SSISDB:

- **Limpiar registros periódicamente**: habilita la limpieza de los registros de ejecución de paquetes, establecidos de forma predeterminada en *True*.
- **Período de retención (días)** : especifica la antigüedad máxima de los registros retenidos (en días), establecida de forma predeterminada en *365*, y los registros más antiguos se eliminan cuando se invoca el procedimiento almacenado de SSISDB pertinente.
- **Quitar periódicamente versiones anteriores**: habilita la limpieza de las versiones de proyecto almacenadas, de forma predeterminada establecida en *True*.
- **Número máximo de versiones por Project**: especifica el número máximo de versiones de proyecto almacenadas; de forma predeterminada, se establece en *10* y las versiones anteriores se eliminan cuando se invoca el procedimiento almacenado de SSISDB pertinente.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-ssisdb-properties.png" alt-text="Propiedades de limpieza de registros de SSISDB":::

Una vez configuradas las propiedades de limpieza de registros de SSISDB, puede invocar el procedimiento almacenado de SSISDB pertinente, `[internal].[cleanup_server_retention_window_exclusive]`, para limpiar los registros de ejecución de paquetes SSIS.

Para limpiar los registros de trabajo de SSIS, puede invocar el procedimiento almacenado de SSISDB pertinente, `[internal].[cleanup_completed_jobs_exclusive]`. El período de retención se establece de forma predeterminada en 60 minutos y los registros más antiguos se eliminan cuando se invoca el procedimiento almacenado.

Para limpiar los registros de operaciones de SSIS IR, puede invocar el procedimiento almacenado de SSISDB pertinente, `[internal].[cleanup_expired_worker]`. El período de retención se establece de forma predeterminada en 168 horas y los registros más antiguos se eliminan cuando se invoca el procedimiento almacenado.

Estos procedimientos almacenados de SSISDB limpian diferentes tablas de SSISDB:

| Procedimientos almacenados de SSISDB | Tablas de SSISDB para limpiar |
|--------------------------|---------------------------|
| `[internal].[cleanup_server_retention_window_exclusive]` | `[internal].[event_message_context_scaleout]`<br/>`[internal].[event_messages_scaleout]`<br/>`[internal].[executable_statistics]`<br/>`[internal].[execution_component_phases]`<br/>`[internal].[execution_data_statistics]`<br/>`[internal].[execution_data_taps]`<br/>`[internal].[execution_parameter_values]`<br/>`[internal].[execution_parameter_values_noncatalog]`<br/>`[internal].[execution_property_override_values]`<br/>`[internal].[execution_property_override_values_noncatalog]`<br/>`[internal].[executions]`<br/>`[internal].[executions_noncatalog]`<br/>`[internal].[extended_operation_info]`<br/>`[internal].[operation_messages]`<br/>`[internal].[operation_messages_scaleout]`<br/>`[internal].[operation_permissions]`<br/>`[internal].[operations]`<br/>`[internal].[validations]` |
| `[internal].[cleanup_completed_jobs_exclusive]` | `[internal].[job_worker_agents]`<br/>`[internal].[jobs]`<br/>`[internal].[tasks]` |
| `[internal].[cleanup_expired_worker]` | `[internal].[worker_agents]` |

Estos procedimientos almacenados de SSISDB también se pueden invocar automáticamente según la programación a través de ADF, el agente de Azure SQL Managed Instance o los trabajos de Base de datos elástica.

## <a name="clean-up-ssisdb-logs-automatically-via-adf"></a>Limpieza automática de registros de SSISDB a través de ADF
Independientemente de si usa el servidor de Azure SQL Database o Instancia administrada para hospedar SSISDB, siempre puede usar ADF para limpiar los registros de SSISDB automáticamente según la programación. Para ello, puede preparar una actividad de ejecución de paquete SSIS en la canalización de ADF con un paquete insertado que contenga una única tarea de ejecución de SQL que invoque los procedimientos almacenados de SSISDB pertinentes. Vea el ejemplo 4) en nuestro blog: [Run Any SQL Anywhere in 3 Easy Steps with SSIS in Azure Data Factory](https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244) (Ejecutar cualquier instancia de SQL en cualquier lugar en 3 sencillos pasos con SSIS en Azure Data Factory).

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/run-sql-ssis-activity-ssis-parameters-ssisdb-clean-up.png" alt-text="Limpieza de registros de SSISDB a través de ADF":::

Para el parámetro **SQLStatementSource**, puede escribir `EXEC internal.cleanup_server_retention_window_exclusive` para limpiar los registros de ejecución de paquetes SSIS. 

Para limpiar los registros de trabajos de SSIS, puede agregar `EXEC internal.cleanup_completed_jobs_exclusive [@minutesToKeep=’Number of minutes to set as retention period’]`. 

Para limpiar los registros de operaciones de SSIS IR, puede agregar `EXEC internal.cleanup_expired_worker [@hoursToKeep=’Number of hours to set as retention period’] `.

Una vez preparada la canalización de ADF, puede adjuntar un desencadenador de programación para ejecutarlo periódicamente. Consulte [Cómo desencadenar la canalización de ADF según una programación](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="clean-up-ssisdb-logs-automatically-via-azure-sql-managed-instance-agent"></a>Limpieza automática de los registros de SSISDB a través del agente de Azure SQL Managed Instance
Si usa Azure SQL Managed Instance para hospedar SSISDB, también puede usar su orquestador o programador de trabajos integrado, el agente de Azure SQL Managed Instance, para limpiar los registros de SSISDB automáticamente según la programación. Si SSISDB se creó recientemente en Azure SQL Managed Instance, también se creó un trabajo de T-SQL denominado **Trabajo de mantenimiento del servidor SSIS** en el Agente de Azure SQL Managed Instance para limpiar específicamente los registros de ejecución de paquetes SSIS. De forma predeterminada, está deshabilitado y configurado con una programación para ejecutarse diariamente.  Si desea habilitarlo o volver a configurar su programación, puede hacerlo conectándose a Azure SQL Managed Instance mediante SSMS. Una vez establecida la conexión, en la ventana **Explorador de objetos** de SSMS, puede expandir el nodo **Agente SQL Server**, expandir el subnodo **Trabajos** y hacer doble clic en el **Trabajo de mantenimiento del servidor SSIS** para habilitarlo o volver a configurarlo.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-maintenance-job.png" alt-text="Limpieza de registros de SSISDB a través del Agente de Azure SQL Managed Instance":::

Si aún no se ha creado el **Trabajo de mantenimiento del servidor SSIS** en el agente de Azure SQL Managed Instance, puede agregarlo manualmente ejecutando el siguiente script de T-SQL en Azure SQL Managed Instance.

```sql
USE msdb
IF EXISTS(SELECT * FROM sys.server_principals where name = '##MS_SSISServerCleanupJobLogin##')
   DROP LOGIN ##MS_SSISServerCleanupJobLogin##

DECLARE @loginPassword nvarchar(256)
SELECT @loginPassword = REPLACE (CONVERT( nvarchar(256), CRYPT_GEN_RANDOM( 64 )), N'''', N'''''')
EXEC ('CREATE LOGIN ##MS_SSISServerCleanupJobLogin## WITH PASSWORD =''' +@loginPassword + ''', CHECK_POLICY = OFF')
ALTER LOGIN ##MS_SSISServerCleanupJobLogin## DISABLE

USE master
GRANT VIEW SERVER STATE TO ##MS_SSISServerCleanupJobLogin##

USE SSISDB
IF EXISTS (SELECT name FROM sys.database_principals WHERE name = '##MS_SSISServerCleanupJobUser##')
    DROP USER ##MS_SSISServerCleanupJobUser##
CREATE USER ##MS_SSISServerCleanupJobUser## FOR LOGIN ##MS_SSISServerCleanupJobLogin##
GRANT EXECUTE ON [internal].[cleanup_server_retention_window_exclusive] TO ##MS_SSISServerCleanupJobUser##
GRANT EXECUTE ON [internal].[cleanup_server_project_version] TO ##MS_SSISServerCleanupJobUser##

USE msdb
EXEC dbo.sp_add_job
    @job_name = N'SSIS Server Maintenance Job', 
    @enabled = 0,
    @owner_login_name = '##MS_SSISServerCleanupJobLogin##',
    @description = N'Runs every day. The job removes operation records from the database that are outside the retention period and maintains a maximum number of versions per project.'

DECLARE @IS_server_name NVARCHAR(30)
SELECT @IS_server_name = CONVERT(NVARCHAR, SERVERPROPERTY('ServerName'))
EXEC sp_add_jobserver  @job_name = N'SSIS Server Maintenance Job',
                       @server_name = @IS_server_name

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Operation Records Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_retention_window_exclusive]',
    @database_name = N'msdb',
    @on_success_action = 3,
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Max Version Per Project Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_project_version]',
    @database_name = N'msdb',
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobschedule
    @job_name = N'SSIS Server Maintenance Job',
    @name = 'SSISDB Scheduler',
    @enabled = 1,
    @freq_type = 4, /*daily*/
    @freq_interval = 1,/*every day*/
    @freq_subday_type = 0x1,
    @active_start_date = 20001231,
    @active_end_date = 99991231,
    @active_start_time = 0,
    @active_end_time = 120000
```

También puede configurar el **Trabajo de mantenimiento del servidor SSIS** existente o modificar el script de T-SQL anterior para limpiar además los registros de operaciones de trabajos de SSIS/IR invocando los procedimientos almacenados de SSISDB pertinentes.

## <a name="clean-up-ssisdb-logs-automatically-via-elastic-database-jobs"></a>Limpieza automática de los registros de SSISDB a través de trabajos de Base de datos elástica
Si usa un servidor de Azure SQL Database para hospedar SSISDB, este no tiene un orquestador o programador de trabajos integrado, por lo que debe usar un componente externo, como ADF (consulte más arriba) o trabajos de Base de datos elástica (consulte el resto de esta sección), para limpiar los registros de SSISDB automáticamente según la programación.

Trabajos de Base de datos elástica es un servicio de Azure que puede automatizar y ejecutar trabajos en una base de datos o un grupo de bases de datos. Puede programar, ejecutar y supervisar estos trabajos mediante Azure Portal, Azure PowerShell, T-SQL o las API de REST. Use trabajos de Base de datos elástica para invocar los procedimientos almacenados de SSISDB pertinentes para la limpieza de registros única o según una programación. Puede elegir el intervalo de programación en función del uso de recursos de SSISDB para evitar la carga intensiva de la base de datos.

Para más información, consulte [Administración de grupos de bases de datos con trabajos de Elastic Database](../azure-sql/database/elastic-jobs-overview.md).

En las secciones siguientes se describe cómo invocar los procedimientos almacenados de SSISDB pertinentes, `[internal].[cleanup_server_retention_window_exclusive]`/`[internal].[cleanup_completed_jobs_exclusive]`/`[internal].[cleanup_expired_worker]`, que quitan los registros de SSISDB que están fuera de los períodos de retención específicos.

### <a name="configure-elastic-database-jobs-using-azure-powershell"></a>Configuración de trabajos de Base de datos elástica mediante Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Los siguientes scripts de Azure PowerShell crean un nuevo trabajo elástico que invoca el procedimiento almacenado de limpieza de registros de SSISDB seleccionado. Para más información, consulte [Creación de un agente de trabajos elásticos mediante PowerShell](../azure-sql/database/elastic-jobs-powershell-create.md).

#### <a name="create-parameters"></a>Creación de parámetros

``` powershell
# Parameters needed to create your job database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL Database server, for example myjobserver, to hold your job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for your job database to be created in the given Azure SQL Database server"),
$StoredProcName = $(Read-Host "Please enter the name of SSISDB log clean-up stored procedure to be invoked by your job (internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker)"), 

# Your job database should be a clean, empty S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create your Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your Elastic Job agent"),

# Parameters needed to create credentials in your job database for connecting to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for the log clean-up job user to connect to SSISDB"),

# Parameters needed to create the login and user for SSISDB
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of target Azure SQL Database server that contains SSISDB, for example myssisdbserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set the job schedule for invoking SSISDB log clean-up stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run your job that cleans up SSISDB logs outside their retention period immediately (Y/N). Make sure the specific retention period is set properly before running the following scripts as deleted logs cannot be recovered."),
$IntervalType = $(Read-Host "Please enter the interval type for SSISDB log clean-up schedule: Year, Month, Day, Hour, Minute, Second are supported."),
$IntervalCount = $(Read-Host "Please enter the count of interval type for SSISDB log clean-up schedule."),

# The start time for SSISDB log clean-up schedule is set to current time by default. 
$StartTime = (Get-Date)
```

#### <a name="invoke-ssisdb-log-clean-up-stored-procedure"></a>Invocación del procedimiento almacenado de limpieza de registros de SSISDB

```powershell
# Install the latest PowerShell PackageManagement module that PowerShellGet v1.6.5 depends on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart your PowerShell session
# Install the latest PowerShellGet module that adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Install AzureRM.Sql preview cmdlets side by side with the existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create your job database for defining SSISDB log clean-up job and tracking the job history
Write-Output "Creating a blank SQL database to be used as your job database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable Elastic Database Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create your Elastic Job agent
Write-Output "Creating your Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create job credentials in your job database for connecting to SSISDB in target server
Write-Output "Creating job credentials for connecting to SSISDB..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# Create the job user login in master database of target server
Write-Output "Grant permissions on the master database of target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# Create SSISDB log clean-up user from login in SSISDB and grant it permissions to invoke SSISDB log clean-up stored procedure
Write-Output "Grant appropriate permissions on SSISDB..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON " + $StoredProcName + " TO SSISDBLogCleanupUser" 

$TargetDatabase | ForEach-Object -Process {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create your target group that includes only SSISDB to clean up
Write-Output "Creating your target group that includes only SSISDB to clean up..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create your job to invoke SSISDB log clean-up stored procedure
Write-Output "Creating your job to invoke SSISDB log clean-up stored procedure..."
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add your job step to invoke SSISDB log clean-up stored procedure
Write-Output "Adding your job step to invoke SSISDB log clean-up stored procedure..."
$SqlText = "EXEC " + $StoredProcName 
$Job | Add-AzureRmSqlElasticJobStep -Name "Step to invoke SSISDB log clean-up stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run your job to immediately invoke SSISDB log clean-up stored procedure once
if ($RunJobOrNot -eq 'Y')
{
Write-Output "Invoking SSISDB log clean-up stored procedure immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule your job to invoke SSISDB log clean-up stored procedure periodically, deleting SSISDB logs outside their retention period
Write-Output "Starting your schedule to invoke SSISDB log clean-up stored procedure periodically..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

### <a name="configure-elastic-database-jobs-using-t-sql"></a>Configuración de trabajos de Base de datos elástica mediante T-SQL

Los siguientes scripts de T-SQL crean un nuevo trabajo elástico que invoca el procedimiento almacenado de limpieza de registros de SSISDB seleccionado. Para más información, consulte [Uso de T-SQL para crear y administrar trabajos de Base de datos elástica](../azure-sql/database/elastic-jobs-tsql-create-manage.md).

1. Identifique un nivel de servicio S0 o superior vacío de Azure SQL Database o cree uno nuevo para la base de datos de trabajos. A continuación, cree un agente de trabajos elásticos en [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent).

2. En la base de datos de trabajos, cree credenciales para conectarse a SSISDB en el servidor de destino.

   ```sql
   -- Connect to the job database specified when creating your job agent.
   -- Create a database master key if one doesn't already exist, using your own password.
   CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';

   -- Create credentials for SSISDB log clean-up.
   CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
   ```

3. Defina un grupo de destino que incluya solo SSISDB para la limpieza.

   ```sql
   -- Connect to your job database.
   -- Add your target group.
   EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

   -- Add SSISDB to your target group
   EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
   @target_type = 'SqlDatabase',
   @server_name = '<EnterSSISDBTargetServerName>',
   @database_name = 'SSISDB'

   -- View your recently created target group and its members.
   SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
   SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
   ```

4. Cree un usuario de limpieza de registros de SSISDB a partir del inicio de sesión en SSISDB y concédale permisos para invocar el procedimiento almacenado de limpieza de registros de SSISDB. Para ver una guía detallada, consulte [Administración de inicios de sesión](../azure-sql/database/logins-create-manage.md).

   ```sql
   -- Connect to the master database of target server that hosts SSISDB 
   CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

   -- Connect to SSISDB
   CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
   GRANT EXECUTE ON '<internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker>' TO SSISDBLogCleanupUser 
   ```

5. Cree el trabajo y agregue el paso de trabajo para invocar el procedimiento almacenado de limpieza de registros de SSISDB.

   ```sql
   -- Connect to your job database.
   -- Add your job to invoke the relevant SSISDB log clean-up stored procedure.
   EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs outside their specific retention period'

   -- Add your job step to invoke the relevant SSISDB log clean-up stored procedure
   EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
   @command=N'<EXEC internal.cleanup_server_retention_window_exclusive/EXEC internal.cleanup_completed_jobs_exclusive/EXEC internal.cleanup_expired_worker>',
   @credential_name='SSISDBLogCleanupCred',
   @target_group_name='SSISDBTargetGroup'
   ```

6. Antes de continuar, asegúrese de establecer correctamente el período de retención específico. Los registros de SSISDB fuera de este período se eliminarán y no se podrán recuperar. A continuación, puede ejecutar el trabajo inmediatamente para iniciar la limpieza de registros de SSISDB.

   ```sql
   -- Connect to your job database.
   -- Run your job immediately to invoke SSISDB log clean-up stored procedure.
   declare @je uniqueidentifier
   exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

   -- Watch SSISDB log clean-up results 
   select @je
   select * from jobs.job_executions where job_execution_id = @je
   ```

7. Opcionalmente, puede eliminar los registros de SSISDB fuera del período de retención según una programación. Configure los parámetros del trabajo como se muestra a continuación.

   ```sql
   -- Connect to your job database.
   EXEC jobs.sp_update_job
   @job_name='CleanupSSISDBLog',
   @enabled=1,
   @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
   @schedule_interval_count='<EnterDetailedIntervalValue>',
   @schedule_start_time='<EnterProperStartTimeForSchedule>',
   @schedule_end_time='<EnterProperEndTimeForSchedule>'
   ```

### <a name="monitor-ssisdb-log-clean-up-job-using-azure-portal"></a>Supervisión del trabajo de limpieza de registros de SSISDB mediante Azure Portal

Puede supervisar el trabajo de limpieza de registros de SSISDB en Azure Portal. Puede ver el estado, la hora de inicio y la hora de finalización de cada ejecución.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png" alt-text="Supervisión del trabajo de limpieza de registros de SSISDB en Azure Portal":::

### <a name="monitor-ssisdb-log-clean-up-job-using-t-sql"></a>Supervisión del trabajo de limpieza de registros de SSISDB mediante T-SQL

También puede utilizar T-SQL para ver el historial de ejecuciones del trabajo de limpieza de registros de SSISDB.

```sql
-- Connect to your job database.
-- View all SSISDB log clean-up job executions.
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions.
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>Pasos siguientes

Para administrar y supervisar Azure-SSIS IR, consulte los siguientes artículos.

- [Reconfiguración de un entorno de ejecución para la integración de SSIS en Azure](manage-azure-ssis-integration-runtime.md)

- [Supervisión de Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)

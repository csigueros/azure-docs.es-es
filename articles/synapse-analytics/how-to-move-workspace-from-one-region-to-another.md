---
title: Traslado de un área de trabajo de Azure Synapse Analytics de una región a otra
description: En este artículo, se explica cómo mover un área de trabajo de Azure Synapse Analytics de una región a otra.
author: phanir
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 08/16/2021
ms.author: phanir
ms.reviewer: jrasnick
ms.openlocfilehash: 6ad246eefb86c31291d2a9745c6f77e276701744
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214092"
---
# <a name="move-an-azure-synapse-analytics-workspace-from-one-region-to-another"></a>Traslado de un área de trabajo de Azure Synapse Analytics de una región a otra

Este artículo es una guía paso a paso que muestra cómo mover un área de trabajo de Azure Synapse Analytics de una región de Azure a otra.

> [!NOTE]
> Los pasos de este artículo no mueven realmente el área de trabajo, sino que muestran cómo se crea una nueva área de trabajo de Azure Synapse Analytics en otra región usando los artefactos y copias de seguridad de grupos de SQL dedicados de Azure Synapse Analytics de la región de origen.

## <a name="prerequisites"></a>Requisitos previos

- Integre el área de trabajo de Azure Synapse de la región de origen con Azure DevOps o GitHub. Para más información, consulte [Control de código fuente en Synapse Studio](cicd/source-control.md).
- Debe tener instalados los módulos de [Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.3.0&preserve-view=true) y la [CLI de Azure](/cli/azure/install-azure-cli) en el servidor donde se ejecutan los scripts.
- Asegúrese de que todos los servicios dependientes (por ejemplo, Azure Machine Learning, Azure Storage y los centros de conectividad de Azure Private Link) se vuelvan a crear en la región de destino o se muevan a esta región si el servicio admite el traslado a otra región. 
- Mueva Azure Storage a otra región. Para más información, consulte [Traslado de una cuenta de Azure Storage a otra región](../storage/common/storage-account-move.md).
- Asegúrese de que el nombre del grupo de SQL dedicado y el nombre del grupo de Apache Spark sean el mismo en el área de trabajo de las regiones de origen y de destino.

## <a name="scenarios-for-a-region-move"></a>Escenarios para un traslado de región

- **Nuevos requisitos de cumplimiento normativo:** las organizaciones requieren que los datos y los servicios estén en la misma región debido a nuevos requisitos de cumplimiento normativo.
- **Disponibilidad de una nueva región de Azure:** escenarios en los que hay una nueva región de Azure disponible y hay requisitos empresariales o de un proyecto para mover el área de trabajo y otros recursos de Azure a la región de Azure que ahora está disponible.
- **Región incorrecta seleccionada:** se había seleccionado la región incorrecta cuando se crearon los recursos de Azure.

## <a name="steps-to-move-an-azure-synapse-workspace-to-another-region"></a>Pasos para mover un área de trabajo de Azure Synapse a otra región

Mover un área de trabajo de Azure Synapse de una región a otra es un proceso de varios pasos. Los pasos generales son los siguientes:

1. Cree una nueva área de trabajo de Azure Synapse en la región de destino junto con un grupo de Spark que tenga la misma configuración que en el área de trabajo de la región de origen.
1. Restaure el grupo de SQL dedicado en la región de destino usando puntos de restauración o copias de seguridad geográficas.
1. Vuelva a crear todos los inicios de sesión necesarios en el nuevo servidor SQL Server lógico.
1. Cree los objetos y bases de datos de los grupos de Spark y SQL sin servidor.
1. Si usa una canalización de versión de Azure DevOps para implementar los artefactos, agregue una entidad de servicio de Azure DevOps al rol de publicador de artefactos de Synapse del control de acceso basado en rol (RBAC) de Azure Synapse.
1. En el área de trabajo de Azure Synapse de la región de destino, implemente el artefacto de código (scripts de SQL, cuadernos), los servicios vinculados, las canalizaciones, los conjuntos de datos, los desencadenadores de definiciones de trabajos de Spark y las credenciales de las canalizaciones de versión de Azure DevOps.
1. Agregue los usuarios o grupos de Azure Active Directory (Azure AD) a los roles de RBAC de Azure Synapse. Si utiliza la autenticación con una identidad administrada, conceda acceso de colaborador de Storage Blob a la identidad administrada asignada por el sistema (SA-MI) en Azure Storage y Azure Key Vault.
1. Conceda los roles de lector o colaborador de Storage Blob a los usuarios de Azure AD necesarios en el almacenamiento asociado predeterminado o en la cuenta de almacenamiento que tiene los datos que se van a consultar mediante un grupo de SQL sin servidor.
1. Vuelva a crear el entorno de ejecución de integración autohospedado (SHIR).
1. Cargue manualmente todas las bibliotecas y archivos JAR necesarios en el área de trabajo Azure Synapse de destino.
1. Cree todos los puntos de conexión privados administrados si el área de trabajo está implementada en una red virtual administrada.
1. Pruebe la nueva área de trabajo en la región de destino y actualice las entradas DNS, que apuntan al área de trabajo de la región de origen.
1. Si se ha creado una conexión mediante un punto de conexión privado en el área de trabajo de origen, cree otra en el área de trabajo de la región de destino.
1. Puede eliminar el área de trabajo de la región de origen después de haberla probado exhaustivamente y haber enrutado todas las conexiones hacia el área de trabajo de la región de destino.
## <a name="prepare"></a>Preparación
## <a name="step-1-create-an-azure-synapse-workspace-in-a-target-region"></a>Paso 1: Creación de un área de trabajo de Azure Synapse en una región de destino

En esta sección, creará el área de trabajo de Azure Synapse con Azure PowerShell, la CLI de Azure y Azure Portal. Creará un grupo de recursos junto con una cuenta de Azure Data Lake Storage Gen2 que se usará como almacenamiento predeterminado para el área de trabajo, como parte de los scripts de PowerShell y de la CLI. Si desea automatizar el proceso de implementación, invoque estos scripts de PowerShell o de la CLI desde la canalización de versión de DevOps.

### <a name="azure-portal"></a>Azure portal
Para crear un área de trabajo desde Azure Portal, siga los pasos que se indican en [Inicio rápido: Creación de un área de trabajo de Synapse](quickstart-create-workspace.md).

### <a name="azure-powershell"></a>Azure PowerShell 
El siguiente script crea el grupo de recursos y el área de trabajo de Azure Synapse con los cmdlets New-AzResourceGroup y New-AzSynapseWorkspace.

#### <a name="create-a-resource-group"></a>Crear un grupo de recursos

```powershell
$storageAccountName= "<YourDefaultStorageAccountName>"
$resourceGroupName="<YourResourceGroupName>"
$regionName="<YourTargetRegionName>"
$containerName="<YourFileSystemName>" # This is the file system name
$workspaceName="<YourTargetRegionWorkspaceName>"

$sourcRegionWSName="<Your source region workspace name>"
$sourceRegionRGName="<YourSourceRegionResourceGroupName>"
$sqlUserName="<SQLUserName>"
$sqlPassword="<SQLStrongPassword>"

$sqlPoolName ="<YourTargetSQLPoolName>" #Both Source and target workspace SQL pool name will be same
$sparkPoolName ="<YourTargetWorkspaceSparkPoolName>"
$sparkVersion="2.4"

New-AzResourceGroup -Name $resourceGroupName -Location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Creación de una cuenta de Data Lake Storage Gen2

```powershell
#If the Storage account is already created, then you can skip this step.
New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location  $regionName `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $true 
```


#### <a name="create-an-azure-synapse-workspace"></a>Creación de un área de trabajo de Azure Synapse

```powershell
$password = ConvertTo-SecureString $sqlPassword -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ($sqlUserName, $password)

New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds 
```

Si desea crear el área de trabajo con una red virtual administrada, agregue también el parámetro "ManagedVirtualNetwork" al script. Para obtener más información sobre las opciones disponibles, consulte [New-AzSynapseManagedVirtualNetworkConfig](/powershell/module/az.synapse/new-azsynapsemanagedvirtualnetworkconfig?view=azps-6.3.0&preserve-view=true).


```powershell
#Creating a managed virtual network configuration
$config = New-AzSynapseManagedVirtualNetworkConfig -PreventDataExfiltration -AllowedAadTenantIdsForLinking ContosoTenantId 

#Creating an Azure Synapse workspace
New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds `
                              -ManagedVirtualNetwork $config
```


### <a name="azure-cli"></a>Azure CLI

Este script de la CLI de Azure crea un grupo de recursos, una cuenta de Data Lake Storage Gen2 y un sistema de archivos. Después, crea el área de trabajo de Azure Synapse.

#### <a name="create-a-resource-group"></a>Crear un grupo de recursos

```azurecli
az group create --name $resourceGroupName --location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Creación de una cuenta de Data Lake Storage Gen2

```azurecli
# Checking if name is not used only then creates it.

$StorageAccountNameAvailable=(az storage account check-name --name $storageAccountName --subscription $subscriptionId | ConvertFrom-Json).nameAvailable

if($StorageAccountNameAvailable)
{
Write-Host "Storage account Name is available to be used...creating storage account"

#Creating an Data Lake Storage Gen2 account
$storgeAccountProvisionStatus=az storage account create `
  --name $storageAccountName `
  --resource-group $resourceGroupName `
  --location $regionName `
  --sku Standard_GRS `
  --kind StorageV2 `
  --enable-hierarchical-namespace $true

($storgeAccountProvisionStatus| ConvertFrom-Json).provisioningState
}
else
{
    Write-Host "Storage account Name is NOT available to be used...use another name --    exiting the script..."
    EXIT
}

#Creating a container in a Data Lake Storage Gen2 account

$key=(az storage account keys list -g $resourceGroupName -n $storageAccountName|ConvertFrom-Json)[0].value

$fileShareStatus=(az storage share create --account-name $storageAccountName --name $containerName --account-key $key)

if(($fileShareStatus|ConvertFrom-Json).created -eq "True")
{
      Write-Host f"Successfully created the fileshare - '$containerName'"
}
```


#### <a name="create-an-azure-synapse-workspace"></a>Creación de un área de trabajo de Azure Synapse

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $containerName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName
```
Para habilitar una red virtual administrada, incluya el parámetro `--enable-managed-virtual-network` en el script anterior. Para ver más opciones, consulte [az synapse workspace](/cli/azure/synapse/workspace?view=azure-cli-latest&preserve-view=true).

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $FileShareName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName `
  --enable-managed-virtual-network true `
  --allowed-tenant-ids "Contoso"
```

## <a name="step-2-create-an-azure-synapse-workspace-firewall-rule"></a>Paso 2: Creación de una regla de firewall para el área de trabajo de Azure Synapse 
Una vez creada el área de trabajo, agregue reglas de firewall para ella. Restrinja las direcciones IP a un intervalo determinado. Puede agregar un firewall desde Azure Portal o usando PowerShell o la CLI.

### <a name="azure-portal"></a>Azure portal
Seleccione las opciones de firewall y agregue el intervalo de direcciones IP como se muestra en la siguiente captura de pantalla.

:::image type="icon" source="media/how-to-move-workspace-from-one-region-to-another/firewall.png" border="false":::


### <a name="azure-powershell"></a>Azure PowerShell 
Ejecute los siguientes comandos de PowerShell para agregar reglas de firewall especificando las direcciones IP inicial y final. Actualice el intervalo de direcciones IP en función de sus requisitos.


```powershell
$WorkspaceWeb = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Web
$WorkspaceDev = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Dev

# Adding firewall rules
$FirewallParams = @{
  WorkspaceName = $workspaceName
  Name = 'Allow Client IP'
  ResourceGroupName = $resourceGroup
  StartIpAddress = "0.0.0.0"
  EndIpAddress = "255.255.255.255"
}
New-AzSynapseFirewallRule @FirewallParams
```

Ejecute el siguiente script para actualizar la configuración de control de SQL de la identidad administrada del área de trabajo:

```powershell 
Set-AzSynapseManagedIdentitySqlControlSetting -WorkspaceName $workspaceName -Enabled $true 
```

### <a name="azure-cli"></a>Azure CLI


```azurecli
az synapse workspace firewall-rule create --name allowAll --workspace-name $workspaceName  `
--resource-group $resourceGroupName --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
 
Ejecute el siguiente script para actualizar la configuración de control de SQL de la identidad administrada del área de trabajo:

```azurecli
az synapse workspace managed-identity grant-sql-access `
--workspace-name $workspaceName --resource-group $resourceGroupName
```


## <a name="step-3-create-an-apache-spark-pool"></a>Paso 3: Creación de un grupo de Apache Spark

Cree el grupo de Spark con la misma configuración que en el área de trabajo de la región de origen.

### <a name="azure-portal"></a>Azure portal

Para crear un grupo de Spark desde Azure Portal, consulte [Inicio rápido: Creación de un grupo de Apache Spark sin servidor mediante Azure Portal](quickstart-create-apache-spark-pool-portal.md).


También puede crear el grupo de Spark desde Synapse Studio siguiendo los pasos que se indican en [Inicio rápido: Creación de un grupo de Apache Spark sin servidor mediante Synapse Studio](quickstart-create-apache-spark-pool-studio.md).

### <a name="azure-powershell"></a>Azure PowerShell

El siguiente script crea un grupo de Spark con dos nodos de trabajo y un nodo de controlador. Actualice los valores para que coincidan con el grupo de Spark del área de trabajo de la región de origen.


```powershell
#Creating a Spark pool with 3 nodes (2 worker + 1 driver) and a small cluster size with 4 cores and 32 GB RAM. 
New-AzSynapseSparkPool `
    -WorkspaceName  $workspaceName `
    -Name $sparkPoolName `
    -NodeCount 3 `
    -SparkVersion $sparkVersion `
    -NodeSize Small
```
 
### <a name="azure-cli"></a>Azure CLI

```azurecli
az synapse spark pool create --name $sparkPoolName --workspace-name $workspaceName --resource-group $resourceGroupName `
--spark-version $sparkVersion --node-count 3 --node-size small
```
## <a name="move"></a>Move
## <a name="step-4-restore-a-dedicated-sql-pool"></a>Paso 4: Restauración de un grupo de SQL dedicado 

### <a name="restore-from-geo-redundant-backups"></a>Restauración a partir de copias de seguridad con redundancia geográfica

Para restaurar los grupos de SQL dedicados a partir de copias de seguridad geográficas mediante Azure Portal y PowerShell, consulte [Restauración geográfica de un grupo de SQL dedicado en Azure Synapse Analytics](sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup.md).


### <a name="restore-by-using-restore-points-from-the-source-region-workspace-dedicated-sql-pool"></a>Restauración a partir de puntos de restauración del grupo de SQL dedicado del área de trabajo de la región de origen

Restaure el grupo de SQL dedicado en el área de trabajo de la región de destino usando un punto de restauración del grupo de SQL dedicado del área de trabajo de la región de origen. Puede usar Azure Portal, Synapse Studio o PowerShell para la restauración con puntos de restauración. Si la región de origen no está accesible, no se puede usar esta opción para la restauración.

#### <a name="synapse-studio"></a>Synapse Studio

En Synapse Studio, puede restaurar el grupo de SQL dedicado desde cualquier área de trabajo de la suscripción usando *puntos de restauración*. Al crear el grupo de SQL dedicado, en **Configuración adicional**, seleccione **Punto de restauración** y elija el área de trabajo, como se muestra en la siguiente captura de pantalla. Si ha creado un punto de restauración definido por el usuario, úselo para restaurar el grupo de SQL. De lo contrario, puede seleccionar el punto de restauración automática más reciente.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/restore-sql-pool.png" alt-text="Restauración de un grupo de SQL":::


#### <a name="azure-powershell"></a>Azure PowerShell

Ejecute el siguiente script de PowerShell para restaurar el área de trabajo. Este script usa el punto de restauración más reciente del grupo de SQL dedicado del área de trabajo de origen para restaurar el grupo de SQL en el área de trabajo de destino. Antes de ejecutar el script, actualice el nivel de rendimiento de DW100c al valor necesario. 

> [!IMPORTANT]
> El nombre del grupo de SQL dedicado debe ser el mismo en las dos áreas de trabajo.


```powershell
#Getting the restore points
$restorePoint=Get-AzSynapseSqlPoolRestorePoint -WorkspaceName $sourceRegionWSName -Name $sqlPoolName|Sort-Object  -Property RestorePointCreationDate -Descending `
                                                                                         | SELECT RestorePointCreationDate -ExpandProperty  RestorePointCreationDate -First 1
```
 
 

```powershell
<#
Transform Synapse SQL pool resource ID to SQL database ID because currently the command only accepts the SQL database ID. 
For example: /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Sql/servers/<WorkspaceName>/databases/<DatabaseName>
#>
$pool = Get-AzSynapseSqlPool -ResourceGroupName $sourceRegionRGName -WorkspaceName $sourcRegionWSName -Name $sqlPoolName
$databaseId = $pool.Id `
    -replace "Microsoft.Synapse", "Microsoft.Sql" `
    -replace "workspaces", "servers" `
    -replace "sqlPools", "databases" 
 

$restoredPool = Restore-AzSynapseSqlPool -FromRestorePoint `
                                         -RestorePoint $restorePoint `
                                         -TargetSqlPoolName $sqlPoolName `
                                         -ResourceGroupName $resourceGroupName `
                                         -WorkspaceName $workspaceName `
                                         -ResourceId $databaseId `
                                         -PerformanceLevel DW100c -AsJob


#Tracks the status of the restore 

Get-Job | Where-Object Command -In ("Restore-AzSynapseSqlPool") | `
Select-Object Id,Command,JobStateInfo,PSBeginTime,PSEndTime,PSJobTypeName,Error |Format-Table
```
Después de restaurar el grupo de SQL dedicado, cree todos los inicios de sesión de SQL en Azure Synapse. Para crear todos los inicios de sesión, siga los pasos que se indican en [CREATE LOGIN (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="step-5-create-a-serverless-sql-pool-spark-pool-database-and-objects"></a>Paso 5: Creación de un grupo de SQL sin servidor y de una base de datos y objetos de grupo de Spark

No se pueden crear copias de seguridad ni restaurar los grupos de Spark y de SQL sin servidor. Como posible solución alternativa, puede hacer lo siguiente:

1. Cree cuadernos y scripts de SQL que tengan el código para volver a crear todos los grupos de Spark, las bases de datos del grupo de SQL sin servidor, las tablas, los roles y los usuarios necesarios con todas las asignaciones de roles. Inserte estos artefactos en el repositorio de Azure DevOps o GitHub.
1. Si se cambia el nombre de la cuenta de almacenamiento, asegúrese de que los artefactos de código apunten al nombre correcto.
1. Cree canalizaciones que invoquen a estos artefactos de código en una secuencia específica. Cuando se ejecuten estas canalizaciones en el área de trabajo de la región de destino, se crearán en ella las bases de datos de Spark SQL y del grupo de SQL sin servidor, los orígenes de datos externos, las vistas, los roles, los usuarios y los permisos.
1. Al integrar el área de trabajo de la región de origen con Azure DevOps, estos artefactos de código pasan a formar parte del repositorio. Más adelante, puede implementar estos artefactos de código en el área de trabajo de la región de destino usando la canalización de versión de DevOps, como se explica en el paso 6.
1. En el área de trabajo de la región de destino, desencadene estas canalizaciones manualmente.

## <a name="step-6-deploy-artifacts-and-pipelines-by-using-cicd"></a>Paso 6: Implementación de artefactos y canalizaciones mediante CI/CD 

 Para saber cómo integrar un área de trabajo de Azure Synapse con Azure DevOps o GitHub, y cómo implementar los artefactos en un área de trabajo de la región de destino, siga los pasos que se indican en [Integración y entrega continuas para las áreas de trabajo de Azure Synapse](cicd/continuous-integration-delivery.md). 

Una vez que el área de trabajo está integrada con Azure DevOps, verá una rama con el nombre workspace_publish. Esta rama contiene la plantilla de área de trabajo que incluye definiciones para los artefactos, como cuadernos, scripts de SQL, conjuntos de datos, servicios vinculados, canalizaciones, desencadenadores y trabajos de Spark.

Esta captura de pantalla del repositorio de Azure DevOps muestra los archivos de plantilla de área de trabajo para los artefactos y otros componentes.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/devops-repo-workspace-publish.png" alt-text="Captura de pantalla que muestra workspace_publish.":::

Puede usar la plantilla de área de trabajo para implementar artefactos y canalizaciones en un área de trabajo usando la canalización de versión de Azure DevOps, como se muestra en la siguiente captura de pantalla.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/release-pipeline.png" alt-text="Captura de pantalla que muestra synapse-release-pipeline.":::

Si el área de trabajo no está integrada con GitHub ni Azure DevOps, tendrá que volver a crear o escribir manualmente los scripts personalizados de PowerShell o la CLI de Azure para implementar todos los artefactos, canalizaciones, servicios vinculados, credenciales, desencadenadores y definiciones de Spark en el área de trabajo de la región de destino.


> [!NOTE]
> Este proceso requiere que siga actualizando las canalizaciones y los artefactos de código para incluir los cambios realizados en los grupos de Spark y de SQL sin servidor, los objetos y los roles del área de trabajo de la región de origen.

## <a name="step-7-create-a-shared-integration-runtime"></a>Paso 7: Creación de un entorno de ejecución de integración compartido

Para crear un entorno SHIR, siga los pasos que se indican en [Creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="step-8-assign-an-azure-role-to-managed-identity"></a>Paso 8: Asignación de un rol de Azure a una identidad administrada

 Asigne el acceso `Storage Blob Contributor` a la identidad administrada de la nueva área de trabajo en la cuenta de Data Lake Storage Gen2 asociada predeterminada. Asigne también acceso a otras cuentas de almacenamiento en las que se use SA-MI para la autenticación. Asigne el acceso `Storage Blob Contributor` o `Storage Blob Reader` a los usuarios y grupos de Azure AD para todas las cuentas de almacenamiento necesarias.

### <a name="azure-portal"></a>Azure portal
Siga los pasos que se indican en [Concesión de permisos a una identidad administrada de área de trabajo (versión preliminar)](security/how-to-grant-workspace-managed-identity-permissions.md) para asignar el rol de colaborador de datos de Storage Blob a la identidad administrada del área de trabajo.

### <a name="azure-powershell"></a>Azure PowerShell
Asigne el rol de colaborador de datos de Storage Blob a la identidad administrada del área de trabajo.

```powershell

$workSpaceIdentityObjectID= (Get-AzSynapseWorkspace -ResourceGroupName $resourceGroupName -Name $workspaceName).Identity.PrincipalId 
$scope = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Storage/storageAccounts/$($storageAccountName)"

# Adding Storage Blob Data Contributor to WS Managed Identity on the storage account. This errors out with the message New-AzRoleAssignment : Exception of type 'Microsoft.Rest.Azure.CloudException' was thrown.
# But it creates the required permissions on the storage account.
$roleAssignedforManagedIdentity=New-AzRoleAssignment -ObjectId $workSpaceIdentityObjectID `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope $scope -ErrorAction SilentlyContinue
```


### <a name="azure-cli"></a>Azure CLI

```azurecli
# Getting Role name
$roleName =az role definition list --query "[?contains(roleName, 'Storage Blob Data Contributor')].{roleName:roleName}" --output tsv

#Getting resource id for storage account
$scope= (az storage account show --name $storageAccountName|ConvertFrom-Json).id

#Getting principal ID for WS Managed Identity
$workSpaceIdentityObjectID=(az synapse workspace show --name $workspaceName --resource-group $resourceGroupName|ConvertFrom-Json).Identity.PrincipalId 
                    
# Adding Storage Blob Data Contributor Azure role to SA-MI
az role assignment create --assignee $workSpaceIdentityObjectID `
--role $roleName `
--scope $scope
```

## <a name="step-9-assign-azure-synapse-rbac-roles"></a>Paso 9: Asignación de roles de RBAC de Azure Synapse

Agregue todos los usuarios que necesitan acceso al área de trabajo de destino con roles y permisos independientes. El siguiente script de PowerShell y la CLI agrega un usuario de Azure AD al rol de administrador de Synapse en el área de trabajo de la región de destino. Para obtener todos los nombres de rol de RBAC de Azure Synapse, consulte [Roles de Synapse RBAC](security/synapse-workspace-synapse-rbac-roles.md).

### <a name="synapse-studio"></a>Synapse Studio

Para agregar o eliminar asignaciones de RBAC de Azure Synapse en Synapse Studio, siga los pasos que se indican en [Cómo administrar asignaciones de roles de Synapse RBAC en Synapse Studio](security/how-to-manage-synapse-rbac-role-assignments.md).


### <a name="azure-powershell"></a>Azure PowerShell

El siguiente script de PowerShell agrega la asignación del rol de administrador de Synapse a un usuario o grupo de Azure AD. Puede usar -RoleDefinitionId en lugar de -RoleDefinitionName con el siguiente comando para agregar los usuarios al área de trabajo:

```powershell
# Add the Synapse RBAC assignment. Use the objectId of the Azure AD user or group you want to assign.
New-AzSynapseRoleAssignment `
   -WorkspaceName $workspaceName  `
   -RoleDefinitionName "Synapse Administrator" `
   -ObjectId 1c02d2a6-ed3d-46ec-b578-6f36da5819c6

# Check if user is added to the access control by running this command.
Get-AzSynapseRoleAssignment -WorkspaceName $workspaceName  
```

Para obtener los id. de los objetos y roles del área de trabajo de la región de origen, ejecute el comando Get-AzSynapseRoleAssignment. Asigne los mismos roles de RBAC de Azure Synapse a los usuarios o grupos de Azure AD en el área de trabajo de la región de destino.

En lugar de usar -ObjectId como parámetro, puede usar también -SignInName, donde debe proporcionar la dirección de correo electrónico o el nombre principal del usuario. Para obtener más información sobre las opciones disponibles, consulte [New-AzSynapseRoleAssignment](/powershell/module/az.synapse/new-azsynapseroleassignment?view=azps-6.3.0&preserve-view=true). 

### <a name="azure-cli"></a>Azure CLI

```azurecli
#Get the Object Id of the user and assign the required Azure Synapse RBAC permissions to the Azure AD user. You can provide the email address of the user (username@contoso.com) for the --assignee parameter.
az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Administrator" --assignee adasdasdd42-0000-000-xxx-xxxxxxx

az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Contributor" --assignee "user1@contoso.com"

```

Para obtener más información sobre las opciones disponibles, consulte [az synapse role assignment](/cli/azure/synapse/role/assignment?view=azure-cli-latest&preserve-view=true). 

## <a name="step-10-upload-workspace-packages"></a>Paso 10: Carga de paquetes del área de trabajo

Cargue todos los paquetes del área de trabajo necesarios en la nueva área de trabajo. Para automatizar el proceso de carga de los paquetes del área de trabajo, consulte la [biblioteca cliente de artefactos de Microsoft Azure Synapse Analytics.](https://www.nuget.org/packages/Azure.Analytics.Synapse.Artifacts/1.0.0-preview.10)

## <a name="step-11-permissions"></a>Paso 11: Permisos
    
Para configurar el control de acceso para el área de trabajo de Azure Synapse en la región de destino, siga los pasos que se indican en [Procedimiento para configurar el control de acceso para el área de trabajo de Azure Synapse](security/how-to-set-up-access-control.md). 


## <a name="step-12-create-managed-private-endpoints"></a>Paso 12: Creación de puntos de conexión privados administrados

Para volver a crear los puntos de conexión privados administrados del área de trabajo de la región de origen en el área de trabajo de la región de destino, consulte [Creación de un punto de conexión privado administrado al origen de datos](security/how-to-create-managed-private-endpoints.md). 

## <a name="discard"></a>Discard (Descartar)
Si desea descartar el área de trabajo de la región de destino, elimínela de allí. Para ello, en el portal, vaya al grupo de recursos en el panel, seleccione el área de trabajo y seleccione Eliminar en la parte superior de la página Grupo de recursos.

## <a name="clean-up"></a>Limpieza
Para confirmar los cambios y completar el traslado del área de trabajo, elimine el área de trabajo de la región de origen después de probar el área de trabajo en la región de destino. Para ello, en el portal, vaya al grupo de recursos que tiene el área de trabajo de la región de origen en el panel, seleccione el área de trabajo y seleccione Eliminar en la parte superior de la página Grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [redes virtuales administradas de Azure Synapse Analytics](security/synapse-workspace-managed-vnet.md).
- Más información sobre los [puntos de conexión privados administrados de Azure Synapse](security/synapse-workspace-managed-private-endpoints.md).
- Más información sobre cómo [conectarse a los recursos de un área de trabajo desde una red restringida](security/how-to-connect-to-workspace-from-restricted-network.md).

---
title: Restauración de Azure Managed Disks mediante Azure PowerShell
description: Aprenda a hacer una copia de seguridad de Azure Managed Disks con Azure PowerShell.
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: beb6a266a9436b7c26f5786c5f5a57f10fb9319a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672716"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Restauración de Azure Managed Disks mediante Azure PowerShell

En este artículo se explica cómo realizar copias de seguridad de [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) mediante Azure PowerShell.

En este artículo, aprenderá a:

- Creación de un almacén de Backup

- Crear una directiva de copia de seguridad

- Configurar una copia de seguridad de Azure Disk

- Ejecutar un trabajo de copia de seguridad a petición.

Para obtener información sobre la disponibilidad de la región de la copia de seguridad de Azure Disk, los escenarios admitidos y las limitaciones, consulte la [matriz de compatibilidad](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de Backup es una entidad de almacenamiento de Azure que contiene los datos de las copias de seguridad de varias cargas de trabajo recientes que admite Azure Backup, como los servidores de Azure Database for PostgreSQL y los discos de Azure Disk. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de copias de seguridad se basan en el modelo de Azure Resource Manager de Azure, que proporciona características para proteger los datos de las copias de seguridad.

Antes de crear un almacén de copia de seguridad, elija la redundancia de almacenamiento de los datos dentro del almacén. Después, continúe con la creación del almacén de copia de seguridad con esa redundancia de almacenamiento y la ubicación. En este artículo, se creará el almacén de copia de seguridad "TestBkpVault" en la región "westus" en el grupo de recursos "testBkpVaultRG". Use el comando [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) para crear un almacén de copia de seguridad. Más información sobre [cómo crear un almacén de copia de seguridad](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Después de la creación del almacén, vamos a crear una directiva de copia de seguridad para proteger los discos de Azure.

## <a name="create-a-backup-policy"></a>Creación de una directiva de copia de seguridad

Para comprender los componentes internos de una directiva de copia de seguridad para los discos de Azure, recupere la plantilla de directiva con el comando [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Este comando devuelve una plantilla de directiva predeterminada para un tipo de origen de datos determinado. Use esta plantilla para crear una directiva.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

La plantilla de directiva consta de un desencadenador (que decide lo que desencadena la copia de seguridad) y un ciclo de vida (que decide cuándo se debe eliminar, copiar o trasladar la copia de seguridad). En la copia de seguridad de discos de Azure, el desencadenador predeterminado es un desencadenador por hora que está programado para activarse cada 4 horas (PT4H) y para conservar cada copia de seguridad durante 7 días.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure Disk Backup le ofrece varias copias de seguridad al día. Si necesita realizar copias de seguridad con más frecuencia, elija la frecuencia de copia de seguridad **Cada hora**, ya que le ofrece la posibilidad de realizar copias de seguridad a intervalos de 4, 6, 8 o 12 horas. Las copias de seguridad se programan en función del intervalo de **tiempo** seleccionado. Por ejemplo, si selecciona **Cada 4 horas**, las copias de seguridad se realizan aproximadamente cada 4 horas, por lo que las mismas se distribuyen equitativamente a lo largo del día. Si realizar una copia de seguridad al día es suficiente, elija la frecuencia **Diaria**. En la frecuencia de copia de seguridad diaria, puede especificar la hora del día en la que se realizarán las copias de seguridad. Es importante tener en cuenta que la hora del día indica la hora de inicio de la copia de seguridad y no la hora en que se completa la misma. El tiempo necesario para completar la operación de copia de seguridad depende de varios factores, como el tamaño del disco y la tasa de abandono entre copias de seguridad consecutivas. No obstante, Azure Disk Backup es una opción de copias de seguridad sin agente que usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md), lo que no afecta al rendimiento de la aplicación de producción.

   >[!NOTE]
   > Aunque el almacén seleccionado puede tener la configuración de redundancia global, Azure Disk Backup solo admite el almacén de datos de la instantánea. Todas las copias de seguridad se almacenan en un grupo de recursos de la suscripción y no se copian en el almacén de copias de seguridad.

Para más información sobre la creación de directivas, consulte el documento [Creación de una directiva de copia de seguridad](backup-managed-disks.md#create-backup-policy).

Si quiere editar la frecuencia por hora o el período de retención, use los comandos [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) o [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true). Una vez que el objeto de directiva tenga todos los valores deseados, continúe con la creación de una directiva a partir de dicho objeto con el comando [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Configuración de la copia de seguridad

Una vez que se crea el almacén y la directiva, hay tres puntos críticos que el usuario debe tener en cuenta para proteger los discos de Azure.

### <a name="key-entities-involved"></a>Entidades clave implicadas

#### <a name="disk-to-be-protected"></a>Disco que se va a proteger

Recuperación del identificador de ARM del disco que se va a proteger. Este servirá como identificador del disco. Usaremos el ejemplo de un disco denominado "PSTestDisk" en un grupo de recursos "diskrg" de una suscripción diferente.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Grupo de recursos de instantáneas

Las instantáneas de disco se almacenan en un grupo de recursos de la suscripción. Se recomienda crear un grupo de recursos dedicado como un almacén de datos de instantáneas que usará el servicio Azure Backup. Si tiene un grupo de recursos dedicado, podrá restringir los permisos de acceso en el grupo de recursos para asegurar y administrar fácilmente los datos de copia de seguridad. Anote el identificador de ARM del grupo de recursos en el que quiere colocar las instantáneas del disco

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Almacén de Backup

Los almacenes de copia de seguridad requieren permisos en el disco y en el grupo de recursos de instantáneas para poder desencadenar instantáneas y administrar su ciclo de vida. Para asignar estos permisos, se usa la identidad del almacén administrada asignada por el sistema. Use el comando [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) para habilitar la identidad administrada asignada por el sistema para el almacén de Recovery Services.

### <a name="assign-permissions"></a>Asignación de permisos

El usuario debe asignar algunos permisos mediante RBAC al almacén (representado por el valor de MSI del almacén) y al disco o el grupo de recursos del disco pertinentes. Para ello se puede usar el portal o PowerShell.

El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para configurar la copia de seguridad de los discos administrados, la identidad administrada del almacén de Backup necesita un conjunto de permisos en los discos de origen y los grupos de recursos donde se crean y administran las instantáneas.

Solo hay un identidad administrada asignada por cada recurso y está asociada al ciclo de vida del recurso. Puede conceder permisos a la identidad administrada mediante el control de acceso basado en roles de Azure (Azure RBAC). Tenga en cuenta que una identidad administrada es una entidad de servicio de un tipo especial que solo se puede usar con recursos de Azure. Obtenga más información sobre las [identidades administradas](/azure/active-directory/managed-identities-azure-resources/overview).

Para configurar la copia de seguridad de los discos administrados, se necesitan los siguientes requisitos previos:

- Asigne el rol **Lector de copias de seguridad de disco** a la identidad administrada del almacén de Backup en el disco de origen del que se tiene que realizar la copia de seguridad.

  1. Vaya al disco del que se debe hacer una copia de seguridad.
  1. Vaya al **Control de acceso (IAM)** y seleccione **Add role assignments** (Agregar asignaciones de roles).
  1. En el panel contextual de la derecha, seleccione **Lector de copias de seguridad de disco** en la lista desplegable **Rol**.
  1. Seleccione la identidad administrada del almacén de Backup y haga clic en **Guardar**.
  
     >[!Tip]
     >Escriba el nombre del almacén de Backup para seleccionar su identidad administrada.

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso de asignación del rol Lector de copias de seguridad de disco a la identidad administrada del almacén de Backup en el disco de origen del que se tiene que realizar la copia de seguridad." lightbox="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-expanded.png":::

- Asigne el rol de **Colaborador de instantáneas de discos** a la identidad administrada del almacén de Backup que se encuentra en el grupo de recursos donde el servicio Azure Backup crea y administra las copias de seguridad. Las instantáneas de disco se almacenan en un grupo de recursos de la suscripción. Para permitir que el servicio Azure Backup cree, almacene y administre instantáneas, debe proporcionar permisos al almacén de Backup.

  1. Vaya al grupo de recursos. Por ejemplo, el grupo de recursos _SnapshotRG_ se encuentra en la misma suscripción que el disco del que se va a realizar la copia de seguridad.
  1. Vaya al **Control de acceso (IAM)** y seleccione **Add role assignments** (Agregar asignaciones de roles).
  1. En el panel contextual de la derecha, seleccione **Colaborador de instantáneas de discos** en la lista desplegable **Rol**. 
  1. Seleccione la identidad administrada del almacén de Backup y haga clic en **Guardar**.
  
     >[!Tip]
     >Escriba el nombre del almacén de Backup para seleccionar la identidad administrada del mismo.

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-inline.png" alt-text="Captura de pantalla en la que se muestra el proceso de asignación del rol Colaborador de instantáneas de discos a la identidad administrada del almacén de Backup en el grupo de recursos." lightbox="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-expanded.png":::

- Compruebe que la identidad administrada del almacén de Backup tiene el conjunto adecuado de asignaciones de roles en el disco de origen y el grupo de recursos que servirán de almacén de datos de instantáneas.

  1. Vaya a **Almacén de Backup** -> **Identidad** y seleccione **Asignaciones de roles de Azure**.
 
     :::image type="content" source="./media/backup-managed-disks-ps/select-azure-role-assignments-inline.png" alt-text="Captura de pantalla en la que se muestra la selección de asignaciones de roles de Azure." lightbox="./media/backup-managed-disks-ps/select-azure-role-assignments-expanded.png":::

  1. Compruebe que el rol, el nombre del recurso y el tipo de recurso son correctos.
 
     :::image type="content" source="./media/backup-managed-disks-ps/verify-role-assignment-details-inline.png" alt-text="Captura de pantalla en la que se muestra la comprobación del rol, el nombre del recurso y el tipo de recurso." lightbox="./media/backup-managed-disks-ps/verify-role-assignment-details-expanded.png":::

>[!Note]
>Aunque las asignaciones de roles se reflejan correctamente en el portal, el permiso puede tardar aproximadamente entre 15 y 30 minutos en aplicarse en la identidad administrada del almacén de Backup.

### <a name="prepare-the-request"></a>Preparación de la solicitud

Una vez que se han establecido todos los permisos pertinentes, la configuración de la copia de seguridad se realiza en dos pasos. En primer lugar, se prepara la solicitud correspondiente mediante el almacén, la directiva, el disco y el grupo de recursos de instantáneas pertinentes con el comando [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true). A continuación, se envía la solicitud para proteger el disco con el comando [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

Capture la instancia de copia de seguridad pertinente en la que el usuario quiere desencadenar una copia de seguridad con el comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Mientras desencadena la copia de seguridad, puede especificar una regla de retención. Para ver las reglas de retención de la directiva, desplácese por el objeto de directiva de reglas de retención. En el ejemplo siguiente, se muestra la regla con el nombre "default" y esta es la que se usará para la copia de seguridad a petición.

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Desencadene una copia de seguridad a petición mediante el comando [backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar Az.ResourceGraph para realizar el seguimiento de todos los trabajos en todos los almacenes de copia de seguridad. Use el comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para obtener el trabajo pertinente, que puede encontrarse en cualquier almacén de copia de seguridad.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Pasos siguientes

- [Restauración de Azure Managed Disks mediante Azure PowerShell](restore-managed-disks-ps.md)

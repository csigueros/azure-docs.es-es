---
title: Copia de seguridad Azure Database for PostgreSQL con retención a largo plazo mediante Azure PowerShell
description: Aprenda a hacer una copia de seguridad Azure Database for PostgreSQL utilizando Azure PowerShell.
ms.topic: conceptual
ms.author: v-amallick
ms.date: 10/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 47beb64c518bbd55090c9cf6cd50068635310d25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093773"
---
# <a name="back-up-azure-postgresql-databases-using-azure-powershell"></a>Restauración de bases de datos de Azure PostgreSQL mediante Azure PowerShell

En este artículo se explica cómo realizar copias de seguridad de una base de datos de [Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) utilizando Azure PowerShell.

En este artículo, aprenderá a:

- Creación de un almacén de Backup

- Crear una directiva de copia de seguridad

- Configurar una copia de seguridad de una base de datos de Azure PostgreSQL

- Ejecutar un trabajo de copia de seguridad a petición.

Para obtener información sobre los escenarios admitidos y las limitaciones de las bases de datos de Azure PostgreSQL, consulte la [matriz de compatibilidad](backup-azure-database-postgresql-overview.md#support-matrix).

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de Backup es una entidad de almacenamiento de Azure que almacena los datos de las copias de seguridad de varias cargas de trabajo recientes que admite Azure Backup, tales como los servidores de Azure Database for PostgreSQL, Azure Disks y los blobs de Azure. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de copias de seguridad se basan en el modelo de Azure Resource Manager de Azure, que proporciona características para proteger los datos de las copias de seguridad.

Antes de crear un almacén de copia de seguridad, elija la redundancia del almacenamiento de los datos dentro del almacén. Después, continúe con la creación del almacén de copia de seguridad con esa redundancia de almacenamiento y la ubicación.

En este artículo, se creará el almacén de copia de seguridad *TestBkpVault* en la región *westus* en el grupo de recursos *testBkpVaultRG*. Use el comando [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) para crear un almacén de copia de seguridad. Obtenga más información sobre cómo [crear un almacén de Backup](./backup-vault-overview.md#create-a-backup-vault).

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

Después de crear el almacén, vamos a crear una directiva de copia de seguridad para proteger las bases de datos de Azure PostgreSQL.

## <a name="create-a-backup-policy"></a>Crear una directiva de copia de seguridad

### <a name="understand-postgresql-backup-policy"></a>Información sobre la directiva de copia de seguridad de PostgreSQL

Aunque la copia de seguridad de disco ofrece varias copias de seguridad al día y la copia de seguridad de blobs es una copia de seguridad *continua* sin desencadenador, la copia de seguridad de PostgreSQL ofrece protección de archivo. Los datos de copia de seguridad que se envían por primera vez al almacén se pueden mover al *nivel de acceso de archivo* en base a una regla definida o a un *ciclo de vida*. En este contexto, vamos a entender el objeto de directiva de copia de seguridad para PostgreSQL.

-  Regla de directiva
   -  Regla de copia de seguridad
      -  Parámetro de copia de seguridad
         -  Tipo de copia de seguridad (una copia de seguridad completa de la base de datos en este caso)
         -  Almacén de datos inicial (dónde llegarán inicialmente las copias de seguridad)
         -  Desencadenador (cómo se desencadena la copia de seguridad)
            -  Basada en una programación
            -  Criterios de etiquetado predeterminados (una "etiqueta" predeterminada para todas las copias de seguridad programadas. Esta etiqueta vincula las copias de seguridad a la regla de retención).
   -  Regla de retención predeterminada (regla que se aplicará a todas las copias de seguridad, de forma predeterminada, en el almacén de datos inicial)

Por lo tanto, este objeto define qué tipo de copias de seguridad se desencadenan, cómo se desencadenan (a través de una programación), con qué se etiquetan, a dónde llegan (un almacén de datos) y el ciclo de vida de los datos de copia de seguridad en un almacén de datos. El objeto predeterminado de PowerShell para PostgreSQL indica que se desencadena una copia de seguridad *completa* cada semana y que llegarán al almacén, donde se almacenan durante tres meses.

Si desea agregar el nivel de acceso de *archivo* a la directiva, debe decidir cuándo se van a mover los datos del almacén al archivo, cuánto tiempo permanecerán los datos en el archivo y cuál de las copias de seguridad programadas debe *etiquetarse* como _archivable_. Por lo tanto, tiene que agregar una *regla de retención*, la cual definirá el ciclo de vida de los datos de la copia de seguridad desde el almacén de datos del almacén al almacén de datos de archivo y el tiempo que permanecerán en el *almacén de datos de archivo*. A continuación, debe agregar una *etiqueta* que marque las copias de seguridad programadas como _aptas para archivarse_.

El objeto de PowerShell resultante es el siguiente:


-  Regla de directiva
   -  Regla de copia de seguridad
      -  Parámetro de copia de seguridad
         -  Tipo de copia de seguridad (una copia de seguridad completa de la base de datos en este caso)
         -  Almacén de datos inicial (dónde llegarán inicialmente las copias de seguridad)
         -  Desencadenador (cómo se desencadena la copia de seguridad)
            -  Basada en una programación
            -  Criterios de etiquetado predeterminados (una "etiqueta" predeterminada para todas las copias de seguridad programadas. Esta etiqueta vincula las copias de seguridad a la regla de retención).
            -  Nuevos criterios de etiquetado para la nueva regla de retención con el mismo nombre "X"
   -  Regla de retención predeterminada (regla que se aplicará a todas las copias de seguridad, de forma predeterminada, en el almacén de datos inicial)
   -  Una nueva regla de retención denominada "X"
      -  Ciclo de vida
         -  Almacén de datos de origen
         -  Eliminación después del período de tiempo en el almacén de datos de origen
         -  Copiar en el almacén de datos de destino

### <a name="retrieving-the-policy-template"></a>Recuperación de la plantilla de directiva

Para comprender los componentes internos de una directiva de copia de seguridad para una base de datos Azure PostgreSQL, recupere la plantilla de directiva con el comando [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Este comando devuelve una plantilla de directiva predeterminada para un tipo de origen de datos determinado. Use esta plantilla para crear una directiva.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDatabaseForPostgreSQL
$policyDefn | fl


DatasourceType : {Microsoft.DBforPostgreSQL/servers/databases}
ObjectType     : BackupPolicy
PolicyRule     : {BackupWeekly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : VaultStore
Name                      : BackupWeekly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.ScheduleBasedTriggerCo
                            ntext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

La plantilla de directiva consta de un desencadenador (que decide lo que desencadena la copia de seguridad) y un ciclo de vida (que decide cuándo se debe eliminar, copiar o trasladar la copia de seguridad). En la copia de seguridad de la base de datos de Azure PostgreSQL, el valor predeterminado del desencadenador es un desencadenador semanal programado (1 copia de seguridad cada 7 días) y conservar cada copia de seguridad durante tres meses.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2021-08-22T02:00:00+00:00/P1W}
ScheduleTimeZone              : UTC
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P3M
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : VaultStore
TargetDataStoreCopySetting : {}
```

### <a name="modify-the-policy-template"></a>Modificación de la plantilla de directiva

#### <a name="modify-the-schedule"></a>Modificación de la programación

La plantilla de directiva predeterminada ofrece una copia de seguridad una vez por semana. Puede modificar la programación para que la copia de seguridad se realice varios días a la semana. Para cambiar la programación, use el comando[Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-6.5.0&preserve-view=true)

En el ejemplo siguiente se modifica la copia de seguridad semanal para realizar una copia de seguridad que se realice todos los domingos, miércoles y viernes de cada semana. La matriz de fechas de programación menciona las fechas y los días de la semana de esas fechas se toman como días de la semana. También debe especificar que estas programaciones se repitan cada semana. Por lo tanto, el intervalo de programación es "1" y el tipo de intervalo es "semanal".

```azurepowershell-interactive
$schDates = @(
    (
        (Get-Date -Year 2021 -Month 08 -Day 15 -Hour 22 -Minute 0 -Second 0)
    ), 
    (
        (Get-Date -Year 2021 -Month 08 -Day 18 -Hour 22 -Minute 0 -Second 0)
    ),
  (
        (Get-Date -Year 2021 -Month 08 -Day 20 -Hour 22 -Minute 0 -Second 0)
    )
)
$trigger = New-AzDataProtectionPolicyTriggerScheduleClientObject -ScheduleDays $schDates -IntervalType Weekly -IntervalCount 1 
Edit-AzDataProtectionPolicyTriggerClientObject -Schedule $trigger -Policy $policyDefn   
```

#### <a name="add-a-new-retention-rule"></a>Adición de una nueva regla de retención

Si desea agregar la protección de _archivo_, debe modificar la plantilla de directiva como se indica a continuación.

La plantilla predeterminada tendrá un ciclo de vida para el almacén de datos inicial bajo la regla de retención predeterminada. En este escenario, la regla indica que se eliminarán los datos de copia de seguridad después de tres meses. Debe agregar una nueva regla de retención que defina cuándo se *moverán* los datos al *almacén de datos de archivo*, es decir, los datos de copia de seguridad se copian primero en el almacén de datos de archivo y, a continuación, se eliminan en el almacén de datos del almacén. Además, la regla debe definir durante cuánto tiempo se mantendrán los datos en el *almacén de datos de archivo*. Utilice el comando [New-AzDataProtectionRetentionLifeCycleClientObject](/powershell/module/az.dataprotection/new-azdataprotectionretentionlifecycleclientobject?view=azps-6.5.0&preserve-view=true) para crear nuevos ciclos de vida y el comando [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-6.5.0&preserve-view=true) para asociarlos con las nuevas reglas o con las reglas existentes.

En el ejemplo siguiente se crea una nueva regla de retención denominada *Mensual*, donde la primera copia de seguridad correcta de cada mes se debe conservar en el almacén durante seis meses, se mueve al nivel de archivo y se mantiene en el nivel de archivo 24 meses.

```azurepowershell-interactive
$VaultToArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore VaultStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 6 -TargetDataStore ArchiveStore -CopyOption CopyOnExpiryOption

$OnArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore ArchiveStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 24

Edit-AzDataProtectionPolicyRetentionRuleClientObject -Policy $policyDefn -Name Monthly -LifeCycles $VaultToArchiveLifeCycle, $OnArchiveLifeCycleLifeCycle -IsDefault $false
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>Agregue una etiqueta y los criterios pertinentes

Una vez creada una regla de retención, debe crear una *etiqueta* correspondiente en la propiedad *Desencadenador* de la directiva de copia de seguridad. Utilice el comando [New-AzDataProtectionPolicyTagCriteriaClientObject](/powershell/module/az.dataprotection/new-azdataprotectionpolicytagcriteriaclientobject?view=azps-6.5.0&preserve-view=true) para crear el nuevo criterio de etiquetado y utilice el comando [Edit-AzDataProtectionPolicyTagClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytagclientobject?view=azps-6.5.0&preserve-view=true) para actualizar la etiqueta existente o crear una nueva etiqueta.

En el ejemplo siguiente se crea una nueva *etiqueta* junto con los criterios (que es la primera copia de seguridad correcta del mes) con el mismo nombre que la regla de retención correspondiente que se va a aplicar.

En este ejemplo, el criterio de la etiqueta debería denominarse *Mensual*.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -AbsoluteCriteria FirstOfMonth
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

Si la programación es de varias copias de seguridad por semana (todos los domingos, miércoles y jueves, como se especifica en el ejemplo anterior) y desea archivar las copias de seguridad de domingo y viernes, los criterios de etiquetado se pueden cambiar de la siguiente manera:

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -DaysOfWeek @("Sunday", "Friday")
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

### <a name="create-a-new-postgresql-backup-policy"></a>Creación de una nueva directiva de copia de seguridad de PostgreSQL

Una vez modificada la plantilla según los requisitos, use el comando [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-6.5.0&preserve-view=true) para crear una directiva con la plantilla modificada.

```azurepowershell-interactive
$polOss = New-AzDataProtectionBackupPolicy -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Name "TestOSSPolicy" -Policy $policyDefn
```

## <a name="configure-backup"></a>Configuración de la copia de seguridad

Una vez que se crean el almacén y la directiva, hay tres puntos críticos que debe tener en cuenta para proteger una base de datos de Azure PostgreSQL.

### <a name="key-entities-involved"></a>Entidades clave implicadas

#### <a name="postgresql-database-to-be-protected"></a>Base de datos PostgreSQL que se va a proteger

Obtenga el identificador de Azure Resource Manager (id. de ARM) de la base de datos de PostgreSQL que se va a proteger. Esto actúa como identificador de la base de datos. Usaremos un ejemplo de una base de datos denominada **empdb11** en un servidor de PostgreSQL **testpostgresql**, que está presente en el grupo de recursos **ossdemoRG** bajo una suscripción diferente.

```azurepowershell-interactive
$ossId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

El servicio Azure Backup no almacena el nombre de usuario y la contraseña para conectarse a la base de datos de PostgreSQL. En su lugar, el administrador de copia de seguridad incluye las *claves* en el almacén de claves y, a continuación, el servicio de copia de seguridad accederá al almacén de claves, leerá las claves y accederá a la base de datos. Anote el identificador secreto de la clave pertinente.

```azurepowershell-interactive
$keyURI = "https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Almacén de Backup

El almacén de Backup tiene que conectarse al servidor de PostgreSQL y, a continuación, acceder a la base de datos a través de las claves presentes en el almacén de claves. Por lo tanto, requiere acceso al servidor de PostgreSQL y al almacén de claves. Se concede acceso al MSI del almacén de Backup.

[Obtenga información sobre los permisos adecuados](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup) que debe conceder para realizar una copia de seguridad del MSI del almacén en el servidor de PostgreSQL y el almacén de claves de Azure, donde se almacenan las claves de la base de datos.

### <a name="prepare-the-request"></a>Preparación de la solicitud

Una vez que se han establecido todos los permisos pertinentes, la configuración de la copia de seguridad se realiza en dos pasos.

1. En primer lugar, se prepara la solicitud correspondiente mediante el almacén, la directiva y la base de datos PostgreSQL pertinentes con el comando [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).
1. A continuación, se envía la solicitud para proteger la base de datos con el comando [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDatabaseForPostgreSQL -DatasourceLocation $TestBkpvault.Location -PolicyId $polOss[0].Id -DatasourceId $ossId -SecretStoreURI $keyURI -SecretStoreType AzureKeyVault
ConvertTo-Json -InputObject $instance -Depth 4 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                        Type                                         BackupInstanceName
----                        ----                                          ------------------
ossrg-empdb11       Microsoft.DataProtection/backupVaults/backupInstances ossrg-empdb11
```

## <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

Capture la instancia de copia de seguridad pertinente en la que necesita desencadenar una copia de seguridad con el comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Mientras desencadena la copia de seguridad, puede especificar una regla de retención. Para ver las reglas de retención de la directiva, desplácese por el objeto de directiva de reglas de retención. En el ejemplo siguiente, se muestra la regla con el nombre *predeterminado*. Usaremos esa regla para la copia de seguridad a petición.

```azurepowershell-interactive
$ossPol.PolicyRule | fl


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

## <a name="trigger-an-on-demand-backup"></a>Desencadenamiento de una copia de seguridad a petición

Para desencadenar una copia de seguridad a petición, utilice el comando[backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="track-jobs"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar _Az.ResourceGraph_ para realizar el seguimiento de todos los trabajos en todos los almacenes de copia de seguridad. Use el comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para buscar el trabajo pertinente, que puede encontrarse en cualquier almacén de copia de seguridad.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Pasos siguientes

- [Restauración de una base de datos de Azure PostgreSQL utilizando Azure PowerShell](restore-managed-disks-ps.md)

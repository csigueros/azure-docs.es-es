---
title: Restauración de bases de datos de Azure PostgreSQL mediante Azure PowerShell
description: Aprenda a restaurar bases de datos de Azure PostgreSQL mediante Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 6b271f823d8c0547a0fd48861e463bfcc1513e97
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093291"
---
# <a name="restore-azure-postgresql-databases-using-azure-powershell"></a>Restauración de bases de datos de Azure PostgreSQL mediante Azure PowerShell

En este artículo se explica cómo restaurar [bases de datos de Azure PostgreSQL](/azure/postgresql/overview#azure-database-for-postgresql---single-server) en un servidor de Azure PostgreSQL del que se ha creado una copia de seguridad con Azure Backup.

Al ser una base de datos PaaS, no se admite la opción Recuperación de la ubicación original (OLR) para restaurar mediante la sustitución de la base de datos existente (desde la que se realizaron las copias de seguridad). Puede restaurar desde un punto de recuperación para crear una nueva base de datos en el mismo servidor de Azure PostgreSQL o en otro servidor de PostgreSQL. Esto se denomina Recuperación de ubicación alternativa (ALR) y ayuda a mantener tanto la base de datos de origen como la base de datos restaurada (nueva).

En este artículo, aprenderá a:

- Restaurar para crear una nueva base de datos PostgreSQL

- Realizar el seguimiento del estado de la operación de restauración.

Haremos referencia a un almacén de copia de seguridad existente _TestBkpVault_ en el grupo de recursos _testBkpVaultRG_ en los ejemplos.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-postgresql-database"></a>Restaurar para crear una nueva base de datos PostgreSQL

### <a name="set-up-permissions"></a>Configuración de permisos

El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para restaurar desde la copia de seguridad, la identidad administrada del almacén de Backup requiere un conjunto de permisos en el servidor de Azure PostgreSQL en el que se debe restaurar la base de datos.

Para asignar los permisos pertinentes para la identidad administrada asignada por el sistema del almacén en el servidor PostgreSQL de destino, consulte el [conjunto de permisos necesarios para realizar una copia de seguridad de la base de datos de Azure PostgreSQL](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore).

Para restaurar el punto de recuperación como archivos en una cuenta de almacenamiento, la [identidad administrada asignada por el sistema del almacén de Backup necesita acceso a la cuenta de almacenamiento de destino](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account).

### <a name="fetching-the-relevant-recovery-point"></a>Captura del punto de recuperación pertinente

Recupere todas las instancias mediante el comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) e identifique la instancia pertinente.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

También puede usar **Az.Resourcegraph** y el comando [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) para buscar puntos de recuperación en instancias en muchos almacenes y suscripciones.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured
```

Para filtrar los criterios de búsqueda, use las funcionalidades de búsqueda de cliente de PowerShell como se muestra a continuación:

```azurepowershell-interactive
Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured | Where-Object { $_.BackupInstanceName -match "empdb11"}
```

Una vez identificada la instancia, capture el punto de recuperación pertinente.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

Si necesita capturar el punto de recuperación del nivel de archivo, agregue un filtro de cliente como se muestra a continuación:

```azurepowershell-interactive
Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName | Where-Object {$_.Property.RecoveryPointDataStoresDetail[0].Type -match "Archive" }
```

### <a name="prepare-the-restore-request"></a>Preparación de la solicitud de restauración

Hay varias opciones de restauración para una base de datos de PostgreSQL. Puede restaurar el punto de recuperación como otra base de datos o restaurar como archivos. El punto de recuperación también puede estar en el nivel de archivo.

#### <a name="restore-as-database"></a>Restaurar como base de datos

Construya el identificador de Azure Resource Manager (id. de ARM) de la nueva base de datos de PostgreSQL que se va a crear con el servidor de PostgreSQL de destino, al que se asignaron los permisos tal y como se ha indicado [anteriormente](#set-up-permissions), y el nombre de la base de datos de PostgreSQL necesaria. Por ejemplo, una base de datos de PostgreSQL se puede denominar **emprestored21** en un servidor de PostgreSQL de destino **targetossserver** en el grupo de recursos **targetrg** con una suscripción diferente.

```azurepowershell-interactive
$targetOssId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21
```

Use el comando [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) para preparar la solicitud de restauración con todos los detalles pertinentes.

```azurepowershell-interactive
$OssRestoreReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault
```

Para un punto de recuperación basado en archivo, debe:

1. Rehidratar desde el almacén de datos de archivo al almacén.
1. Modificar el almacén de datos de origen.
1. Agregar otros parámetros para especificar la prioridad de rehidratación.
1. Especificar la duración durante la que se debe conservar el punto de recuperación rehidratado en el almacén de datos.
1. Restaurar como base de datos desde este punto de recuperación.

Use el siguiente comando para preparar la solicitud para todas las operaciones mencionadas anteriormente, a la vez.

```azurepowershell-interactive
$OssRestoreFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault -RehydrationDuration 12 -RehydrationPriority Standard
```

#### <a name="restore-as-files"></a>Restaurar como archivos

Recupere el URI del contenedor, dentro de la cuenta de almacenamiento a la que se le asignaron permisos como se detalla [arriba](#set-up-permissions). Por ejemplo, un contenedor denominado **testcontainerrestore** en una cuenta de almacenamiento **testossstorageaccount** con una suscripción diferente.

```azurepowershell-interactive
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Use el comando [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) para preparar la solicitud de restauración con todos los detalles pertinentes.

```azurepowershell-interactive
$OssRestoreAsFilesReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" 
```

Para el punto de recuperación basado en archivo, modifique el almacén de datos de origen y agregue la prioridad de rehidratación y la duración de retención, en días, del punto de recuperación rehidratado, como se menciona a continuación:

```azurepowershell-interactive
$OssRestoreAsFilesFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" -RehydrationDuration "14" -RehydrationPriority Standard
```

### <a name="trigger-the-restore"></a>Desencadenamiento de la restauración

Use el comando [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) para desencadenar la restauración con la solicitud preparada anteriormente.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $OssRestoreReq
```

## <a name="tracking-job"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar *Az.ResourceGraph* para realizar el seguimiento de todos los trabajos en todos los almacenes de Backup. Use el comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para obtener el trabajo pertinente, que puede encontrarse en cualquier almacén de copia de seguridad.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -Operation OnDemandBackup
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al Backup de Azure PostgreSQL](backup-azure-database-postgresql-overview.md)

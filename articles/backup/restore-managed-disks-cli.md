---
title: Restauración de Azure Managed Disks mediante la CLI de Azure
description: Aprenda a restaurar Azure Managed Disks con la CLI de Azure.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 87ca3597b7ad73aa1ac0ba5cff6c75b5ec880750
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287496"
---
# <a name="restore-azure-managed-disks-using-azure-cli"></a>Restauración de Azure Managed Disks mediante la CLI de Azure

En este artículo se indica cómo restaurar [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) desde un punto de restauración que haya creado Azure Backup mediante la CLI de Azure.

> [!IMPORTANT]
> La compatibilidad con la copia de seguridad y la restauración de Azure Managed Disks mediante la CLI está en versión preliminar y está disponible como una extensión en la versión 2.15.0 y posteriores de Az. La extensión se instala automáticamente al ejecutar los comandos **az dataprotection**. [Obtenga más información](/cli/azure/azure-cli-extensions-overview) sobre las extensiones.

Actualmente, no se admite la opción de Recuperación de la ubicación original (OLR) de la restauración mediante la sustitución del disco de origen desde el que se realizaron las copias de seguridad. Puede realizar la restauración desde un punto de recuperación para crear un nuevo disco en el mismo grupo de recursos que el disco de origen o en cualquier otro grupo de recursos. Esto se conoce como recuperación de ubicación alternativa (ALR).

En este artículo, aprenderá a:

- Realizar una restauración para crear un disco nuevo.

- Realizar el seguimiento del estado de la operación de restauración.

Haremos referencia a un almacén de copia de seguridad existente _TestBkpVault_ en el grupo de recursos _testBkpVaultRG_ en los ejemplos.

## <a name="restore-to-create-a-new-disk"></a>Realizar una restauración para crear un disco nuevo.

### <a name="setting-up-permissions"></a>Configuración de permisos

El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para restaurar contenido a partir de una copia de seguridad, la identidad administrada del almacén de Backup debe tener un conjunto de permisos en el grupo de recursos en el que se vaya a restaurar el disco.

El almacén de Backup usa una identidad administrada asignada por el sistema; cada recurso solo puede tener una identidad, y cada una de ellas está asociada al ciclo de vida del recurso. Puede conceder permisos a la identidad administrada mediante el control de acceso basado en roles de Azure (Azure RBAC). Tenga en cuenta que una identidad administrada es una entidad de servicio de un tipo especial que solo se puede usar con recursos de Azure. Obtenga más información sobre las [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md).

Asigne los permisos pertinentes para la identidad administrada asignada por el sistema del almacén en el grupo de recursos de destino en el que se restaurarán o crearán los discos tal y como se mencionó [aquí](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Captura del punto de recuperación pertinente

Enumere todas las instancias de copia de seguridad de un almacén mediante el comando[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) y, a continuación, obtenga la instancia correspondiente mediante el comando [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show). Como alternativa, para escenarios a escala, puede enumerar las instancias de copia de seguridad entre almacenes y suscripciones mediante el comando [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Compute/disks",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
        "resourceLocation": "westus",
        "resourceName": "CLITestDisk",
        "resourceType": "Microsoft.Compute/disks",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestDisk",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
            }
          ]
        },
        "policyVersion": null
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]


```

Una vez identificada la instancia, capture el punto de recuperación correspondiente mediante el comando [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list).

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493",
"name": "5081ad8f1e6c4548ae89536d0d45c493",
"properties": {
"friendlyName": "0f598ced-cbfe-4169-b962-ee94b0210490",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T09:01:57.708319+00:00",
"expiryTime": "2021-06-15T09:01:57.708319+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "5081ad8f1e6c4548ae89536d0d45c493",
"recoveryPointTime": "2021-06-08T09:01:57.708319+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
},
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/039322cc563049bcbdb77bd695d4c02c",
"name": "039322cc563049bcbdb77bd695d4c02c",
"properties": {
"friendlyName": "af6512b6-aa38-4966-b8e1-660c4eccdc0d",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T05:01:55.426507+00:00",
"expiryTime": "2021-06-15T05:01:55.426507+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "039322cc563049bcbdb77bd695d4c02c",
"recoveryPointTime": "2021-06-08T05:01:55.426507+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
]
```

Por ejemplo, la consulta siguiente devuelve el punto de recuperación más reciente.

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault --query "[0].id"

"/subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493"
```

### <a name="preparing-the-restore-request"></a>Preparación de la solicitud de restauración

Construya el id. de ARM del nuevo disco que se va a crear con el grupo de recursos de destino, al que se asignaron los permisos tal y como se ha indicado [anteriormente](#setting-up-permissions), y el nombre de disco necesario. Usaremos el ejemplo de un disco llamado _CLITestDisk2_ en el grupo de recursos _targetrg_ de otra suscripción.

```azurecli-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2
```

Use el comando [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) para preparar la solicitud de restauración con todos los detalles pertinentes.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDisk --restore-location southeastasia --source-datastore OperationalStore --recovery-point-id /subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493 --target-resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2 > restore.json
```

```json
{
  "object_type": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recovery_point_id": "77594ce0470849e79b86a6875b726dca",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk2",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}

```

También puede validar si el archivo JSON va a crear correctamente nuevos recursos mediante el comando [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore).

```azurecli-interactive
az dataprotection backup-instance validate-for-restore -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --restore-request-object restore.json
```

### <a name="trigger-the-restore"></a>Desencadenamiento de la restauración

Use el comando [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) para desencadenar la restauración con la solicitud preparada anteriormente.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --parameters restore.json
```

## <a name="tracking-job"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar Az.ResourceGraph para realizar el seguimiento de todos los trabajos en todos los almacenes de copia de seguridad. Use el comando [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) para obtener el trabajo pertinente, que puede encontrarse en cualquier almacén de copia de seguridad.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --operation Restore
```

## <a name="next-steps"></a>Pasos siguientes

[Preguntas más frecuentes sobre Azure Disk Backup](./disk-backup-faq.yml)
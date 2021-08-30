---
title: Restauración de blobs de Azure mediante la CLI de Azure
description: Aprenda a restaurar blobs de Azure a cualquier momento dado mediante la CLI de Azure.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 620b98d40c66b8af2360559c6f71db5ef6c07114
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2021
ms.locfileid: "113644318"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-cli"></a>Restauración de los blobs de Azure a un momento dado mediante la CLI de Azure

En este artículo se describe cómo restaurar [blobs](blob-backup-overview.md) a cualquier momento dado mediante Azure Backup.

> [!IMPORTANT]
> La compatibilidad con la copia de seguridad y restauración de blobs de Azure mediante la CLI está en versión preliminar y está disponible como una extensión en la versión Az 2.15.0 y posteriores. La extensión se instala automáticamente al ejecutar los comandos **az dataprotection**. [Obtenga más información](/cli/azure/azure-cli-extensions-overview) sobre las extensiones.

> [!IMPORTANT]
> Antes de restaurar los blobs de mediante Azure Backup, consulte los [puntos importantes](blob-restore.md#before-you-start).

En este artículo, aprenderá a:

- Restauración de blobs de Azure a un momento dado

- Realizar el seguimiento del estado de la operación de restauración.

Haremos referencia a un almacén de copia de seguridad existente _TestBkpVault_ en el grupo de recursos _testBkpVaultRG_ en los ejemplos.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Restauración de blobs de Azure dentro de una cuenta de almacenamiento

### <a name="fetching-the-valid-time-range-for-restore"></a>Captura del intervalo de tiempo válido para la restauración

Como la copia de seguridad operativa de blobs es continua, no hay puntos distintos desde los que restaurar. En su lugar, es necesario capturar el intervalo de tiempo válido en el que los blobs se pueden restaurar a cualquier momento dado. En este ejemplo, vamos a comprobar los intervalos de tiempo válidos para restaurar en los últimos 30 días.

En primer lugar, es necesario capturar el identificador de la instancia de copia de seguridad correspondiente. Enumere todas las instancias de copia de seguridad de un almacén mediante el comando[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) y, a continuación, obtenga la instancia correspondiente mediante el comando [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show). Como alternativa, para escenarios a escala, puede enumerar las instancias de copia de seguridad entre almacenes y suscripciones mediante el comando [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureBlob --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"

[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestSA",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": ""
            }
          ]
        },
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "rg-bv",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]
```

Una vez identificada la instancia, capture el intervalo de recuperación correspondiente mediante el comando [az dataprotection restorable-time-range find](/cli/azure/dataprotection/restorable-time-range?view=azure-cli-latest&preserve-view=true#az_dataprotection_restorable_time_range_find).

```azurecli-interactive
az dataprotection restorable-time-range find --start-time 2021-05-30T00:00:00 --end-time 2021-05-31T00:00:00 --source-data-store-type OperationalStore -g testBkpVaultRG --vault-name TestBkpVault --backup-instances CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036

{
  "id": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
  "name": null,
  "properties": {
    "objectType": "AzureBackupFindRestorableTimeRangesResponse",
    "restorableTimeRanges": [
      {
        "endTime": "2021-05-31T00:00:00.0000000Z",
        "objectType": "RestorableTimeRange",
        "startTime": "2021-06-13T18:53:44.4465407Z"
      }
    ]
  },
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges"
}
```

### <a name="preparing-the-restore-request"></a>Preparación de la solicitud de restauración

Una vez corregido el momento dado para la restauración, hay varias opciones para restaurar.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Restauración de todos los blobs a un momento dado

Con esta opción, puede restaurar todos los blobs en bloques de la cuenta de almacenamiento al momento dado seleccionado para restaurarlos. Las cuentas de almacenamiento que contienen grandes cantidades de datos o con una alta tasa de renovación pueden tardar más tiempo en restaurarse. Para restaurar todos los blobs en bloques, use el comando [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery). La ubicación de restauración y el identificador del recurso de destino serán los mismos que la cuenta de almacenamiento protegida.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z > restore.json
```

#### <a name="restoring-selected-containers"></a>Restauración de los contenedores seleccionados

Con esta opción, puede examinar y seleccionar hasta 10 contenedores para restaurar. Para restaurar los contenedores seleccionados, use el comando [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery).

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
      {
        "max_matching_value": "container1-0",
        "min_matching_value": "container1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2-0",
        "min_matching_value": "container2",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2 > restore.json
```

#### <a name="restoring-containers-using-a-prefix-match"></a>Restauración de contenedores mediante una coincidencia de prefijo

Esta opción permite restaurar un subconjunto de blobs que coincidan con un prefijo. Puede restaurar hasta 10 intervalos lexicográficos de blobs dentro de un solo contenedor o en varios contenedores para devolver esos blobs a su estado anterior en un momento dado. Hay algunos aspectos que se deben tener en cuenta:

- Puede usar una barra diagonal (/) para delimitar el nombre del contenedor del prefijo de blob.
- El inicio del intervalo especificado es inclusivo, pero el intervalo especificado es exclusivo.

[Obtenga más información](blob-restore.md#use-prefix-match-for-restoring-blobs) sobre el uso de prefijos para restaurar intervalos de blobs.

Para restaurar los contenedores seleccionados, use el comando [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery).

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
       {
        "max_matching_value": "container1/text4",
        "min_matching_value": "container1/text1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2/text41",
        "min_matching_value": "container2/text4",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}



az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41 > restore.json
```

### <a name="trigger-the-restore"></a>Desencadenamiento de la restauración

Use el comando [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) para desencadenar la restauración con la solicitud preparada anteriormente.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036 --restore-request-object restore.json
```

## <a name="tracking-job"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar Az.ResourceGraph para realizar el seguimiento de todos los trabajos en todos los almacenes de copia de seguridad. Use el comando [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) para obtener el trabajo pertinente, que puede encontrarse en cualquier almacén de copia de seguridad.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureBlob --operation Restore
```

## <a name="next-steps"></a>Pasos siguientes

[Matriz de compatibilidad para la copia de seguridad de blobs de Azure](blob-backup-support-matrix.md)

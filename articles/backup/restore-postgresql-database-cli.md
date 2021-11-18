---
title: Restauración de bases de datos de Azure PostgreSQL mediante CLI de Azure
description: Aprenda a restaurar bases de datos de Azure PostgreSQL mediante CLI de Azure.
ms.topic: conceptual
ms.date: 10/25/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 3b748c8279740b4ef3a4e3b97b6acbc4a5aae6c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707395"
---
# <a name="restore-azure-postgresql-databases-using-azure-cli"></a>Restauración de bases de datos de Azure PostgreSQL mediante CLI de Azure

En este artículo se explica cómo restaurar [bases de datos de Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) en un servidor de Azure PostgreSQL del que se ha creado una copia de seguridad con Azure Backup.

Al ser una base de datos PaaS, no se admite la opción Recuperación de la ubicación original (OLR) para restaurar mediante la sustitución de la base de datos existente (desde la que se realizaron las copias de seguridad). Puede restaurar desde un punto de recuperación para crear una nueva base de datos en el mismo servidor de Azure PostgreSQL o en cualquier otro servidor de PostgreSQL. Esto se denomina Recuperación de ubicación alternativa (ALR) y ayuda a mantener tanto la base de datos de origen como la base de datos restaurada (nueva).

En este artículo, aprenderá a:

- Restaurar para crear una nueva base de datos PostgreSQL

- Realizar el seguimiento del estado de la operación de restauración.

Haremos referencia a un almacén de copia de seguridad existente _TestBkpVault_ en el grupo de recursos _testBkpVaultRG_ en los ejemplos.

## <a name="restore-a-backed-up-postgresql-database"></a>Restauración de una base de datos de PostgreSQL de la que se ha hecho una copia de seguridad

### <a name="set-up-permissions"></a>Configuración de permisos

El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para restaurar desde la copia de seguridad, la identidad administrada del almacén de Backup requiere un conjunto de permisos en el servidor de Azure PostgreSQL en el que se debe restaurar la base de datos.

Para asignar los permisos pertinentes para la identidad administrada asignada por el sistema del almacén en el servidor PostgreSQL de destino, consulte el [conjunto de permisos necesarios para realizar una copia de seguridad de la base de datos de Azure PostgreSQL](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore).

Para restaurar el punto de recuperación como archivos en una cuenta de almacenamiento, la [identidad administrada asignada por el sistema del almacén de Backup necesita acceso a la cuenta de almacenamiento de destino](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account).

### <a name="fetch-the-relevant-recovery-point"></a>Captura del punto de recuperación pertinente

Enumere todas las instancias de copia de seguridad de un almacén mediante el comando[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) y, a continuación, obtenga la instancia correspondiente mediante el comando [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show). Como alternativa, para escenarios a _gran escala_, puede enumerar las instancias de copia de seguridad entre almacenes y suscripciones mediante el comando [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL -subscriptions "xxxxxxxx-xxxx-xxxx-xxxx"

  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceLocation": "westus",
        "resourceName": "postgres",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceUri": ""
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceLocation": "westus",
        "resourceName": "testpostgresql",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceUri": ""
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "friendlyName": "testpostgresql\\empdb11",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyParameters": null,
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded",
      "validationType": null
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "testBkpVault",
    "zones": null
  }
.
.
.
.
.
```

Una vez identificada la instancia, capture el punto de recuperación correspondiente mediante el comando [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list).

```azurecli
az dataprotection recovery-point list --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 -g testBkpVaultRG --vault-name TestBkpVault

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints/9da55e757af94261afa009b43cd3222a",
  "name": "9da55e757af94261afa009b43cd3222a",
  "properties": {
    "friendlyName": "2031fdb43a914114b6ce644eb6fcb5ce",
    "objectType": "AzureBackupDiscreteRecoveryPoint",
    "policyName": "oss-clitest-policy",
    "policyVersion": null,
    "recoveryPointDataStoresDetails": [
      {
        "creationTime": "2021-09-13T15:17:41.209845+00:00",
        "expiryTime": null,
        "id": "beddea84-7b30-42a5-a752-7c75baf96a52",
        "metaData": "{\"objectType\":\"PostgresBackupMetadata\",\"version\":\"1.0\",\"postgresVersion\":\"11\",\"dbName\":\"postgres\",\"serverName\":\"testpostgresql\",\"serverFQDN\":\"testpostgresql.postgres.database.azure.com\",\"usernameUsed\":\"backupadmin@testpostgresql\",\"backupToolPath\":\"postgresql-11.6-1\\\\bin\\\\pg_dump.exe\",\"backupType\":\"Full\",\"backupDumpFormat\":\"CUSTOM\",\"backupToolArgsFormat\":\"--no-acl --no-owner --serializable-deferrable --no-tablespaces --quote-all-identifiers -Fc -d postgres://{0}:{1}@{2}:5432/{3}?sslmode=verify-full&sslrootcert=E:\\\\approot\\\\Plugins\\\\Postgres\\\\..\\\\..\\\\postgres-root.crt\",\"storageUnits\":{\"1\":\"DbBackupDumpData\"},\"streamNamesInFirstStorageUnit\":[\"dbbkpdmpdatastream-1631546260050\"],\"pitId\":\"2031fdb43a914114b6ce644eb6fcb5ce\",\"bytesTransferred\":2063,\"dataSourceSize\":8442527,\"backupToolVersion\":\"11\"}",
        "rehydrationExpiryTime": null,
        "rehydrationStatus": null,
        "state": "COMMITTED",
        "type": "VaultStore",
        "visible": true
      }
    ],
    "recoveryPointId": "9da55e757af94261afa009b43cd3222a",
    "recoveryPointTime": "2021-09-13T15:17:41.209845+00:00",
    "recoveryPointType": "Full",
    "retentionTagName": "default",
    "retentionTagVersion": "637671427933449525"
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
```

Si necesita capturar el punto de recuperación del nivel de archivo, la variable de _tipo_ en _recoveryPointDataStoreDetails_ será _ArchiveStore_.

### <a name="prepare-the-restore-request"></a>Preparación de la solicitud de restauración

Hay varias opciones de restauración para una base de datos de PostgreSQL. Puede restaurar el punto de recuperación como otra base de datos o restaurar como archivos. El punto de recuperación también puede estar en el nivel de archivo.

#### <a name="restore-as-database"></a>Restaurar como base de datos

Construya el identificador de Azure Resource Manager (id. de ARM) de la nueva base de datos de PostgreSQL que se va a crear con el servidor de PostgreSQL de destino, al que se asignaron los permisos tal y como se ha indicado [anteriormente](#set-up-permissions), y el nombre de la base de datos de PostgreSQL necesaria. Por ejemplo, una base de datos de PostgreSQL se puede denominar **emprestored21** en un servidor de PostgreSQL de destino **targetossserver** en el grupo de recursos **targetrg** con una suscripción diferente.

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

Use el comando [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) para preparar la solicitud de restauración con todos los detalles pertinentes.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

Para un punto de recuperación basado en archivo, debe:

1. Rehidratar desde el almacén de datos de archivo al almacén.
1. Modificar el almacén de datos de origen.
1. Agregar otros parámetros para especificar la prioridad de rehidratación.
1. Especificar la duración durante la que se debe conservar el punto de recuperación rehidratado en el almacén de datos.
1. Restaurar como base de datos desde este punto de recuperación.

Use el siguiente comando para preparar la solicitud para todas las operaciones mencionadas anteriormente, a la vez.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### <a name="restore-as-files"></a>Restaurar como archivos

Recupere el URI del contenedor, dentro de la cuenta de almacenamiento a la que se le asignaron permisos como se detalla [arriba](#set-up-permissions). Por ejemplo, un contenedor denominado **testcontainerrestore** en una cuenta de almacenamiento **testossstorageaccount** con una suscripción diferente.

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Use el comando [az dataprotection backup-instance restore initialize-for-data-recovery-as-files](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery_as_files) para preparar la solicitud de restauración con todos los detalles pertinentes.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

Para el punto de recuperación basado en archivo, modifique el almacén de datos de origen y agregue la prioridad de rehidratación y la duración de retención, en días, del punto de recuperación rehidratado, como se menciona a continuación:

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

También puede validar si el archivo JSON creará correctamente nuevos recursos mediante el comando [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore).

### <a name="trigger-the-restore"></a>Desencadenamiento de la restauración

Use el comando [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) para desencadenar la operación de restauración con la solicitud preparada anteriormente.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 --parameters OssRestoreReq.JSON
```

## <a name="tracking-job"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar _Az.ResourceGraph_ para realizar el seguimiento de todos los trabajos en todos los almacenes de Backup. Use el comando [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) para obtener el trabajo pertinente que se encuentra en todos los almacenes de Backup.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la copia de seguridad de Azure PostgreSQL](backup-azure-database-postgresql-overview.md)
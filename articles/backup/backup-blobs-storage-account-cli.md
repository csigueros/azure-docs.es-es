---
title: Copia de seguridad de los blobs de Azure mediante la CLI de Azure
description: Obtenga información sobre cómo realizar una copia de seguridad de los blobs de Azure mediante la CLI de Azure.
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: e8ffd1d369df816bd1020b0b2ec2c1696e6c433e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181513"
---
# <a name="back-up-azure-blobs-in-a-storage-account-using-azure-cli"></a>Copia de seguridad de los blobs de Azure de una cuenta de almacenamiento mediante la CLI de Azure

En este artículo se describe cómo realizar una copia de seguridad de los [blobs de Azure](./blob-backup-overview.md) con la CLI de Azure.

> [!IMPORTANT]
> La compatibilidad con la copia de seguridad y la restauración de blobs de Azure mediante la CLI está en versión preliminar y está disponible como una extensión en la versión Az 2.15.0 y posteriores. La extensión se instala automáticamente al ejecutar los comandos **az dataprotection**. [Obtenga más información](/cli/azure/azure-cli-extensions-overview) sobre las extensiones.

En este artículo, aprenderá a:

- Antes de comenzar

- Creación de un almacén de Backup

- Creación de una directiva de copia de seguridad

- Configuración de la copia de seguridad de un blob de Azure

- Ejecutar un trabajo de copia de seguridad a petición.

Para obtener información sobre la disponibilidad regional de los blobs de Azure, los escenarios admitidos y las limitaciones, consulte la [matriz de compatibilidad](blob-backup-support-matrix.md).

## <a name="before-you-start"></a>Antes de comenzar

Vea los [requisitos previos](./blob-backup-configure-manage.md#before-you-start) y la [matriz de compatibilidad](./blob-backup-support-matrix.md) antes de empezar.

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de copia se seguridad es una entidad de almacenamiento de Azure que contiene los datos de las copias de seguridad de varias cargas de trabajo recientes que admite Azure Backup, como los servidores de Azure Database for PostgreSQL, y los blobs de una cuenta de almacenamiento y los discos de Azure. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de copias de seguridad se basan en el modelo de Azure Resource Manager de Azure, que proporciona características para proteger los datos de las copias de seguridad.

Antes de crear un almacén de copia de seguridad, elija la redundancia de almacenamiento de los datos dentro del almacén. Después, continúe con la creación del almacén de copia de seguridad con esa redundancia de almacenamiento y la ubicación. En este artículo, se creará el almacén de copia de seguridad _TestBkpVault_ en la región _westus_ dentro del grupo de recursos _testBkpVaultRG_. Use el comando [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) para crear un almacén de copia de seguridad. Obtenga más información sobre cómo [crear un almacén de Backup](./backup-vault-overview.md#create-a-backup-vault).

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

> [!IMPORTANT]
> Aunque verá la redundancia del almacenamiento de copia de seguridad del almacén, esta redundancia no se aplica a la copia de seguridad operativa de blobs. Esto se debe a que la copia de seguridad es local por naturaleza y no se almacenan datos en el almacén de copia de seguridad. Aquí, el almacén de copia de seguridad es la entidad de administración que le ayuda a administrar la protección de los blobs en bloques en las cuentas de almacenamiento.

Después de crear un almacén, vamos a crear una directiva de copia de seguridad para proteger los blobs de Azure en una cuenta de almacenamiento.

## <a name="create-a-backup-policy"></a>Creación de una directiva de copia de seguridad

> [!IMPORTANT]
> Lea [esta sección](blob-backup-configure-manage.md#before-you-start) antes de crear la directiva y configurar las copias de seguridad de los blobs de Azure.

Para comprender los componentes internos de una directiva de copia de seguridad para los blobs de Azure, recupere la plantilla de directiva con el comando [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template). Este comando devuelve una plantilla de directiva predeterminada para un tipo de origen de datos determinado. Use esta plantilla para crear una directiva.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob

{
  "datasourceTypes": [
    "Microsoft.Storage/storageAccounts/blobServices"
  ],
  "name": "BlobPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

La plantilla de directiva solo consta de un ciclo de vida (que decide cuándo eliminar, copiar o mover la copia de seguridad). Dado que la copia de seguridad operativa de los blobs es continua por naturaleza, no necesita una programación para realizar copias de seguridad.

```json
"policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
```

> [!NOTE]
> La restauración de duraciones largas puede dar lugar a que las operaciones de restauración tarden más en completarse. Por otra parte, el tiempo que se tarda en restaurar un conjunto de datos se basa en el número de operaciones de escritura y eliminación realizadas durante el período de restauración. Por ejemplo, una cuenta con un millón de objetos con 3000 objetos agregados al día y 1000 objetos eliminados al día requerirá aproximadamente dos horas para restaurar a un período de 30 días determinado del pasado.<br><br>Con esta tasa de cambio, no se recomienda un período de retención ni una restauración que vayan más allá de los 90 días para una cuenta.

Una vez que el objeto JSON de directiva tenga todos los valores necesarios, continúe con la creación de una directiva a partir del objeto de directiva con el comando [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create).

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n BlobBackup-Policy --policy policy.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
    "name": "BlobBackup-Policy",
    "properties": {
      "datasourceTypes": [
        "Microsoft.Storage/storageAccounts/blobServices"
      ],
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P2D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              },
              "targetDataStoreCopySettings": []
            }
          ],
          "name": "Default",
          "objectType": "AzureRetentionRule"
        }
      ]
    },
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
  }
```

## <a name="configure-backup"></a>Configurar la copia de seguridad

Una vez que se hayan creado el almacén y la directiva, hay dos puntos esenciales que deberá tener en cuenta para proteger todos los blobs de Azure de una cuenta de almacenamiento.

### <a name="key-entities-involved"></a>Entidades clave implicadas

#### <a name="storage-account-that-contains-the-blobs-to-be-protected"></a>Cuenta de almacenamiento que contiene los blobs que se protegerán

Recupere el identificador de Azure Resource Manager de la cuenta de almacenamiento que contiene los blobs que se protegerán. Servirá como identificador de la cuenta de almacenamiento. Usaremos un ejemplo de una cuenta de almacenamiento denominada _CLITestSA_, incluida en el grupo de recursos _blobrg_, en una suscripción diferente presente en la región del sudeste asiático.

```azurecli-interactive
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
```

#### <a name="backup-vault"></a>Almacén de Backup

El almacén de Backup requiere permisos en la cuenta de almacenamiento para habilitar las copias de seguridad en los blobs presentes en la cuenta de almacenamiento. Para asignar estos permisos, se usa la identidad del almacén administrada asignada por el sistema.

### <a name="assign-permissions"></a>Asignación de permisos

Debe asignar algunos permisos a través de RBAC al almacén (representado por el MSI del almacén) y la cuenta de almacenamiento correspondiente. Para ello se puede usar el portal o PowerShell. Obtenga más información sobre todos los [permisos relacionados](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts).

### <a name="prepare-the-request"></a>Preparación de la solicitud

Una vez que se han establecido todos los permisos pertinentes, la configuración de la copia de seguridad se realiza en dos pasos. En primer lugar, prepararemos la solicitud correspondiente mediante el almacén, la directiva y la cuenta de almacenamiento pertinentes con el comando [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize). Después, enviaremos la solicitud para proteger el disco con el comando [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create).

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureBlob  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" > backup_instance.json
```

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceSetInfo": null,
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
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
  }
```

> [!IMPORTANT]
> Una vez configurada una cuenta de almacenamiento para la copia de seguridad de blobs, algunas funcionalidades se ven afectadas, como la fuente de cambios y el bloqueo de eliminación. [Más información](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts).

## <a name="next-steps"></a>Pasos siguientes

[Restauración de blobs de Azure mediante la CLI de Azure](restore-blobs-storage-account-cli.md)
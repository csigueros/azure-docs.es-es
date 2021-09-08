---
title: Copia de seguridad de Azure Managed Disks mediante la CLI de Azure
description: Obtenga información sobre cómo hacer una copia de seguridad de Azure Managed Disks mediante la CLI de Azure.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 42bbc54a545cdddf049163b3040d9701f4304a52
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112381212"
---
# <a name="back-up-azure-managed-disks-using-azure-cli"></a>Copia de seguridad de Azure Managed Disks mediante la CLI de Azure

En este artículo, se describe cómo hacer una copia de seguridad de un [disco administrado de Azure](../virtual-machines/managed-disks-overview.md) mediante la CLI de Azure.

> [!IMPORTANT]
> La compatibilidad con la copia de seguridad y la restauración de Azure Managed Disks mediante la CLI está en versión preliminar y está disponible como una extensión en la versión 2.15.0 y posteriores de Az. La extensión se instala automáticamente al ejecutar los comandos **az dataprotection**. [Obtenga más información](/cli/azure/azure-cli-extensions-overview) sobre las extensiones.

En este artículo, aprenderá a:

- Creación de un almacén de Backup

- Creación de una directiva de copia de seguridad

- Configuración de la copia de seguridad de un disco de Azure

- Ejecutar un trabajo de copia de seguridad a petición.

Para obtener información sobre la disponibilidad de la región de la copia de seguridad de Azure Disk, los escenarios admitidos y las limitaciones, consulte la [matriz de compatibilidad](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de copia se seguridad es una entidad de almacenamiento de Azure que almacena los datos de las copias de seguridad de varias cargas de trabajo recientes que admite Azure Backup, como los servidores de Azure Database for PostgreSQL, los blobs de una cuenta de almacenamiento y los discos de Azure. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de copias de seguridad se basan en el modelo de Azure Resource Manager de Azure, que proporciona características para proteger los datos de las copias de seguridad.

Antes de crear un almacén de copia de seguridad, elija la redundancia del almacenamiento de los datos dentro del almacén. Después, continúe con la creación del almacén de copia de seguridad con esa redundancia de almacenamiento y la ubicación. En este artículo, se creará el almacén de copia de seguridad _TestBkpVault_ en la región _westus_ dentro del grupo de recursos _testBkpVaultRG_. Use el comando [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) para crear un almacén de copia de seguridad. Obtenga más información sobre cómo [crear un almacén de Backup](./backup-vault-overview.md#create-a-backup-vault).

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

Después de la creación del almacén, vamos a crear una directiva de copia de seguridad para proteger los discos de Azure.

## <a name="create-a-backup-policy"></a>Creación de una directiva de copia de seguridad

Para comprender los componentes internos de una directiva de copia de seguridad para la copia de seguridad de discos de Azure, recupere la plantilla de directiva con el comando [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template). Este comando devuelve una plantilla de directiva predeterminada para un tipo de origen de datos determinado. Use esta plantilla para crear una directiva.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk

{
  "datasourceTypes": [
    "Microsoft.Compute/disks"
  ],
  "name": "DiskPolicy",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Incremental",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "OperationalStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupHourly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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

La plantilla de directiva consta de un desencadenador (que decide lo que desencadena la copia de seguridad) y un ciclo de vida (que decide cuándo se debe eliminar, copiar o trasladar la copia de seguridad). En la copia de seguridad de discos de Azure, los valores predeterminados del desencadenador son un desencadenador programado para activarse cada 4 horas (PT4H) y conservar cada copia de seguridad durante 7 días.

**Desencadenador programado:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        }

```

**Ciclo de vida de retención predeterminado:**

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ]
```

Azure Disk Backup le ofrece varias copias de seguridad al día. Si necesita realizar copias de seguridad con más frecuencia, elija la frecuencia de copia de seguridad **Cada hora**, ya que le ofrece la posibilidad de realizar copias de seguridad a intervalos de 4, 6, 8 o 12 horas. Las copias de seguridad se programan en función del intervalo de **tiempo** seleccionado.

Por ejemplo, si selecciona **Cada 4 horas**, las copias de seguridad se realizan aproximadamente cada 4 horas, por lo que las mismas se distribuyen equitativamente a lo largo del día. Si es suficiente hacer una copia de seguridad al día, elija la frecuencia de copia de seguridad **Diaria**. En la frecuencia de copia de seguridad diaria, puede especificar la hora del día en la que se realizarán las copias de seguridad.

>[!IMPORTANT]
>La hora del día indica la hora de inicio de la copia de seguridad, no la hora en que se completa la misma.

El tiempo necesario para completar la operación de copia de seguridad depende de varios factores, como el tamaño del disco y la tasa de renovación entre copias de seguridad consecutivas. No obstante, la copia de seguridad de discos de Azure es una opción de copias de seguridad sin agente que usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md), lo que no afecta al rendimiento de la aplicación de producción.

   >[!NOTE]
   >Aunque el almacén seleccionado puede tener la configuración de redundancia global, actualmente, la copia de seguridad de discos de Azure solo admite el almacén de datos de instantáneas. Todas las copias de seguridad se almacenan en un grupo de recursos de la suscripción y no se copian en el almacenamiento de copias de seguridad.

Para más información sobre la creación de directivas, consulte el documento [Creación de una directiva de copia de seguridad](backup-managed-disks.md#create-backup-policy).

Una vez que la plantilla se haya descargado como un archivo JSON, puede editarla para establecer la programación y la retención según sea necesario. A continuación, cree una nueva directiva con el archivo JSON resultante. Si desea editar la frecuencia horaria o el período de retención, use los comandos [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_trigger_set) o [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_retention_rule_set). Una vez que el archivo JSON de la directiva tenga todos los valores necesarios, continúe con la creación de una directiva a partir del objeto de directiva con el comando [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create).

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n mypolicy --policy policy.json

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy",
"name": "mypolicy",
"properties": {
"datasourceTypes": [
"Microsoft.Compute/disks"
],
"objectType": "BackupPolicy",
"policyRules": [
{
"backupParameters": {
"backupType": "Incremental",
"objectType": "AzureBackupParams"
},
"dataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"name": "BackupHourly",
"objectType": "AzureBackupRule",
"trigger": {
"objectType": "ScheduleBasedTriggerContext",
"schedule": {
"repeatingTimeIntervals": [
"R/2020-04-05T13:00:00+00:00/PT4H"
]
},
"taggingCriteria": [
{
"criteria": null,
"isDefault": true,
"tagInfo": {
"eTag": null,
"id": "Default_",
"tagName": "Default"
},
"taggingPriority": 99
}
]
}
},
{
"isDefault": true,
"lifecycles": [
{
"deleteAfter": {
"duration": "P7D",
"objectType": "AbsoluteDeleteOption"
},
"sourceDataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"targetDataStoreCopySettings": null
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

Una vez que se crean el almacén y la directiva, hay tres puntos críticos que debe tener en cuenta para proteger un disco de Azure.

### <a name="key-entities-involved"></a>Entidades clave implicadas

#### <a name="disk-to-be-protected"></a>Disco que se va a proteger

Recupere el identificador de ARM y la ubicación del disco que se va a proteger. Este servirá como identificador del disco. Usaremos el ejemplo de un disco llamado _CLITestDisk_ en el grupo de recursos _diskrg_ de otra suscripción.

```azurecli-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
```

#### <a name="snapshot-resource-group"></a>Grupo de recursos de instantáneas

Las instantáneas de disco se almacenan en un grupo de recursos de la suscripción. Se recomienda crear un grupo de recursos dedicado como un almacén de datos de instantáneas que usará el servicio Azure Backup. Si tiene un grupo de recursos dedicado, podrá restringir los permisos de acceso en el grupo de recursos para asegurar y administrar fácilmente los datos de copia de seguridad. Anote el identificador de ARM del grupo de recursos en el que quiere colocar las instantáneas del disco

```azurecli-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Almacén de Backup

Los almacenes de copia de seguridad requieren permisos en el disco y en el grupo de recursos de instantáneas para poder desencadenar instantáneas y administrar su ciclo de vida. Para asignar estos permisos, se usa la identidad del almacén administrada asignada por el sistema. Use el comando [az dataprotection backup-vault update](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_update) para habilitar la identidad administrada asignada por el sistema para el almacén de Recovery Services.

```azurecli-interactive
az dataprotection backup-vault update -g testBkpVaultRG --vault-name TestBkpVault --type SystemAssigned
```

### <a name="assign-permissions"></a>Asignación de permisos

Debe asignar algunos permisos mediante RBAC al almacén (representado por el valor de MSI del almacén) y al disco o el grupo de recursos del disco pertinentes. Para ello, se pueden usar Azure Portal o la CLI. Todos los permisos relacionados se detallan en los puntos 1, 2 y 3 de [Configuración de la copia de seguridad](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Preparación de la solicitud

Una vez que se han establecido todos los permisos pertinentes, la configuración de la copia de seguridad se realiza en dos pasos. En primer lugar, se prepara la solicitud correspondiente mediante el almacén, la directiva, el disco y el grupo de recursos de instantáneas pertinentes con el comando [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize). El comando initialize devolverá un archivo JSON y, a continuación, tendrá que actualizar el valor del grupo de recursos de instantáneas. Después, enviaremos la solicitud para proteger el disco con el comando [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create).

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureDisk  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk" > backup_instance.json
```

Abra el archivo JSON y edite el **identificador del grupo de recursos de instantáneas** en el elemento ``` resource_group_id ``` de la sección ```data_store_parameters_list```.

```json
{
  "backup_instance_name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "data_source_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "data_source_set_info": null,
    "object_type": "BackupInstance",
    "policy_info": {
      "policy_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policy_parameters": {
        "data_store_parameters_list": [
          {
            "data_store_type": "OperationalStore",
            "object_type": "AzureOperationalStoreParameters",
            "resource_group_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
          }
        ]
      }
    }
  }
}
```

> [!NOTE]
> Los clientes generan el nombre de la instancia de copia de seguridad para que sea un valor único. Se basa en el nombre del origen de datos y en un GUID único. Una vez enumeradas las instancias de copia de seguridad, podrá comprobar el nombre de la instancia de copia de seguridad y el nombre del origen de datos correspondiente.

Use el archivo JSON editado para crear una instancia de copia de seguridad del disco administrado de Azure.

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json


{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "currentProtectionState": "ProtectionConfigured",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resourceLocation": "southeastasia",
      "resourceName": "CLITestDisk",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
    },
    "dataSourceSetInfo": null,
    "friendlyName": "CLITestDisk",
    "objectType": "BackupInstance",
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/sarath-rg"
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
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

Una vez creada la instancia de copia de seguridad, puede desencadenar una copia de seguridad a petición si no desea esperar hasta la hora programada de la directiva.

## <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

Enumere todas las instancias de copia de seguridad de un almacén mediante el comando [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) y, a continuación, recupere la instancia correspondiente mediante el comando [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true). Como alternativa, para escenarios a gran escala, puede enumerar las instancias de copia de seguridad entre almacenes y suscripciones mediante el comando [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
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

Mientras desencadena la copia de seguridad, puede especificar una regla de retención. Para ver las reglas de retención de la directiva, desplácese por el código JSON de la directiva en busca de reglas de retención. En el ejemplo siguiente, se muestra la regla con el nombre _default_ y esta es la que se usará para la copia de seguridad a petición.

```JSON
{
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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
```

Desencadene una copia de seguridad a petición con el comando [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup).

```azurecli-interactive
az dataprotection backup-instance adhoc-backup --name "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166" --rule-name "Default" --resource-group "000pikumar" --vault-name "PratikPrivatePreviewVault1"
```

## <a name="tracking-jobs"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar Az.ResourceGraph para realizar el seguimiento de todos los trabajos en todos los almacenes de copia de seguridad. Use el comando [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) para obtener el trabajo pertinente, que puede encontrarse en cualquier almacén de copia de seguridad.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --status Completed
```

## <a name="next-steps"></a>Pasos siguientes

[Restauración de Azure Managed Disks mediante la CLI de Azure](restore-managed-disks-cli.md)

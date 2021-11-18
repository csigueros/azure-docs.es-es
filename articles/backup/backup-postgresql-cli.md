---
title: Copia de seguridad Azure Database for PostgreSQL con retención a largo plazo mediante la CLI de Azure
description: Aprenda a hacer una copia de seguridad Azure Database for PostgreSQL utilizando la CLI de Azure.
ms.topic: conceptual
ms.date: 10/24/2021
ms.custom: devx-track-azurecli
ms.author: v-amallick
ms.openlocfilehash: 2f032091eb6a0e7807046bb59dfc37709fb4c272
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717779"
---
# <a name="back-up-azure-postgresql-databases-using-azure-cli"></a>Restauración de bases de datos de Azure PostgreSQL mediante CLI de Azure

En este artículo se explica cómo realizar copias de seguridad de una base de datos de [Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) utilizando la CLI de Azure.

En este artículo, aprenderá a:

-  Creación de un almacén de Backup
-  Crear una directiva de copia de seguridad
-  Configurar una copia de seguridad de una base de datos de Azure PostgreSQL
-  Ejecutar un trabajo de copia de seguridad a petición.

Para obtener información sobre los escenarios admitidos y las limitaciones de las bases de datos de informgreSQL, consulte la [matriz de compatibilidad](backup-azure-database-postgresql-overview.md#support-matrix).

## <a name="create-a-backup-vault"></a>Creación de un almacén de Backup

Un almacén de copia de seguridad es una entidad de almacenamiento de Azure que almacena los datos de las copias de seguridad de varias cargas de trabajo recientes que admite Azure Backup, como los servidores de Azure Database for PostgreSQL, los blobs de una cuenta de almacenamiento y Azure Disks. Los almacenes de Backup facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reducen al mínimo la sobrecarga administrativa. Los almacenes de copias de seguridad se basan en el modelo de Azure Resource Manager de Azure, que proporciona características para proteger los datos de las copias de seguridad.

Antes de crear un almacén de copia de seguridad, elija la redundancia del almacenamiento de los datos dentro del almacén. Después, continúe con la creación del almacén de copia de seguridad con esa redundancia de almacenamiento y la ubicación.

En este artículo, se creará el almacén de copia de seguridad _TestBkpVault_ en la región _westus_ dentro del grupo de recursos _testBkpVaultRG_. Use el comando [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) para crear un almacén de copia de seguridad. Obtenga más información sobre cómo [crear un almacén de Backup](./backup-vault-overview.md#create-a-backup-vault).

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

Después de crear el almacén, vamos a crear una directiva de copia de seguridad para proteger las bases de datos de Azure PostgreSQL.

## <a name="create-a-backup-policy"></a>Creación de una directiva de copia de seguridad

### <a name="understanding-postgresql-backup-policy"></a>Información sobre la directiva de copia de seguridad de PostgreSQL

Aunque la copia de seguridad de disco ofrece varias copias de seguridad al día y la copia de seguridad de blobs es una copia de seguridad _continua_ sin desencadenador, la copia de seguridad de PostgreSQL ofrece protección de archivo. Los datos de copia de seguridad que se envían por primera vez al almacén se pueden mover al _nivel de acceso de archivo_ en base a una regla definida o a un _ciclo de vida_. En este contexto, vamos a entender el objeto de directiva de copia de seguridad para PostgreSQL.

- Regla de directiva
   -  Regla de copia de seguridad
      - Parámetro de copia de seguridad
        -  Tipo de copia de seguridad (una copia de seguridad completa de la base de datos en este caso)
        -  Almacén de datos inicial (al que llegarán inicialmente las copias de seguridad)
        -  Desencadenador (cómo se desencadena la copia de seguridad)
           -  Basada en una programación
           -  Criterios de etiquetado predeterminados (una "etiqueta" predeterminada para todas las copias de seguridad programadas. Esta etiqueta vincula las copias de seguridad a la regla de retención).
   -  Regla de retención predeterminada (regla que se aplicará a todas las copias de seguridad, de forma predeterminada, en el almacén de datos inicial)

Por lo tanto, este objeto define qué tipo de copias de seguridad se desencadenan, cómo se desencadenan (a través de una programación), con qué se etiquetan, a dónde llegan (un almacén de datos) y el ciclo de vida de los datos de copia de seguridad en un almacén de datos. El objeto predeterminado de PowerShell para PostgreSQL indica que se desencadena una copia de seguridad *completa* cada semana y que llegarán al almacén, donde se almacenan durante tres meses.

Si desea agregar el nivel de acceso de *archivo* a la directiva, debe decidir cuándo se van a mover los datos del almacén al archivo, cuánto tiempo permanecerán los datos en el archivo y cuál de las copias de seguridad programadas debe etiquetarse como _archivable_. Por lo tanto, tiene que agregar una *regla de retención*, la cual definirá el ciclo de vida de los datos de la copia de seguridad desde el almacén de datos del almacén al almacén de datos de archivo y el tiempo que permanecerán en el *almacén de datos de archivo*. A continuación, debe agregar una *etiqueta* que marque las copias de seguridad programadas como _aptas para archivarse_.

El objeto de PowerShell resultante es el siguiente:

-  Regla de directiva
   -  Regla de copia de seguridad
      - Parámetro de copia de seguridad
        -  Tipo de copia de seguridad (una copia de seguridad completa de la base de datos en este caso)
        -  Almacén de datos inicial (al que llegarán inicialmente las copias de seguridad)
        -  Desencadenador (cómo se desencadena la copia de seguridad)
           -  Basada en una programación
           -  Criterios de etiquetado predeterminados (una "etiqueta" predeterminada para todas las copias de seguridad programadas. Esta etiqueta vincula las copias de seguridad a la regla de retención).
           -  Nuevos criterios de etiquetado para la nueva regla de retención con el mismo nombre "X"
   -  Regla de retención predeterminada (regla que se aplicará a todas las copias de seguridad, de manera predeterminada, en el almacén de datos inicial)
   -  Una nueva regla de retención llamada "X"
      -  Ciclo de vida
         -  Almacén de datos de origen
         -  Eliminación después del período de tiempo en el almacén de datos de origen
         -  Copiar en el almacén de datos de destino

### <a name="retrieve-the-policy-template"></a>Recuperación de la plantilla de directiva

Para comprender los componentes internos de una directiva de copia de seguridad para la copia de seguridad de la base de datos PostgreSQL de Azure, recupere la plantilla de directiva utilizando el comando [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template). Este comando devuelve una plantilla de directiva predeterminada para un tipo de origen de datos determinado. Use esta plantilla para crear una directiva.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDatabaseForPostgreSQL
{
  "datasourceTypes": [
    "Microsoft.DBforPostgreSQL/servers/databases"
  ],
  "name": "OssPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Full",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "VaultStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupWeekly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
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
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}
```

La plantilla de directiva consta de un desencadenador (que decide lo que desencadena la copia de seguridad) y un ciclo de vida (que decide cuándo se debe eliminar, copiar o trasladar la copia de seguridad). En la copia de seguridad de la base de datos de Azure PostgreSQL, el valor predeterminado del desencadenador es un desencadenador semanal programado (1 copia de seguridad cada 7 días) y conservar cada copia de seguridad durante tres meses.

**Desencadenador programado:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

**Ciclo de vida de la regla de retención predeterminada:**

```json
 {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
```

### <a name="modifying-the-policy-template"></a>Modificación de la plantilla de directiva

> [!IMPORTANT]
> En Azure PowerShell, _los objetos_ se pueden usar como ubicaciones de almacenamiento provisional para realizar todas las modificaciones. En CLI de Azure, tenemos que usar archivos, ya que no hay ninguna noción de *objetos*. Cada operación de edición se debe redirigir a un nuevo archivo, donde el contenido se lee desde el archivo de entrada y se vuelve a dirigir al archivo de salida. Más adelante puede cambiar el nombre del archivo según sea necesario mientras se usa en un script.

#### <a name="modify-the-schedule"></a>Modificación de la programación

La plantilla de directiva predeterminada ofrece una copia de seguridad una vez por semana. Puede modificar la programación para que la copia de seguridad se realice varios días a la semana. Para modificar la programación, utilice el comando [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest#az_dataprotection_backup_policy_trigger_set&preserve-view=true).

En el ejemplo siguiente se modifica la copia de seguridad semanal para realizar una copia de seguridad que se realice todos los domingos, miércoles y viernes de cada semana. La matriz de fechas de programación menciona las fechas y los días de la semana de esas fechas se toman como días de la semana. También debe especificar que estas programaciones se repitan cada semana. Por lo tanto, el intervalo de programación es "1" y el tipo de intervalo es "semanal".

```azurecli
az dataprotection backup-policy trigger create-schedule --interval-type Weekly --interval-count 1 --schedule-days 2021-08-15T22:00:00 2021-08-18T22:00:00 2021-08-20T22:00:00
[
  "R/2021-08-15T22:00:00+00:00/P1W",
  "R/2021-08-18T22:00:00+00:00/P1W",
  "R/2021-08-20T22:00:00+00:00/P1W"
]

az dataprotection backup-policy trigger set --policy .\OSSPolicy.json  --schedule R/2021-08-15T22:00:00+00:00/P1W R/2021-08-18T22:00:00+00:00/P1W R/2021-08-20T22:00:00+00:00/P1W > EditedOSSPolicy.json
```

#### <a name="add-a-new-retention-rule"></a>Adición de una nueva regla de retención

Si desea agregar la protección de *archivo*, debe modificar la plantilla de directiva como se indica a continuación.

La plantilla predeterminada tendrá un ciclo de vida para el almacén de datos inicial bajo la regla de retención predeterminada. En este escenario, la regla indica que se eliminarán los datos de copia de seguridad después de tres meses. Debe agregar una nueva regla de retención que defina cuándo se *moverán* los datos al *almacén de datos de archivo*, es decir, los datos de copia de seguridad se copian primero en el almacén de datos de archivo y, a continuación, se eliminan en el almacén de datos del almacén. Además, la regla debe definir durante cuánto tiempo se mantendrán los datos en el *almacén de datos de archivo*. Use el comando [az dataprotection backup-policy retention-rule create-lifecycle](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_create_lifecycle&preserve-view=true) para crear nuevos ciclos de vida y use el comando[az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_set&preserve-view=true) para asociarlos a las nuevas reglas o a las reglas existentes.

En el ejemplo siguiente se crea una nueva regla de retención denominada *Mensual*, donde la primera copia de seguridad correcta de cada mes se debe conservar en el almacén durante seis meses, se mueve al nivel de archivo y se mantiene en el nivel de archivo durante 24 meses.

```azurecli
az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 6 --retention-duration-type Months --source-datastore VaultStore --target-datastore ArchiveStore --copy-option CopyOnExpiryOption > VaultToArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 24 --retention-duration-type Months -source-datastore ArchiveStore > OnArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule set --lifecycles .\VaultToArchiveLifeCycle.JSON .\OnArchiveLifeCycle.JSON --name Monthly --policy .\EditedOSSPolicy.JSON > AddedRetentionRulePolicy.JSON
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>Agregue una etiqueta y los criterios pertinentes

Una vez creada una regla de retención, debe crear una *etiqueta* correspondiente en la propiedad *Desencadenador* de la directiva de copia de seguridad. Utilice el comando [az dataprotection backup-policy tag create-absolute-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_absolute_criteria&preserve-view=true) para crear un nuevo criterio de etiquetado y use el comando [az dataprotection backup-policy tag set](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_set&preserve-view=true) para actualizar la etiqueta existente o crear una nueva etiqueta.

En el ejemplo siguiente se crea una nueva *etiqueta* junto con los criterios (que es la primera copia de seguridad correcta del mes) con el mismo nombre que la regla de retención correspondiente que se va a aplicar.

En este ejemplo, el criterio de la etiqueta debería denominarse *Mensual*.

```azurecli
az dataprotection backup-policy tag create-absolute-criteria --absolute-criteria FirstOfMonth > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

Suponga que la programación es de varias copias de seguridad por semana (todos los domingos, miércoles y jueves, como se especifica en el ejemplo anterior) y desea archivar las copias de seguridad de domingo y viernes, los criterios de etiquetado se pueden cambiar de la siguiente manera, utilizando el comando [az dataprotection backup-policy tag create-generic-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_generic_criteria&preserve-view=true).

```azurecli
az dataprotection backup-policy tag create-generic-criteria --days-of-week Sunday Friday > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

### <a name="create-a-new-postgresql-backup-policy"></a>Creación de una nueva directiva de copia de seguridad de PostgreSQL

Una vez modificada la plantilla según los requisitos, use el comando [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest#az_dataprotection_backup_policy_create&preserve-view=true) para crear una directiva con la plantilla modificada.

```azurecli
az dataprotection backup-policy create --backup-policy-name FinalOSSPolicy --policy AddedRetentionRuleAndTag.JSON --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="configure-backup"></a>Configuración de la copia de seguridad

Una vez que se crean el almacén y la directiva, hay tres puntos críticos que debe tener en cuenta para proteger una base de datos de Azure PostgreSQL.

### <a name="key-entities-involved"></a>Entidades clave implicadas

#### <a name="postgresql-database-to-be-protected"></a>Base de datos PostgreSQL que se va a proteger

Obtenga el identificador de Azure Resource Manager (id. de ARM) de la base de datos de PostgreSQL que se va a proteger. Esto actúa como identificador de la base de datos. Usaremos un ejemplo de una base de datos denominada **empdb11** en un servidor de PostgreSQL **testpostgresql**, que está presente en el grupo de recursos **ossdemoRG** bajo una suscripción diferente.

En el ejemplo siguiente se usa bash.

```azurecli
ossId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

El servicio Azure Backup no almacena el nombre de usuario y la contraseña para conectarse a la base de datos de PostgreSQL. En su lugar, el administrador de copia de seguridad incluye las *claves* en el almacén de claves y, a continuación, el servicio de copia de seguridad accederá al almacén de claves, leerá las claves y accederá a la base de datos. Anote el identificador secreto de la clave pertinente.

En el ejemplo siguiente se usa bash.

```azure-cli
keyURI="https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Almacén de Backup

El almacén de copias de seguridad tiene que conectarse al servidor de PostgreSQL y, a continuación, acceder a la base de datos a través de las claves presentes en el almacén de claves. Por lo tanto, requiere acceso al servidor de PostgreSQL y al almacén de claves. Se concede acceso al MSI del almacén de Backup.

[Obtenga información sobre los permisos adecuados](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup) que debe conceder para realizar una copia de seguridad del MSI del almacén en el servidor de PostgreSQL y el almacén de claves de Azure, donde se almacenan las claves de la base de datos.

### <a name="prepare-the-request"></a>Preparación de la solicitud

Una vez que se han establecido todos los permisos pertinentes, la configuración de la copia de seguridad se realiza en dos pasos.

1. En primer lugar, prepararemos la solicitud correspondiente mediante el almacén, la directiva y la bas de datos de PostgreSQL pertinentes con el comando [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_initialize&preserve-view=true).
1. Después, enviaremos la solicitud para proteger la base de datos con el comando [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_create&preserve-view=true).

```azurecli
az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQL -l <vault-location> --policy-id <policy_arm_id>  --secret-store-type AzureKeyVault --secret-store-uri $keyURI > OSSBkpInstance.JSON

az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
```

## <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

Tiene que especificar una regla de retención mientras desencadena la copia de seguridad. Para ver las reglas de retención de la directiva, desplácese por el archivo JSON de la directiva para las reglas de retención. En el ejemplo siguiente, hay dos reglas de retención con los nombres **Predeterminado** y **Mensual**. Usaremos la regla **Mensual** para la copia de seguridad a petición.

```azurecli
az dataprotection backup-policy show  -g ossdemorg --vault-name ossdemovault-1 --subscription e3d2d341-4ddb-4c5d-9121-69b7e719485e --name osspol5
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/resourceGroups/ossdemorg/providers/Microsoft.DataProtection/backupVaults/ossdemovault-1/backupPolicies/osspol5",
  "name": "osspol5",
  "properties": {
    "datasourceTypes": [
      "Microsoft.DBforPostgreSQL/servers/databases"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "backupParameters": {
          "backupType": "Full",
          "objectType": "AzureBackupParams"
        },
        "dataStore": {
          "dataStoreType": "VaultStore",
          "objectType": "DataStoreInfoBase"
        },
        "name": "BackupWeekly",
        "objectType": "AzureBackupRule",
        "trigger": {
          "objectType": "ScheduleBasedTriggerContext",
          "schedule": {
            "repeatingTimeIntervals": [
              "R/2020-04-04T20:00:00+00:00/P1W",
              "R/2020-04-01T20:00:00+00:00/P1W"
            ],
            "timeZone": "UTC"
          },
          "taggingCriteria": [
            {
              "criteria": [
                {
                  "absoluteCriteria": [
                    "FirstOfMonth"
                  ],
                  "daysOfMonth": null,
                  "daysOfTheWeek": null,
                  "monthsOfYear": null,
                  "objectType": "ScheduleBasedBackupCriteria",
                  "scheduleTimes": null,
                  "weeksOfTheMonth": null
                }
              ],
              "isDefault": false,
              "tagInfo": {
                "eTag": null,
                "id": "Monthly_",
                "tagName": "Monthly"
              },
              "taggingPriority": 15
            },
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
        "isDefault": false,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P10Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
              "objectType": "DataStoreInfoBase"
            },
            "targetDataStoreCopySettings": []
          }
        ],
        "name": "Monthly",
        "objectType": "AzureRetentionRule"
      },
      {
        "isDefault": true,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P1Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
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
  "resourceGroup": "ossdemorg",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="trigger-an-on-demand-backup-using-command"></a>Desencadenamiento de una copia de seguridad a petición mediante un comando

Desencadene una copia de seguridad a petición con el comando [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup).

```azurecli
az dataprotection backup-instance adhoc-backup --name "ossrg-empdb11" --rule-name "Monthly" --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="track-jobs"></a>Seguimiento de trabajos

Realice el seguimiento de todos los trabajos mediante el comando [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Puede enumerar todos los trabajos y capturar un detalle de trabajo determinado.

También puede usar _Az.ResourceGraph_ para realizar el seguimiento de todos los trabajos en todos los almacenes de copias de seguridad. Use el comando [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) para buscar los trabajos relevantes en los almacenes de copias de seguridad.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --status Completed
```

## <a name="next-steps"></a>Pasos siguientes

- [Restauración de una base de datos de Azure PostgreSQL mediante CLI de Azure](restore-postgresql-database-cli.md)
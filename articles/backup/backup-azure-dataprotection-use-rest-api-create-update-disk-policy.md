---
title: Creación de directivas de copia de seguridad para discos mediante la API de REST de protección de datos
description: En este artículo, aprenderá a crear y administrar directivas de copia de seguridad para discos mediante la API de REST.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: ecc107c0-311c-42d0-a094-654d7ee30443
ms.openlocfilehash: 0df0b23d921076959718e79d5674aa8d561639e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620870"
---
# <a name="create-azure-data-protection-backup-policies-for-disks-using-rest-api"></a>Creación de directivas de copia de seguridad de protección de datos de Azure para discos mediante la API de REST

Normalmente, una directiva de copia de seguridad rige la retención y la programación de las copias de seguridad. Azure Disk Backup le ofrece varias copias de seguridad al día.

Puede reutilizar la directiva de copia de seguridad para configurar la copia de seguridad de varios discos de Azure en un almacén o [crear una directiva de copia de seguridad para un almacén de Azure Recovery Services mediante la API de REST](/rest/api/dataprotection/backup-policies/create-or-update).

Para crear una directiva de copia de seguridad de discos, realice las siguientes acciones:

## <a name="create-a-policy"></a>Crear una directiva

>[!IMPORTANT]
>Actualmente, no se admite la actualización o modificación de una directiva existente. Una alternativa es crear una directiva con los detalles necesarios y asignarla a la instancia de copia de seguridad correspondiente.

Para crear una directiva de Azure Backup, use la siguiente operación *PUT*:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

Los valores de `{policyName}` y `{vaultName}` se proporcionan en el URI. Se proporciona información adicional en el cuerpo de la solicitud.

## <a name="create-the-request-body"></a>Creación del cuerpo de la solicitud

Por ejemplo, para crear una directiva para la copia de seguridad en disco, el cuerpo de la solicitud necesita los siguientes componentes:

|Nombre  |Obligatorio  |Tipo  |Descripción  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | Propiedades BaseBackupPolicyResource        |

Para obtener una lista completa de las definiciones en el cuerpo de la solicitud, consulte el [documento de la directiva de copia de seguridad de API REST](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

La directiva indica:

- Desencadenador programado cada 4 horas (PT4H). Las copias de seguridad se realizan aproximadamente cada 4 horas, por lo que estas se distribuyen equitativamente a lo largo del día.
- Puede elegir que el intervalo de desencadenador cada 4, 6, 8 o 12 horas. Para programar una copia de seguridad una vez al día, use **P1D**. Las copias de seguridad se desencadenan una vez al día a la hora estipulada.
- El almacén de datos es el _almacén operativo_, ya que las copias de seguridad son locales y no se almacenan datos en el almacén de Backup. En el almacén operativo, cada instancia de copia de seguridad se almacena durante siete días (P7D).

```json
{
"properties": {
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
}
```

>[!IMPORTANT]
>Los formatos de fecha y hora admiten solo el valor DateTime. No admiten solo el valor Time. Recuerde que la hora del día indica la hora de inicio de la copia de seguridad, no la hora en que se completa la misma.

El tiempo necesario para completar la operación de copia de seguridad depende de varios factores, como el tamaño del disco y la tasa de renovación entre copias de seguridad consecutivas. No obstante, la copia de seguridad de discos de Azure es una opción de copias de seguridad sin agente que usa [instantáneas incrementales](../virtual-machines/disks-incremental-snapshots.md), lo que no afecta al rendimiento de la aplicación de producción.

Para más información sobre la creación de directivas, consulte el documento [Creación de una directiva de copia de seguridad](backup-managed-disks.md#create-backup-policy).

## <a name="responses"></a>Respuestas

La creación o actualización de la directiva de copia de seguridad es una operación sincrónica y devuelve Correcta una vez que la operación se realiza correctamente.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  Aceptar       |

### <a name="example-responses"></a>Respuestas de ejemplo

Una vez completada la operación, devuelve 200 (OK) con el contenido de la directiva en el cuerpo de respuesta.

```json
{
    "id": "/subscriptions/73307177-bb00-4801-bd11-894b2f2d5162/resourceGroups/RG-BV/providers/Microsoft.DataProtection/backupVaults/BV-JPE-GRS/backupPolicies/DiskBackupPolicy-03",
    "name": "DiskBackupPolicy-03",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Incremental",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-07-01T19:00:00+00:00/P1D"
                        ],
                      },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "OperationalStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupDaily",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P7D"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "OperationalStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": true,
                "name": "Default",
                "objectType": "AzureRetentionRule"
            }
        ],
        "datasourceTypes": [
            "Microsoft.Compute/disks"
        ],
        "objectType": "BackupPolicy"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

[Habilitación de la protección para Azure Disks](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Para obtener más información sobre las API de REST de Azure Backup, consulte los siguientes artículos:

- [API de REST de Azure Data Protection](/rest/api/dataprotection/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)

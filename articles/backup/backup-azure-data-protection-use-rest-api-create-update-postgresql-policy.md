---
title: Creación de directivas de copia de seguridad para bases de datos de Azure PostgreSQL mediante la API REST de protección de datos
description: En este artículo, obtendrá información sobre cómo crear y administrar directivas de copia de seguridad para bases de datos de Azure PostgreSQL mediante la API REST.
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: v-amallick
ms.assetid: 759ee63f-148b-464c-bfc4-c9e640b7da6b
ms.openlocfilehash: 2caa9d89ae69d9710f42dc4b3c958d6fa8873098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093183"
---
# <a name="create-azure-data-protection-backup-policies-for-azure-postgresql-databases-using-rest-api"></a>Creación de directivas de copia de seguridad de protección de datos de Azure para bases de datos de Azure PostgreSQL mediante la API REST

Normalmente, una directiva de copia de seguridad rige la retención y la programación de las copias de seguridad. La copia de seguridad de bases de datos de Azure PostgreSQL ofrece retención a largo plazo y admite una copia de seguridad al día.

Puede reutilizar una directiva de copia de seguridad existente para configurar la copia de seguridad de las bases de datos de PostgreSQL en un almacén o [crear una directiva de copia de seguridad para un almacén de Azure Recovery Services mediante la API REST](/rest/api/dataprotection/backup-policies/create-or-update).

## <a name="understanding-postgresql-backup-policy"></a>Información sobre la directiva de copia de seguridad de PostgreSQL

Aunque la copia de seguridad de disco ofrece varias copias de seguridad al día y la copia de seguridad de blobs es una copia de seguridad *continua* sin desencadenador, la copia de seguridad de PostgreSQL ofrece protección de archivo. Los datos de copia de seguridad que se envían por primera vez al almacén se pueden mover al *nivel de acceso de archivo* según una regla definida o un *ciclo de vida*. En este contexto, vamos a conocer el objeto de directiva de copia de seguridad para PostgreSQL.

-  Regla de directiva
   -  Regla de copia de seguridad
      -  Parámetro de copia de seguridad
         -  Tipo de copia de seguridad (una copia de seguridad completa de la base de datos en este caso)
         -  Almacén de datos inicial (al que llegarán inicialmente las copias de seguridad)
         -  Desencadenador (cómo se desencadena la copia de seguridad)
            -  Basada en una programación
            -  Criterios de etiquetado predeterminados (una "etiqueta" predeterminada para todas las copias de seguridad programadas. Esta etiqueta vincula las copias de seguridad a la regla de retención).
   -  Regla de retención predeterminada (regla que se aplicará a todas las copias de seguridad, de manera predeterminada, en el almacén de datos inicial)

Por lo tanto, este objeto define qué tipo de copias de seguridad se desencadenan, cómo se desencadenan (mediante una programación), con qué se etiquetan, a dónde llegan (un almacén de datos) y el ciclo de vida de los datos de copia de seguridad de un almacén de datos. El objeto predeterminado de PowerShell para PostgreSQL indica que se desencadenará una copia de seguridad *completa* cada semana y que llegará al almacén, donde se almacena durante tres meses.

Si desea agregar el nivel de acceso de *archivo* a la directiva, debe decidir cuándo se van a mover los datos del almacén al nivel de archivo, cuánto tiempo permanecerán los datos en el nivel de archivo y cuál de las copias de seguridad programadas debe etiquetarse como *archivable*. Por lo tanto, tiene que agregar una *regla de retención*, la cual definirá el ciclo de vida de los datos de la copia de seguridad desde el almacén de datos del almacén al almacén de datos de archivo y el tiempo que permanecerán en el almacén de datos de *archivo*. A continuación, debe agregar una etiqueta que marque las copias de seguridad programadas como _aptas para archivarse_. El objeto de PowerShell resultante es el siguiente:

-  Regla de directiva
   -  Regla de copia de seguridad
      -  Parámetro de copia de seguridad
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
         - Copiar en el almacén de datos de destino

Para crear una directiva de copia de seguridad de bases de datos de PostgreSQL, realice las siguientes acciones:

## <a name="create-a-policy"></a>Crear una directiva

>[!IMPORTANT]
>Actualmente, no se admite la actualización o modificación de una directiva existente. Una alternativa es crear una directiva con los detalles necesarios y asignarla a la instancia de copia de seguridad correspondiente.

Para crear una directiva de Azure Backup, use la siguiente operación *PUT*:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

Los valores de `{policyName}` y `{vaultName}` se proporcionan en el URI. Se proporciona información adicional en el cuerpo de la solicitud.

## <a name="create-the-request-body"></a>Creación del cuerpo de la solicitud

Por ejemplo, para crear una directiva para la copia de seguridad de PostgreSQL, el cuerpo de la solicitud necesita los siguientes componentes:

|Nombre  |Obligatorio  |Tipo  |Descripción  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | Propiedades BaseBackupPolicyResource        |

Para obtener una lista completa de las definiciones del cuerpo de la solicitud, consulte el [documento de la API REST de directivas de copia de seguridad](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

La directiva indica:

- Desencadenador programado para una copia de seguridad semanal y se elige la hora de inicio. (Hora + P1W).
- El almacén de datos _es el almacén_, ya que las copias de seguridad se transfieren directamente al almacén.
- Las copias de seguridad se conservan en el almacén durante tres meses (P3M).

```json
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

>[!IMPORTANT]
>Los formatos de fecha y hora admiten solo el valor _DateTime_; no se admite utilizar solo un valor de tipo _Time_. Recuerde que la hora del día indica la hora de inicio de la copia de seguridad, no la hora en que se completa la misma.

Vamos a actualizar el código **JSON** anterior con dos cambios: copias de seguridad en varios días de la semana y adición de un almacén de datos de *archivo* para la retención a largo plazo de copias de seguridad de bases de datos de PostgreSQL.

En el ejemplo siguiente, se modifica la copia de seguridad semanal para realizar una copia de seguridad todos los domingos, miércoles y viernes de cada semana. La matriz de fechas de programación menciona las fechas y los días de la semana de esas fechas se toman como días de la semana. También debe especificar que estas programaciones se repitan cada semana. Por lo tanto, el intervalo de programación es *1* y el tipo de intervalo es *semanal*.

**Desencadenador programado:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

Si desea agregar la protección de *nivel de archivo*, debe modificar el código JSON de la directiva como se indica a continuación:

El código JSON anterior tiene un ciclo de vida para el almacén de datos inicial según la regla de retención predeterminada. En este escenario, la regla indica que se eliminarán los datos de copia de seguridad después de tres meses. Debe agregar una nueva regla de retención que defina cuándo se *moverán* los datos al almacén de datos de *archivo*, es decir, los datos de copia de seguridad se copian primero en el almacén de datos de archivo y, a continuación, se eliminan en el almacén de datos del almacén. Además, la regla debe definir las duraciones que se van a mantener los datos en el almacén de datos del nivel de archivo*. Vamos a llamar a esta nueva regla **Mensual** y la regla define que las copias de seguridad se deben conservar en el almacén de datos del almacén durante 6 meses y, a continuación, copiarse en el almacén de datos de archivo. A continuación, se *eliminan* del almacén de datos del almacén, se conservan los datos durante 24 meses en el almacén de datos de archivo y, a continuación, se eliminan los datos del almacén de datos de archivo.

**Ciclo de vida de retención:**

```json
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
```

Cada vez que agregue una regla de retención, deberá agregar la etiqueta correspondiente en la propiedad *Trigger* de la directiva. En el ejemplo siguiente, se crea una nueva *etiqueta* junto con los criterios (que son la primera copia de seguridad correcta del mes) con el mismo nombre exacto que la regla de retención correspondiente que se va a aplicar. 

En este ejemplo, el criterio de la etiqueta se debería llamar *Mensual*.

**Criterios de etiquetado:**

```json
{
  "criteria": [
    {
      "absoluteCriteria": [
        "FirstOfMonth"
      ],
      "objectType": "ScheduleBasedBackupCriteria"
    }
  ],
  "isDefault": false,
  "tagInfo": {
    "tagName": "Monthly"
  },
  "taggingPriority": 15
}
```

Después de incluir todos los cambios, el código JSON de la directiva tendrá un aspecto similar al siguiente:

```json
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
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
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
          },
          {
            "criteria": [
              {
                "absoluteCriteria": [
                  "FirstOfMonth"
                ],
                "objectType": "ScheduleBasedBackupCriteria"
              }
            ],
            "isDefault": false,
            "tagInfo": {
              "tagName": "Monthly"
            },
            "taggingPriority": 15
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

Para más información sobre la creación de directivas, consulte el documento [Copia de seguridad de Azure Database for PostgreSQL con retención a largo plazo (versión preliminar)](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="responses"></a>Respuestas

La creación o actualización de la directiva de copia de seguridad es una operación sincrónica y devuelve _Correcto_ una vez que la operación se realiza correctamente.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  Aceptar       |

### <a name="example-responses"></a>Respuestas de ejemplo

Una vez completada la operación, devuelve 200 (OK) con el contenido de la directiva en el cuerpo de respuesta.

```json
{
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Full",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-08-15T22:00:00+00:00/P1W",
                            "R/2021-08-18T22:00:00+00:00/P1W",
                            "R/2021-08-20T22:00:00+00:00/P1W"
                        ],
                        "timeZone": "UTC"
                    },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Monthly",
                                "id": "Monthly_"
                            },
                            "taggingPriority": 15,
                            "isDefault": false,
                            "criteria": [
                                {
                                    "absoluteCriteria": [
                                        "FirstOfMonth"
                                    ],
                                    "objectType": "ScheduleBasedBackupCriteria"
                                }
                            ]
                        },
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
                    "dataStoreType": "VaultStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupWeekly",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P6M"
                        },
                        "targetDataStoreCopySettings": [
                            {
                                "dataStore": {
                                    "dataStoreType": "ArchiveStore",
                                    "objectType": "DataStoreInfoBase"
                                },
                                "copyAfter": {
                                    "objectType": "CopyOnExpiryOption"
                                }
                            }
                        ],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    },
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P24M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "ArchiveStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": false,
                "name": "Monthly",
                "objectType": "AzureRetentionRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P3M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
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
            "Microsoft.DBforPostgreSQL/servers/databases"
        ],
        "objectType": "BackupPolicy"
    },
    "id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/DebRG1/providers/Microsoft.DataProtection/backupVaults/DebBackupVault/backupPolicies/OssPolicy1",
    "name": "OssPolicy1",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="next-steps"></a>Pasos siguientes

[Habilitación de la protección para Azure Disks](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Para obtener más información sobre las API de REST de Azure Backup, consulte los siguientes artículos:

- [API de REST de Azure Data Protection](/rest/api/dataprotection/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)

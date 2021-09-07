---
title: Creación de directivas de copia de seguridad para blobs mediante la API de REST de protección de datos
description: En este artículo, aprenderá a crear y administrar directivas de copia de seguridad para blobs mediante la API de REST.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 472d6a4f-7914-454b-b8e4-062e8b556de3
ms.openlocfilehash: 96c76eb592b0fb7b94aa8da6c4f975878dc8c913
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471568"
---
# <a name="create-azure-data-protection-backup-policies-for-blobs-using-rest-api"></a>Creación de directivas de copia de seguridad de protección de datos de Azure para blobs mediante la API de REST

> [!IMPORTANT]
> Lea [esta sección](blob-backup-configure-manage.md#before-you-start) antes de continuar para crear la directiva y configurar las copias de seguridad de los blobs de Azure.

Normalmente, una directiva de copia de seguridad rige la retención y la programación de las copias de seguridad. Dado que la copia de seguridad operativa de los blobs es continua por naturaleza, no necesita una programación para realizar copias de seguridad. La directiva es básicamente necesaria para especificar el período de retención. Puede reutilizar la directiva de copia de seguridad para configurar la copia de seguridad de varias cuentas de almacenamiento en un almacén.

>[!NOTE]
>La restauración de duraciones largas puede dar lugar a que las operaciones de restauración tarden más en completarse. Además, el tiempo que se tarda en restaurar un conjunto de datos se basa en el número de operaciones de escritura y eliminación realizadas durante el período de restauración. Por ejemplo, una cuenta con un millón de objetos con 3000 objetos agregados al día y 1000 objetos eliminados al día requerirá aproximadamente dos horas para restaurar a un período de 30 días determinado del pasado. No se recomendarían un período de retención ni una restauración superiores a 90 días en el pasado para una cuenta con esta tasa de cambio.

Los pasos para crear una directiva de copia de seguridad para un almacén de Azure Recovery Services se describen en el [documento de la API de REST](/rest/api/dataprotection/backup-policies/create-or-update) de directivas. Vamos a usar este documento como referencia para crear una directiva para blobs en una cuenta de almacenamiento.

## <a name="create-a-policy"></a>Crear una directiva

> [!IMPORTANT]
> Actualmente, no se admite la actualización o modificación de una directiva existente. Una alternativa es crear una directiva con los detalles necesarios y asignarla a la instancia de copia de seguridad correspondiente.

Para crear una directiva de Azure Backup, use la siguiente operación *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

Los valores de `{policyName}` y `{vaultName}` se proporcionan en el URI. Se proporciona información adicional en el cuerpo de la solicitud.

## <a name="create-the-request-body"></a>Creación del cuerpo de la solicitud

Por ejemplo, para crear una directiva para la copia de seguridad de blobs, los componentes del cuerpo de la solicitud son los siguientes.

|Nombre  |Obligatorio  |Tipo  |Descripción  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | Propiedades BaseBackupPolicyResource        |

Para obtener una lista completa de las definiciones en el cuerpo de la solicitud, consulte el [documento de la directiva de copia de seguridad de API REST](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

El cuerpo de la solicitud siguiente define una directiva de copia de seguridad para las copias de seguridad de blobs.

La directiva indica:

- El período de retención es de 30 días.
- El almacén de datos es el "almacén operativo", ya que las copias de seguridad son locales y no se almacenan datos en el almacén de Backup.

```json
{
  "properties": {
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "name": "Default",
        "objectType": "AzureRetentionRule",
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
        ]
      }
    ]
  }
}
```

> [!IMPORTANT]
> Los formatos de fecha y hora admiten solo DateTime. No admiten el formato de hora por separado.

## <a name="responses"></a>Respuestas

La creación o actualización de la directiva de copia de seguridad es una operación sincrónica y devuelve Correcta una vez que la operación se realiza correctamente.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  Aceptar       |

### <a name="example-responses"></a>Respuestas de ejemplo

Una vez completada la operación, devuelve 200 (OK) con el contenido de la directiva en el cuerpo de respuesta.

```json
{
  "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups//TestBkpVaultRG/providers/Microsoft.RecoveryServices/vaults/testBkpVault/backupPolicies/TestBlobPolicy",
  "name": "TestBlobPolicy",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
  "properties": {
    "policyRules": [
      {
        "lifecycles": [
          {
            "deleteAfter": {
              "objectType": "AbsoluteDeleteOption",
              "duration": "P30D"
            },
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
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Habilite la protección de blobs en una cuenta de almacenamiento.

Para más información sobre las API REST de Azure Backup, consulte los siguientes documentos:

- [API de REST de Azure Data Protection](/rest/api/dataprotection/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)

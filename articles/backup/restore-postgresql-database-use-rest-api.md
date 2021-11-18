---
title: Restauración de bases de datos de Azure PostgreSQL mediante la API REST de Azure Data Protection
description: Aprenda a restaurar bases de datos de Azure PostGreSQL mediante la API REST de Azure Data Protection.
ms.topic: conceptual
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 0d30731af6366a859b86341b318b1f8d9b5663a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714207"
---
# <a name="restore-azure-postgresql-databases-using-azure-data-protection-rest-api"></a>Restauración de bases de datos de Azure PostgreSQL mediante la API REST de Azure Data Protection

En este artículo se explica cómo restaurar [bases de datos de Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) en un servidor de Azure PostgreSQL del que se ha creado una copia de seguridad con Azure Backup.

Al ser una base de datos PaaS, no se admite la opción Recuperación de la ubicación original (OLR) para restaurar mediante la sustitución de la base de datos existente (desde la que se realizaron las copias de seguridad). Puede restaurar desde un punto de recuperación para crear una nueva base de datos en el mismo servidor de Azure PostgreSQL o en cualquier otro servidor de PostgreSQL. Esto se denomina Recuperación de ubicación alternativa (ALR) y ayuda a mantener tanto la base de datos de origen como la base de datos restaurada (nueva).

En este artículo, aprenderá a:

- Restaurar para crear una nueva base de datos PostgreSQL

- Realizar el seguimiento del estado de la operación de restauración.

## <a name="prerequisites"></a>Requisitos previos

- [Creación de un almacén de Backup](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Creación de una directiva de copia de seguridad de base de datos de PostgreSQL](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

- [Configuración de una copia de seguridad de base de datos de PostgreSQL](backup-azure-data-protection-use-rest-api-backup-postgresql.md)

Haremos referencia a un almacén de copia de seguridad existente _TestBkpVault_ en el grupo de recursos _testBkpVaultRG_ en los ejemplos.

## <a name="restore-a-backed-up-postgresql-database"></a>Restauración de una base de datos de PostgreSQL de la que se ha hecho una copia de seguridad

### <a name="set-up-permissions"></a>Configuración de permisos

El almacén de Backup usa la identidad administrada para obtener acceso a otros recursos de Azure. Para restaurar desde la copia de seguridad, la identidad administrada del almacén de Backup requiere un conjunto de permisos en el servidor de Azure PostgreSQL en el que se debe restaurar la base de datos.

Para asignar los permisos pertinentes para la identidad administrada asignada por el sistema del almacén en el servidor PostgreSQL de destino, consulte el [conjunto de permisos necesarios para realizar una copia de seguridad de la base de datos de Azure PostgreSQL](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore).

Para restaurar el punto de recuperación como archivos en una cuenta de almacenamiento, la identidad administrada asignada por el sistema del almacén de Backup necesita acceso a la cuenta de almacenamiento de destino, como se menciona [aquí](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account).

### <a name="fetching-the-relevant-recovery-point"></a>Captura del punto de recuperación pertinente

Para enumerar todos los puntos de recuperación disponibles para una instancia de copia de seguridad, use la API [list recovery points](/rest/api/dataprotection/recovery-points/list).

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-07-01
```

En el ejemplo, esta API se traduce en:

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints?api-version=2021-07-01
```

#### <a name="responses-for-list-of-recovery-points"></a>Respuestas para la lista de puntos de recuperación

Una vez que se envía la solicitud *GET*, esta devuelve la respuesta 200 (OK) y la lista de todos los puntos de recuperación discretos con todos los detalles pertinentes.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   Aceptar      |
|Otros códigos de estado     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     Respuesta que describe el motivo del error de la operación.    |

##### <a name="example-response-for-list-of-recovery-points"></a>Respuesta de ejemplo de la lista de puntos de recuperación

```http
HTTP/1.1 200 OK
Content-Length: 53396
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 41f7ef85-f31e-4db7-87ef-115e3ca65b93
x-ms-routing-request-id: SOUTHINDIA:20211022T200018Z:ba3bc1ce-c081-4895-a292-beeeb6eb22cc
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:00:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
        "recoveryPointTime": "2021-10-21T16:31:16.8316716Z",
        "recoveryPointType": "Full",
        "friendlyName": "794ead7c7661410da03997d210d469e7",
        "recoveryPointDataStoresDetails": [
          {
            "id": "9ea7eaf4-eeb8-4c8f-90a7-7f04b60bf075",
            "type": "VaultStore",
            "creationTime": "2021-10-21T16:31:16.8316716Z",
            "expiryTime": "2022-10-21T16:31:16.8316716Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637212748405148394",
        "policyName": "osspol3",
        "policyVersion": null
      },
.
.
.
.
```

Para obtener el punto de recuperación del nivel de archivo, modifique la variable _type_ en _recoveryPointDataStoreDetails_ como _ArchiveStore_.

Seleccione los puntos de recuperación pertinentes de la lista anterior y continúe con la preparación de la solicitud de restauración. Se va a elegir un punto de recuperación denominado _794ead7c7661410da03997d210d469e7_ de la lista anterior para su restauración.

### <a name="prepare-the-restore-request"></a>Preparación de la solicitud de restauración

Hay varias opciones de restauración para una base de datos de PostgreSQL. Puede restaurar el punto de recuperación como otra base de datos o restaurar como archivos. El punto de recuperación también puede estar en el nivel de archivo.

#### <a name="restore-as-database"></a>Restaurar como base de datos

Construya el identificador de Azure Resource Manager (id. de ARM) de la nueva base de datos de PostgreSQL que se va a crear con el servidor de PostgreSQL de destino, al que se asignaron los permisos tal y como se ha indicado [anteriormente](#set-up-permissions), y el nombre de la base de datos de PostgreSQL necesaria. Por ejemplo, una base de datos de PostgreSQL se puede denominar **emprestored21** en un servidor de PostgreSQL de destino **targetossserver** en el grupo de recursos **targetrg** con una suscripción diferente.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

El siguiente cuerpo de la solicitud contiene el identificador del punto de recuperación y los detalles del destino de restauración.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

Para un punto de recuperación basado en archivo, debe:

1. Rehidratar desde el almacén de datos de archivo al almacén.
1. Modificar el almacén de datos de origen.
1. Agregar otros parámetros para especificar la prioridad de rehidratación.
1. Especificar la duración durante la que se debe conservar el punto de recuperación rehidratado en el almacén de datos.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="restore-as-files"></a>Restaurar como archivos

Recupere el URI del contenedor, dentro de la cuenta de almacenamiento a la que se le asignaron permisos como se detalla [arriba](#set-up-permissions). Por ejemplo, un contenedor denominado **testcontainerrestore** en una cuenta de almacenamiento **testossstorageaccount** con una suscripción diferente.

```http
"https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

Para el punto de recuperación basado en archivo, modifique el almacén de datos de origen y agregue la prioridad de rehidratación y la duración de retención, en días, del punto de recuperación rehidratado, como se menciona a continuación:

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="validate-restore-requests"></a>Validación de las solicitudes de restauración

Una vez preparado el cuerpo de la solicitud, valídelo mediante la [validación de la API de restauración](/rest/api/dataprotection/backup-instances/validate-for-restore). Al igual que la validación de la API de copia de seguridad, se trata de una operación *POST*.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-07-01
```

En el ejemplo, esta API se traduce en:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/ValidateRestore?api-version=2021-07-01"
```

[Obtenga más información](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body) sobre el cuerpo de la solicitud de esta API POST.

##### <a name="request-body-to-validate-restore-request"></a>Cuerpo de la solicitud para validar la solicitud de restauración

Hemos creado una sección del mismo en la [sección anterior](#create-a-request-body-for-restore-operations). Ahora, agregaremos el tipo de objeto y lo usaremos para desencadenar una operación de validación.

```json
{
    "objectType": "ValidateRestoreRequestObject",
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "restoreRequestObject": {
      "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
      "sourceDataStoreType": "VaultStore",
      "restoreTargetInfo": {
        "objectType": "restoreTargetInfo",
        "recoveryOption": "FailIfExists",
        "dataSourceInfo": {
          "objectType": "Datasource",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "resourceName": "emprestored21",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westus",
          "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "dataSourceSetInfo": {
          "objectType": "DatasourceSet",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
          "resourceName": "targetossserver",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westus",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "datasourceAuthCredentials": {
          "objectType": "SecretStoreBasedAuthCredentials",
          "secretStoreResource": {
            "secretStoreType": "AzureKeyVault",
            "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
            "value": null
          }
        },
        "restoreLocation": "westus"
      }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>Respuesta a las solicitudes de validación de una restauración

La _solicitud de validación de una restauración_ es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Por consiguiente, esta operación crea otra operación de la que se debe hacer un seguimiento por separado.

Devuelve las dos respuestas: 202 - Aceptado cuando se crea otra operación y 200 - Correcto cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |         |  Estado de la solicitud de validación       |
|202 - Aceptado     |         |     Accepted    |

###### <a name="example-response-to-restore-validate-request"></a>Respuesta de ejemplo a la solicitud de validación de una restauración

Una vez enviada la operación *POST*, la respuesta inicial será 202 (Aceptado) con un encabezado _Azure-asyncOperation_.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

Realice un seguimiento del encabezado _Azure-AsyncOperation_ con una solicitud *GET* sencilla. Si la solicitud se realiza correctamente, devuelve 200 - Correcto con una respuesta de estado.

```http
GET https://management.azure.com/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==?api-version=2021-07-01
```

```http
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Inprogress",
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "0001-01-01T00:00:00Z"
}
```

La respuesta indica los errores que deben resolverse antes de enviar la solicitud de restauración. En el ejemplo siguiente se representa cuándo la base de datos de destino es de una versión inferior y, por tanto, no se puede restaurar.

```http
---------- Response (1892 ms) ------------

HTTP/1.1 200 OK
Content-Length: 1236
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 784764f8-941d-4f05-8d8c-c02d2c05f799
x-ms-routing-request-id: SOUTHINDIA:20211022T202725Z:e109a061-a09e-4f13-acd0-9b9833f851ac
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:27:25 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported.",
          "recommendedAction": [
            "Restore to the same or a higher PostgreSQL version from which the backup was taken."
          ],
          "code": "UserErrorRestoreToLowerVersion",
          "target": "",
          "innerError": {
            "code": "InnerErrorCodeUnavailable",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Restoring backup from version:10 of PostgreSQL to 9.5 of PostgreSQL not supported, as the restore server version is lower."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "2a23524f-0217-4bc1-bbe8-1546d2e6204d-Ibz"
          }
        }
      }
    ],
    "code": "UserErrorRestoreToLowerVersion",
    "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported."
  },
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "2021-10-22T20:23:11Z"
}
```

Una vez que se corrigen los errores y se vuelve a validar la solicitud, 200 - Correcto devuelve una respuesta correcta.

```http
HTTP/1.1 200 OK
Content-Length: 443
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 61d62dd8-8e1a-473c-bcc6-c6a7a19fb035
x-ms-routing-request-id: SOUTHINDIA:20211022T203846Z:89af04a6-4e91-4b64-8998-a369dc763408
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:38:46 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "status": "Succeeded",
  "startTime": "2021-10-22T20:28:24.3820169Z",
  "endTime": "2021-10-22T20:28:49Z"
}
```

#### <a name="trigger-restore-requests"></a>Desencadenar solicitudes de restauración

La operación para desencadenar una restauración es una API ***POST***. [Obtenga más información](/rest/api/dataprotection/backup-instances/trigger-restore) sobre el desencadenamiento de la operación de restauración.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-07-01
```

En el ejemplo, la API se traduce en:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/restore?api-version=2021-07-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>Creación de un cuerpo de solicitud para operaciones de restauración

Una vez validadas las solicitudes, use el mismo cuerpo de la solicitud para desencadenar la _solicitud de restauración_ con pocos cambios.

###### <a name="example-request-body-for-restore"></a>Cuerpo de solicitud de ejemplo para una restauración

El único cambio del cuerpo de la _solicitud de restauración de validación_ es quitar el objeto _restoreRequest_ al principio.

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "sourceDataStoreType": "VaultStore",
  "restoreTargetInfo": {
    "objectType": "restoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "dataSourceInfo": {
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "resourceName": "emprestored21",
      "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
      "resourceLocation": "westus",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "dataSourceSetInfo": {
      "objectType": "DatasourceSet",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
      "resourceName": "targetossserver",
      "resourceType": "Microsoft.DBforPostgreSQL/servers",
      "resourceLocation": "westus",
      "resourceUri": "",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "datasourceAuthCredentials": {
      "objectType": "SecretStoreBasedAuthCredentials",
      "secretStoreResource": {
        "secretStoreType": "AzureKeyVault",
        "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
        "value": null
      }
    },
    "restoreLocation": "westus"
  },
  "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
}
```

#### <a name="response-to-trigger-restore-requests"></a>Respuesta a las solicitudes para desencadenar una restauración

La solicitud para _desencadenar una solicitud de restauración_ es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Por consiguiente, esta operación crea otra operación de la que se debe hacer un seguimiento por separado.

Devuelve las dos respuestas: 202 - Aceptado cuando se crea otra operación y 200 - Correcto cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |         |  Estado de la solicitud de restauración       |
|202 - Aceptado     |         |     Accepted    |

##### <a name="example-response-to-trigger-restore-request"></a>Respuesta de ejemplo a la solicitud para desencadenar una restauración

Una vez enviada la operación *POST*, la respuesta inicial será 202 (Aceptado) con un encabezado _Azure-asyncOperation_.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

Realice un seguimiento del encabezado _Azure-AsyncOperation_ con una solicitud *GET* sencilla. Cuando la solicitud se realiza correctamente, devuelve "200 (OK)" con un identificador de trabajo del que se debe realizar un seguimiento adicional para la finalización de la solicitud de restauración.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:46:52.4110868Z",
  "endTime": "2021-07-08T20:46:56Z",
  "properties": {
    "jobId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
    "objectType": "OperationJobExtendedInfo"
  }
}
```

#### <a name="track-jobs"></a>Seguimiento de trabajos

El _desencadenamiento de solicitudes de restauración_ desencadenó el trabajo de restauración. Para realizar el seguimiento del identificador de trabajo resultante, use [GET Jobs API](/rest/api/dataprotection/jobs/get).

Use el comando *GET* simple para realizar el seguimiento del _JobId_ presente en la [respuesta al desencadenamiento de la restauración](#example-response-to-trigger-restore-request) anterior.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-07-01
```

El estado del trabajo mencionado anteriormente indicará que el trabajo de restauración se ha completado.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la copia de seguridad de Azure PostgreSQL](backup-azure-database-postgresql-overview.md)
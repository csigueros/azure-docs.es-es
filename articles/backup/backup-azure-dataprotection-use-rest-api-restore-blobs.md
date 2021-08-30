---
title: Restauración de blobs en una cuenta de almacenamiento mediante la API de REST Azure Data Protection.
description: En este artículo, aprenderá a restaurar blobs de una cuenta de almacenamiento mediante la API de REST.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 9b8d21e6-3e23-4345-bb2b-e21040996afd
ms.openlocfilehash: 0ba444126026dc77d6e9b963edb6bed3d108c97b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598801"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-data-protection-rest-api"></a>Restauración de blobs de Azure a un momento dado mediante la API de REST Azure Data Protection

En este artículo se describe cómo restaurar [blobs](blob-backup-overview.md) a cualquier momento dado mediante Azure Backup.

> [!IMPORTANT]
> Antes de continuar con la restauración de blobs de Azure mediante Azure Backup, consulte los [puntos importantes](blob-restore.md#before-you-start).

En este artículo, aprenderá a:

- Restauración de blobs de Azure a un momento dado

- Realizar el seguimiento del estado de la operación de restauración.

## <a name="prerequisites"></a>Requisitos previos

- [Creación de un almacén de Backup](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Creación de una directiva de copia de seguridad de blobs](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

- [Configuración de una copia de seguridad de blobs](backup-azure-dataprotection-use-rest-api-backup-blobs.md)

Haremos referencia a un almacén de copia de seguridad existente _TestBkpVault_, en el grupo de recursos _testBkpVaultRG_, donde los blobs de una cuenta de almacenamiento se denominaban "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d" en los ejemplos.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Restauración de blobs de Azure dentro de una cuenta de almacenamiento

### <a name="fetching-the-valid-time-range-for-restore"></a>Captura del intervalo de tiempo válido para la restauración

Como la copia de seguridad operativa de blobs es continua, no hay puntos distintos desde los que restaurar. En su lugar, es necesario capturar el intervalo de tiempo válido en el que los blobs se pueden restaurar a cualquier momento dado. En este ejemplo, vamos a comprobar los intervalos de tiempo válidos para restaurar en los últimos 30 días.

Los intervalos de tiempo restaurables se pueden enumerar mediante la API [find restorable time range](/rest/api/dataprotection/restorable-time-ranges/find). Se trata de una API *POST* que desencadena una operación para calcular el intervalo de copias de seguridad continuas de los blobs de la cuenta de almacenamiento.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/findRestorableTimeRanges?api-version=2021-01-01
```

En nuestro ejemplo, esto se traduce en lo siguiente:

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/findRestorableTimeRanges?api-version=2021-01-01
```

#### <a name="create-the-request-body-to-fetch-valid-time-ranges-for-restore"></a>Creación del cuerpo de la solicitud para capturar intervalos de tiempo válidos para la restauración

Para desencadenar una operación para calcular intervalos de tiempo válidos, a continuación se indican los componentes del cuerpo de una solicitud.

|**Nombre**  |**Tipo**  |**Descripción**  |
|---------|---------|---------|
|sourceDatastoreType     |   [RestoreSourceDataStoreType](/rest/api/dataprotection/restorable-time-ranges/find#restoresourcedatastoretype)      |    Almacén de datos que contiene los datos que se restaurarán.     |
|startTime     |    String     |  Hora de inicio de la solicitud Enumerar intervalos de restauración. Formato ISO 8601.       |
|endTime     |    String     |    Hora de finalización de la solicitud Enumerar intervalos de restauración. Formato ISO 8601.     |

##### <a name="example-request-body-to-fetch-valid-time-range"></a>Cuerpo de la solicitud de ejemplo para capturar un intervalo de tiempo válido

El cuerpo de la solicitud siguiente define las propiedades necesarias para capturar los intervalos de tiempo de los datos continuos que se pueden restaurar. Puesto que las copias de seguridad de blobs residen en la cuenta de almacenamiento, el almacén de datos es "Operativo". Puede proporcionar una hora de inicio y finalización que ayude a restringir el proceso de búsqueda y a devolver el intervalo de tiempo disponible.

```json
{
  "sourceDataStoreType": "OperationalStore",
  "startTime": "",
  "endTime": ""
}
```

#### <a name="responses-for-fetch-valid-time-ranges"></a>Respuestas para capturar intervalos de tiempo válidos

Una vez que envíe la solicitud *POST*, la respuesta es 200(OK) con la hora de inicio y finalización del intervalo disponible para la restauración dentro de la hora de inicio y finalización especificada de la solicitud.

|**Nombre**  |**Tipo**  |**Descripción**  |
|---------|---------|---------|
|200(OK)     |   [AzureBackupFindRestorableTimeRangesResponseResource](/rest/api/dataprotection/restorable-time-ranges/find#azurebackupfindrestorabletimerangesresponseresource)      |    Aceptar     |
|Otros códigos de estado     |    [CloudError](/rest/api/dataprotection/restorable-time-ranges/find#clouderror)     |  Respuesta de error que describe el motivo del error de la operación.       |

##### <a name="example-response-for-fetch-valid-time-ranges"></a>Respuesta de ejemplo para capturar intervalos de tiempo válidos

```http
HTTP/1.1 200 OK
Content-Length: 379
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a2b7c2d9-01f5-499a-b521-55da4862c79a
x-ms-routing-request-id: CENTRALUSEUAP:20210708T184646Z:4996a2bf-2df8-48a7-9b53-a552466a27f7
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 18:46:45 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges",
  "properties": {
    "restorableTimeRanges": [
      {
        "startTime": "2021-07-06T18:46:45.947728Z",
        "endTime": "2021-07-08T18:46:45.9932408Z",
        "objectType": "RestorableTimeRange"
      }
    ],
    "objectType": "AzureBackupFindRestorableTimeRangesResponse"
  }
}
```

### <a name="preparing-the-restore-request"></a>Preparación de la solicitud de restauración

Una vez corregido el momento dado para la restauración en la misma cuenta de almacenamiento, hay varias opciones para restaurar.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Restauración de todos los blobs a un momento dado

Con esta opción, se revierten todos los blobs en bloques de la cuenta de almacenamiento al momento dado seleccionado para restaurarlos. Las cuentas de almacenamiento que contienen grandes cantidades de datos o con una alta tasa de renovación pueden tardar más tiempo en restaurarse.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-all-blobs"></a>Creación del cuerpo de la solicitud para la recuperación a un momento dado de todos los blobs

Los puntos clave que debe recordar en este escenario son los siguientes:

- La restauración se está realizando en la misma cuenta de almacenamiento, lo que significa que el objeto de destino de la restauración es el mismo que el origen de datos. Esto se refleja en la siguiente sección de información del destino de restauración.
- Se trata de copias de seguridad continuas y, por tanto, el tiempo de restauración es un momento dado y no un punto de recuperación distinto.
- Se restauran todos los blobs
- El almacén de datos de origen, es decir, donde residen las copias de seguridad, es la misma cuenta de almacenamiento. Por lo tanto, el almacén de datos de origen es "Operativo".

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "RestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="restoring-few-containers-to-a-point-in-time"></a>Restauración de algunos contenedores a un momento dado

El uso de esta opción permite seleccionar hasta 10 contenedores para restaurar o restaurar un subconjunto de blobs mediante una coincidencia de prefijo. Puede restaurar hasta 10 intervalos lexicográficos de blobs dentro de un solo contenedor o en varios contenedores para devolver esos blobs a su estado anterior en un momento dado. En caso de usar prefijos, estos son algunos aspectos que debe tener en cuenta:

- Puede usar una barra diagonal (/) para delimitar el nombre del contenedor del prefijo de blob.
- El inicio del intervalo especificado es inclusivo, pero el intervalo especificado es exclusivo.

[Obtenga más información](blob-restore.md#use-prefix-match-for-restoring-blobs) sobre el uso de prefijos para restaurar intervalos de blobs.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs"></a>Creación del cuerpo de la solicitud para la restauración a un momento dado de los contenedores seleccionados o de algunos blobs

Los puntos clave que debe recordar en este escenario son los siguientes:

- La restauración se está realizando en la misma cuenta de almacenamiento, lo que significa que el objeto de destino de la restauración es el mismo que el origen de datos. Esto se refleja en la siguiente sección de información del destino de restauración.
- Se trata de copias de seguridad continuas y, por tanto, el tiempo de restauración es un momento dado y no un punto de recuperación distinto.
- Se restauran algunos elementos de la cuenta de almacenamiento. Podrían ser contenedores o blobs con un patrón de prefijo.
- El almacén de datos de origen, es decir, donde residen las copias de seguridad, es la misma cuenta de almacenamiento. Por lo tanto, el almacén de datos de origen es "Operativo".

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "ItemLevelRestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "restoreCriteria": [
        {
          "objectType": "RangeBasedItemLevelRestoreCriteria",
          "minMatchingValue": "Container1",
          "maxMatchingValue": "Container10-0"
        }
      ],
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="validating-restore-requests"></a>Validación de solicitudes de restauración

Una vez preparado el cuerpo de la solicitud, se puede validar mediante la [validación de la API de restauración](/rest/api/dataprotection/backup-instances/validate-for-restore). Al igual que la validación de la API de copia de seguridad, se trata de una operación *POST*.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

En nuestro ejemplo, esto se traduce en:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/validateRestore?api-version=2021-01-01"
```

El cuerpo de la solicitud para esta API POST se detalla [aquí](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body). Hemos creado lo mismo en la sección anterior para todos los escenarios de [restauración de blobs](#constructing-the-request-body-for-point-in-time-restore-of-all-blobs) y de [restauración de algunos elementos](#constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs). Usaremos lo mismo para desencadenar una operación de validación.

##### <a name="response-to-validate-restore-requests"></a>Respuesta a las solicitudes de validación de una restauración

La solicitud de validación de una restauración es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Significa que esta operación crea otra que tiene que ser seguida por separado.

Devuelve las dos respuestas: 202 (Accepted) (aceptado) cuando se crea otra operación y, a continuación, 200 (OK) cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |         |  Estado de la solicitud de validación       |
|202 - Aceptado     |         |     Accepted    |

###### <a name="example-response-to-restore-validate-request"></a>Respuesta de ejemplo a la solicitud de validación de una restauración

Una vez enviada la operación *POST*, la respuesta inicial será "202 - Aceptado" junto con un encabezado Azure-asyncOperation.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Realice un seguimiento del encabezado Azure-AsyncOperation con una solicitud *GET* sencilla. Si la solicitud se realiza correctamente, devolverá "200 (OK)" con una respuesta de estado correcto.

```http
 GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:59:35.0060264Z",
  "endTime": "2021-07-08T20:59:57Z"
}
```

#### <a name="triggering-restore-requests"></a>Desencadenamiento de solicitudes de restauración

La operación para desencadenar una restauración es una API ***POST***. Todos los detalles sobre la operación para desencadenar una restauración están disponibles [aquí](/rest/api/dataprotection/backup-instances/trigger-restore).

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

En nuestro ejemplo, esto se traduce en:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/restore?api-version=2021-01-01"
```

##### <a name="creating-a-request-body-for-restore-operations"></a>Creación de un cuerpo de solicitud para operaciones de restauración

Una vez validadas las solicitudes, se puede usar el mismo cuerpo de la solicitud para desencadenar la solicitud de restauración con pocos cambios.

###### <a name="example-request-body-for-all-blobs-restore"></a>Cuerpo de la solicitud de ejemplo para la restauración de todos los blobs

El único cambio del cuerpo de la solicitud de restauración de validación es quitar el objeto "restoreRequest" al principio.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "RestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00Z"
}
```

###### <a name="example-request-body-for-items-or-few-blobs-restore"></a>Cuerpo de la solicitud de ejemplo para la restauración de elementos o pocos blobs

El único cambio del cuerpo de la solicitud de restauración de validación es quitar el objeto "restoreRequest" al principio.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "ItemLevelRestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "restoreCriteria": [
      {
        "objectType": "RangeBasedItemLevelRestoreCriteria",
        "minMatchingValue": "Container1",
        "maxMatchingValue": "Container2"
      }
    ],
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
}
```

#### <a name="response-to-trigger-restore-requests"></a>Respuesta a las solicitudes para desencadenar una restauración

La solicitud para desencadenar una restauración es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Significa que esta operación crea otra que tiene que ser seguida por separado.

Devuelve las dos respuestas: 202 (Accepted) (aceptado) cuando se crea otra operación y, a continuación, 200 (OK) cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |         |  Estado de la solicitud de restauración       |
|202 - Aceptado     |         |     Accepted    |

##### <a name="example-response-to-trigger-restore-request"></a>Respuesta de ejemplo a la solicitud para desencadenar una restauración

Una vez enviada la operación *POST*, la respuesta inicial será "202 - Aceptado" junto con un encabezado Azure-asyncOperation.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Realice un seguimiento del encabezado Azure-AsyncOperation con una solicitud *GET* sencilla. Cuando la solicitud se realiza correctamente, devuelve "200 (OK)" con un identificador de trabajo del que se debe realizar un seguimiento adicional para la finalización de la solicitud de restauración.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01

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

#### <a name="tracking-jobs"></a>Seguimiento de trabajos

Las solicitudes para desencadenar una restauración generaron un trabajo de restauración, y se debe realizar un seguimiento del identificador de trabajo resultante con [GET Jobs API](/rest/api/dataprotection/jobs/get).

Use el comando GET simple para realizar el seguimiento del JobId resultante en la [respuesta al desencadenamiento de la restauración](#example-response-to-trigger-restore-request) anterior.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "4195ca6c-e02d-11eb-b0b1-70bc105e2242",
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
    "vaultName": "BV-JPE-GRS",
    "backupInstanceFriendlyName": "msblobbackup",
    "policyName": "BlobBackup-Policy",
    "sourceResourceGroup": "RG-BlobBackup",
    "dataSourceName": "msblobbackup",
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-07-08T20%3A48%3A36.6999667Z'\"",
    "sourceSubscriptionID": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "dataSourceLocation": "westus",
    "startTime": "2021-07-08T20:44:19.5467125Z",
    "endTime": "2021-07-08T20:48:35.8297312Z",
    "dataSourceType": "Microsoft.Storage/storageAccounts/blobServices",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "isUserTriggered": true,
    "supportedActions": [
      ""
    ],
    "duration": "PT4M16.2830187S",
    "extendedInfo": {
      "sourceRecoverPoint": {
        "recoveryPointTime": "2021-07-08T00:00:00Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed"
        }
      ]
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
  "name": "c4bd49a1-0645-4eec-b207-feb818962852",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

El estado del trabajo anterior indica que el trabajo de restauración se ha completado y que todos los blobs se han recuperado a un momento dado especificado.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la copia de seguridad de blobs de Azure](blob-backup-overview.md)

Para más información sobre las API REST de Azure Backup, consulte los siguientes documentos:

- [API de REST del proveedor de Azure Data Protection](/rest/api/dataprotection/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)
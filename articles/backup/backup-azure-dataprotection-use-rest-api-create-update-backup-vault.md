---
title: Creación de una directiva de Azure Backup para blobs mediante la API REST.
description: En este artículo, aprenderá a crear una directiva para hacer una copia de seguridad de blobs en una cuenta de almacenamiento mediante la API REST.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 93861379-5bec-4ed5-95d2-46f534a115fd
ms.openlocfilehash: 9b32e69bcd96d48dcd1321a69132790a93b1220b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598803"
---
# <a name="create-azure-backup-vault-using-rest-api"></a>Creación de un almacén de Azure Backup mediante la API REST

La nueva plataforma de protección de datos de Azure Backup proporciona funcionalidades mejoradas de copia de seguridad y restauración para cargas de trabajo más recientes, como los blobs de las cuentas de almacenamiento, disco administrado y plataforma PaaS de PostGre SQL Server. Su objetivo es minimizar la sobrecarga de administración a la vez que facilita la organización de las copias de seguridad. Un "almacén de Backup" es la piedra angular de la plataforma de protección de datos y es algo diferente del almacén de "Recovery Services".

Los pasos para crear un almacén de Azure Backup mediante una API REST se describen en la documentación sobre [creación de almacenes de API REST](/rest/api/dataprotection/backup-vaults/create-or-update). Vamos a usar este documento como referencia para crear un almacén de prueba llamado "testBkpVault" en "Oeste de EE. UU." y en el grupo de recursos "TestBkpVaultRG".

Para crear o actualizar un almacén de Azure Backup, use la siguiente operación *PUT*.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/testBkpVault?api-version=2021-01-01
```

## <a name="create-a-request"></a>Creación de una solicitud

Para crear la solicitud *PUT*, se necesita el parámetro `{subscription-id}`. Si tiene varias suscripciones, consulte [Trabajo con varias suscripciones](/cli/azure/manage-azure-subscriptions-azure-cli). Definirá los elementos `{resourceGroupName}` y `{vaultName}` para sus recursos, junto con el parámetro `api-version`. En este artículo se usa `api-version=2021-01-01`.

Los siguientes encabezados son obligatorios:

| Encabezado de solicitud   | Descripción |
|------------------|-----------------|
| *Content-Type:*  | Necesario. Establézcalo en `application/json`. |
| *Authorization:* | Necesario. Establézcalo en un [token de acceso](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` válido. |

Para más información sobre cómo crear la solicitud, consulte [ Componentes de una solicitud/respuesta de la API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Creación del cuerpo de la solicitud

Para crear el cuerpo de la solicitud, se usan las siguientes definiciones comunes:

|Nombre  |Obligatorio  |Tipo  |Descripción  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag opcional       |
|ubicación     |  true       |String         |   Ubicación de los recursos      |
|properties     |   true      | [BackupVault](/rest/api/dataprotection/backup-vaults/create-or-update#backupvault)        |  Propiedades del almacén       |
|Identidad     |         |  [DPPIdentityDetails](/rest/api/dataprotection/backup-vaults/create-or-update#dppidentitydetails)       |    Identifica el identificador único de sistema de cada recurso de Azure.     |
|etiquetas     |         | Object        |     Etiquetas del recurso    |

Tenga en cuenta que el nombre del almacén y el nombre del grupo de recursos se proporcionan en el URI de PUT. El cuerpo de solicitud define la ubicación.

## <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

El cuerpo de ejemplo siguiente se usa para crear un almacén en "Oeste de EE. UU.". Especifique la ubicación.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "None"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

Si desea crear un almacén de Backup y generar también una identidad asignada por el sistema, se debe dar el siguiente cuerpo de la solicitud.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "systemAssigned"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

## <a name="responses"></a>Respuestas

La creación de un almacén de Backup es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Significa que esta operación crea otra que tiene que ser seguida por separado.
Hay dos respuestas correctas a la operación para crear o actualizar un almacén de Backup:

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |   [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)      | Aceptar        |
|201 Creado     | [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)        |   Creado      |
| Otros códigos de estado  |  [CloudError](/rest/api/dataprotection/backup-vaults/create-or-update#clouderror)

Para más información sobre las respuestas de API REST, consulte [Process the response message](/rest/api/azure/#process-the-response-message) (Procesamiento del mensaje de respuesta).

### <a name="example-response"></a>Respuesta de ejemplo

Una respuesta *201 Created* (creado) condensada del cuerpo de solicitud de ejemplo anterior muestra que se ha asignado un *identificador* y que *provisioningState* es *Succeeded* (con éxito):

```json
{
    "eTag": null,
    "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/TestBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/testBkpVault",
    "identity": {
      "principalId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenantId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "SystemAssigned"
    },
    "location": "westUS",
    "name": "testBkpVault",
    "properties": {
      "provisioningState": "Succeeded",
      "storageSettings": [
        {
          "datastoreType": "VaultStore",
          "type": "GeoRedundant"
        }
      ]
    },
    "resourceGroup": "TestBkpVaultRG",
    "systemData": null,
    "tags": {},
    "type": "Microsoft.DataProtection/backupVaults"
  }
```

## <a name="next-steps"></a>Pasos siguientes

[Creación de una directiva de copia de seguridad para la copia de seguridad de blobs en este almacén](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md).

Para más información sobre las API REST de Azure, consulte los siguientes documentos:

- [API REST del proveedor de Azure Data Protection](/rest/api/dataprotection/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)

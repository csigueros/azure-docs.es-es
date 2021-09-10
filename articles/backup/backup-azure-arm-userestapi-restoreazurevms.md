---
title: Restauración de máquinas virtuales de Azure mediante API REST
description: En este artículo obtendrá información sobre cómo administrar las operaciones de restauración de copias de seguridad de Azure VM mediante la API REST.
ms.topic: conceptual
ms.date: 08/26/2021
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: f82adee9690c0114fef17640672c7326cffc8481
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966082"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Restauración de máquinas virtuales de Azure mediante API REST

Una vez completada la copia de seguridad de una máquina virtual de Azure mediante Azure Backup, se pueden restaurar máquinas virtuales o discos o archivos de Azure completos a partir de la misma copia de seguridad. En este artículo se describe cómo restaurar una máquina virtual de Azure o los discos mediante la API REST.

Para cualquier operación de restauración, se tiene que identificar primero el punto de recuperación correspondiente.

## <a name="select-recovery-point"></a>Selección de punto de recuperación

Se pueden enumerar los puntos de recuperación disponibles de un elemento de copia de seguridad mediante la [enumeración de puntos de recuperación de la API REST](/rest/api/backup/recovery-points/list). Es una sencilla operación *GET* con todos los valores pertinentes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}` y `{protectedItemName}` son tal y como se construyen [aquí](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` es "Azure".

El identificador URI de *GET* tiene todos los parámetros necesarios. No es necesario un cuerpo de solicitud adicional.

### <a name="responses"></a>Respuestas

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points/list#recoverypointresourcelist)      |       Aceptar  |

#### <a name="example-response"></a>Respuesta de ejemplo

Una vez que se emita el identificador URI de *GET*, se devuelve una respuesta 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

El punto de recuperación se identifica con el campo `{name}` en la respuesta anterior.

## <a name="restore-operations"></a>Operaciones de restauración

Después de seleccionar el [punto de restauración pertinente](#select-recovery-point), desencadene la operación de restauración.

***Todas las operaciones de restauración en el elemento de copia de seguridad se realizan con la misma API *POST*. Solo cambia el cuerpo de la solicitud con los escenarios de restauración.***

> [!IMPORTANT]
> [Aquí](./backup-azure-arm-restore-vms.md#restore-options) se mencionan todos los detalles sobre diversas opciones de restauración y sus dependencias. Revíselos antes de llevar a cabo estas operaciones.

Desencadenar operaciones de restauración es una solicitud *POST*. Para más información sobre la API, consulte [API REST "desencadenar restauración"](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}` y `{protectedItemName}` son tal y como se construyen [aquí](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` es "Azure" y el `{recoveryPointId}` es el `{name}` campo del punto de recuperación mencionado [anteriormente](#example-response).

Una vez que se obtiene el punto de recuperación, es necesario construir el cuerpo de la solicitud para el escenario de restauración correspondiente. En las secciones siguientes se describe el cuerpo de la solicitud para cada escenario.

- [Restaura los discos](#restore-disks)
- [Sustitución de discos](#replace-disks-in-a-backed-up-virtual-machine)
- [Restauración como una máquina virtual nueva](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Restauración de la respuesta

El desencadenamiento de cualquier operación de restauración es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Significa que esta operación crea otra que tiene que ser seguida por separado.

Devuelve las dos respuestas: 202 - Aceptado cuando se crea otra operación y 200 - Correcto cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|202 - Aceptado     |         |     Accepted    |

#### <a name="example-responses"></a>Respuestas de ejemplo

Una vez enviado el URI de *POST* para desencadenar la restauración de discos, la respuesta inicial es 202 (Accepted) (aceptado) con un encabezado de ubicación o Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

A continuación, realice un seguimiento de la operación resultante con el encabezado de ubicación o el encabezado Azure-AsyncOperation y un simple comando *GET*.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Una vez completada la operación, devuelve 200 (OK) con el identificador del trabajo de restauración resultante en el cuerpo de respuesta.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Puesto que el trabajo de restauración es una operación de larga duración, se debe seguir como se explica en el [documento sobre la supervisión de trabajos con la API de REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Restauración de los discos

Si es necesario personalizar la creación de una máquina virtual a partir de los datos de copia de seguridad, puede restaurar solo los discos en una cuenta de almacenamiento elegida y crear una máquina virtual a partir de esos discos según sus requisitos. La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services y no debe ser una zona redundante. Tanto los discos como la configuración de la máquina virtual con copia de seguridad ("vmconfig.json") se almacenarán en la cuenta de almacenamiento dada. Como se explicó [anteriormente](#restore-operations), se proporciona el cuerpo de la solicitud correspondiente para restaurar los discos a continuación.

#### <a name="create-request-body"></a>Creación del cuerpo de la solicitud

Para desencadenar una restauración de disco desde una copia de seguridad de máquina virtual de Azure, los siguientes son los componentes del cuerpo de la solicitud.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Para obtener una lista completa de las definiciones del cuerpo de la solicitud y otros detalles, consulte el [documento de la API REST sobre desencadenar la restauración](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Solicitud de ejemplo

El cuerpo de solicitud siguiente define las propiedades necesarias para desencadenar una restauración del disco.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Restauración de discos de forma selectiva

Si está [realizando copias de seguridad de discos de forma selectiva](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup), la lista actual de discos de los que se ha hecho una de copia de seguridad se proporciona en el [resumen de puntos de recuperación](#select-recovery-point) y en la [respuesta detallada](/rest/api/backup/recovery-points/get). También puede restaurar los discos de forma selectiva. [Aquí](selective-disk-backup-restore.md#selective-disk-restore) se proporcionan más detalles. Para restaurar de forma selectiva un disco entre la lista de discos de los que se ha hecho copia de seguridad, busque el LUN del disco de la respuesta del punto de recuperación y agregue la propiedad **restoreDiskLunList** al [cuerpo de solicitud anterior](#example-request) como se muestra a continuación.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Después de hacer el seguimiento de la respuesta tal como se explica [antes](#responses), y completar el trabajo de larga duración, los discos y la configuración de la máquina virtual con copia de seguridad ("VMConfig.json") estarán presentes en la cuenta de almacenamiento dada.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Sustitución de discos en una máquina virtual con copia de seguridad

Mientras la restauración de discos crea discos a partir del punto de recuperación, la sustitución de discos reemplaza los discos actuales de la VM de la copia de seguridad por los discos del punto de recuperación. Como se explicó [anteriormente](#restore-operations), se proporciona el cuerpo de la solicitud correspondiente para reemplazar los discos a continuación.

#### <a name="create-request-body"></a>Creación del cuerpo de la solicitud

Para desencadenar una sustitución de disco desde una copia de seguridad de máquina virtual de Azure, los siguientes son los componentes del cuerpo de la solicitud.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Para obtener una lista completa de las definiciones del cuerpo de la solicitud y otros detalles, consulte el [documento de la API REST sobre desencadenar la restauración](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Solicitud de ejemplo

El cuerpo de solicitud siguiente define las propiedades necesarias para desencadenar una restauración del disco.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Restauración como otra máquina virtual

Tal como se explicó [anteriormente](#restore-operations), el cuerpo de la solicitud siguiente define las propiedades necesarias para desencadenar una restauración de la máquina virtual.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
     }
 }
```

La respuesta debe controlarse de la misma manera que [se explicó anteriormente para restaurar los discos](#responses).

## <a name="cross-region-restore"></a>Restauración entre regiones

Si la restauración entre regiones (CRR) está habilitada en el almacén con el que ha protegido las VM, los datos de copia de seguridad se replican en la región secundaria. Puede usar los datos de copia de seguridad para realizar una operación de restauración. Para desencadenar una operación de restauración en la región secundaria mediante la API de REST, siga estos pasos:

### <a name="select-recovery-point-in-secondary-region"></a>Selección de un punto de recuperación en la región secundaria

Puede enumerar los puntos de recuperación disponibles de un elemento de copia de seguridad en la región secundaria mediante la [API de REST de enumeración de puntos de recuperación para CRR](/rest/api/backup/recovery-points-crr/list). Es una sencilla operación GET con todos los valores pertinentes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2018-12-20

```

`{containerName}` y `{protectedItemName}` son tal y como se construyen [aquí](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` es "Azure".

El identificador URI de *GET* tiene todos los parámetros necesarios. No se requiere un cuerpo de solicitud adicional.

>[!NOTE]
>Para obtener puntos de recuperación en la región secundaria, use la versión de API 2018-12-20 como en el ejemplo anterior.

#### <a name="responses"></a>Respuestas

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points-crr/list#recoverypointresourcelist)      |       Aceptar  |

#### <a name="example-response"></a>Respuesta de ejemplo

Una vez que se emita el identificador URI de *GET*, se devuelve una respuesta 200 (OK).

```http
Headers:
Pragma                        : no-cache
X-Content-Type-Options        : nosniff
x-ms-request-id               : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-client-request-id        : 4344a9c2-70d8-482d-b200-0ca9cc498812,4344a9c2-70d8-482d-b200-0ca9cc498812
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 149
x-ms-correlation-request-id   : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-routing-request-id       : SOUTHINDIA:20210731T112441Z:bfc4a4e6-c585-46e0-8e38-f11a86093701
Cache-Control                 : no-cache
Date                          : Sat, 31 Jul 2021 11:24:40 GMT
Server                        : Microsoft-IIS/10.0
X-Powered-By                  : ASP.NET

Body:
{
  "value": [
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932895704780058094",
      "name": "932895704780058094",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-31T09:24:34.687561Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932891484644960954",
      "name": "932891484644960954",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-30T09:20:01.8355052Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
.....
```

El punto de recuperación se identifica con el campo `{name}` en la respuesta anterior.

### <a name="get-access-token"></a>Obtención de un token de acceso

Para realizar la restauración entre regiones, necesitará un token de acceso para autorizar a la solicitud el acceso a los puntos de restauración replicados en la región secundaria. Para obtener un token de acceso, siga estos pasos:

#### <a name="step-1"></a>Paso 1:

Use la [API de propiedades de AAD](/rest/api/backup/aad-properties/get) para obtener las propiedades de AAD para la región secundaria (*westus* en el ejemplo siguiente):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/westus/backupAadProperties?api-version=2018-12-20
```

##### <a name="response-example"></a>Ejemplo de respuesta

La respuesta se devuelve con el formato siguiente:

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

#### <a name="step-2"></a>Paso 2:

Use [Get Access Token API](/rest/api/backup/recovery-points-get-access-token-for-crr/get-access-token) para autorizar a la solicitud el acceso a los puntos de restauración replicados en la región secundaria:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/accessToken?api-version=2018-12-20
```

Para el cuerpo de la solicitud, pegue el contenido de la respuesta devuelta por la API de propiedades de AAD del paso anterior.

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

##### <a name="response-example"></a>Ejemplo de respuesta

La respuesta se devuelve con el formato siguiente:

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/26083826328862",
  "name": "932879774590051503",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
    "properties": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "resourceName": "testVault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    }
}
```

### <a name="restore-disks-to-the-secondary-region"></a>Restauración de discos en la región secundaria

Use [Cross-Region Restore Trigger API](/rest/api/backup/cross-region-restore/trigger) para restaurar un elemento en la región secundaria.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/{azureRegion}/backupCrossRegionRestore?api-version=2018-12-20
```

El cuerpo de la solicitud debe tener dos partes:

1. ***crossRegionRestoreAccessDetails** _: pegue el bloque _properties* de la respuesta de la solicitud Get Access Token API realizada en el paso anterior para rellenar este segmento del cuerpo de la solicitud.

1. ***restoreRequest** _: para rellenar el segmento _restoreRequest* del cuerpo de la solicitud, deberá pasar el identificador de punto de recuperación obtenido anteriormente, junto con el identificador de Azure Resource Manager (ARM) de la VM de origen, así como los detalles de la cuenta de almacenamiento de la región secundaria que se usará como ubicación de almacenamiento provisional. Para realizar la restauración del disco, especifique *RestoreDisks* como tipo de recuperación.

A continuación se muestra un cuerpo de solicitud de ejemplo para restaurar los discos de una VM en la región secundaria:

```json
 {
  "crossRegionRestoreAccessDetails": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "azurefiles",
        "resourceName": "azurefilesvault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    },
    "restoreRequest": {
        "affinityGroup": "",
        "createNewCloudService": false,
        "encryptionDetails": {
            "encryptionEnabled": false
        },
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "932879774590051503",
        "recoveryType": "RestoreDisks",
        "sourceResourceId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Compute/virtualMachines/testVM",
        "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1",
        "storageAccountId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Storage/storageAccounts/testStorageAccount",
        "region": "westus",
        "affinityGroup": "",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "restoreDiskLunList": []
    }
}
```

De forma similar a la operación de restauración de la región primaria, se trata de una operación asincrónica de la que se debe [realizar un seguimiento por separado](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-response).



## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las API REST de Azure Backup, consulte los siguientes documentos:

- [API REST del proveedor de Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)

---
title: Azure Disk Encryption para Windows
description: Implementa Azure Disk Encryption en una máquina virtual Windows mediante una extensión de máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 679682ed0a268306bfb6c7afcf9abbd94a4d45b7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738976"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Información general

Azure Disk Encryption aprovecha BitLocker para ofrecer cifrado de disco completo en las máquinas virtuales de Azure que ejecutan Windows.  Esta solución se integra con Azure Key Vault para administrar los secretos y las claves de cifrado de los discos de la suscripción de Key Vault.

## <a name="prerequisites"></a>Requisitos previos

Para una lista completa de los requisitos previos, consulte [Azure Disk Encryption para máquinas virtuales Windows](../windows/disk-encryption-overview.md), en concreto las secciones siguientes:

- [Máquinas virtuales y sistemas operativos compatibles](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de red](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisitos de la directiva de grupo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Esquema de extensión

Hay dos versiones del esquema de extensión para Azure Disk Encryption (ADE):
- v2.2: un esquema recomendado más reciente que no usa las propiedades de Azure Active Directory (AAD).
- v1.1: un esquema anterior que requiere las propiedades de Azure Active Directory (AAD).

Para seleccionar un esquema de destino, la propiedad `typeHandlerVersion` debe establecerse en la versión del esquema que desee usar.

### <a name="schema-v22-no-aad-recommended"></a>Esquema v2.2: sin AAD (recomendado)

El esquema v2.2 se recomienda para todas las máquinas virtuales nuevas y no requiere propiedades de Azure Active Directory.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Esquema v1.1: con AAD

El esquema 1.1 requiere `aadClientID` y `aadClientSecret` o `AADClientCertificate`, y no se recomienda para las nuevas máquinas virtuales.

Usar `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Usar `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Valores de propiedad

Nota: Todos los valores distinguen mayúsculas de minúsculas.

| Nombre | Valor / ejemplo | Tipo de datos |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryption | string |
| typeHandlerVersion | 2.2, 1.1 | string |
| (esquema 1.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid |
| (esquema 1.1) AADClientSecret | password | string |
| (esquema 1.1) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption | string |
| (opcional: RSA-OAEP predeterminado) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (opcional) KeyEncryptionKeyURL | url | string |
| (opcional) KekVaultResourceId | url | string |
| (opcional) SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>Implementación de plantilla

Para ver un ejemplo de una implementación de plantilla basada en la versión 2.2 del esquema, consulte la plantilla de inicio rápido de Azure [encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm-without-aad).

Para un ejemplo de una implementación de plantilla basada en un esquema de la versión 1.1, consulte la plantilla de inicio rápido de Azure [encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm).

>[!NOTE]
> Además, si el parámetro `VolumeType` está establecido en Todos, los discos de datos se cifrarán solo si tienen el formato correcto.

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Para solucionar el problema, consulte [Guía de solución de problemas de Azure Disk Encryption](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/community/).

Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya a [Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte técnico. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes del soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre las extensiones, consulte el artículo [Características y extensiones de las máquinas virtuales para Windows](features-windows.md).
* Para más información sobre Azure Disk Encryption para Windows, consulte [Máquinas virtuales Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).

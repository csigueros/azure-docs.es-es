---
title: Introducción a las opciones de cifrado de discos administrados
description: Introducción a las opciones de cifrado de discos administrados
author: msmbaldwin
ms.date: 06/05/2021
ms.topic: conceptual
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1acfd3d1a4c358e22b3371960ff358f647afe339
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063661"
---
# <a name="overview-of-managed-disk-encryption-options"></a>Introducción a las opciones de cifrado de discos administrados

Hay varios tipos de cifrado disponibles para los discos administrados, incluidos Azure Disk Encryption (ADE), el cifrado del lado servidor (SSE) y el cifrado en el host.

- **Azure Disk Encryption** ayuda a custodiar y proteger sus datos con el fin de satisfacer los compromisos de cumplimiento y seguridad de su organización. ADE ofrece cifrado de volumen para los discos de datos y del sistema operativo en las máquinas virtuales de Azure mediante el uso de la característica de Linux o la característica [BitLocker](https://en.wikipedia.org/wiki/BitLocker) de Windows. ADE está integrado con Azure Key Vault para ayudarle a controlar y administrar los secretos y las claves de cifrado de discos.  Para obtener más información, consulte [Azure Disk Encryption para máquinas virtuales Linux](./linux/disk-encryption-overview.md) o [Azure Disk Encryption para máquinas virtuales Windows](./windows/disk-encryption-overview.md).

- El **cifrado del lado servidor**(también denominado cifrado en reposo o cifrado de Azure Storage) cifra automáticamente los datos almacenados en discos administrados de Azure (discos de datos y del sistema operativo) al guardarlos en la nube.  Para obtener más información, consulte [Cifrado del lado servidor de Azure Disk Storage](./disk-encryption.md).

- El **cifrado en el host** garantiza que los datos almacenados en el host de máquina virtual se cifran en reposo y se transmiten cifrados al servido de almacenamiento. Los discos con cifrado en el host habilitado no se cifran con SSE; en su lugar, el servidor que hospeda la máquina virtual proporciona el cifrado de los datos, y dichos datos cifrados se transmiten a Azure Storage. Para obtener más información, consulte [Cifrado en el host: cifrado de un extremo a otro para los datos de la máquina virtual](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="comparison"></a>De comparación

A continuación se muestra una comparación de SSE, ADE y el cifrado en el host.

| | Cifrado en reposo (discos de datos y del sistema operativo) | Cifrado de disco temporal | Cifrado de cachés | Flujos de datos cifrados entre el proceso y el almacenamiento | Control de claves por parte del cliente | Estado de cifrado de discos en Azure Security Center |
|--|--|--|--|--|--|--|
| **Cifrado en reposo con clave administrada por la plataforma (SSE+PMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#10060; | Incorrecto, no aplicable si está exento |
| **Cifrado en reposo con clave administrada por el cliente (SSE+CMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#x2705; | Incorrecto, no aplicable si está exento |
| **Azure Disk Encryption** | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Healthy |
| **Cifrado en el host**  | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Incorrecto, no aplicable si está exento |

> [!Important]
> Para el cifrado en el host, Azure Security Center no detecta el estado de cifrado.

## <a name="next-steps"></a>Pasos siguientes

- [Azure Disk Encryption para máquinas virtuales Linux](./linux/disk-encryption-overview.md)
- [Azure Disk Encryption para máquinas virtuales Windows](./windows/disk-encryption-overview.md)
- [Cifrado del lado servidor de Azure Disk Storage](./disk-encryption.md)
- [Cifrado en el host](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Aspectos básicos de seguridad de Azure: Información general del cifrado de Azure](../security/fundamentals/encryption-overview.md)
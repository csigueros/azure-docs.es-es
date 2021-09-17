---
title: Habilitación de Azure Disk Encryption para conjuntos de escalado de máquinas virtuales
description: En este artículo se proporcionan instrucciones sobre cómo habilitar Microsoft Azure Disk Encryption para conjuntos de escalado de máquinas virtuales.
author: ju-shim
ms.author: jushiman
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.openlocfilehash: 579315379923b5ba8a16f94cff5984e6e904a5ec
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691305"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption para conjuntos de escalado de máquinas virtuales.

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado uniformes

Azure Disk Encryption proporciona cifrado de volumen para los discos de datos y del sistema operativo de las máquinas virtuales, lo que ayuda a proteger y salvaguardar los datos para satisfacer los compromisos de cumplimiento y seguridad de la organización. Para más información, consulte [Azure Disk Encryption: máquinas virtuales Linux](../virtual-machines/linux/disk-encryption-overview.md) y [Azure Disk Encryption: máquinas virtuales Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption también se pueden aplicar a los conjuntos de escalado de máquinas virtuales Windows y Linux, en estos casos:
- Conjuntos de escalado creados con discos administrados. Azure Disk Encryption no es compatible con los conjuntos de escalado con disco nativo (o no administrado).
- Volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows.
- Volúmenes de datos en conjuntos de escalado de Linux. El cifrado de discos del sistema operativo NO se admite actualmente en los conjuntos de escalado de Linux.

Puede obtener información sobre los aspectos básicos de Azure Disk Encryption para conjuntos de escalado de máquinas virtuales en tan solo unos minutos con los tutoriales [Cifrado de conjuntos de escalado de máquinas virtuales mediante la CLI de Azure](disk-encryption-cli.md) o [Cifrado de conjuntos de escalado de máquinas virtuales mediante Azure PowerShellAzure](disk-encryption-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

- [Cifrado de conjuntos de escalado de máquinas virtuales con Resource Manager](disk-encryption-azure-resource-manager.md)
- [Creación y configuración de un almacén de claves para Azure Disk Encryption](disk-encryption-key-vault.md)
- [Uso de Azure Disk Encryption con la secuenciación de extensiones de un conjunto de escalado de máquinas virtuales](disk-encryption-extension-sequencing.md)

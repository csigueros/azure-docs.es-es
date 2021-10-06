---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8d5ad743c425d921a4fa9abbf3948aa3b69b8390
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908411"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Los discos sin asignar deben cifrarse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Esta directiva audita cualquier disco sin adjuntar y que no tenga el cifrado habilitado. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |
|[Las máquinas virtuales deben cifrar los discos temporales, las cachés y los flujos de datos entre los recursos de Proceso y Almacenamiento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |De manera predeterminada, los discos del sistema operativo y de datos de una máquina virtual se cifran en reposo mediante claves administradas por la plataforma. Los discos temporales, las cachés de datos y los datos que fluyen entre el proceso y el almacenamiento no se cifran. Ignore esta recomendación si: 1. Usa el cifrado en el host, o 2. El cifrado del lado servidor en Managed Disks cumple sus requisitos de seguridad. Más información en [Cifrado del lado servidor de Azure Disk Storage](../../../../../articles/virtual-machines/disk-encryption.md). y [Diferentes ofertas de cifrado de disco](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison). |AuditIfNotExists, Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
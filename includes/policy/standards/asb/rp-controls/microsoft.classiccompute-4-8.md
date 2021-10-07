---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 386109e6a28514c6d57f201bdc6097f4d969cdad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909505"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Las máquinas virtuales deben cifrar los discos temporales, las cachés y los flujos de datos entre los recursos de Proceso y Almacenamiento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |De manera predeterminada, los discos del sistema operativo y de datos de una máquina virtual se cifran en reposo mediante claves administradas por la plataforma. Los discos temporales, las memorias caché de datos y los datos que fluyen entre el proceso y el almacenamiento no se cifran. Ignore esta recomendación si: 1. Se utiliza el cifrado en el host, o 2. El cifrado del lado servidor en Managed Disks cumple sus requisitos de seguridad. Más información en [Cifrado del lado servidor de Azure Disk Storage](../../../../../articles/virtual-machines/disk-encryption.md). y en [Introducción a las opciones de cifrado de discos administrados](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison). |AuditIfNotExists, Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
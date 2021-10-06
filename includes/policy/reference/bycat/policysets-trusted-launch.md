---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b0ce25df54d966e952a2fd026ee66940af173770
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618355"
---
|Nombre |Descripción |Directivas |Versión |
|---|---|---|---|
|[\[Versión preliminar\]: configura los requisitos previos para habilitar la atestación de invitado en máquinas virtuales habilitadas para el inicio seguro](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Trusted%20Launch/TrustedLaunch_GuestAttestationPrerequisites.json). |Configure las máquinas virtuales habilitadas para el inicio seguro para instalar automáticamente la extensión de atestación de invitado y la identidad administrada asignada por el sistema a fin de permitir que Azure Security Center atestigüe y supervise proactivamente la integridad del arranque. La integridad del arranque se atestigua a través de la atestación remota. Para más información, consulte el siguiente vínculo: [https://aka.ms/trustedlaunch](../../../../articles/virtual-machines/trusted-launch.md). |5 |1.0.0-preview |
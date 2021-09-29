---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8d7c78a30aa865ef1a2c0aecbb5a47b735d25112
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655106"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Los dispositivos Azure Stack Edge deben usar cifrado doble](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Para proteger los datos en reposo del dispositivo, asegúrese de que tienen cifrado doble, se controla el acceso a ellos y, una vez desactivado el dispositivo, se borran de los discos de datos de forma segura. El cifrado doble consiste en dos capas de cifrado: XTS-AES de BitLocker de 256 bits en los volúmenes de datos y cifrado integrado en los discos duros. Más información en la documentación de información general sobre seguridad del dispositivo Stark Edge en cuestión. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |

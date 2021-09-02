---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 577e0d6f7b08c5ba5957e0225689cac3ce0f93ac
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106872"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Las cuentas de HPC Cache deben usar la clave administrada por el cliente para el cifrado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Administre el cifrado en reposo de Azure HPC Cache con claves administradas por el cliente. De manera predeterminada, los datos del cliente se cifran con claves administradas por el servicio, pero las claves administradas por el cliente suelen ser necesarias para cumplir estándares de cumplimiento normativo. Las claves administradas por el cliente permiten cifrar los datos con una clave de Azure Key Vault creada por el usuario y propiedad de este. Tiene control y responsabilidad totales del ciclo de vida de la clave, incluidos la rotación y administración. |Audit, Disabled, Deny |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |

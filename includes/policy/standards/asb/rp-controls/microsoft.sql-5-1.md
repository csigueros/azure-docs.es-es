---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d20c2cd20b30c9d17aa2cf6467a11b1415560b0a
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122635703"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La evaluación de vulnerabilidades debe estar habilitada en Instancia administrada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Audita cada servicio SQL Managed Instance que no tiene habilitado los exámenes de evaluación de vulnerabilidades periódicos. La evaluación de vulnerabilidades permite detectar las vulnerabilidades potenciales de la base de datos, así como hacer un seguimiento y ayudar a corregirlas. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[La evaluación de vulnerabilidades debe estar activada en sus servidores de SQL Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Audita los servidores Azure SQL Server que no tienen habilitados los exámenes de evaluación de vulnerabilidades periódicos. La evaluación de vulnerabilidades permite detectar las vulnerabilidades potenciales de la base de datos, así como hacer un seguimiento y ayudar a corregirlas. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

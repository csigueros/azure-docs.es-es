---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0d09b938f820a1f1754c80c1881f0235652bfce1
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605294"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Configurar los recursos de Azure Migrate para usar zonas DNS privadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |Use zonas DNS privadas para invalidar la resolución DNS de un punto de conexión privado. Una zona DNS privada se vincula a la red virtual para resolver el proyecto de Azure Migrate. Más información en: [https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |
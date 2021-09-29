---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 09899ce768daca6dbd960d67d8d03aca5cec56a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654988"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Los dominios administrados de Azure Active Directory Domain Services solo deben usar el modo TLS 1.2.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Use solo el modo TLS 1.2 para los dominios administrados. De manera predeterminada, Azure AD Domain Services permite el uso de cifrados tales como NTLM v1 y TLS v1. Aunque estos cifrados pueden ser necesarios para algunas aplicaciones heredadas, se consideran poco seguros y se pueden deshabilitar si no se necesitan. Cuando solo se habilita el modo TLS 1.2, cualquier cliente que realice una solicitud que no use TLS 1.2 producirá un error. Obtenga más información en [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](../../../../articles/active-directory-domain-services/secure-your-domain.md). |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
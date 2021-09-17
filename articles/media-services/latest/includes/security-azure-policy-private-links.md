---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 08/17/2021
ms.author: johndeu
ms.openlocfilehash: d6641a3ca8181f7b3c8538179623549cc92a098a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429173"
---
## <a name="azure-policy-definitions-for-private-links"></a>Definiciones de Azure Policy para instancias de Private Link

Azure Media Services admite varias definiciones de [Azure Policy](../../../governance/policy/overview.md) integradas que se basan en casos de uso. 

Las siguientes definiciones de directiva están disponibles para su uso con instancias de Private Link:

|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Las cuentas de Azure Media Services deben usar una API que admita Private Link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Las cuentas de Media Services se deben crear con una API que admita vínculo privado. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[Azure Media Services debe usar un vínculo privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a591bf5-918e-4a5f-8dad-841863140d61) |Azure Private Link permite conectar las redes virtuales a los servicios de Azure sin una dirección IP pública en el origen o el destino. La plataforma Private Link administra la conectividad entre el consumidor y los servicios a través de la red troncal de Azure. Al asignar puntos de conexión privados a Media Services, se puede reducir el riesgo de pérdida de datos. Más información sobre los vínculos privados en [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_AuditIfNotExists.json) |
|[Configuración de Azure Media Services para usar zonas DNS privadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4a7f6c1-585e-4177-ad5b-c2c93f4bb991) |Use zonas DNS privadas para invalidar la resolución DNS de un punto de conexión privado. Una zona DNS privada se vincula a la red virtual para resolverse en las cuentas de Media Services. Más información en: [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLinkDns_DeployIfNotExists.json) |
|[Configuración de Azure Media Services con puntos de conexión privados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5632066-946d-4766-9544-cd79bcc1286e) |Los puntos de conexión privados conectan las redes virtuales a los servicios de Azure sin una dirección IP pública en el origen o el destino. Al asignar puntos de conexión privados a Media Services, se puede reducir el riesgo de pérdida de datos. Más información sobre los vínculos privados en [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_DeployIfNotExists.json) |

Consulte [Azure Policy para Media Services](../security-azure-policy.md) para obtener una lista completa de todas las definiciones de directivas integradas y casos de uso.

Para más información sobre cómo implementar y usar definiciones de Azure Policy, consulte el artículo ["¿Qué es Azure Policy?"](../../../governance/policy/overview.md)
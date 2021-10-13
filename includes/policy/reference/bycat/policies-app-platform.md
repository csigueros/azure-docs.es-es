---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 1ecc71be5243581576687620e72dd4caca63debe
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810316"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Versión preliminar\]: Auditoría de las instancias de Azure Spring Cloud en las que el seguimiento distribuido no está habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Las herramientas de seguimiento distribuido de Azure Spring Cloud permiten depurar y supervisar las complejas interconexiones entre los microservicios de una aplicación. Las herramientas de seguimiento distribuido deben estar habilitadas y en un estado correcto. |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud debe usar la inserción de red](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Las instancias de Azure Spring Cloud deberían utilizar la inserción de red virtual con los fines siguientes: 1. Aislar Azure Spring Cloud de Internet. 2. Permitir que Azure Spring Cloud interactúe con sistemas en centros de datos locales o con el servicio de Azure en otras redes virtuales. 3. Permite a los clientes controlar las comunicaciones de red entrantes y salientes para Azure Spring Cloud. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |

---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: c67a87f660c0e32e0655fe333fb40b6c60319857
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810361"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[El cifrado en reposo de Azure Data Explorer debe usar una clave administrada por el cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Al habilitar el cifrado en reposo con una clave administrada por el cliente en el clúster de Azure Data Explorer, se proporciona un mayor control sobre la clave que usa el cifrado en reposo. Esta característica se suele aplicar a los clientes con requisitos de cumplimiento especiales y requiere un almacén de claves para administrar las claves. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[El cifrado de disco debe estar habilitado en Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |La habilitación del cifrado de disco ayuda a custodiar y proteger sus datos con el fin de satisfacer los compromisos de cumplimiento y seguridad de la organización. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[El cifrado doble debe estar habilitado en Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |La habilitación del cifrado doble ayuda a custodiar y proteger sus datos con el fin de satisfacer los compromisos de cumplimiento y seguridad de la organización. Cuando está habilitado el cifrado doble, los datos de las cuentas de almacenamiento se cifran dos veces, una vez en el nivel de servicio y otra en el nivel de infraestructura, con dos algoritmos de cifrado y dos claves diferentes. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[La inserción de red virtual debe estar habilitada para Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Proteja el perímetro de la red con la inserción de red virtual, que le permite aplicar reglas de grupo de seguridad de red, conectarse de forma local y proteger los orígenes de la conexión de datos con puntos de conexión de servicio. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

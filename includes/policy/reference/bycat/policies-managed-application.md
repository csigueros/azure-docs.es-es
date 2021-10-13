---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 21178fb4e03c0a7b9b81a2f54492c183a7fa8dd8
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810644"
---
|Nombre<br /><sub>(Azure Portal)</sub> |Descripción |Efectos |Versión<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La definición de aplicación para la aplicación administrada debe usar la cuenta de almacenamiento proporcionada por el cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633). |Use su propia cuenta de almacenamiento para controlar los datos de definición de la aplicación cuando se trate de un requisito de cumplimiento normativo. Puede optar por almacenar la definición de aplicación administrada en la cuenta de almacenamiento que ha proporcionado durante la creación para que pueda administrar totalmente su ubicación y acceso para cumplir con los requisitos de cumplimiento normativo. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Implementación de asociaciones para aplicaciones administradas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Implementa un recurso de asociación que asocia los tipos de recursos seleccionados con la aplicación administrada especificada.  Esta implementación de directiva no admite los tipos de recursos anidados. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |

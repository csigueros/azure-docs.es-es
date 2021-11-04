---
title: Error RequestDisallowedByPolicy
description: Describe la causa del error RequestDisallowedByPolicy al implementar recursos con Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ef38591fb47f7d7dcfce96551c756b11a42b2a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093250"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Error RequestDisallowedByPolicy con la directiva de recursos de Azure

Este artículo describe la causa del error RequestDisallowedByPolicy y también proporciona soluciones para este error.

## <a name="symptom"></a>Síntoma

Durante la implementación, es posible que reciba un error **RequestDisallowedByPolicy** que impida crear los recursos. El siguiente ejemplo muestra el error:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Solución de problemas

Para recuperar los detalles de la directiva que bloqueó la implementación, use uno de los métodos siguientes:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

En PowerShell, proporcione ese identificador de directiva como el parámetro `Id` para recuperar los detalles de la directiva que bloqueó la implementación.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

En la CLI de Azure, proporcione el nombre de la definición de directiva:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solución

Por motivos de seguridad o conformidad, los administradores de la suscripción deben asignar directivas que limiten la forma en que se implementan los recursos. Por ejemplo, la suscripción puede tener una directiva que impide crear direcciones IP públicas, grupos de seguridad de red, rutas definidas por el usuario o tablas de rutas. En el mensaje de error de la sección **Síntoma** aparece el nombre de la directiva.
Para resolver este problema, revise las directivas de recursos y determine cómo implementar los recursos que cumplen con dichas directivas.

Para más información, consulte los siguientes artículos.

- [¿Qué es Azure Policy?](../../governance/policy/overview.md)
- [Creación y administración de directivas para aplicar el cumplimiento](../../governance/policy/tutorials/create-and-manage.md)

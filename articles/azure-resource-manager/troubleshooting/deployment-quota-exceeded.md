---
title: Cuota de implementaciones superada
description: En este artículo se describe cómo resolver el error de tener más de 800 implementaciones en el historial del grupo de recursos.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 64845ad3aed7dcccb7623a84552459ac9de78945
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489858"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Resolución de error cuando el recuento de implementaciones es superior a 800

Cada grupo de recursos está limitado a 800 implementaciones en su historial de implementaciones. En este artículo se describe el error que se recibe cuando se produce un error en una implementación porque se superarían las 800 implementaciones permitidas. Para resolver este error, elimine implementaciones del historial del grupo de recursos. La eliminación de una implementación del historial no afecta a ninguno de los recursos implementados.

Azure Resource Manager elimina automáticamente las implementaciones del historial a medida que se acerca al límite. Puede seguir viendo este error por uno de los motivos siguientes:

1. Tiene un bloqueo [CanNotDelete](../management/lock-resources.md) en el grupo de recursos que evita las eliminaciones en el historial de implementaciones.
1. No participa en las eliminaciones automáticas.
1. Tiene un gran número de implementaciones que se ejecutan simultáneamente y las eliminaciones automáticas no se procesan lo suficientemente rápido como para reducir el número total.

Para obtener información sobre cómo quitar un bloqueo o participar en las eliminaciones automáticas, vea [Eliminaciones automáticas del historial de implementaciones](../templates/deployment-history-deletions.md).

En este artículo se describe cómo eliminar manualmente las implementaciones del historial.

## <a name="symptom"></a>Síntoma

Durante una implementación se recibe un error que indica que la implementación actual va a superar la cuota de 800 implementaciones.

## <a name="solution"></a>Solución

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Utilice el comando [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete) para eliminar implementaciones del historial.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Para eliminar todas las implementaciones de más de cinco días, use:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Puede obtener el recuento actual del historial de implementaciones con el siguiente comando:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilice el comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) para eliminar implementaciones del historial.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Para eliminar todas las implementaciones de más de cinco días, use:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object -Property Timestamp -LT -Value ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Puede obtener el recuento actual del historial de implementaciones con el siguiente comando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

---

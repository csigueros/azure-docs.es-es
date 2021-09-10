---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bf6960556dec2c9ddd081b9cd4a66274710350ba
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536005"
---
### <a name="count-of-virtual-machines-by-availability-state-and-subscription-id"></a>Recuento de máquinas virtuales por estado de disponibilidad e identificador de suscripción

Devuelve el recuento de máquinas virtuales (tipo `Microsoft.Compute/virtualMachines`) agregadas por su estado de disponibilidad en cada una de las suscripciones.

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-and-associated-availability-states-by-resource-ids"></a>Lista de máquinas virtuales y estados de disponibilidad asociados por identificadores de recursos

Devuelve la lista más reciente de máquinas virtuales (tipo `Microsoft.Compute/virtualMachines`) agregadas por estado de disponibilidad. La consulta también proporciona el identificador de recurso asociado basado en `properties.targetResourceId` para facilitar la depuración y la mitigación. Los estados de disponibilidad pueden ser uno de los cuatro valores: Disponible, No disponible, Degradado y Desconocido. Para obtener más información sobre lo que significa cada uno de los estados de disponibilidad, consulte [Información general sobre Azure Resource Health](../../../../articles/service-health/resource-health-overview.md#health-status).

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups"></a>Lista de máquinas virtuales por estado de disponibilidad y estado de energía con identificadores de recursos y grupos de recursos

Devuelve una lista de máquinas virtuales (tipo `Microsoft.Compute/virtualMachines`) agregadas en su estado de energía y estado de disponibilidad para proporcionar un estado de mantenimiento coherente para las máquinas virtuales. La consulta también proporciona detalles sobre el grupo de recursos y el identificador de recurso asociados a cada entrada para una visibilidad detallada de los recursos.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code)
| join kind=leftouter (
    HealthResources
    | where type =~ 'microsoft.resourcehealth/availabilitystatuses'
    | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines'
    | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState))
    on $left.Id == $right.targetResourceId
| project-away targetResourceId
| where PowerState != 'PowerState/deallocated'
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code) | join kind=leftouter ( HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines' | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)) on $left.Id == $right.targetResourceId | project-away targetResourceId | where PowerState != 'PowerState/deallocated'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code) | join kind=leftouter ( HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines' | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)) on $left.Id == $right.targetResourceId | project-away targetResourceId | where PowerState != 'PowerState/deallocated'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-that-are-not-available-by-resource-ids"></a>Lista de máquinas virtuales que no están disponibles por identificadores de recursos

Devuelve la lista más reciente de máquinas virtuales (tipo `Microsoft.Compute/virtualMachines`) agregadas por su estado de disponibilidad. La lista rellenada solo resalta las máquinas virtuales cuyo estado de disponibilidad no es "Disponible" para asegurarse de que conoce todos los estados relacionados en los que se encuentran las máquinas virtuales. Cuando todas las máquinas virtuales estén disponibles, puede esperar no recibir ningún resultado.

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| where tostring(properties.availabilityState) != 'Available'
| summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.availabilityState) != 'Available' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.availabilityState) != 'Available' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---


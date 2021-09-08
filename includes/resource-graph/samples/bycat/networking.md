---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f309bd9e75abdaace892273c0ef74e79cad80c80
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535175"
---
### <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a>Count resources that have IP addresses configured by subscription

Con la consulta de ejemplo "Lista de todas las direcciones IP públicas" y al agregar `summarize` y `count()`, se puede obtener una lista por suscripción de recursos con direcciones IP configuradas.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty(properties.ipAddress)%0a%7c%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty(properties.ipAddress)%0a%7c%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty(properties.ipAddress)%0a%7c%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a>

---

### <a name="get-virtual-networks-and-subnets-of-network-interfaces"></a>Obtención de redes virtuales y subredes de interfaces de red

Use una expresión regular `parse` para obtener los nombres de red virtual y de subred de la propiedad de identificador de recurso. Aunque `parse` permite obtener datos de un campo complejo, es óptimo acceder directamente a las propiedades si existen, en lugar de usar `parse`.

```kusto
Resources
| where type =~ 'microsoft.network/networkinterfaces'
| project id, ipConfigurations = properties.ipConfigurations
| mvexpand ipConfigurations
| project id, subnetId = tostring(ipConfigurations.properties.subnet.id)
| parse kind=regex subnetId with '/virtualNetworks/' virtualNetwork '/subnets/' subnet
| project id, virtualNetwork, subnet
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networkinterfaces' | project id, ipConfigurations = properties.ipConfigurations | mvexpand ipConfigurations | project id, subnetId = tostring(ipConfigurations.properties.subnet.id) | parse kind=regex subnetId with '/virtualNetworks/' virtualNetwork '/subnets/' subnet | project id, virtualNetwork, subnet"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networkinterfaces' | project id, ipConfigurations = properties.ipConfigurations | mvexpand ipConfigurations | project id, subnetId = tostring(ipConfigurations.properties.subnet.id) | parse kind=regex subnetId with '/virtualNetworks/' virtualNetwork '/subnets/' subnet | project id, virtualNetwork, subnet"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%7c%20project%20id%2c%20ipConfigurations%20%3d%20properties.ipConfigurations%0a%7c%20mvexpand%20ipConfigurations%0a%7c%20project%20id%2c%20subnetId%20%3d%20tostring(ipConfigurations.properties.subnet.id)%0a%7c%20parse%20kind%3dregex%20subnetId%20with%20%27%2fvirtualNetworks%2f%27%20virtualNetwork%20%27%2fsubnets%2f%27%20subnet%0a%7c%20project%20id%2c%20virtualNetwork%2c%20subnet" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%7c%20project%20id%2c%20ipConfigurations%20%3d%20properties.ipConfigurations%0a%7c%20mvexpand%20ipConfigurations%0a%7c%20project%20id%2c%20subnetId%20%3d%20tostring(ipConfigurations.properties.subnet.id)%0a%7c%20parse%20kind%3dregex%20subnetId%20with%20%27%2fvirtualNetworks%2f%27%20virtualNetwork%20%27%2fsubnets%2f%27%20subnet%0a%7c%20project%20id%2c%20virtualNetwork%2c%20subnet" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%7c%20project%20id%2c%20ipConfigurations%20%3d%20properties.ipConfigurations%0a%7c%20mvexpand%20ipConfigurations%0a%7c%20project%20id%2c%20subnetId%20%3d%20tostring(ipConfigurations.properties.subnet.id)%0a%7c%20parse%20kind%3dregex%20subnetId%20with%20%27%2fvirtualNetworks%2f%27%20virtualNetwork%20%27%2fsubnets%2f%27%20subnet%0a%7c%20project%20id%2c%20virtualNetwork%2c%20subnet" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-public-ip-addresses"></a>List all public IP addresses

De forma similar a la consulta "Visualización de los recursos que contienen almacenamiento", encontrará todos los tipos que contengan la palabra **publicIPAddresses**. Esta consulta amplía ese patrón para incluir solo los resultados donde **properties.ipAddress** `isnotempty`, para devolver solo **properties.ipAddress** y para definir `limit` a fin de limitar los resultados a los 100 primeros. Es posible que sea necesario escapar las comillas según su shell elegido.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty(properties.ipAddress)%0a%7c%20project%20properties.ipAddress%0a%7c%20limit%20100" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty(properties.ipAddress)%0a%7c%20project%20properties.ipAddress%0a%7c%20limit%20100" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty(properties.ipAddress)%0a%7c%20project%20properties.ipAddress%0a%7c%20limit%20100" target="_blank">portal.azure.cn</a>

---

### <a name="show-unassociated-network-security-groups"></a>Visualización de los grupos de seguridad de red sin asociar

Esta consulta devuelve los grupos de seguridad de red (NSG) que no están asociados a una interfaz de red o subred.

```kusto
Resources
| where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworksecuritygroups%27%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%0a%7c%20project%20name%2c%20resourceGroup%0a%7c%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworksecuritygroups%27%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%0a%7c%20project%20name%2c%20resourceGroup%0a%7c%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworksecuritygroups%27%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%0a%7c%20project%20name%2c%20resourceGroup%0a%7c%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---


---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9b3e559e2fe0ea02978593aa3d2725c4e1d3b71d
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536435"
---
### <a name="count-azure-resources"></a>Count Azure resources

Esta consulta devuelve el número de recursos de Azure que existen en las suscripciones a las que tiene acceso. También es una buena consulta para validar que el shell elegido tiene los componentes apropiados de Azure Resource Graph instalados y en funcionamiento.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.cn</a>

---

### <a name="list-impacted-resources-when-transferring-an-azure-subscription"></a>Enumeración de los recursos afectados al transferir una suscripción de Azure

Devuelve algunos de los recursos de Azure que se ven afectados al transferir una suscripción a otro directorio de Azure Active Directory (Azure AD). Tendrá que volver a crear algunos de los recursos que existían antes de la transferencia de la suscripción.

```kusto
Resources
| where type in (
    'microsoft.managedidentity/userassignedidentities',
    'microsoft.keyvault/vaults',
    'microsoft.sql/servers/databases',
    'microsoft.datalakestore/accounts',
    'microsoft.containerservice/managedclusters')
    or identity has 'SystemAssigned'
    or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true)
| summarize count() by type
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type in ( 'microsoft.managedidentity/userassignedidentities', 'microsoft.keyvault/vaults', 'microsoft.sql/servers/databases', 'microsoft.datalakestore/accounts', 'microsoft.containerservice/managedclusters') or identity has 'SystemAssigned' or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true) | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type in ( 'microsoft.managedidentity/userassignedidentities', 'microsoft.keyvault/vaults', 'microsoft.sql/servers/databases', 'microsoft.datalakestore/accounts', 'microsoft.containerservice/managedclusters') or identity has 'SystemAssigned' or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true) | summarize count() by type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.cn</a>

---

### <a name="list-resources-sorted-by-name"></a>List resources sorted by name

Esta consulta devuelve cualquier tipo de recurso, pero solo las propiedades **name** (nombre), **type** (tipo) y **location** (ubicación). Usa `order by` para ordenar las propiedades por la propiedad **name** en orden ascendente (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="remove-columns-from-results"></a>Eliminación de columnas de los resultados

En la consulta siguiente se usa `summarize` para contar los recursos por suscripción, `join` para combinarlos con los detalles de la suscripción de la tabla _ResourceContainers_ y, después, `project-away` para quitar algunas de las columnas.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.cn</a>

---

### <a name="show-resource-types-and-api-versions"></a>Mostrar tipos de recurso y versiones de API

Resource Graph usa principalmente la versión no preliminar más reciente de la API de un proveedor de recursos para `GET` (Obtener) las propiedades de los recursos durante una actualización. En algunos casos, la versión de la API usada se ha invalidado para proporcionar propiedades más actuales o ampliamente utilizadas en los resultados. La siguiente consulta detalla la versión de API que se usa para recopilar propiedades en cada tipo de recurso:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a>

---


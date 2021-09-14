---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60ce5f19b16926692da046eb4bd910fb9f9a1548
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536360"
---
### <a name="get-count-and-percentage-of-arc-enabled-servers-by-domain"></a>Obtención del recuento y el porcentaje de servidores habilitados para Arc por dominio

En esta consulta se resume la propiedad **domainName** en [servidores habilitados para Azure Arc](../../../../articles/azure-arc/servers/overview.md) y se usa un cálculo con `bin` para crear una columna **Pct** para el porcentaje de servidores habilitados para Arc por dominio.

```kusto
Resources
| where type == 'microsoft.hybridcompute/machines'
| project domain=tostring(properties.domainName)
| summarize Domains=make_list(domain), TotalMachineCount=sum(1)
| mvexpand EachDomain = Domains
| summarize PerDomainMachineCount = count() by tostring(EachDomain), TotalMachineCount
| extend Pct = 100 * bin(todouble(PerDomainMachineCount) / todouble(TotalMachineCount), 0.001)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.hybridcompute/machines' | project domain=tostring(properties.domainName) | summarize Domains=make_list(domain), TotalMachineCount=sum(1) | mvexpand EachDomain = Domains | summarize PerDomainMachineCount = count() by tostring(EachDomain), TotalMachineCount | extend Pct = 100 * bin(todouble(PerDomainMachineCount) / todouble(TotalMachineCount), 0.001)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.hybridcompute/machines' | project domain=tostring(properties.domainName) | summarize Domains=make_list(domain), TotalMachineCount=sum(1) | mvexpand EachDomain = Domains | summarize PerDomainMachineCount = count() by tostring(EachDomain), TotalMachineCount | extend Pct = 100 * bin(todouble(PerDomainMachineCount) / todouble(TotalMachineCount), 0.001)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%20domain%3dtostring(properties.domainName)%0a%7c%20summarize%20Domains%3dmake_list(domain)%2c%20TotalMachineCount%3dsum(1)%0a%7c%20mvexpand%20EachDomain%20%3d%20Domains%0a%7c%20summarize%20PerDomainMachineCount%20%3d%20count()%20by%20tostring(EachDomain)%2c%20TotalMachineCount%0a%7c%20extend%20Pct%20%3d%20100%20*%20bin(todouble(PerDomainMachineCount)%20%2f%20todouble(TotalMachineCount)%2c%200.001)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%20domain%3dtostring(properties.domainName)%0a%7c%20summarize%20Domains%3dmake_list(domain)%2c%20TotalMachineCount%3dsum(1)%0a%7c%20mvexpand%20EachDomain%20%3d%20Domains%0a%7c%20summarize%20PerDomainMachineCount%20%3d%20count()%20by%20tostring(EachDomain)%2c%20TotalMachineCount%0a%7c%20extend%20Pct%20%3d%20100%20*%20bin(todouble(PerDomainMachineCount)%20%2f%20todouble(TotalMachineCount)%2c%200.001)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%20domain%3dtostring(properties.domainName)%0a%7c%20summarize%20Domains%3dmake_list(domain)%2c%20TotalMachineCount%3dsum(1)%0a%7c%20mvexpand%20EachDomain%20%3d%20Domains%0a%7c%20summarize%20PerDomainMachineCount%20%3d%20count()%20by%20tostring(EachDomain)%2c%20TotalMachineCount%0a%7c%20extend%20Pct%20%3d%20100%20*%20bin(todouble(PerDomainMachineCount)%20%2f%20todouble(TotalMachineCount)%2c%200.001)" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-extensions-installed-on-an-azure-arc-enabled-server"></a>Enumeración de todas las extensiones instaladas en un servidor habilitado para Azure Arc

En primer lugar, esta consulta usa `project` en el tipo de recurso de máquina híbrida para obtener el identificador en mayúsculas (`toupper()`), obtener el nombre del equipo y el sistema operativo que se ejecuta en la máquina. Obtener el identificador de recurso en mayúsculas es una ayuda en la preparación para `join` a otra propiedad. A continuación, la consulta utiliza `join` con _leftouter_ como **tipo** para obtener las extensiones mediante la coincidencia de una `substring` en mayúsculas del id. de extensión. La parte del identificador antes de `/extensions/<ExtensionName>` tiene el mismo formato que el identificador de las máquinas virtuales, por lo que usamos esta propiedad para `join`. `summarize` se usa entonces con `make_list` en el nombre de la extensión de máquina virtual para combinar el nombre de cada extensión, donde _id_, _OSName_ y _ComputerName_ son iguales en una única propiedad de matriz. Por último, se ordena por _OSName_ en minúsculas con **asc**. De forma predeterminada, el valor predeterminado de `order by` es descendente.

```kusto
Resources
| where type == 'microsoft.hybridcompute/machines'
| project
    id,
    JoinID = toupper(id),
    ComputerName = tostring(properties.osProfile.computerName),
    OSName = tostring(properties.osName)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.hybridcompute/machines/extensions'
    | project
        MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.MachineId
| summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.hybridcompute/machines' | project id, JoinID = toupper(id), ComputerName = tostring(properties.osProfile.computerName), OSName = tostring(properties.osName) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines/extensions' | project  MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.MachineId | summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.hybridcompute/machines' | project id, JoinID = toupper(id), ComputerName = tostring(properties.osProfile.computerName), OSName = tostring(properties.osName) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines/extensions' | project  MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.MachineId | summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName | order by tolower(OSName) asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%0a%09id%2c%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09ComputerName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSName%20%3d%20tostring(properties.osName)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%2fextensions%27%0a%09%7c%20project%0a%09%09MachineId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.MachineId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20ComputerName%2c%20OSName%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%0a%09id%2c%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09ComputerName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSName%20%3d%20tostring(properties.osName)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%2fextensions%27%0a%09%7c%20project%0a%09%09MachineId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.MachineId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20ComputerName%2c%20OSName%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%0a%09id%2c%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09ComputerName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSName%20%3d%20tostring(properties.osName)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%2fextensions%27%0a%09%7c%20project%0a%09%09MachineId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.MachineId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20ComputerName%2c%20OSName%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="list-arc-enabled-servers-not-running-latest-released-agent-version"></a>Enumeración de los servidores habilitados para Arc que no ejecutan la versión del agente publicada más recientemente

Esta consulta devuelve todos los servidores habilitados para Arc que ejecutan una versión obsoleta del agente de Connected Machine. Los agentes que tengan el estado **Expirado** se excluirán de los resultados. La consulta usa _leftouter_ `join` para agrupar las recomendaciones de Advisor sobre los agentes de Connected Machine identificados como obsoletos, y las máquinas de equipo híbrido para filtrar cualquier agente que no se haya comunicado con Azure durante un periodo de tiempo.

```kusto
AdvisorResources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'HighAvailability'
| where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent'
| project
        id,
        JoinId = toupper(properties.resourceMetadata.resourceId),
        machineName = tostring(properties.impactedValue),
        agentVersion = tostring(properties.extendedProperties.installedVersion),
        expectedVersion = tostring(properties.extendedProperties.latestVersion)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.hybridcompute/machines'
    | project
        machineId = toupper(id),
        status = tostring (properties.status)
    ) on $left.JoinId == $right.machineId
| where status != 'Expired'
| summarize by id, machineName, agentVersion, expectedVersion
| order by tolower(machineName) asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'HighAvailability' | where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent' | project  id,  JoinId = toupper(properties.resourceMetadata.resourceId),  machineName = tostring(properties.impactedValue),  agentVersion = tostring(properties.extendedProperties.installedVersion),  expectedVersion = tostring(properties.extendedProperties.latestVersion) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines' | project  machineId = toupper(id),  status = tostring (properties.status) ) on $left.JoinId == $right.machineId | where status != 'Expired' | summarize by id, machineName, agentVersion, expectedVersion | order by tolower(machineName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'HighAvailability' | where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent' | project  id,  JoinId = toupper(properties.resourceMetadata.resourceId),  machineName = tostring(properties.impactedValue),  agentVersion = tostring(properties.extendedProperties.installedVersion),  expectedVersion = tostring(properties.extendedProperties.latestVersion) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines' | project  machineId = toupper(id),  status = tostring (properties.status) ) on $left.JoinId == $right.machineId | where status != 'Expired' | summarize by id, machineName, agentVersion, expectedVersion | order by tolower(machineName) asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.cn</a>

---


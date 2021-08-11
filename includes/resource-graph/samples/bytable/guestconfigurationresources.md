---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 07/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 97bf2986271e2c7317ffb6d1ff4dc315dcbcafe7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456890"
---
### <a name="count-machines-in-scope-of-guest-configuration-policies"></a>Recuento de máquinas en el ámbito de las directivas de configuración de invitado

Se muestra el recuento de máquinas virtuales de Azure y los servidores conectados de Arc en el ámbito para las asignaciones de [configuración de invitado de Azure Policy](../../../../articles/governance/policy/concepts/guest-configuration.md).

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.'
| project machine = tostring(vmid[(-1)]),type = tostring(vmid[(-3)])
| distinct machine, type
| summarize count() by type
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]),type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]),type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3d%20%27Invalid%20assignment%20package.%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2ctype%20%3d%20tostring(vmid%5b(-3)%5d)%0a%7c%20distinct%20machine%2c%20type%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3d%20%27Invalid%20assignment%20package.%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2ctype%20%3d%20tostring(vmid%5b(-3)%5d)%0a%7c%20distinct%20machine%2c%20type%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3d%20%27Invalid%20assignment%20package.%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2ctype%20%3d%20tostring(vmid%5b(-3)%5d)%0a%7c%20distinct%20machine%2c%20type%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-non-compliant-guest-configuration-assignments"></a>Recuento de asignaciones de configuración de invitado no compatibles

Se muestra un recuento de las máquinas no compatibles por [razón de asignación de configuración de invitado](../../../../articles/governance/policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration). Por motivos de rendimiento, los resultados se limitan a las 100 primeras.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| where properties.complianceStatus == 'NonCompliant'
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)]),
    name,
    status = tostring(properties.complianceStatus),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)
| summarize count() by resource, name
| order by count_
| limit 100
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | where properties.complianceStatus == 'NonCompliant' | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase) | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | where properties.complianceStatus == 'NonCompliant' | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase) | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%0a%09type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%0a%09name%2c%0a%09status%20%3d%20tostring(properties.complianceStatus)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%0a%7c%20summarize%20count()%20by%20resource%2c%20name%0a%7c%20order%20by%20count_%0a%7c%20limit%20100" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%0a%09type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%0a%09name%2c%0a%09status%20%3d%20tostring(properties.complianceStatus)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%0a%7c%20summarize%20count()%20by%20resource%2c%20name%0a%7c%20order%20by%20count_%0a%7c%20limit%20100" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%0a%09type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%0a%09name%2c%0a%09status%20%3d%20tostring(properties.complianceStatus)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%0a%7c%20summarize%20count()%20by%20resource%2c%20name%0a%7c%20order%20by%20count_%0a%7c%20limit%20100" target="_blank">portal.azure.cn</a>

---

### <a name="find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments"></a>Búsqueda de todas las razones por las que una máquina no es compatible con las asignaciones de configuración de invitado

Se muestran todas las [razones de la asignación de una configuración de invitado](../../../../articles/governance/policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) en una máquina determinada. Quite la primera cláusula `where` para incluir también las auditorías en las que la máquina es compatible.

```kusto
GuestConfigurationResources
| where properties.complianceStatus == 'NonCompliant'
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| extend machine = tostring(vmid[(-1)])
| where machine == 'MACHINENAME'
| project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    name,
    machine,
    resourceGroup,
    subscriptionId
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where properties.complianceStatus == 'NonCompliant' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | extend machine = tostring(vmid[(-1)]) | where machine == 'MACHINENAME' | project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase), resource = tostring(properties_latestAssignmentReport_resources.resourceId), name, machine, resourceGroup, subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where properties.complianceStatus == 'NonCompliant' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | extend machine = tostring(vmid[(-1)]) | where machine == 'MACHINENAME' | project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase), resource = tostring(properties_latestAssignmentReport_resources.resourceId), name, machine, resourceGroup, subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20extend%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%0a%7c%20where%20machine%20%3d%3d%20%27MACHINENAME%27%0a%7c%20project%20phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09name%2c%0a%09machine%2c%0a%09resourceGroup%2c%0a%09subscriptionId" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20extend%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%0a%7c%20where%20machine%20%3d%3d%20%27MACHINENAME%27%0a%7c%20project%20phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09name%2c%0a%09machine%2c%0a%09resourceGroup%2c%0a%09subscriptionId" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20where%20properties.complianceStatus%20%3d%3d%20%27NonCompliant%27%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20extend%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%0a%7c%20where%20machine%20%3d%3d%20%27MACHINENAME%27%0a%7c%20project%20phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09name%2c%0a%09machine%2c%0a%09resourceGroup%2c%0a%09subscriptionId" target="_blank">portal.azure.cn</a>

---

### <a name="query-details-of-guest-configuration-assignment-reports"></a>Consulta de detalles de los informes de asignaciones de configuración de invitado

Se muestran informes con los detalles del [motivo de la asignación de la configuración de invitado](../../../../articles/governance/policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration). En el ejemplo siguiente, la consulta devuelve solo los resultados en los que el nombre de la asignación de invitado es `installed_application_linux` y la salida contiene la cadena `Python` para enumerar todas las máquinas Linux en las que se ha instalado un paquete que incluye el nombre **Python**. Para consultar el cumplimiento de todas las máquinas de una asignación específica, quite la segunda cláusula `where`.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| where name in ('installed_application_linux')
| where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python'
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)]),
    name,
    status = tostring(properties.complianceStatus),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | where name in ('installed_application_linux') | where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | where name in ('installed_application_linux') | where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20where%20name%20in%20(%27installed_application_linux%27)%0a%7c%20where%20properties_latestAssignmentReport_resources_reasons.phrase%20contains%20%27Python%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%0a%09type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%0a%09name%2c%0a%09status%20%3d%20tostring(properties.complianceStatus)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20where%20name%20in%20(%27installed_application_linux%27)%0a%7c%20where%20properties_latestAssignmentReport_resources_reasons.phrase%20contains%20%27Python%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%0a%09type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%0a%09name%2c%0a%09status%20%3d%20tostring(properties.complianceStatus)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%0a%7c%20extend%20vmid%20%3d%20split(properties.targetResourceId%2c%27%2f%27)%0a%7c%20mvexpand%20properties.latestAssignmentReport.resources%0a%7c%20mvexpand%20properties_latestAssignmentReport_resources.reasons%0a%7c%20where%20name%20in%20(%27installed_application_linux%27)%0a%7c%20where%20properties_latestAssignmentReport_resources_reasons.phrase%20contains%20%27Python%27%0a%7c%20project%20machine%20%3d%20tostring(vmid%5b(-1)%5d)%2c%0a%09type%20%3d%20tostring(vmid%5b(-3)%5d)%2c%0a%09name%2c%0a%09status%20%3d%20tostring(properties.complianceStatus)%2c%0a%09resource%20%3d%20tostring(properties_latestAssignmentReport_resources.resourceId)%2c%0a%09phrase%20%3d%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)" target="_blank">portal.azure.cn</a>

---


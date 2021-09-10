---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 716ac7b7d1c3b6577b9eb7290e249b306a617254
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304116"
---
### <a name="count-of-os-update-installation-done"></a>Recuento de instalaciones de actualizaciones de SO realizadas

Devuelve una lista con los estados de las instalaciones de actualizaciones del SO que se han realizado en las máquinas en los últimos 7 días.

```kusto
PatchAssessmentResources
| where type !has 'softwarepatches'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount)
| where lTime > ago(7d)
| project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type !has 'softwarepatches' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount) | where lTime > ago(7d) | project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type !has 'softwarepatches' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount) | where lTime > ago(7d) | project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.cn</a>

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

### <a name="count-of-virtual-machines-by-power-state"></a>Número de máquinas virtuales por estado de energía

Devuelve el número de máquinas virtuales (tipo `Microsoft.Compute/virtualMachines`) clasificadas según su estado de energía. Para más información sobre los estados de energía, consulte [Introducción a los estados de energía](../../../../articles/virtual-machines/states-billing.md).

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.cn</a>

---

### <a name="count-virtual-machines-by-os-type"></a>Count virtual machines by OS type

Basado en la consulta anterior, todavía se limita por los recursos de Azure de tipo `Microsoft.Compute/virtualMachines`, pero ya no se limita el número de registros devueltos. En su lugar, hemos usado `summarize` y `count()` para definir cómo agrupar y agregar los valores por propiedad, que en este ejemplo es `properties.storageProfile.osDisk.osType`. Para un ejemplo del aspecto de esta cadena en el objeto completo, vea [explorar recursos: detección de máquinas virtuales](../../../../articles/governance/resource-graph/concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-virtual-machines-by-os-type-with-extend"></a>Recuento de máquinas virtuales por tipo de sistema operativo con extensión

Otra forma de escribir la consulta "Recuento de las máquinas virtuales por tipo de sistema operativo" consiste en aplicar `extend` a una propiedad y asignarle un nombre temporal para su uso dentro de la consulta, en este caso **os**. Después, `summarize` y `count()` usan **os** como en el ejemplo al que se hace referencia.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a>

---

### <a name="get-virtual-machine-scale-set-capacity-and-size"></a>Obtener la capacidad y el tamaño del conjunto de escalado de máquinas virtuales

Esta consulta busca los recursos del conjunto de escalado de máquinas virtuales y obtiene varios detalles, incluido el tamaño de la máquina virtual y la capacidad del conjunto de escalado. La consulta usa la función `toint()` para convertir la capacidad en un número para que se pueda ordenar. Por último, se cambia el nombre de las columnas por propiedades con nombre personalizadas.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-extensions-installed-on-a-virtual-machine"></a>Enumeración de todas las extensiones instaladas en una máquina virtual

En primer lugar, esta consulta usa `extend` en el tipo de recurso de máquinas virtuales para obtener el identificador en mayúsculas (`toupper()` el identificador), el nombre y el tipo del sistema operativo y el tamaño de la máquina virtual. Obtener el identificador de recurso en mayúsculas es una ayuda en la preparación para unirse a otra propiedad. A continuación, la consulta utiliza `join` con _leftouter_ como **tipo** para obtener las extensiones de máquina virtual mediante la coincidencia de una `substring` en mayúsculas del ID. de extensión. La parte del identificador antes de "/extensions/\<ExtensionName\>" tiene el mismo formato que el identificador de las máquinas virtuales, por lo que usamos esta propiedad para `join`. `summarize` se usa entonces con `make_list` en el nombre de la extensión de máquina virtual para combinar el nombre de cada extensión, donde _ID_, _OSName_, _OSType_ y _VMSize_ son iguales en una única propiedad de matriz. Por último, se aplica `order by` a _OSName_ en minúsculas con **asc**. De forma predeterminada, el valor predeterminado de `order by` es descendente.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| extend
    JoinID = toupper(id),
    OSName = tostring(properties.osProfile.computerName),
    OSType = tostring(properties.storageProfile.osDisk.osType),
    VMSize = tostring(properties.hardwareProfile.vmSize)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.compute/virtualmachines/extensions'
    | extend 
        VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.VMId
| summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend  VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend  VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="list-available-os-updates-for-all-your-machines-grouped-by-update-category"></a>Lista de las actualizaciones de SO disponibles para todas las máquinas agrupadas por categoría de actualización

Devuelve una lista de SO pendientes para las máquinas.

```kusto
PatchAssessmentResources
| where type !has 'softwarepatches'
| extend prop = parse_json(properties)
| extend lastTime = properties.lastModifiedDateTime
| extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType
| project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type !has 'softwarepatches' | extend prop = parse_json(properties) | extend lastTime = properties.lastModifiedDateTime | extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType | project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type !has 'softwarepatches' | extend prop = parse_json(properties) | extend lastTime = properties.lastModifiedDateTime | extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType | project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-linux-os-update-installation-done"></a>Lista de las instalaciones de actualizaciones realizadas en el SO Linux

Devuelve una lista con los estados de las instalaciones de actualizaciones del SO Linux Server que se han realizado en las máquinas en los últimos 7 días.

```kusto
PatchAssessmentResources
| where type has 'softwarepatches' and properties has 'version'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState
| sort by RunID
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type has 'softwarepatches' and properties has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState | sort by RunID"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type has 'softwarepatches' and properties has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState | sort by RunID"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.cn</a>

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

### <a name="list-of-windows-server-os-update-installation-done"></a>Lista de las instalaciones de actualizaciones realizadas en el SO Windows Server

Devuelve una lista con los estados de las instalaciones de actualizaciones del SO Windows Server que se han realizado en las máquinas en los últimos 7 días.

```kusto
PatchAssessmentResources
| where type has 'softwarepatches' and properties !has 'version'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState
| sort by RunID
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type has 'softwarepatches' and properties !has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState | sort by RunID"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type has 'softwarepatches' and properties !has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState | sort by RunID"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.cn</a>

---

### <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a>Enumeración de máquinas virtuales con su interfaz de red y dirección IP pública

Esta consulta usa dos comandos **leftouter** `join` para reunir las máquinas virtuales creadas con el modelo de implementación de Resource Manager, sus interfaces de red relacionadas y todas las direcciones IP públicas relacionadas con dichas interfaces.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces)
| mv-expand nic=properties.networkProfile.networkInterfaces
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic)
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id)
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations)
    | mv-expand ipconfig=properties.ipConfigurations
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
    on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a>

---

### <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a>Show all virtual machines ordered by name in descending order

Para enumerar solo las máquinas virtuales (que son de tipo `Microsoft.Compute/virtualMachines`), podemos hacer coincidir la propiedad **type** en los resultados. De forma similar a la consulta anterior, `desc` cambia el `order by` a descendente. `=~` en el tipo de coincidencia indica a Resource Graph que distinga mayúsculas de minúsculas.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a>Show first five virtual machines by name and their OS type

En esta consulta se usa `top` para recuperar solo cinco registros coincidentes que se ordenan por nombre. El tipo de recurso de Azure es `Microsoft.Compute/virtualMachines`. `project` indica a Azure Resource Graph qué propiedades se incluirán.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="summarize-virtual-machine-by-the-power-states-extended-property"></a>Resumen de la máquina virtual mediante la propiedad extendida de estados de energía

Esta consulta usa las [propiedades extendidas](../../../../articles/governance/resource-graph/concepts/query-language.md#extended-properties) de las máquinas virtuales para hacer un resumen por estados de energía.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.cn</a>

---

### <a name="virtual-machines-matched-by-regex"></a>Máquinas virtuales que coinciden con regex

Esta consulta busca las máquinas virtuales que coincidan con una [expresión regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conocida como _regex_). **matches regex \@** nos permite definir regex para coincidir, que es `^Contoso(.*)[0-9]+$`. Esa definición de regex se explica como:

- `^` - La coincidencia debe empezar al principio de la cadena.
- `Contoso`: la cadena distingue mayúsculas de minúsculas.
- `(.*)`: una coincidencia de subexpresión:
  - `.` - Coincide con cualquier carácter individual (excepto saltos de línea).
  - `*` - Coincide con el elemento anterior cero o más veces.
- `[0-9]` - Coincidencia de grupo caracteres para los números del 0 al 9.
- `+` - Coincide con el elemento anterior una o más veces.
- `$` - La coincidencia del elemento anterior se debe producir al final de la cadena.

Después de la coincidencia del nombre, la consulta proyecta el nombre y ordena por nombre de forma ascendente.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---


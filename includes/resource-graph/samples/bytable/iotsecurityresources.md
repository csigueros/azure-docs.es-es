---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 34079d499b5c583ea12fbaac5ba76d9e6139d5ea
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058207"
---
### <a name="count-all-sensors-by-type"></a>Recuento de todos los sensores por tipo

Esta consulta resume todos los sensores de acuerdo con su tipo (OT, EIoT).

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sensors'
| summarize count() by tostring(properties.sensorType)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-how-many-iot-devices-there-are-in-your-network-by-operation-system"></a>Recuento del número de dispositivos IoT que hay en la red, por sistema operativo

Esta consulta resume todos los dispositivos IoT según la plataforma de su sistema operativo.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/devices'
| summarize count() by tostring(properties.operatingSystem.platform)
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.cn</a>

---

### <a name="get-all-high-severity-recommendations"></a>Obtención de todas las recomendaciones de gravedad alta

Esta consulta proporciona una lista de todas las recomendaciones de gravedad alta del usuario.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations'
| where properties.severity == 'High'
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-sites-with-a-specific-tag-value"></a>Enumeración de los sitios con un valor de etiqueta específico

Esta consulta proporciona una lista de todos los sitios con un valor de etiqueta específico.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sites'
| where properties.tags['key'] =~ 'value1'
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png":::Pruebe a ejecutar esta consulta en el Explorador de Azure Resource Graph:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.com</a>
- Portal de Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.us</a>
- Portal de Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.cn</a>

---


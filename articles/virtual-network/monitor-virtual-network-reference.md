---
title: Supervisión de la referencia de datos de red virtual de Azure
description: Material de referencia importante necesario al supervisar una red virtual de Azure
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 2494c0c0cdb2b4a7b06b4cf246f70a46deb8a3a4
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109870"
---
# <a name="monitoring-azure-virtual-network-data-reference"></a>Supervisión de la referencia de datos de red virtual de Azure

Consulte [Supervisión de una red virtual de Azure](monitor-virtual-network.md) para más información sobre la recopilación y el análisis de datos de supervisión de las redes virtuales de Azure.

## <a name="metrics"></a>Métricas

En esta sección se enumeran todas las métricas de la plataforma recopiladas automáticamente para una red virtual de Azure.  

| Tipo de métrica | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Red virtual | [Microsoft.Network/virtualNetworks](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkvirtualnetworks) |
| Interfaz de red | [Microsoft.Network/networkInterfaces](../azure-monitor/essentials/metrics-supported.md#microsoftnetworknetworkinterfaces) |
| Dirección IP pública | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) |
| Puertas de enlace NAT | [Microsoft.Network/natGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)

Para obtener más información, vea una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Dimensiones de métricas

Para obtener más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Una red virtual de Azure tiene las siguientes dimensiones asociadas a sus métricas.

### <a name="dimensions-for-nat-gateway"></a>Dimensiones para la puerta de enlace NAT

| Nombre de dimensión | Descripción |
| ------------------- | ----------------- |
| **Dirección (Salida - Entrada)** | La dirección del flujo de tráfico. Los valores admitidos son In (Entrada) y Out (Salida). |
| **Protocolo** | El tipo de protocolo de transporte. Los valores admitidos son TCP y UDP. |

## <a name="resource-logs"></a>Registros del recurso

En esta sección se enumeran los tipos de registros de recursos que se pueden recopilar para los recursos que se usan con la red virtual de Azure. 

Como referencia, vea una lista de [todos los tipos de categorías de registros de recursos admitidos en Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

|Tipo de registro de recursos | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Grupo de seguridad de red | [Microsoft.Network/networksecuritygroups](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworknetworksecuritygroups) |
| Dirección IP pública | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkpublicipaddresses) |

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor

En esta sección se hace referencia a todas las tablas de Kusto de registros de Azure Monitor relacionadas con una red virtual de Azure y que están disponibles para consulta por Log Analytics. 

|Tipo de recurso | Notas |
|-------|-----|
| Red virtual | [Microsoft.Network/virtualNetworks](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-networks) |
| Interfaz de red | [Microsoft.Network/networkInterface](/azure/azure-monitor/reference/tables/tables-resourcetype#network-interfaces) |
| Dirección IP pública | [Microsoft.Network/publicIP](/azure/azure-monitor/reference/tables/tables-resourcetype#public-ip-addresses) |

### <a name="diagnostics-tables"></a>Tablas de diagnóstico

**Red virtual**

La red virtual de Azure no tiene registros de diagnóstico.

## <a name="activity-log"></a>Registro de actividades

En la tabla siguiente se enumeran las operaciones relacionadas con una red virtual de Azure que se pueden crear en el registro de actividad.

| Operación | Descripción |
|:---|:---|
| Todas las operaciones administrativas | Todas las operaciones administrativas, incluidas la creación, actualización y eliminación de una red virtual de Azure. |
| Creación o actualización de una red virtual | Se creó o actualizó una red virtual. |
| Elimina una red virtual | Se eliminó una red virtual.|

Para más información sobre el esquema de las entradas del registro de actividad, consulte [Esquema del registro de actividad](../azure-monitor/essentials/activity-log-schema.md).

## <a name="see-also"></a>Vea también

- Para ver una descripción de la supervisión de una red virtual de Azure, consulte [Supervisión de una red virtual de Azure](monitor-virtual-network.md).
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

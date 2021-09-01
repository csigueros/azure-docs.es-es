---
title: Referencia de datos de supervisión de ExpressRoute
description: Material de referencia importante necesario al supervisar ExpressRoute
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: cd5a4ef0196843069d50f333089f8bffca956acc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289797"
---
# <a name="monitoring-expressroute-data-reference"></a>Referencia de datos de supervisión de ExpressRoute

En este artículo, se proporciona una referencia de los datos de registro y métricas recopilados para analizar el rendimiento y la disponibilidad de ExpressRoute.
Consulte [Supervisión de ExpressRoute](monitor-expressroute.md) para más información sobre la recopilación y el análisis de datos de supervisión de ExpressRoute.

## <a name="metrics"></a>Métricas

En esta sección, se enumeran todas las métricas de la plataforma recopiladas automáticamente para ExpressRoute. Para obtener más información, vea una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

| Tipo de métrica | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Circuito ExpressRoute | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
| Emparejamiento de circuitos de ExpressRoute | [Microsoft.Network/expressRouteCircuits/peerings](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuitspeerings) |
| Puertas de enlace de ExpressRoute | [Microsoft.Network/expressRouteGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutegateways) |
| ExpressRoute Direct | [Microsoft.Network/expressRoutePorts](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |

>[!NOTE]
> El uso de *GlobalGlobalReachBitsInPerSecond* y *GlobalGlobalReachBitsOutPerSecond* solo será visible si se ha establecido al menos una conexión Global Reach.
>

## <a name="metric-dimensions"></a>Dimensiones de métricas

Para obtener más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

ExpressRoute tiene las siguientes dimensiones asociadas a sus métricas.

### <a name="dimension-for-expressroute-circuit"></a>Dimensión del circuito de ExpressRoute

| Nombre de dimensión | Descripción |
| ------------------- | ----------------- |
| **PeeringType** | Tipo de emparejamiento configurado. Los valores admitidos son Emparejamiento privado y Microsoft. |
| **Emparejamiento** | Los valores válidos son: Principal y Secundario. |
| **PeeredCircuitSkey** | Clave de servicio del circuito de ExpressRoute remoto conectada mediante Global Reach. |

### <a name="dimension-for-expressroute-gateway"></a>Dimensión de la puerta de enlace de ExpressRoute

| Nombre de dimensión | Descripción |
| ------------------- | ----------------- |
| **roleInstance** | Instancia de puerta de enlace. Cada puerta de enlace de ExpressRoute consta de varias instancias y los valores admitidos son GatewayTenantWork_IN_X (donde X tiene un valor mínimo de 0 y un valor máximo del número de instancias de puerta de enlace -1). |

### <a name="dimension-for-express-direct"></a>Dimensión de Express Direct

| Nombre de dimensión | Descripción |
| ------------------- | ----------------- |
| **Vínculo** | Vínculo físico. Cada par de puertos de ExpressRoute Direct está formado por dos vínculos físicos para la redundancia y los valores admitidos son link1 y link2. |

## <a name="resource-logs"></a>Registros del recurso

En esta sección, se enumeran los tipos de registros de recursos que se pueden recopilar para ExpressRoute. 

|Tipo de registro de recursos | Espacio de nombres de proveedor de recursos/tipo<br/> y vínculo a métricas individuales |
|-------|-----|
| Circuito ExpressRoute | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkexpressroutecircuits) |

Como referencia, vea una lista de [todos los tipos de categorías de registros de recursos admitidos en Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor

Azure ExpressRoute usa tablas de Kusto de los registros de Azure Monitor. Puede consultar estas tablas con Log Analytics. Puede encontrar una referencia de todas las tablas de registros de Azure Monitor o de Log Analytics en [Referencia de la tabla de registros de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="activity-log"></a>Registro de actividades

En la tabla siguiente, se enumeran las operaciones relacionadas con ExpressRoute que se pueden crear en el registro de actividad.

| Operación | Descripción |
|:---|:---|
| Todas las operaciones administrativas | Todas las operaciones administrativas, incluidas la creación, actualización y eliminación de un circuito de ExpressRoute. |
| Creación o actualización de un circuito de ExpressRoute | Se ha creado o actualizado un circuito de ExpressRoute. |
| Eliminación del circuito de ExpressRoute | Se ha eliminado un circuito de ExpressRoute.|

Para más información sobre el esquema de las entradas del registro de actividad, consulte [Esquema del registro de actividad](../azure-monitor/essentials/activity-log-schema.md).

## <a name="schemas"></a>Esquemas

Para obtener una descripción detallada del esquema de registros de diagnóstico de nivel superior, consulte [Servicios, esquemas y categorías admitidos en los registros de diagnóstico de Azure](../azure-monitor/essentials/resource-logs-schema.md).

Al revisar las métricas mediante Log Analytics, la salida contendrá las columnas siguientes:

|**Columna**|**Tipo**|**Descripción**|
| --- | --- | --- |
|TimeGrain|string|PT1M (los valores de métricas se insertan cada minuto)|
|Count|real|Normalmente es igual a 2 (cada MSEE inserta un solo valor de métrica cada minuto)|
|Mínima|real|El mínimo de los dos valores de métrica insertados por los dos MSEE|
|Máxima|real|El máximo de los dos valores de métrica insertados por los dos MSEE|
|Average|real|Igual a (mínimo + máximo)/2|
|Total|real|Suma de los dos valores de métrica de ambos MSEE (el valor principal en el que se centrará para la métrica consultada)|

## <a name="see-also"></a>Vea también

- Consulte [Supervisión de Azure ExpressRoute](monitor-expressroute.md) para ver una descripción de la supervisión de Azure ExpressRoute.
- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
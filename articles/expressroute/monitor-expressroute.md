---
title: Supervisión de Azure ExpressRoute
description: Comience aquí para obtener información sobre cómo supervisar Azure ExpressRoute.
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 2e54166f71071a21b84e5f58e27c4ebc8a7df03a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587865"
---
# <a name="monitoring-azure-expressroute"></a>Supervisión de Azure ExpressRoute

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. 

En este artículo, se describen los datos de supervisión generados por Azure ExpressRoute. Azure ExpressRoute utiliza [Azure Monitor](../azure-monitor/overview.md). Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
 
## <a name="expressroute-insights"></a>ExpressRoute Insights

Algunos servicios en Azure tienen un panel de supervisión pregenerado enfocado especial en Azure Portal que proporciona un punto inicial para supervisar el servicio. Estos paneles especiales se llaman "conclusiones".

ExpressRoute usa Network Insights para proporcionar una asignación de topología detallada de todos los componentes de ExpressRoute (emparejamientos, conexiones, puertas de enlace) en relación entre sí. Network Insights para ExpressRoute también tiene un panel de métricas cargado previamente para la disponibilidad, el rendimiento, las pérdidas de paquetes y las métricas de puerta de enlace. Para más información, consulte [Azure ExpressRoute Insights mediante Network Insights](expressroute-network-insights.md).

## <a name="monitoring-data"></a>Supervisión de datos 

Azure ExpressRoute recopila los mismos tipos de datos de supervisión que otros recursos de Azure, que se describen en [Datos de supervisión de los recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte [Referencia de datos de supervisión de ExpressRoute](monitor-expressroute-reference.md) para obtener información detallada sobre las métricas y las métricas de registros creadas por Azure ExpressRoute.

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Las categorías de *Azure ExpressRoute* se enumeran en [Referencia de datos de supervisión de ExpressRoute](monitor-expressroute-reference.md#resource-logs).

> [!IMPORTANT]
> La habilitación de esta configuración requiere servicios adicionales de Azure (cuenta de almacenamiento, centro de eventos o Log Analytics), lo que puede aumentar el costo. Para calcular un costo estimado, visite la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator).

En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a>Análisis de métricas

Puede analizar las métricas de *Azure ExpressRoute* con las de otros servicios de Azure mediante el explorador de métricas; para ello, abra **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="Captura de pantalla del panel de métricas para ExpressRoute.":::

Como referencia, puede ver una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

* Para ver las métricas de **ExpressRoute**, filtre por el tipo de recurso *Circuitos de ExpressRoute*. 
* Para ver las métricas de **Global Reach**, filtre por el tipo de recurso *Circuitos de ExpressRoute* y seleccione un recurso de circuito de ExpressRoute que tenga Global Reach habilitado. 
* Para ver las métricas **ExpressRoute Direct** , filtre el Tipo de recurso por *Puertos ExpressRoute*. 

Una vez seleccionada una métrica, se aplicará la agregación predeterminada. Opcionalmente, puede aplicar la división, que mostrará la métrica con diferentes dimensiones.

## <a name="analyzing-logs"></a>Análisis de datos

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas.  

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquema de registros de recursos de Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). El esquema de los registros de recursos de ExpressRoute se encuentra en [Referencia de datos de supervisión de ExpressRoute](monitor-expressroute-reference.md#schemas). 

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un registro de la plataforma que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.

ExpressRoute almacena los datos en las tablas siguientes.

| Tabla | Descripción |
| ----- | ----------- |
| AzureDiagnostics | Tabla común que utilizan varios servicios para almacenar registros de recursos. Los registros de recursos de ExpressRoute se pueden identificar por `MICROSOFT.NETWORK`. |
| AzureMetrics | Datos de métricas emitidos por ExpressRoute que miden su estado y rendimiento. 

Para ver estas tablas, vaya al recurso del circuito de ExpressRoute y seleccione **Registros** en *Supervisión*.

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

Estas son algunas consultas que puede escribir en la barra de búsqueda de registros para ayudarle a supervisar los recursos de Azure ExpressRoute. Estas consultas funcionan con el [nuevo lenguaje](../azure-monitor/logs/log-query-overview.md).

* Para consultar la tabla de rutas de BGP aprendida en las últimas 12 horas.

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* Para consultar los mensajes informativos de BGP por nivel, tipo de recurso y red.

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* Para consultar el gráfico de tráfico BitInPerSeconds en la última hora.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* Para consultar el gráfico de tráfico BitOutPerSeconds en la última hora.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* Para consultar el gráfico de disponibilidad de ARP en intervalos de 5 minutos.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* Para consultar el gráfico de disponibilidad de BGP en intervalos de 5 minutos.

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

En la tabla siguiente, se enumeran las reglas de alertas comunes y recomendadas para ExpressRoute.

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
| Sin disponibilidad de ARP | Nombre de dimensión: tipo de emparejamiento, tipo de agregación: promedio, operador: menor que, valor del umbral: 100 % | Cuando no hay disponibilidad de ARP para un tipo de emparejamiento. |
| Sin disponibilidad de BGP | Nombre de dimensión: nodo del mismo nivel, tipo de agregación: promedio, operador: menor que, valor del umbral: 100 % | Cuando no hay disponibilidad de BGP para un nodo del mismo nivel. |

### <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para las conexiones de puerta de enlace de ExpressRoute

1. Para configurar las alertas, vaya a **Azure Monitor** y, a continuación, seleccione **Alertas**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Seleccione **+Seleccionar destino** y elija el recurso de conexión de puerta de enlace de ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Destino":::
3. Defina los detalles de la alerta.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="grupo de acciones":::
4. Defina y agregue el grupo de acciones.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="adición de grupo de acciones":::


### <a name="alerts-based-on-each-peering"></a>Alertas basadas en cada emparejamiento

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="cada emparejamiento":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configuración de alertas para los registros de actividad en circuitos

En **Criterios de alerta**, puede seleccionar **Registro de actividad** para el tipo de señal y seleccionar la señal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="registros de actividad":::

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Referencia de datos de supervisión de ExpressRoute](monitor-expressroute-reference.md) para obtener una referencia de las métricas, los registros y otros valores importantes creados por ExpressRoute.
* Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/overview.md).

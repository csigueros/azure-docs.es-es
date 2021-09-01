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
ms.openlocfilehash: 4f07659381c852e35585b5127d97c932a05088c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735269"
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

### <a name="aggregation-types"></a>Tipos de agregación

El explorador de métricas admite los [tipos de agregación](../azure-monitor/essentials/metrics-charts.md#aggregation) SUM, MAX, MIN, AVG y COUNT. Utilice el tipo de agregación recomendado al revisar la información de cada métrica de ExpressRoute.

* Suma: la suma de todos los valores capturados durante el intervalo de agregación. 
* Recuento: el número de medidas capturadas durante el intervalo de agregación. 
* Average: el promedio de los valores de la métrica capturados durante el intervalo de agregación. 
* Mínimo: el menor valor capturado durante el intervalo de agregación. 
* Máximo: el mayor valor capturado durante el intervalo de agregación.

>[!NOTE]
>No se recomienda el uso de **métricas clásicas**.
>

### <a name="circuits-metrics"></a>Métricas de circuito

#### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits dentro y fuera: métricas de todos los emparejamientos

Tipo de agregación: *Avg*

Puede ver las métricas de todos los emparejamientos de un circuito ExpressRoute determinado.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métricas de circuito":::

#### <a name="bits-in-and-out---metrics-per-peering"></a>Bits de entrada y salida: métricas por emparejamiento

Tipo de agregación: *Avg*

Puede ver las métricas de emparejamiento público, privado y de Microsoft en bits por segundo.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métricas por emparejamiento":::

#### <a name="bgp-availability---split-by-peer"></a>Disponibilidad de BGP: dividido por nodo del mismo nivel  

Tipo de agregación: *Avg*

Puede ver la disponibilidad casi en tiempo real de BGP (conectividad de nivel 3) entre emparejamientos y pares (enrutadores de ExpressRoute principales y secundarios). En este panel se muestra el estado de la sesión de BGP principal como activo para el emparejamiento privado y el estado de la segunda sesión BGP como inactivo para el emparejamiento privado. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilidad de BGP por par":::

### <a name="arp-availability---split-by-peering"></a>Disponibilidad de ARP: dividido por emparejamiento  

Tipo de agregación: *Avg*

Puede ver la disponibilidad casi en tiempo real de [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (conectividad de nivel 3) entre emparejamientos y pares (enrutadores de ExpressRoute principales y secundarios). En este panel se muestra el estado de la sesión de ARP del emparejamiento privado como activo entre ambos pares, pero inactivo para el emparejamiento de Microsoft para ambos pares. La agregación predeterminada (promedio) se usó en ambos pares.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilidad de ARP por par":::

### <a name="expressroute-direct-metrics"></a>Métricas de ExpressRoute Direct

#### <a name="admin-state---split-by-link"></a>Estado de administración: dividido por vínculo

Tipo de agregación: *Avg*

Puede ver el estado del administrador para cada vínculo del par de puertos de ExpressRoute Direct. El estado del administrador representa si el puerto físico está activo o inactivo. Este estado es necesario para pasar el tráfico a través de la conexión de ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Estado de administrador ER directo":::

#### <a name="bits-in-per-second---split-by-link"></a>Bits de entrada por segundo: dividido por vínculo

Tipo de agregación: *Avg*

Puede ver los bits por segundo en ambos enlaces del par de puertos directos de ExpressRoute. Supervise este panel para comparar el ancho de banda de entrada de ambos vínculos.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER bits directos por segundo":::

#### <a name="bits-out-per-second---split-by-link"></a>Bits de salida por segundo: dividido por vínculo

Tipo de agregación: *Avg*

También puede ver los bits por segundo en ambos enlaces del par de puertos directos de ExpressRoute. Supervise este panel para comparar el ancho de banda de salida de ambos vínculos.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER bits directos por segundo":::

#### <a name="line-protocol---split-by-link"></a>Protocolo de línea: dividido por vínculo

Tipo de agregación: *Avg*

Puede ver el protocolo de línea en cada enlace del par de puertos directos de ExpressRoute. El protocolo de línea indica si el vínculo físico está en funcionamiento a través de ExpressRoute Direct. Supervise este panel y establezca alertas para saber cuándo se ha interrumpido la conexión física.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocolo ER de línea directa":::

#### <a name="rx-light-level---split-by-link"></a>Nivel de luz de Rx: dividido por vínculo

Tipo de agregación: *Avg*

Puede ver el nivel de luz de Rx (el nivel de luz que el puerto directo de ExpressRoute está **recibiendo**) para cada puerto. Un nivel de luz de Rx correcto suele situarse en un intervalo de -10 dBm a 0 dBm. Establezca alertas para recibir una notificación si el nivel de luz de Rx está fuera del intervalo correcto.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Nivel de luz de recepción de línea directa de ER":::

#### <a name="tx-light-level---split-by-link"></a>Nivel de luz de Tx: dividido por vínculo

Tipo de agregación: *Avg*

Puede ver el nivel de luz Tx (el nivel de luz que el puerto directo de ExpressRoute **transmite**) para cada puerto. Un nivel de luz de Tx correcto suele situarse en un intervalo de -10 dBm a 0 dBm. Establezca alertas para recibir una notificación si el nivel de luz Tx está fuera del intervalo correcto.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Nivel de luz de recepción de línea directa de ER":::

### <a name="expressroute-virtual-network-gateway-metrics"></a>Métricas de puerta de enlace de red virtual de ExpressRoute

Tipo de agregación: *Avg*

Al implementar una puerta de enlace de ExpressRoute, Azure administra el proceso y las funciones de la puerta de enlace. Hay seis métricas de puerta de enlace disponibles para comprender mejor el rendimiento de la puerta de enlace:

* Uso de CPU
* Paquetes por segundo
* Recuento de rutas anunciadas al par
* Recuento de rutas aprendidas del par
* Frecuencia de cambio de rutas
* Número de máquinas virtuales en la red virtual  

Se recomienda establecer alertas para cada una de estas métricas para saber cuándo la puerta de enlace puede estar experimentando problemas de rendimiento.

#### <a name="cpu-utilization---split-instance"></a>Uso de CPU: dividido por instancia

Tipo de agregación: *Avg*

Puede ver el uso de CPU de cada una de las instancias de la puerta de enlace. El uso de CPU puede repuntar brevemente durante el mantenimiento rutinario del host, pero un elevado uso de CPU prolongado podría indicar que la puerta de enlace está alcanzando un cuello de botella de rendimiento. Este problema puede resolverse aumentando el tamaño de la puerta de enlace de ExpressRoute. Establezca una alerta sobre la frecuencia con la que el uso de CPU supera un umbral determinado.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Captura de pantalla del uso de CPU: métricas de división.":::

#### <a name="packets-per-second---split-by-instance"></a>Paquetes por segundo: dividido por instancia

Tipo de agregación: *Avg*

Esta métrica captura el número de paquetes entrantes que atraviesan la puerta de enlace de ExpressRoute. Aquí debería mostrarse un flujo coherente de datos si la puerta de enlace recibe tráfico de la red local. Establezca una alerta para cuando el número de paquetes por segundo caiga por debajo de un umbral, lo que indicará que la puerta de enlace ya no recibe tráfico.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Captura de pantalla de paquetes por segundo: métricas de división.":::

#### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Recuento de rutas anunciadas al nodo del mismo nivel: dividido por instancia

Tipo de agregación: *Count*

Esta métrica es el recuento del número de rutas que la puerta de enlace de ExpressRoute anuncia al circuito. Los espacios de direcciones pueden incluir redes virtuales que están conectadas mediante el emparejamiento de VNet y usan la puerta de enlace de ExpressRoute remota. El número de rutas debe ser coherente a menos que se produzcan cambios frecuentes en los espacios de direcciones de la red virtual. Establezca una alerta para cuando el número de rutas anunciadas caiga por debajo del umbral para el número de espacios de direcciones de red virtual que conozca.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Captura de pantalla del recuento de rutas anunciadas al par.":::

#### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Recuento de rutas aprendidas del nodo del mismo nivel: dividido por instancia

Tipo de agregación: *Max*

Esta métrica muestra el número de rutas que la puerta de enlace de ExpressRoute está aprendiendo de los pares conectados al circuito de ExpressRoute. Estas rutas pueden ser de otra red virtual conectada al mismo circuito o aprenderse del entorno local. Establezca una alerta para cuando el número de rutas aprendidas caiga por debajo de un umbral determinado. Un número elevado de rutas eliminadas podría indicar que la puerta de enlace está experimentando un problema de rendimiento o que los nodos del mismo nivel remotos ya no anuncian rutas al circuito de ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Captura de pantalla del recuento de rutas aprendidas del par.":::

#### <a name="frequency-of-routes-change---split-by-instance"></a>Frecuencia de cambio de rutas: dividido por instancia

Tipo de agregación: *Sum*

Esta métrica muestra la frecuencia de las rutas que se aprenden de pares remotos o se anuncian a pares remotos. En primer lugar, debe investigar los dispositivos locales para comprender por qué la red cambia con tanta frecuencia. Si las rutas cambian con mucha frecuencia podría existir un problema de rendimiento en la puerta de enlace de ExpressRoute, que podría resolverse escalando verticalmente la SKU de la puerta de enlace. Establezca una alerta para un umbral de frecuencia para saber cuándo la puerta de enlace de ExpressRoute experimenta cambios de ruta anómalos.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Captura de pantalla de la métrica de frecuencia de cambios en las rutas.":::

#### <a name="number-of-vms-in-the-virtual-network"></a>Número de máquinas virtuales en la red virtual

Tipo de agregación: *Max*

Esta métrica muestra el número de máquinas virtuales que usan la puerta de enlace de ExpressRoute. El número de máquinas virtuales puede incluir máquinas virtuales de redes virtuales emparejadas que usan la misma puerta de enlace de ExpressRoute. Establezca una alerta para esta métrica si el número de máquinas virtuales supera un umbral determinado que podría afectar al rendimiento de la puerta de enlace. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Captura de pantalla de la métrica del número de máquinas virtuales en la red virtual.":::

#### <a name="expressroute-gateway-connections-in-bitsseconds"></a>Conexiones de puerta de enlace de ExpressRoute en bits por segundo

Tipo de agregación: *Avg*

Esta métrica muestra el uso de ancho de banda para una conexión específica a un circuito de ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Captura de pantalla de la métrica de uso del ancho de banda de conexión de la puerta de enlace.":::

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

---
title: Supervisión de Azure Virtual WAN
description: Obtenga información sobre los registros y las métricas de Azure Virtual WAN mediante el uso de Azure Monitor.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 25e12ce4fd361cb053eae8b0d9992031a91d4616
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469022"
---
# <a name="monitoring-virtual-wan"></a>Supervisión de Virtual WAN

Puede supervisar Azure Virtual WAN mediante Azure Monitor. Virtual WAN es un servicio de red que aporta muchas funciones de red, seguridad y enrutamiento para proporcionar una única interfaz operativa. Las puertas de enlace de VPN de Virtual WAN, las puertas de enlace de ExpressRoute y Azure Firewall tienen registros y métricas disponibles a través de Azure Monitor.

En este artículo se describen las métricas y los diagnósticos que están disponibles a través del portal. Las métricas son ligeras y pueden admitir escenarios casi en tiempo real, lo que las hace útiles para alertas y detección rápida de problemas.

### <a name="monitoring-secured-hub-azure-firewall"></a>Supervisión del centro protegido (Azure Firewall) 

Puede supervisar el centro protegido mediante los registros de Azure Firewall. También puede usar los registros de actividad para auditar las operaciones de los recursos de Azure Firewall.

Si ha elegido proteger el centro virtual mediante Azure Firewall, los registros y las métricas pertinentes están disponibles aquí: [Métricas y registros de Azure Firewall](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Métricas

Las métricas de Azure Monitor son valores numéricos que describen algunos aspectos de un sistema en un momento dado. Las métricas se recopilan cada minuto y son útiles para las alertas porque se pueden muestrear con frecuencia. Una alerta puede activarse rápidamente con una lógica relativamente simple.

### <a name="site-to-site-vpn-gateways"></a>Puertas de enlace de VPN de sitio a sitio

Las siguientes métricas están disponibles para las puertas de enlace de VPN de sitio a sitio de Azure:

| Métrica | Descripción|
| --- | --- |
| **Ancho de banda de puerta de enlace** | Ancho de banda del agregado de sitio a sitio medio de una puerta de enlace en bytes por segundo.|
| **Ancho de banda de túnel** | Media de ancho de banda de un túnel en bytes por segundo.|
| **Bytes de salida de túnel** | Bytes de salida de un túnel. |
| **Paquetes de salida de túnel** | Recuento de paquetes de salida de un túnel. |
| **Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de salida** | Recuento de colocación de paquetes de salida con error de coincidencia del selector de tráfico de un túnel.|
| **Bytes de salida de túnel** | Bytes de entrada de un túnel.|
| **Paquete de entrada de túnel** | Recuento de paquetes de entrada de un túnel.|
| **Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de entrada** | Recuento de colocación de paquetes de entrada con error de coincidencia del selector de tráfico de un túnel.|

### <a name="point-to-site-vpn-gateways"></a>Puertas de enlace de VPN de punto a sitio

Las siguientes métricas están disponibles para puertas de enlace de VPN de punto a sitio de Azure:

| Métrica | Descripción|
| --- | --- |
| **Ancho de banda P2S de puerta de enlace** | Ancho de banda del agregado de punto a sitio medio de una puerta de enlace en bytes por segundo. |
| **Recuento de conexiones P2S** |Recuento de conexiones de punto a sitio de una puerta de enlace. Recuento de conexiones de punto a sitio de una puerta de enlace. Para asegurarse de que está viendo métricas precisas en Azure Monitor, seleccione **Aggregation Type** (Tipo de agregación) para **P2S Connection Count** (Recuento de conexiones P2S) como **Sum** (Suma). También puede seleccionar **Máximo** si divide por **Instancia**. |

### <a name="azure-expressroute-gateways"></a>Puertas de enlace de Azure ExpressRoute

Las siguientes métricas están disponibles para las puertas de enlace de Azure ExpressRoute:

| Métrica | Descripción|
| --- | --- |
| **BitsInPerSecond** | Bits que ingresan a Azure por segundo.|
| **BitsOutPerSecond** | Bits que salen de Azure por segundo. |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Visualización de métricas de puerta de enlace

Los siguientes pasos le ayudarán a ubicar y ver las métricas:

1. En el portal, navegue hasta el centro virtual que tiene la puerta de enlace.

2. Seleccione **VPN (sitio a sitio)** para buscar una puerta de enlace de sitio a sitio, **ExpressRoute** para buscar una puerta de enlace de ExpressRoute o **VPN de usuario (punto a sitio)** para buscar una puerta de enlace de punto a sitio. En la página, puede ver la información de la puerta de enlace. Copie esta información. La usará más adelante para ver los diagnósticos mediante Azure Monitor.

3. Seleccione **Métricas**.

   :::image type="content" source="./media/monitor-virtual-wan/metrics.png" alt-text="Captura de pantalla que muestra el panel VPN de sitio a sitio con la opción Ver en Azure Monitor seleccionada.":::

4. En la página **Métricas**, puede ver las métricas que le interesen.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="Captura de pantalla que muestra la página &quot;Métricas&quot; con las categorías resaltadas.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Registros de diagnóstico

### <a name="site-to-site-vpn-gateways"></a>Puertas de enlace de VPN de sitio a sitio

Los siguientes diagnósticos están disponibles para las puertas de enlace de VPN de sitio a sitio de Azure:

| Métrica | Descripción|
| --- | --- |
| **Registros de diagnóstico de puerta de enlace** | Diagnósticos específicos de la puerta de enlace, como el estado, la configuración o las actualizaciones del servicio, así como diagnósticos adicionales.|
| **Registros de diagnóstico de túnel** | Registros relacionados con el túnel de IPsec, como los eventos de conexión y desconexión para un túnel de IPsec de sitio a sitio, SA negociados, motivos de desconexión y diagnósticos adicionales.|
| **Registros de diagnóstico de ruta** | Registros relacionados con eventos para rutas estáticas, BGP, actualizaciones de ruta y diagnósticos adicionales. |
| **Registros de IKE Diagnostic** | Diagnósticos específicos de IKE para conexiones de IPsec. |

### <a name="point-to-site-vpn-gateways"></a>Puertas de enlace de VPN de punto a sitio

Los siguientes diagnósticos están disponibles para puertas de enlace de VPN de punto a sitio de Azure:

| Métrica | Descripción|
| --- | --- |
| **Registros de diagnóstico de puerta de enlace** | Diagnósticos específicos de la puerta de enlace, como el estado, la configuración o las actualizaciones del servicio, así como otros diagnósticos. |
| **Registros de IKE Diagnostic** | Diagnósticos específicos de IKE para conexiones de IPsec.|
| **Registros de P2S Diagnostic** | Configuración de punto a sitio y eventos de cliente de la VPN del usuario. Incluyen la conexión/desconexión del cliente y la asignación de direcciones de cliente de VPN, así como otros diagnósticos.|

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Visualización de los registros de diagnóstico

Los siguientes pasos le ayudarán a localizar y ver los diagnósticos:

1. En el portal, navegue hasta el recurso de Virtual WAN. En la sección **Información general** de la página Virtual WAN del portal, seleccione **Información esencial** para expandir la vista y obtener la información del grupo de recursos. Copie la información del grupo de recursos.

   :::image type="content" source="./media/monitor-virtual-wan/3.png" alt-text="Captura de pantalla que muestra la sección &quot;Información general&quot; con una flecha que apunta al botón &quot;Copiar&quot;.":::

2. Vaya a **Monitor** desde la barra de búsqueda y, en la sección Configuración, seleccione **Configuración de diagnóstico** y escriba el grupo de recursos, el tipo de recurso y la información de los recursos. Esta es la información del grupo de recursos que copió en el paso 2 de la sección [View gateway metrics](#metrics-steps) (Visualización de métricas de puerta de enlace) anteriormente en este artículo.

   :::image type="content" source="./media/monitor-virtual-wan/4.png" alt-text="Captura de pantalla que muestra la sección &quot;Supervisión&quot; con una flecha que apunta a la lista desplegable &quot;Recurso&quot;.":::

3. En la página de resultados, seleccione **+ Agregar configuración de diagnóstico** y, después, seleccione una opción. Puede optar por enviar a Log Analytics, transmitir a un centro de eventos o simplemente archivar en una cuenta de almacenamiento.

   :::image type="content" source="./media/monitor-virtual-wan/5.png" alt-text="página Métricas":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Consulta de ejemplo de Log Analytics

Los registros se encuentran en el **área de trabajo de Azure Log Analytics**. Puede configurar una consulta en Log Analytics. El ejemplo siguiente contiene una consulta para obtener los diagnósticos de ruta de sitio a sitio.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Reemplace los valores siguientes, después de **= =** , según sea necesario.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Registros de actividad

Las entradas del **registro de actividad** están habilitadas de forma predeterminada y se pueden ver en Azure Portal. Puede usar los registros de actividad de Azure (anteriormente conocidos como *registros operativos* y *registros de auditoría*) para ver todas las operaciones enviadas a su suscripción de Azure.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo supervisar las métricas y los registros de Azure Firewall, consulte [Tutorial: Supervisión de los registros de Azure Firewall](../firewall/firewall-diagnostics.md).
* Para más información sobre las métricas en Azure Monitor, consulte [Métricas en Azure monitor](../azure-monitor/essentials/data-platform-metrics.md).

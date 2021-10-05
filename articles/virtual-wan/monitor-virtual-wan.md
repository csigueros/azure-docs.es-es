---
title: Supervisión de Azure Virtual WAN
description: Obtenga información sobre los registros y las métricas de Azure Virtual WAN mediante el uso de Azure Monitor.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: d1ac031b79372987561651044e81da2e3d2d2779
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636542"
---
# <a name="monitoring-virtual-wan"></a>Supervisión de Virtual WAN

Puede supervisar Azure Virtual WAN mediante Azure Monitor. Virtual WAN es un servicio de red que aporta muchas funciones de red, seguridad y enrutamiento para proporcionar una única interfaz operativa. Las puertas de enlace de VPN de Virtual WAN, las puertas de enlace de ExpressRoute y Azure Firewall tienen registros y métricas disponibles a través de Azure Monitor. 

En este artículo se describen las métricas y los diagnósticos que están disponibles a través del portal. Las métricas son ligeras y pueden admitir escenarios casi en tiempo real, lo que las hace útiles para alertas y detección rápida de problemas.

### <a name="monitoring-secured-hub-azure-firewall"></a>Supervisión del centro protegido (Azure Firewall) 

Si ha elegido proteger el centro virtual mediante Azure Firewall, los registros y las métricas pertinentes están disponibles aquí: [Métricas y registros de Azure Firewall](../firewall/logs-and-metrics.md).
Puede supervisar el centro protegido mediante los registros y las métricas de Azure Firewall. También puede usar los registros de actividad para auditar las operaciones de los recursos de Azure Firewall.
Para cada instancia de Azure Virtual WAN que quiera proteger y convertir en un centro protegido, se crea un objeto de recurso de firewall explícito en el grupo de recursos donde se encuentra el centro. 

:::image type="content" source="./media/monitor-virtual-wan/firewall-resources-portal.png" alt-text="Captura de pantalla que muestra un recurso de firewall en el grupo de recursos del centro de vWAN.":::

La configuración de diagnóstico y registro debe realizarse desde allí, accediendo a la pestaña **Configuración de diagnóstico**:

:::image type="content" source="./media/monitor-virtual-wan/firewall-diagnostic-settings.png" alt-text="Captura de pantalla que muestra la pestaña Configuración de diagnóstico del firewall.":::


## <a name="metrics"></a>Métricas

Las métricas de Azure Monitor son valores numéricos que describen algunos aspectos de un sistema en un momento dado. Las métricas se recopilan cada minuto y son útiles para las alertas porque se pueden muestrear con frecuencia. Una alerta puede activarse rápidamente con una lógica relativamente simple.

### <a name="site-to-site-vpn-gateways"></a>Puertas de enlace de VPN de sitio a sitio

Las siguientes métricas están disponibles para las puertas de enlace de VPN de sitio a sitio de Azure:

#### <a name="tunnel-packet-drop-metrics"></a>Métricas de descarte de paquetes del túnel
| Métrica | Descripción|
| --- | --- |
| **Tunnel Egress Packet Drop Count** | Recuento de los paquetes salientes descartados por el túnel.|
| **Tunnel Ingress Packet Drop Count** | Recuento de los paquetes entrantes descartados por el túnel.|
| **Paquetes NAT de túnel descartados** | Número de paquetes con NAT en un túnel descartados por tipo de eliminación y regla NAT.|
| **Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de salida** | Recuento de colocación de paquetes de salida con error de coincidencia del selector de tráfico de un túnel.|
| **Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de entrada** | Recuento de colocación de paquetes de entrada con error de coincidencia del selector de tráfico de un túnel.|

#### <a name="ipsec-metrics"></a>Métricas de IPSEC
| Métrica | Descripción|
| --- | --- |
| **Tunnel MMSA Count** | Número de MMSA que se crean o se eliminan.|
| **Tunnel QMSA Count** | Número de QMSA de IPSEC que se crean o se eliminan.|

#### <a name="routing-metrics"></a>Métricas de enrutamiento
| Métrica | Descripción|
| --- | --- |
| **BGP Peer Status** | Estado de conectividad del protocolo de puerta de enlace de borde por pares y por instancias.|
| **BGP Routes Advertised** | Número de rutas anunciadas por pares y por instancias.|
| **BGP Routes Learned** | Número de rutas aprendidas por pares y por instancias.|
| **VNET Address Prefix Count** | Número de prefijos de dirección de red virtual que usa o anuncia la puerta de enlace.|

Puede revisar las métricas por pares e instancias si selecciona **Aplicar división** y elige el valor preferido. 

#### <a name="traffic-flow-metrics"></a>Métricas de flujo de tráfico
| Métrica | Descripción|
| --- | --- |
| **Ancho de banda de puerta de enlace** | Ancho de banda del agregado de sitio a sitio medio de una puerta de enlace en bytes por segundo.|
| **Ancho de banda de túnel** | Media de ancho de banda de un túnel en bytes por segundo.|
| **Bytes de salida de túnel** | Bytes de salida de un túnel. |
| **Paquetes de salida de túnel** | Recuento de paquetes de salida de un túnel. |
| **Bytes de salida de túnel** | Bytes de entrada de un túnel.|
| **Paquete de entrada de túnel** | Recuento de paquetes de entrada de un túnel.|
| **Tunnel Peak PPS** | Número de paquetes por segundo por conexión de vínculo en el último minuto.|
| **Tunnel Flow Count** | Número de flujos distintos creados por conexión de vínculo.|

### <a name="point-to-site-vpn-gateways"></a>Puertas de enlace de VPN de punto a sitio

Las siguientes métricas están disponibles para puertas de enlace de VPN de punto a sitio de Azure:

| Métrica | Descripción|
| --- | --- |
| **Ancho de banda P2S de puerta de enlace** | Ancho de banda del agregado de punto a sitio medio de una puerta de enlace en bytes por segundo. |
| **Recuento de conexiones P2S** |Recuento de conexiones de punto a sitio de una puerta de enlace. Recuento de conexiones de punto a sitio de una puerta de enlace. Para asegurarse de que está viendo métricas precisas en Azure Monitor, seleccione **Aggregation Type** (Tipo de agregación) para **P2S Connection Count** (Recuento de conexiones P2S) como **Sum** (Suma). También puede seleccionar **Máximo** si divide por **Instancia**. |
| **User VPN Routes Count** | Número de rutas VPN de usuario configuradas en VPN Gateway. Esta métrica se puede dividir en rutas **estáticas** y **dinámicas**.

### <a name="azure-expressroute-gateways"></a>Puertas de enlace de Azure ExpressRoute

Las siguientes métricas están disponibles para las puertas de enlace de Azure ExpressRoute:

| Métrica | Descripción|
| --- | --- |
| **BitsInPerSecond** | Bits que ingresan a Azure por segundo.|
| **BitsOutPerSecond** | Bits que salen de Azure por segundo. |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Visualización de métricas de puerta de enlace

Los siguientes pasos le ayudarán a ubicar y ver las métricas:

1. En el portal, navegue hasta el centro virtual que tiene la puerta de enlace.

2. Seleccione **VPN (sitio a sitio)** para buscar una puerta de enlace de sitio a sitio, **ExpressRoute** para buscar una puerta de enlace de ExpressRoute o **VPN de usuario (punto a sitio)** para buscar una puerta de enlace de punto a sitio.

3. Seleccione **Métricas**.

   :::image type="content" source="./media/monitor-virtual-wan/view-metrics.png" alt-text="Captura de pantalla que muestra el panel VPN de sitio a sitio con la opción Ver en Azure Monitor seleccionada.":::

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

### <a name="express-route-gateways"></a>Puertas de enlace de ExpressRoute

Los registros de diagnóstico de las puertas de enlace de Express Route de Azure Virtual WAN no se admiten.

### <a name="view-diagnostic-logs-configuration"></a><a name="diagnostic-steps"></a>Visualización de la configuración de registros de diagnóstico

Los pasos siguientes lo ayudarán a crear, editar y ver la configuración de diagnóstico:

1. En el portal, navegue hasta el recurso de Virtual WAN y, luego, seleccione **Centros** en el grupo **Conectividad**. 

   :::image type="content" source="./media/monitor-virtual-wan/select-hub.png" alt-text="Captura de pantalla que muestra la selección del centro en el portal de vWAN.":::

2. En el grupo **Conectividad** de la izquierda, seleccione la puerta de enlace en la que desea examinar los diagnósticos:

   :::image type="content" source="./media/monitor-virtual-wan/select-hub-gateway.png" alt-text="Captura de pantalla que muestra la sección Conectividad del centro.":::

3. En la parte derecha de la página, haga clic en el vínculo **Ver en Azure Monitor** situado a la derecha de **Registros** y seleccione una opción. Puede optar por enviar a Log Analytics, transmitir a un centro de eventos o simplemente archivar en una cuenta de almacenamiento.

   :::image type="content" source="./media/monitor-virtual-wan/view-hub-gateway-logs.png" alt-text="Captura de pantalla de Azure Monitor de Seleccionar vista, en Registros.":::

4. En esta página, puede crear una nueva configuración de diagnóstico ( **+Agregar configuración de diagnóstico**) o editar una (**Editar configuración**). Puede optar por enviar los registros de diagnóstico a Log Analytics (como se muestra en el ejemplo siguiente), transmitirlos a un centro de eventos, enviarlos a una solución de terceros o archivarlos en una cuenta de almacenamiento.

    :::image type="content" source="./media/monitor-virtual-wan/select-gateway-settings.png" alt-text="Captura de pantalla para seleccionar una configuración de registro de diagnóstico.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Consulta de ejemplo de Log Analytics

Si ha seleccionado enviar datos de diagnóstico a un área de trabajo de Log Analytics, puede usar consultas de tipo SQL, como el ejemplo siguiente, para examinar los datos. Para obtener más información, consulte [Lenguaje de consulta de Log Analytics](/services-hub/health/log_analytics_query_language).

El ejemplo siguiente contiene una consulta para obtener los diagnósticos de ruta de sitio a sitio.

`AzureDiagnostics | where Category == "RouteDiagnosticLog"`

Reemplace los valores siguientes, después de **= =** , según sea necesario en función de las tablas notificadas en la sección anterior de este artículo.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

Para ejecutar la consulta, debe abrir el recurso de Log Analytics que configuró para recibir los registros de diagnóstico y, a continuación, seleccionar **Registros** en la pestaña **General** del lado izquierdo del panel:

:::image type="content" source="./media/monitor-virtual-wan/log-analytics-query-samples.png" alt-text="Ejemplos de consulta de Log Analytics.":::

Para obtener más ejemplos de consultas de Log Analytics para Azure VPN Gateway, tanto de sitio a sitio como de punto a sitio, puede visitar la página [Solución de problemas de Azure VPN Gateway mediante registros de diagnóstico](../vpn-gateway/troubleshoot-vpn-with-azure-diagnostics.md). Para Azure Firewall, se proporciona un [libro](../firewall/firewall-workbook.md) para facilitar el análisis de registros. Con su interfaz gráfica, podrá investigar los datos de diagnóstico sin escribir manualmente ninguna consulta de Log Analytics. 

## <a name="activity-logs"></a><a name="activity-logs"></a>Registros de actividad

Las entradas del **registro de actividad** están habilitadas de forma predeterminada y se pueden ver en Azure Portal. Puede usar los registros de actividad de Azure (anteriormente conocidos como *registros operativos* y *registros de auditoría*) para ver todas las operaciones enviadas a su suscripción de Azure.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo supervisar las métricas y los registros de Azure Firewall, consulte [Tutorial: Supervisión de los registros de Azure Firewall](../firewall/firewall-diagnostics.md).
* Para más información sobre las métricas en Azure Monitor, consulte [Métricas en Azure monitor](../azure-monitor/essentials/data-platform-metrics.md).

---
title: Configuración de alertas en métricas de Azure VPN Gateway
description: Aprenda a usar Azure Portal para configurar las alertas de Azure Monitor basadas en métricas para puertas de enlace de VPN de red virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e17f86d08b5892c7df0a761e53e1f16dd43f127a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158312"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configuración de alertas en métricas de VPN Gateway

Este artículo le ayuda a configurar alertas en métricas de Azure VPN Gateway. Azure Monitor proporciona la capacidad de configurar alertas para los recursos de Azure. Puede configurar alertas para las puertas de enlace de red virtual del tipo "VPN".

| **Métrica**                                 | **Unidad**     | **Granularidad**     | **Descripción**                                                                         |
| -------------------------------------------| ------------ | ------------------- | --------------------------------------------------------------------------------------- |
| **BGP Peer Status**                        | Count        | 5 minutos           | Estado de conectividad medio de BGP por pares y por instancias.                              |
| **BGP Routes Advertised**                  | Count        | 5 minutos           | Número de rutas anunciadas por pares y por instancias.                                  |
| **BGP Routes Learned**                     | Count        | 5 minutos           | Número de rutas aprendidas por pares y por instancias.                                     |
| **Ancho de banda P2S de puerta de enlace**                  | Bytes por segundo      | 1 minuto.            | Promedio de uso de ancho de banda combinado de todas las conexiones de punto a sitio en la puerta de enlace. |
| **Ancho de banda S2S de puerta de enlace**                  | Bytes por segundo      | 5 minutos           | Promedio de uso de ancho de banda combinado de todas las conexiones de sitio a sitio en la puerta de enlace.  |
| **Recuento de conexiones P2S**                   | Count        | 1 minuto.            | Recuento de las conexiones de punto a sitio en la puerta de enlace.                                      |
| **Ancho de banda de túnel**                       | Bytes por segundo      | 5 minutos           | Promedio de utilización del ancho de banda de los túneles creados en la puerta de enlace.                        |
| **Bytes de salida de túnel**                    | Bytes        | 5 minutos           | Número de bytes salientes de un túnel.                                                 |
| **Tunnel Egress Packet Drop Count**        | Count        | 5 minutos           | Número de paquetes salientes eliminados por un túnel.                                         |
| **Paquetes de salida de túnel**                  | Count        | 5 minutos           | Número de paquetes salientes de un túnel.                                               |
| **Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de salida**  | Count        | 5 minutos           | Número de paquetes salientes eliminados por los túneles por un error de coincidencia del selector de tráfico.      |
| **Bytes de salida de túnel**                   | Bytes        | 5 minutos           | Número de bytes entrantes a un túnel.                                                   |
| **Tunnel Ingress Packet Drop Count**       | Count        | 5 minutos           | Número de paquetes entrantes eliminados por un túnel.                                         |
| **Paquetes de entrada de túnel**                 | Count        | 5 minutos           | Número de paquetes entrantes a un túnel.                                                 |
| **Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de entrada** | Count        | 5 minutos           | Número de paquetes entrantes eliminados por los túneles por un error de coincidencia del selector de tráfico.      |
| **Tunnel MMSA Count**                      | Count        | 5 minutos           | Número de asociaciones de seguridad del modo principal presentes.                                      |
| **Tunnel Peak PPS**                        | Count        | 5 minutos           | Número máximo de paquetes por segundo por túnel.                                            |
| **Tunnel QMSA Count**                      | Count        | 5 minutos           | Número de asociaciones de seguridad del modo rápido presentes.                                     |
| **Recuento total de flujos del túnel**                | Count        | 5 minutos           | Número de flujos distintos creados por túnel.                                            |
| **Recuento de rutas de VPN de usuario**                   | Count        | 5 minutos           | Número de rutas VPN de usuario configuradas en VPN Gateway.                                |
| **Recuento de prefijos de dirección de la red virtual**              | Count        | 5 minutos           | Número de prefijos de dirección de red virtual que usa o anuncia la puerta de enlace.                |

## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configuración de alertas de Azure Monitor basadas en métricas mediante Azure Portal

En los pasos del ejemplo siguiente se creará una alerta en una puerta de enlace para:

- **Métrica:** TunnelAverageBandwidth
- **Condición:** Ancho de banda > 10 bytes por segundo
- **Ventana:** 5 minutos
- **Acción de alerta:** Email



1. Vaya al recurso de puerta de enlace de red virtual y seleccione **Alertas** en la pestaña **Supervisión**. Después, cree una regla de alertas o modifique una existente.

   ![Selecciones para crear una regla de alertas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Crear")

2. Seleccione la instancia de VPN Gateway como el recurso.

   ![El botón Seleccionar y la instancia de VPN Gateway en la lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Seleccionar")

3. Seleccione una métrica para configurar la alerta.

   ![Métrica seleccionada en la lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Seleccionar")
4. Configure la lógica de señal. Hay tres componentes:

    a. **Dimensiones** Si la métrica tiene dimensiones, puede seleccionar valores de dimensión específicos para que la alerta solo evalúe los datos de esa dimensión. Estos son opcionales.

    b. **Condición**: Es la operación para evaluar el valor de métrica.

    c. **Time**: Especifique la granularidad de los datos de la métrica y el período de tiempo para evaluar la alerta.

   ![Detalles de configuración de la lógica de señal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Seleccionar")

5. Para ver las reglas configuradas, seleccione **Administrar reglas de alertas**.

   ![Botón para administrar las reglas de alertas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Seleccionar")

## <a name="next-steps"></a>Pasos siguientes

Para configurar alertas en los registros de recursos de túnel, consulte [Configuración de alertas en los registros de diagnóstico de VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

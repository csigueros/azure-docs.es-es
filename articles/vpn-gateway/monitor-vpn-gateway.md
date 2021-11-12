---
title: Supervisión de Azure VPN Gateway
description: Aprenda a las métricas de VPN Gateway mediante Azure Monitor.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/08/2021
ms.author: alzam
ms.openlocfilehash: 8ae6519881dd0e41cde8ed0fa4d7ffc64f35bdf2
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058796"
---
# <a name="monitoring-vpn-gateway"></a>Supervisión de VPN Gateway

Puede supervisar puertas de enlace de VPN de Azure mediante Azure Monitor. En este artículo se describen las métricas que están disponibles en el portal. Las métricas son ligeras y pueden admitir escenarios casi en tiempo real, lo que las hace útiles para alertas y detección rápida de problemas.


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

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Los siguientes pasos le ayudarán a ubicar y ver las métricas

1. En el portal, vaya al recurso de puerta de enlace de red virtual.
2. Seleccione **Información general** para ver las métricas de total de entrada y salida del túnel.

   ![Selecciones para crear una regla de alertas](./media/monitor-vpn-gateway/overview.png "Ver")

3. Para ver cualquiera de las otras métricas indicadas anteriormente: Haga clic en la sección **Métricas** en el recurso de puerta de enlace de red virtual y seleccione la métrica en la lista desplegable.

   ![El botón Seleccionar y la instancia de VPN Gateway en la lista de recursos](./media/monitor-vpn-gateway/metrics.png "Seleccionar")

## <a name="next-steps"></a>Pasos siguientes

Para configurar alertas en las métricas de túnel, vea [Configuración de alertas en métricas de VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Para configurar alertas en los registros de recursos de túnel, consulte [Configuración de alertas en los registros de diagnóstico de VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

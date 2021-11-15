---
title: ¿Qué es Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Información general sobre las características, recursos, arquitectura e implementación de Virtual Network NAT. Aprenda cómo funciona Virtual Network NAT y cómo usar los recursos de puerta de enlace de NAT en la nube.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: nat
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: allensu
ms.openlocfilehash: 5e2b9baeef03163e74ad83c97d8e8148d7f6cfaf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228325"
---
# <a name="what-is-virtual-network-nat"></a>¿Qué es NAT de Virtual Network?

Virtual Network NAT es un servicio de traducción de direcciones de red (NAT) totalmente administrado y muy resistente. VNet NAT simplifica la conectividad saliente a Internet para redes virtuales. Cuando se configura en una subred, toda la conectividad saliente usa las direcciones IP públicas estáticas de VNet NAT. 

:::image type="content" source="./media/nat-overview/flow-map.png" alt-text="En la ilustración se muestra una NAT que recibe tráfico de subredes internas y lo dirige a una dirección IP pública (PIP) y un prefijo IP.":::

*Ilustración:* Virtual Network NAT

## <a name="vnet-nat-benefits"></a>Ventajas de VNet NAT

### <a name="security"></a>Seguridad
Con NAT, las máquinas virtuales individuales (u otros recursos de proceso) no necesitan direcciones IP públicas y pueden permanecer totalmente privadas. Estos recursos, a pesar de no tener una dirección IP pública, pueden llegar a orígenes externos fuera de la red virtual. También puede asociar un prefijo de IP pública para asegurarse de que se usará un conjunto contiguo de direcciones IP para la salida. Las reglas de firewall de destino se pueden configurar en función de esta lista de direcciones IP predecibles.

### <a name="resiliency"></a>Resistencia 
NAT es un servicio totalmente administrado y distribuido. No depende de ninguna instancia de proceso individual, como máquinas virtuales o un solo dispositivo de puerta de enlace físico. Aprovecha las redes definidas por software, lo que la hace muy resistente. 

### <a name="scalability"></a>Escalabilidad
NAT se puede asociar a una subred y se puede usar en todos los recursos de proceso de esa subred. Además, todas las subredes de una red virtual pueden aprovechar el mismo recurso. Cuando se asocie a un prefijo de IP pública, se escalará automáticamente al número de direcciones IP necesarias para la salida.

### <a name="performance"></a>Rendimiento
NAT no afecta al ancho de banda de red de los recursos de proceso, ya que es un servicio de red definido por software. Descubra más sobre las [métricas de la puerta de enlace NAT](nat-gateway-resource.md#performance).


## <a name="vnet-nat-basics"></a>Conceptos básicos de VNet NAT

NAT se puede crear en una zona de disponibilidad específica y tiene redundancia integrada dentro de la zona especificada. NAT no es zonal de forma predeterminada. Al crear escenarios de [zonas de disponibilidad](../../availability-zones/az-overview.md), NAT se puede aislar en una zona específica. Esto se conoce como implementación zonal.

NAT se escala de forma horizontal completamente desde el principio. No se precisa ninguna operación de aumento ni de escalabilidad horizontal.  Azure administra automáticamente la operación de NAT.  NAT siempre tiene varios dominios de error y puede soportar varios errores sin que se interrumpa el servicio.

* Se puede definir la conectividad de salida para todas las subredes con NAT.  Distintas subredes dentro de la misma red virtual pueden tener diferentes NAT. Para configurar una subred, es preciso especificar qué recurso de puerta de enlace de NAT se debe usar.  Todo el tráfico de salida para la subred lo procesa NAT automáticamente sin que el cliente tenga que configurar nada.  No se necesitan rutas definidas por el usuario. NAT tiene prioridad sobre otros escenarios de salida y reemplaza el destino de Internet predeterminado de una subred.
* NAT solo admite protocolos TCP y UDP. ICMP no se admite.
* Un recurso de puerta de enlace NAT puede usar lo siguiente:

  * Dirección IP pública
  * Prefijo de IP pública
* NAT es compatible con la dirección IP pública de la SKU estándar, los recursos de prefijo de IP pública o una combinación de ambos. Puede usar un prefijo de IP pública directamente o distribuir las direcciones IP públicas del prefijo entre varios recursos de puerta de enlace de NAT. NAT limpiará todo el tráfico hacia el intervalo de direcciones IP del prefijo. Los recursos básicos, como Load Balancer básico o la dirección IP pública básica, no son compatibles con NAT.  Los recursos básicos deben colocarse en una subred no asociada a una instancia de NAT Gateway.
* NAT no se puede asociar a una dirección IP pública IPv6 ni a un prefijo IP público IPv6. Sin embargo, se puede asociar a una subred de pila dual.
* NAT permite crear flujos entre la red virtual y los servicios que se encuentran fuera de ella. El tráfico de retorno de Internet solo se permite en respuesta a un flujo activo. Los servicios fuera de la red virtual no pueden iniciar una conexión a las instancias.
* NAT no puede abarcar varias redes virtuales.
* NAT no se puede implementar en una [subred de puerta de enlace](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub).
* El lado privado de NAT (instancias de máquina virtual u otros recursos de proceso) envía paquetes de restablecimiento de TCP durante los intentos de comunicación en una conexión TCP que no existe. Un ejemplo son las conexiones que han alcanzado el tiempo de espera de inactividad. El siguiente paquete recibido devolverá un TCP Reset a la dirección IP privada para señalar y forzar el cierre de la conexión. El lado público de NAT no genera paquetes de TCP Reset ni ningún otro tipo de tráfico.  Solo se emite el tráfico generado por la red virtual del cliente.
* Se usa un tiempo de espera de inactividad de TCP predeterminado de 4 minutos que se puede aumentar hasta 120. Cualquier actividad de un flujo puede restablecer también el temporizador de actividad, lo que incluye mensajes TCP Keepalive.

## <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio

Para obtener información detallada sobre los precios, consulte [Precios de Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network). La ruta de acceso a datos de NAT tiene una disponibilidad mínima del 99,9 %.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [obtener una mejor conectividad de salida mediante una instancia de Azure NAT Gateway](https://www.youtube.com/watch?v=2Ng_uM0ZaB4).
* Obtenga más información sobre [recursos de puerta de enlace de NAT](./nat-gateway-resource.md).
* Obtenga más información sobre las [métricas de puerta de enlace NAT](./nat-metrics.md).

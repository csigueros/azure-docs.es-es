---
title: 'Interoperabilidad en Azure: Análisis del plano de control'
description: En este artículo se proporciona el análisis del plano de control de la configuración de prueba que puede usar para analizar la interoperabilidad entre ExpressRoute, una VPN de sitio a sitio y el emparejamiento de redes virtuales en Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7a777208563e13945bd0bdd0aac43560143d0a49
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005069"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilidad en Azure: Análisis del plano de control

En este artículo se describe el análisis del plano de control de la [configuración de la prueba][Setup]. También puede revisar la [configuración de la prueba][Configuration] y el [análisis del plano de control][Data-Analysis] de la configuración de la prueba.

En esencia, el análisis del plano de control examina las rutas que se intercambian entre las redes dentro de una topología. El análisis del plano de control puede ayudar a entender la forma en la que cada red ve la topología.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspectiva de red virtual de tipo hub-and-spoke

En la siguiente ilustración se muestra la red desde la perspectiva de un centro de conectividad y una red virtual de radio (resaltada en azul). En la ilustración también se muestra el número de sistema autónomo (ASN) de las redes y rutas que se intercambian entre las distintas redes: 

![1][1]

Tenga en cuenta que el ASN de la puerta de enlace de Azure ExpressRoute de la red virtual difiere del de los enrutadores de Microsoft Enterprise Edge (MSEE). Generalmente, la puerta de enlace de ExpressRoute usa un ASN privado (con valor **65515**) y los MSEE, uno público (**12076**). Al configurar el emparejamiento de ExpressRoute, como MSEE es del mismo nivel, se usa **12076** como ASN del mismo nivel. Del lado de Azure, MSEE establece emparejamiento de BGP externo con la puerta de enlace de ExpressRoute. El emparejamiento BGP externo dual que el MSEE establece para cada emparejamiento de ExpressRoute es transparente en el nivel del plano de control. Por tanto, al visualizar una tabla de enrutamiento de ExpressRoute, se ve el valor de ASN de la puerta de enlace de ExpressRoute de la red virtual para los prefijos de esta. 

En la siguiente ilustración se muestra una tabla de enrutamiento de ExpressRoute de ejemplo: 

![5][5]

En Azure, el valor de ASN solo es significativo para el emparejamiento. De forma predeterminada, el valor de ASN de la puerta de enlace tanto de ExpressRoute como de VPN en Azure VPN Gateway es **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Perspectiva de la ubicación 1 local y de la red virtual remota a través de ExpressRoute 1

Tanto la ubicación 1 local como la red virtual remota están conectadas al centro de conectividad a través de ExpressRoute 1. Comparten la misma perspectiva de la topología, como se muestra en el siguiente diagrama:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Perspectiva de la ubicación 1 local y de la red virtual remota a través de ExpressRoute 1

Tanto la ubicación 1 local como la red virtual de la rama están conectadas a una puerta de enlace de centro de conectividad a través de una VPN de sitio a sitio. Comparten la misma perspectiva de la topología, como se muestra en el siguiente diagrama:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Perspectiva de la ubicación 2 local

La ubicación 2 local está conectada al centro de conectividad a través del emparejamiento privado de ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Uso simultáneo de ExpressRoute y la conectividad VPN de sitio a sitio

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN de sitio a sitio con ExpressRoute

Puede configurar una VPN de sitio a sitio mediante el emparejamiento de Microsoft con ExpressRoute para intercambiar datos de forma privada entre la red local y las redes virtuales de Azure. Esta configuración le asegura un intercambio de datos con confidencialidad, autenticidad, integridad y antirreproducción. Para más información acerca de cómo configurar una VPN IPsec de sitio a sitio en modo de túnel con emparejamiento de Microsoft de ExpressRoute, consulte [Configuración de una VPN de sitio a sitio a través del emparejamiento de Microsoft de ExpressRoute][S2S-Over-ExR]. 

La limitación principal de la configuración de una VPN de sitio a sitio que use el emparejamiento de Microsoft es el rendimiento. El rendimiento a través del túnel IPsec está limitado por la capacidad de la puerta de enlace de VPN. El rendimiento de la puerta de enlace de VPN es menor que el de ExpressRoute. En este escenario, el uso del túnel IPsec para el tráfico de seguridad alta y con emparejamiento privado para el tráfico restante ayuda a optimizar el uso de ancho de banda de ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de sitio a sitio como ruta de acceso de conmutación por error para ExpressRoute

ExpressRoute sirve como par de circuito redundante para garantizar la alta disponibilidad. Puede configurar la conectividad de ExpressRoute con redundancia geográfica en diferentes regiones de Azure. Además, como se ha demostrado en la configuración de prueba, dentro de una región de Azure puede usar una VPN de sitio a sitio para crear una ruta de acceso de conmutación por error para la conectividad de ExpressRoute. Cuando se anuncian los mismos prefijos a través de ExpressRoute y VPN de sitio a sitio, Azure da prioridad a ExpressRoute. Para evitar el enrutamiento asimétrico entre ExpressRoute y VPN de sitio a sitio, la configuración de la red local también debe tener prioridad mediante el uso de la conectividad ExpressRoute antes de VPN de sitio a sitio.

Para más información acerca de cómo configurar conexiones coexistentes de ExpressRoute y una VPN de sitio a sitio, consulte el artículo sobre la [coexistencia de conexiones de ExpressRoute y de sitio a sitio][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Ampliación de la conectividad de back-end a redes virtuales de radio y ubicaciones de sucursal

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividad de red virtual de radio mediante el emparejamiento de redes virtuales

La arquitectura de red virtual de tipo hub-and-spoke se usa ampliamente. El centro de conectividad es una red virtual de Azure que actúa como punto central de conectividad entre las redes virtuales de radio y la red local. Los radios son redes virtuales que se emparejan con el centro de conectividad y sirven para aislar las cargas de trabajo. El tráfico fluye entre el centro de datos local y el concentrador a través de una conexión de ExpressRoute o VPN. Para más información acerca de la arquitectura, consulte [Implementación de una topología de red en estrella tipo hub-and-spoke en Azure][Hub-n-Spoke].

En el emparejamiento de redes virtuales dentro de una región, las redes virtuales de radio pueden usar las puertas de enlace del centro de conectividad (tanto de VPN como de ExpressRoute) para comunicarse con las redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividad de red virtual de sucursal mediante VPN de sitio a sitio

Es posible que quiera redes virtuales en rama, que están en regiones diferentes, y redes locales para comunicarse entre sí a través de una red virtual de centro de conectividad. La solución de Azure nativa para esta configuración es la conectividad VPN de sitio a sitio mediante una VPN. Una alternativa es usar una aplicación virtual de red (NVA) para el enrutamiento en el centro de conectividad.

Para más información, consulte [¿Qué es VPN Gateway?][VPN] e [Implementación de aplicaciones virtuales de red de alta disponibilidad][Deploy-NVA].

## <a name="next-steps"></a>Pasos siguientes

Más información acerca del [análisis del plano de datos][Data-Analysis] de la configuración de prueba y de las vistas de las características de supervisión de red de Azure.

Consulte las [preguntas más frecuentes de ExpressRoute][ExR-FAQ] para información acerca de:
-   Cuántos circuitos de ExpressRoute puede conectar a una puerta de enlace de ExpressRoute.
-   Cuántas puertas de enlace de ExpressRoute puede conectar a un circuito de ExpressRoute.
-   Los límites de escala de ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/hubview.png "Perspectiva de red virtual de tipo hub-and-spoke de la topología"
[2]: ./media/backend-interoperability/loc1exrview.png "Perspectiva de la topología de la ubicación 1 y de la red virtual remota a través de ExpressRoute 1"
[3]: ./media/backend-interoperability/loc1vpnview.png "Perspectiva de la ubicación 1 y de la red virtual de sucursal de la topología a través de VPN de sitio a sitio"
[4]: ./media/backend-interoperability/loc2view.png "Perspectiva de la ubicación 2 de la topología"
[5]: ./media/backend-interoperability/exr1-routetable.png "Tabla de rutas de ExpressRoute 1"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: /azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md

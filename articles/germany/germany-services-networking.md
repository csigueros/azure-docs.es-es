---
title: Servicios de red de Azure Alemania | Microsoft Docs
description: Se proporciona una comparación de las características y una guía para la conectividad privada con Azure Alemania
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: aa1d648cf3e3f43d4a29c5dc199420f8d731848a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029204"
---
# <a name="azure-germany-networking-services"></a>Servicios de red de Azure Alemania

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="expressroute-private-connectivity"></a>ExpressRoute (conectividad privada)
ExpressRoute está disponible con carácter general en Azure Alemania. Para más información (lo que incluye los asociados y las ubicaciones de emparejamiento), consulte la [ documentación global de ExpressRoute ](../expressroute/index.yml).

### <a name="variations"></a>Variaciones

* Los clientes de Azure Alemania se conectan a una capacidad aislada físicamente a través de una conexión ExpressRoute de Azure Alemania dedicada.
* Azure Alemania proporciona una mayor disponibilidad y durabilidad mediante el uso de varios pares de regiones ubicados a más de 400 km de distancia. 
* De forma predeterminada, toda la conectividad de ExpressRoute de Azure Alemania está configurada con redundancia activo/activo y es compatible con seguridad y entregas hasta una capacidad de circuito de 10 G.
* Las ubicaciones de ExpressRoute de Azure Alemania proporcionan caminos optimizados (lo que incluye saltos más cortos, baja latencia y alto rendimiento) para clientes y regiones con redundancia geográfica de Azure Alemania.
* La conexión privada de ExpressRoute de Azure Alemania no usa, recorre ni depende de Internet.
* La infraestructura física y lógica de Azure Alemania está físicamente dedicada y separada de la red en la nube de Microsoft internacional.
* ExpressRoute de Azure Alemania proporciona conectividad privada con los servicios en la nube de Microsoft Azure, pero no con los de Microsoft 365 o Dynamics 365.

### <a name="considerations"></a>Consideraciones
Dos servicios básicos proporcionan conectividad de red privada a Azure Alemania: ExpressRoute y VPN (de sitio a sitio para una organización típica).

ExpressRoute se puede usar para crear conexiones privadas entre centros de datos de Azure Alemania y una infraestructura local, o en un entorno de coubicación. Las conexiones ExpressRoute no pasan por la red pública de Internet. Ofrecen más confiabilidad, velocidades más altas y menores latencias que las conexiones a Internet habituales. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre sistemas locales y Azure supone importantes ahorros.   

Con ExpressRoute puede establecer conexiones con Azure en una ubicación de ExpressRoute, como por ejemplo, en las instalaciones de un proveedor de ExpressRoute Exchange. O bien conéctese directamente con Azure desde su WAN existente, como una VPN con conmutación de etiquetas multiprotocolo​ (MPSL) que proporcione un proveedor de servicios de red.

Para que los servicios de red admitan aplicaciones y soluciones de cliente de Azure Alemania, se recomienda encarecidamente implementar ExpressRoute (conectividad privada) para conectar con Azure Alemania. Si usa conexiones VPN, tenga en cuenta lo siguiente:

* Póngase en contacto con el organismo o funcionario autorizado para determinar si necesita conectividad privada u otro mecanismo de conexión seguro, así como para identificar las restricciones adicionales.
* Decida si desea ordenar que la VPN de sitio a sitio se enrute a través de una zona de conectividad privada.
* Obtenga un circuito MPLS o una VPN con un proveedor de acceso de conectividad privada con licencia.

Si usa una arquitectura de conectividad privada, valide que se haya establecido y mantenido una implementación adecuada para la conexión al punto de demarcación del enrutador de borde de red de puerta de enlace/Internet (GN/I) para Azure Alemania. De igual forma, la organización debe establecer conectividad de red entre el entorno local y el punto de demarcación del enrutador de borde de red de puerta de enlace/cliente (GN/C) para Azure Alemania.

Si se va a conectar a Microsoft a través de ExpressRoute en cualquier ubicación de configuración de emparejamiento de la región de Azure Alemania, tendrá acceso a todos los servicios en la nube de Microsoft Azure en todas las regiones que se encuentren dentro de los límites de Alemania. Por ejemplo, si se conecta a Microsoft en Berlín mediante ExpressRoute, tendrá acceso a todos los servicios en la nube de Microsoft hospedados en Azure Alemania.

Consulte la pestaña **Introducción** de la [documentación pública de ExpressRoute](../expressroute/index.yml) para más información acerca de ubicaciones y asociados, y una lista detallada de ExpressRoute para las ubicaciones de emparejamiento de Azure Alemania.

Puede comprar más de un circuito de ExpressRoute. Tener varias conexiones ofrece importantes ventajas para la alta disponibilidad debido a la redundancia geográfica. En los casos en que tenga varios circuitos de ExpressRoute, recibirá el mismo conjunto de prefijos anunciados por Microsoft en las rutas de acceso de emparejamiento públicas, lo que significa que tendrá varias rutas de acceso desde su red a Microsoft. Esta situación puede llegar a provocar que se tomen decisiones de enrutamiento en la red que no sean óptimas. Como consecuencia, puede tener que soportar una conectividad con los diferentes servicios que tampoco sea óptima. Para más información, consulte la pestaña **How-to guides > Best practices (Guías paso a paso > Procedimientos recomendados)** de la [documentación global de ExpressRoute](../expressroute/index.yml) y seleccione **Optimize routing (Optimizar enrutamiento)**.

## <a name="support-for-load-balancer"></a>Compatibilidad con Load Balancer
Azure Load Balancer está disponible en Azure Alemania con carácter general. Para más información, consulte la [documentación global de Load Balancer](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manager"></a>Compatibilidad con Traffic Manager
Traffic Manager está disponible en Azure Alemania con carácter general. Para más información, consulte la [documentación global de Traffic Manager](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-virtual-network-peering"></a>Compatibilidad con el emparejamiento de redes virtuales 
El emparejamiento de redes virtuales está disponible en Azure Alemania con carácter general. Para más información, consulte la [documentación global del emparejamiento de redes virtuales](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>Compatibilidad con VPN Gateway 
Azure VPN Gateway está disponible en Azure Alemania con carácter general. Para más información, consulte la [documentación global de VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al [blog de Azure Alemania](/archive/blogs/azuregermany/).
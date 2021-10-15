---
title: Compatibilidad de Azure Route Server con ExpressRoute y VPN de Azure
description: Obtenga información sobre cómo interactúa Azure Route Server con ExpressRoute y las puertas de enlace de VPN de Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: duau
ms.openlocfilehash: 61a712fa747252b07a513cfa6e18e3f3b4a67e6c
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400731"
---
# <a name="about-azure-route-server-support-for-expressroute-and-azure-vpn"></a>Compatibilidad de Azure Route Server con ExpressRoute y VPN de Azure

Azure Route Server admite no solo aplicaciones virtuales de red (NVA) de terceros que se ejecutan en Azure, sino que también se integra perfectamente con ExpressRoute y las puertas de enlace de VPN de Azure. No es necesario configurar ni administrar el emparejamiento de BGP entre la puerta de enlace y Azure Route Server. Puede habilitar el intercambio de rutas entre la puerta de enlace y Azure Route Server con un simple [cambio de configuración](quickstart-configure-route-server-powershell.md#route-exchange).


## <a name="how-does-it-work"></a>¿Cómo funciona?

Cuando se implementa una instancia de Azure Route Server junto con una puerta de enlace de ExpressRoute y una NVA en una red virtual, de manera predeterminada, Azure Route Server no propaga las rutas que recibe de la NVA y de la puerta de enlace de ExpressRoute entre sí. Una vez que habilite el intercambio de rutas, ExpressRoute y la NVA aprenderán las rutas de cada uno.

Por ejemplo, en el siguiente diagrama:

* El dispositivo SDWAN recibirá de Azure Route Server la ruta desde "On-prem 2", que está conectada a ExpressRoute, junto con la ruta de la red virtual.

* La puerta de enlace de ExpressRoute recibirá la ruta de "On-prem 1", que está conectada al dispositivo SDWAN, junto con la ruta de la red virtual desde Azure Route Server.

    ![Diagrama que muestra ExpressRoute configurado con Route Server.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

También puede reemplazar el dispositivo SDWAN por Azure VPN Gateway. Dado que Azure VPN Gateway y ExpressRoute están totalmente administrados, solo tiene que habilitar el intercambio de rutas para las dos redes locales para que se comuniquen entre sí.

> [!IMPORTANT] 
> Azure VPN Gateway debe estar configurado en modo [**activo-activo**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) y tener el valor de ASN establecido en 65515.
>

![Diagrama que muestra ExpressRoute y VPN Gateway configurados con Route Server.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Azure Route Server](route-server-faq.md).
- Consulte [Configuración de una instancia de Azure Route Server](quickstart-configure-route-server-powershell.md).
- Más información sobre [Coexistencia de Azure ExpressRoute y VPN de Azure](../expressroute/expressroute-howto-coexist-resource-manager.md).

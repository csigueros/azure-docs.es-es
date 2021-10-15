---
title: 'Azure ExpressRoute: Prerrequisitos'
description: Esta página proporciona una lista de requisitos que deben cumplirse para poder solicitar un circuito ExpressRoute de Azure. Incluye una lista de comprobación.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: d0b24d902c808f06e49f2c132629484d22921b4b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351301"
---
# <a name="expressroute-prerequisites--checklist"></a>Requisitos previos y lista de comprobación de ExpressRoute
Para conectarse a Microsoft Cloud Services con ExpressRoute, debe comprobar que se han cumplido los requisitos enumerados en las secciones siguientes.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Cuenta de Azure
* Una cuenta de Microsoft Azure válida y activa Esta cuenta es necesaria para configurar el circuito ExpressRoute. Los circuitos ExpressRoute son recursos que hay dentro de las suscripciones de Azure. Se necesita una suscripción de Azure, aunque la conectividad esté limitada a servicios en la nube de Microsoft que no sean Azure, como Microsoft 365.
* Una suscripción de Microsoft 365 activa (si se usa los servicios de Microsoft 365). Para más información, consulte la sección acerca de los requisitos específicos de Microsoft 365 de este mismo artículo.

## <a name="connectivity-provider"></a>Proveedor de conectividad

* Para conectarse a la nube de Microsoft, puede trabajar con un [asociado de conectividad ExpressRoute](expressroute-locations.md#partners) . Se puede configurar una conexión entre la red local y Microsoft de [tres maneras](expressroute-introduction.md).
* Aunque el proveedor no sea un asociado de conectividad ExpressRoute, puede conectarse a la nube de Microsoft mediante un [proveedor de intercambio de nube](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos de red
* **Redundancia en cada ubicación de emparejamiento**: Microsoft requiere que se configuren sesiones BGP redundantes entre enrutadores de Microsoft y enrutadores de emparejamiento en cada circuito ExpressRoute, aunque solo tenga [una conexión física a un intercambio de nube](expressroute-faqs.md#onep2plink).
* **Redundancia para la recuperación ante desastres**: Microsoft recomienda encarecidamente configurar al menos dos circuitos ExpressRoute en distintas ubicaciones de emparejamiento para evitar un único punto de error.
* **Enrutamiento**: según como se conecte a Microsoft Cloud, usted o su proveedor deben configurar y administrar las sesiones de BGP para los [dominios de enrutamiento](expressroute-circuit-peerings.md). Algunos proveedores de conectividad Ethernet o proveedores de intercambio en la nube pueden ofrecer administración de BGP como un servicio de valor añadido.
* **NAT**: Microsoft solo acepta direcciones IP públicas a través de emparejamiento de Microsoft. Si usa direcciones IP privadas en la red local, usted o su proveedor deben convertir las direcciones IP privadas en direcciones IP públicas [con NAT](expressroute-nat.md).
* **QoS**: Skype Empresarial cuenta con varios servicios (por ejemplo, voz, vídeo o texto) que requieren un tratamiento diferenciado de QoS. Usted y su proveedor deben seguir los [requisitos de QoS](expressroute-qos.md).
* **Seguridad de red**: cuando se conecte a Microsoft Cloud mediante ExpressRoute, tenga en cuenta la [seguridad de red](/azure/cloud-adoption-framework/reference/networking-vdc) .

## <a name="microsoft-365"></a>Microsoft 365
Si tiene pensado habilitar Microsoft 365 en ExpressRoute, revise los siguientes documentos para tener más información sobre los requisitos de Microsoft 365.

* [Azure ExpressRoute para Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Enrutamiento con ExpressRoute para Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Alta disponibilidad y conmutación por error con ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Direcciones URL e intervalos de direcciones IP de Microsoft 365](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Planes de red y ajuste del rendimiento para Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Planeación de red y migración para Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Integración de Microsoft 365 con entornos locales](/microsoft-365/enterprise/microsoft-365-integration)
* [Manténgase al día con los cambios en la dirección IP de Office 365](/microsoft-365/enterprise/microsoft-365-ip-web-service)
* [Vídeos sobre el aprendizaje avanzado de ExpressRoute en Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
* Encuentre un proveedor de conectividad ExpressRoute. Consulte [Asociados de ExpressRoute de Azure y ubicaciones de emparejamiento](expressroute-locations.md).
* Consulte los requisitos de [enrutamiento](expressroute-routing.md), [NAT](expressroute-nat.md) y [QoS](expressroute-qos.md).
* Configure su conexión ExpressRoute.
  * [Creación de un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configuración del enrutamiento](expressroute-howto-routing-arm.md)
  * [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md)
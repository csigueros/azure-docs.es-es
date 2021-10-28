---
title: Acerca de Azure ExpressRoute FastPath
description: Más información acerca de Azure ExpressRoute FastPath para enviar tráfico de red omitiendo la puerta de enlace
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/10/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 1fcdeaaee2ebafbfd8e97577ed87a5ef0e545cd5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226402"
---
# <a name="about-expressroute-fastpath"></a>Acerca de FastPath de ExpressRoute

La puerta de enlace de red virtual de ExpressRoute está diseñada para intercambiar las rutas de red y enrutar el tráfico de red. FastPath está diseñado para mejorar el rendimiento de las rutas de acceso a los datos entre la red local y una red virtual. Cuando está habilitado, FastPath envía el tráfico de red directamente a las máquinas virtuales que están en la red, omitiendo la puerta de enlace.

## <a name="requirements"></a>Requisitos

### <a name="circuits"></a>Circuitos

FastPath está disponible en todos los circuitos de ExpressRoute.

### <a name="gateways"></a>Puertas de enlace

FastPath de todos modos necesita que se cree una puerta de enlace de red virtual para intercambiar rutas entre la red virtual y una red local. Para más información acerca de las puertas de enlace de red virtual y ExpressRoute, incluidas las SKU de puerta de enlace y la información sobre el rendimiento, consulte [Puerta de enlace de red virtual de ExpressRoute](expressroute-about-virtual-network-gateways.md).

Para configurar FastPath, la puerta de enlace de red virtual debe ser una de las siguientes:

* Ultrarrendimiento
* ErGw3AZ

## <a name="limitations"></a>Limitaciones

Aunque FastPath admite la mayoría de las configuraciones, no es compatible con las características siguientes:

* UDR en la subred de puerta de enlace: este UDR no tiene ningún impacto en el tráfico de red que FastPath envía directamente desde la red local a las máquinas virtuales de la red virtual de Azure. 

* Equilibrador de carga básico: si implementa un equilibrador de carga interno básico en la red virtual, o el servicio PaaS de Azure que implementa en la red virtual usa un equilibrador de carga interno básico, el tráfico de la red local a las direcciones IP virtuales hospedadas en el equilibrador de carga básico se enviará a la puerta de enlace de red virtual. La solución es actualizar el equilibrador de carga básico a un [equilibrador de carga estándar](../load-balancer/load-balancer-overview.md).

* Vínculo privado: si se conecta con un [punto de conexión privado](../private-link/private-link-overview.md) de la red virtual desde la red local, la conexión pasará por la puerta de enlace de red virtual.

## <a name="public-preview"></a>Versión preliminar pública

Las características siguientes de FastPath se encuentran en versión preliminar pública:

**Emparejamiento de VNet**: FastPath enviará tráfico directamente a cualquier máquina virtual implementada en una red virtual emparejada con la conectada a ExpressRoute, y omitirá la puerta de enlace de red virtual de ExpressRoute.

Vea [Inscripción en las características de FastPath de ExpressRoute](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview).

Disponible en todas las regiones.
 
## <a name="next-steps"></a>Pasos siguientes

Para habilitar FastPath, consulte [Conexión de una red virtual con un circuito de ExpressRoute mediante el portal](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

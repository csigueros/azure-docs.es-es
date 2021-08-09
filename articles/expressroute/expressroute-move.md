---
title: 'ExpressRoute: Traslado de circuitos del modelo clásico a Azure Resource Manager'
description: Obtenga información sobre lo que ocurre cuando mueve un circuito Azure ExpressRoute del modelo de implementación clásica al de Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 06/02/2021
ms.author: duau
ms.openlocfilehash: 5395204f75a0637daedabba6b933036b7144dae1
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111571355"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Transición de los circuitos ExpressRoute del modelo de implementación clásica al modelo de implementación de Resource Manager
En este artículo se ofrece información general de lo que ocurre cuando mueve un circuito Azure ExpressRoute del modelo de implementación clásica al de Azure Resource Manager.

Se puede usar un solo circuito ExpressRoute para conectarse a redes virtuales implementadas tanto en el modelo de implementación clásica como en el modelo de Resource Manager.

![Un circuito ExpressRoute que se vincula a redes virtuales a través de ambos modelos de implementación](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos ExpressRoute creados en el modelo de implementación clásica
Los circuitos ExpressRoute creados en el modelo de implementación clásica deben migrar primero al modelo de implementación de Resource Manager. Solo entonces puede habilitar la conectividad a los modelos de implementación clásica y de Resource Manager. Mientras se traslada una conexión, no se pierde la conectividad ni se producen interrupciones. Todos los vínculos entre el circuito y la red virtual en el modelo de implementación clásica dentro de la misma suscripción y entre suscripciones se conservan.

Una vez que haya finalizado la transición, el circuito ExpressRoute se comportará exactamente igual que un circuito ExpressRoute creado en el modelo de implementación de Resource Manager. Ahora podrá crear conexiones a redes virtuales en el modelo de implementación de Resource Manager.

Una vez que haya trasladado el circuito ExpressRoute al modelo de implementación de Resource Manager, solo podrá administrarlo en el modelo de implementación de Resource Manager. Las operaciones para administrar emparejamientos, actualizar propiedades de circuito y eliminar circuitos solo estarán disponibles a través del modelo de implementación de Resource Manager. Consulte la siguiente sección para obtener más detalles sobre cómo puede administrar el acceso a ambos modelos de implementación.

No es necesario que intervenga su proveedor de conectividad en el traslado del circuito al modelo de implementación de Resource Manager.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos ExpressRoute creados en el modelo de implementación de Resource Manager
Puede habilitar los circuitos ExpressRoute creados en el modelo de implementación de Resource Manager para que sean accesibles desde ambos modelos de implementación. Cualquier circuito ExpressRoute de su suscripción se puede configurar para tener acceso desde ambos modelos de implementación.

* Los circuitos ExpressRoute que se crearon en el modelo de implementación de Resource Manager no tienen acceso al modelo de implementación clásica de forma predeterminada.
* Los circuitos ExpressRoute que se han trasladado del modelo de implementación clásica al modelo de Resource Manager son accesibles desde ambos modelos de implementación de forma predeterminada.
* Un circuito ExpressRoute siempre tendrá acceso al modelo de implementación de Resource Manager, independientemente de si se creó en el modelo de implementación de Resource Manager o en el modelo de implementación clásica. Puede crear conexiones a redes virtuales siguiendo instrucciones sobre [cómo vincular redes virtuales](expressroute-howto-linkvnet-arm.md).
* El acceso al modelo de implementación clásica se controla mediante el parámetro **allowClassicOperations** del circuito ExpressRoute.

> [!IMPORTANT]
> Se aplican todas las cuotas documentadas en la página de [límites de servicio](../azure-resource-manager/management/azure-subscription-service-limits.md) . Por ejemplo, un circuito estándar puede tener como máximo 10 vínculos de red virtual/conexiones entre los modelos de implementación clásica y de Resource Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Control del acceso al modelo de implementación clásica
Puede habilitar un circuito ExpressRoute para vincularlo a redes virtuales en ambos modelos de implementación. Para ello, establezca el parámetro **allowClassicOperations** en el circuito ExpressRoute.

Al definir **allowClassicOperations** en TRUE podrá vincular redes virtuales de ambos modelos de implementación al circuito ExpressRoute. 
* Para vincular redes virtuales en el modelo de implementación clásica, consulte [cómo vincular redes virtuales para el modelo de implementación clásica](expressroute-howto-linkvnet-classic.md).
* Para vincular redes virtuales en el modelo de implementación de Resource Manager, consulte [cómo vincular redes virtuales en el modelo de implementación de Resource Manager](expressroute-howto-linkvnet-arm.md).

Al definir **allowClassicOperations** en FALSE se bloquea el acceso al circuito desde el modelo de implementación clásica. Sin embargo, todavía se conservan todas las redes virtuales vinculadas en el modelo de implementación clásica. El circuito ExpressRoute no es visible en el modelo de implementación clásica.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operaciones admitidas en el modelo de implementación clásica
Cuando se define en TRUE el parámetro **allowClassicOperations** , en un circuito ExpressRoute se admiten las siguientes operaciones clásicas:

* Obtener información del circuito ExpressRoute
* Crear, actualizar, obtener y eliminar vínculos a redes virtuales clásicas
* Crear, actualizar, obtener y eliminar autorizaciones de vínculo de red virtual para la conectividad entre suscripciones

Sin embargo, cuando **allowClassicOperations** se define en TRUE, no se pueden ejecutar las siguientes operaciones clásicas:

* Crear, actualizar, obtener y eliminar emparejamientos de Border Gateway Protocol (BGP) para emparejamientos públicos y privados de Azure y emparejamientos de Microsoft
* Eliminar circuitos ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicación entre los modelos de implementación clásica y de Resource Manager
El circuito ExpressRoute actúa como puente entre los modelos de implementación clásica y de Resource Manager. El tráfico entre redes virtuales para ambos modelos de implementación puede atravesar el circuito ExpressRoute si ambas redes virtuales están vinculadas al mismo circuito.

El rendimiento agregado se ve limitado por la capacidad de procesamiento de la puerta de enlace de red virtual. En tales casos, el tráfico no pasa al proveedor de conectividad ni a las redes. El flujo del tráfico entre las redes virtuales está completamente contenido en la red de Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acceso a recursos de emparejamiento público de Azure y de Microsoft
Puede seguir teniendo acceso a los recursos a los que normalmente se accede mediante el emparejamiento público de Azure y el emparejamiento de Microsoft sin interrupciones.  

## <a name="whats-supported"></a>Lo que se admite
En esta sección se describe lo que se admite para los circuitos ExpressRoute:

* Para acceder a redes virtuales implementadas tanto en el modelo clásico como en el de Resource Manager se puede usar un solo circuito ExpressRoute.
* Un circuito ExpressRoute se puede trasladar del modelo de implementación clásica al modelo de implementación de Resource Manager. Una vez que se haya trasladado, el circuito ExpressRoute seguirá funcionando como cualquier otro circuito ExpressRoute creado en el modelo de implementación de Resource Manager.
* Solo se puede trasladar el circuito ExpressRoute. Los vínculos del circuito, las redes virtuales y las puertas de enlace de VPN no se trasladan mediante esta operación.
* Cuando se haya trasladado el circuito ExpressRoute al modelo de implementación de Resource Manager, solo podrá administrar el ciclo de vida del circuito ExpressRoute con el modelo de implementación de Resource Manager. Esto significa que operaciones tales como agregar, actualizar y eliminar emparejamientos; actualizar propiedades del circuito, como el ancho de banda, el SKU y el tipo de facturación; y eliminar circuitos, solo se pueden ejecutar en el modelo de implementación de Resource Manager.
* El circuito ExpressRoute actúa como puente entre los modelos de implementación clásica y de Resource Manager. El tráfico entre las máquinas virtuales de las redes virtuales clásicas y las máquinas virtuales de las redes virtuales de Resource Manager puede comunicarse a través de ExpressRoute si ambas redes virtuales están vinculadas al mismo circuito ExpressRoute.
* Los modelos de implementación clásica y de Resource Manager admiten ambos la conectividad entre suscripciones.
* Después de mover un circuito ExpressRoute del modelo clásico al de Azure Resource Manager, puede [migrar las redes virtuales vinculadas al circuito ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Lo que no se admite
En esta sección se describe lo que no se admite para los circuitos ExpressRoute:

* Administrar el ciclo de vida de un circuito ExpressRoute desde el modelo de implementación clásica.
* Compatibilidad del control de acceso basado en roles de Azure (Azure RBAC) para el modelo de implementación clásica. No puede ejecutar los controles de Azure RBAC en un circuito del modelo de implementación clásica. Cualquier administrador o coadministrador de la suscripción puede vincular o desvincular redes virtuales al/del circuito.

## <a name="configuration"></a>Configuración
Revise las instrucciones que se ofrecen en [Transición de los circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Pasos siguientes
* [Migración de las redes virtuales vinculadas al circuito ExpressRoute del modelo clásico al de Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Para obtener información sobre los flujos de trabajo, consulte [Flujos de trabajo de aprovisionamiento de circuitos y estados de circuito de ExpressRoute](expressroute-workflows.md).
* Configure su conexión ExpressRoute:
  
  * [Creación de un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configuración del enrutamiento](expressroute-howto-routing-arm.md)
  * [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)


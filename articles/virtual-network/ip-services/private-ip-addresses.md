---
title: Direcciones IP privadas en Azure
titlesuffix: Azure Virtual Network
description: Información sobre las direcciones IP privadas en Azure.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 748d3db418ee41164f7151dc6a62d7cc73a99604
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367868"
---
# <a name="private-ip-addresses"></a>Direcciones IP privadas
Las direcciones IP privadas permiten la comunicación entre los recursos de Azure. 

Los recursos pueden:
* Ser servicios de Azure, como:
    * Interfaces de red de máquinas virtuales
    * Equilibradores de carga internos (ILB)
    * Puertas de enlace de aplicaciones
* Estar en una [red virtual](../../virtual-network/virtual-networks-overview.md).
* Ser una red virtual a una red local mediante una instancia de VPN Gateway o circuito de ExpressRoute.

Las IP privadas permiten la comunicación con estos recursos sin usar una dirección IP pública.

## <a name="allocation-method"></a>Método de asignación

Azure asigna direcciones IP privadas a recursos del intervalo de direcciones de la subred de la red virtual en la que se encuentra el recurso.

Azure reserva las cuatro primeras direcciones en cada intervalo de direcciones de subred. No se pueden asignar direcciones a los recursos. Por ejemplo, si el intervalo de direcciones de la subred es 10.0.0.0/16, las direcciones 10.0.0.0-10.0.0.3 y 10.0.255.255 no están disponibles. Las direcciones IP dentro del intervalo de direcciones de la subred solo pueden asignarse a un recurso a la vez. 

Hay dos métodos para proporcionar direcciones IP privadas:

- **Dinámica**: Azure asigna la siguiente dirección IP sin asignar o no reservada disponible en el intervalo de direcciones de la subred. Por ejemplo, Azure asigna 10.0.0.10 a un nuevo recurso, si las direcciones 10.0.0.4 a 10.0.0.9 ya están asignadas a otros. 

    Este es el método de asignación predeterminado. Una vez asignadas, las direcciones IP dinámicas solo se liberan cuando una interfaz de red:
    
    * Deleted
    * Se reasigna a una subred diferente dentro de la misma red virtual.
    * El método de asignación se cambia a estático y se especifica una dirección IP diferente. 
    
    De forma predeterminada, cuando se cambia el método de asignación de dinámica a estática, Azure asigna la dirección asignada dinámicamente anterior como dirección estática.

- **Estática**: seleccione y asigne cualquier dirección IP sin asignar o no reservada en el intervalo de direcciones de la subred. 

    Por ejemplo, un intervalo de direcciones de la subred es 10.0.0.0/16 y las direcciones 10.0.0.4 a 10.0.0.9 se asignan a otros recursos. Puede asignar cualquier dirección entre 10.0.0.10 y 10.0.255.254. Las direcciones estáticas solo se liberan cuando se elimina la interfaz de red. 
    
    Azure asigna la dirección IP estática como dirección IP dinámica cuando se cambia el método de asignación. La reasignación se produce incluso si la dirección no es la siguiente disponible en la subred. La dirección cambia cuando la interfaz de red se asigna a una subred diferente.
    
    Para asignar la interfaz de red a una subred diferente, debe cambiar el método de asignación de estático a dinámico. Asigne la interfaz de red a una subred diferente y, después, cambie el método de asignación a estático nuevamente. Asigne una dirección IP del nuevo intervalo de direcciones de la subred.
    
## <a name="virtual-machines"></a>Máquinas virtuales

Una o varias direcciones IP privadas se asignan a una o varias **interfaces de red**. Las interfaces de red se asignan a una máquina virtual de [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). En todas las direcciones IP privadas se puede especificar el método de asignación como estática o dinámica.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Resolución de nombres de host DNS internos (para máquinas virtuales)

Las máquinas virtuales de Azure se configuran con [servidores DNS administrados por Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de forma predeterminada. Puede configurar de forma explícita los servidores DNS personalizados. Estos servidores DNS proporcionan la resolución de nombres internos para las máquinas virtuales que se encuentran en la misma red virtual.

Se agrega a los servidores DNS administrados por Azure una asignación para el nombre de host a una dirección IP privada de la máquina virtual. 

Un nombre de host se asigna a la dirección IP principal de la interfaz de red principal cuando una máquina virtual tiene:

* Varias interfaces de red
* Varias direcciones IP
* Ambos

Las máquinas virtuales configuradas con DNS administrado por Azure resuelven los nombres de host dentro de la misma red virtual. Use un servidor DNS personalizado para resolver los nombres de host de las máquinas virtuales en redes virtuales conectadas.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Equilibradores de carga internos (ILB) y puertas de enlace de aplicaciones

Puede asignar una dirección IP privada a la configuración de **front-end** de los siguientes:

* [Equilibrador de carga interno de Azure](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Introducción a Puerta de enlace de aplicaciones](../../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Esta dirección IP privada funciona como punto de conexión interno. A este punto de conexión solo pueden acceder los recursos en su red virtual y las redes remotas conectadas a dicha red virtual. Se puede asignar una dirección IP estática o dinámica.

## <a name="at-a-glance"></a>De un vistazo
En la tabla siguiente se muestra la propiedad a través de la cual una dirección IP privada se puede asociar a un recurso. 

También se muestran los métodos de asignación posibles que pueden usarse:

* Dinámica
* estática

| Recurso de nivel superior | Asociación de dirección IP | Dinámica | estática |
| --- | --- | --- | --- |
| Máquina virtual |interfaz de red |Sí |Sí |
| Equilibrador de carga |Configuración de front-end |Sí |Sí |
| puerta de enlace de aplicaciones |Configuración de front-end |Sí |Sí |

## <a name="limits"></a>Límites
Los límites en una dirección IP se encuentran en el conjunto completo de [límites de red](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) de Azure. Los límites son por región y suscripción. [Póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados hasta alcanzar los límites máximos, según las necesidades empresariales.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las [direcciones IP públicas en Azure](public-ip-addresses.md).
* [Implemente una VM con una dirección IP privada estática](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)
---
title: Preguntas frecuentes sobre Azure Route Server
description: Obtenga respuestas a algunas preguntas frecuentes sobre Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: e17d49654b3c658ed133686e11d70c72b7f7f3b8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426029"
---
# <a name="azure-route-server-faq"></a>Preguntas frecuentes sobre Azure Route Server

## <a name="what-is-azure-route-server"></a>¿Qué es Azure Route Server?

Azure Route Server es un servicio totalmente administrado que permite administrar fácilmente el enrutamiento entre la aplicación virtual de red (NVA) y la red virtual.

### <a name="is-azure-route-server-just-a-vm"></a>¿Azure Route Server es solo una máquina virtual?

No. Azure Route Server es un servicio diseñado con alta disponibilidad. Si se implementa en una región de Azure que admite [Availability Zones](../availability-zones/az-overview.md), tendrá redundancia de nivel de zona.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>¿Cuántos servidores de rutas puedo crear en una red virtual?

Solo puede crear un servidor de rutas en una red virtual, y se debe implementar en una subred dedicada denominada *RouteServerSubnet*.

### <a name="does-azure-route-server-support-virtual-network-peering"></a>¿Admite Azure Route Server el emparejamiento de red virtual?

Sí. Si empareja una red virtual que hospeda Azure Route Server con otra red virtual y habilita la opción Use Remote Gateway (Usar puertas de enlace remotas) en esta última, Azure Route Server aprenderá los espacios de direcciones de esa red virtual y los enviará a todas las aplicaciones virtuales de red emparejadas. También programará las rutas de las aplicaciones virtuales de red en la tabla de enrutamiento de las máquinas virtuales de la red virtual emparejada. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>¿Qué protocolos de enrutamiento admite Azure Route Server?

Azure Route Server solo admite Protocolo de puerta de enlace de borde (BGP). La NVA debe admitir BGP externo de salto múltiple porque deberá implementar Azure Route Server en una subred dedicada de la red virtual. El [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) que elija debe ser diferente del que usa Azure Route Server al configurar BGP en la NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>¿Azure Route Server enruta el tráfico de datos entre la NVA y las máquinas virtuales?

No. Azure Route Server solo intercambia rutas BGP con la NVA. El tráfico de datos va directamente desde la aplicación virtual de red a la máquina virtual de destino y viceversa.

### <a name="does-azure-route-server-store-customer-data"></a>¿Azure Route Server almacena los datos de los clientes?
No. Azure Route Server solo intercambia rutas de BGP con NVA y, a continuación, las propaga a la red virtual.

### <a name="why-does-azure-route-server-require-a-public-ip-address"></a>¿Por qué Azure Route Server necesita una dirección IP pública?

Azure Route Server debe garantizar la conectividad con el servicio back-end que administra la configuración del servidor de rutas, por lo que se requiere una dirección IP pública. 

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Si Azure Route Server recibe la misma ruta de más de una aplicación virtual de red, ¿cómo las administra?

Si la ruta tiene la misma longitud de ruta de acceso del sistema autónomo (AS), Azure Route Server programará varias copias de ella, cada una con un próximo salto diferente, a las máquinas virtuales de la red virtual. Cuando las máquinas virtuales envían tráfico al destino de esta ruta, los hosts de la máquina virtual realizarán un enrutamiento multidireccional de igual costo (ECMP). Sin embargo, si una aplicación virtual de red envía la ruta con una longitud de ruta de acceso de AS más corta que otras aplicaciones virtuales de red, Azure Route Server solo programará la ruta que tenga el próximo salto establecido en esta aplicación virtual de red a las máquinas virtuales de la red virtual.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>¿Conserva Azure Route Server las comunidades BGP de la ruta que recibe?

Sí, Azure Route Server propaga la ruta con las comunidades BGP tal y como está.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>¿Qué números de sistema autónomo (ASN) puedo usar?

Puede utilizar sus propios ASN públicos o ASN privados en la aplicación virtual de red. No se pueden usar los rangos reservados por Azure o IANA.
Los siguientes ASN están reservados por Azure o IANA:

* ASN reservados por Azure:
    * ASN públicos: 8074, 8075, 12076
    * ASN privados: 65515, 65517, 65518, 65519, 65520
* ASN [reservados por IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>¿Se pueden usar ASN de 32 bits (4 bytes)?

No, Azure Route Server solo admite ASN de 16 bits (2 bytes).

### <a name="can-i-configure-a-user-defined-route-udr-in-the-azurerouteserver-subnet"></a>¿Puedo configurar una ruta definida por el usuario (UDR) en la subred AzureRouteServer?

No. Azure Route Server no admite la configuración de una UDR en la subred AzureRouteServer.

### <a name="can-i-peer-two-route-servers-in-two-peered-virtual-networks-and-enable-the-nvas-connected-to-the-route-servers-to-talk-to-each-other"></a>¿Puedo emparejar dos servidores de rutas en dos redes virtuales emparejadas y permitir que las aplicaciones virtuales de red conectadas a los servidores de ruta se comuniquen entre sí? 

***Topología: NVA1 -> RouteServer1 -> (a través de emparejamiento de red virtual) -> RouteServer2 -> NVA2***

No, Azure Route Server no desvía el tráfico de datos. Para permitir la conectividad de tránsito a través de la NVA, configure una conexión directa (por ejemplo, un túnel IPsec) entre las NVA y use los servidores de rutas para la propagación dinámica de rutas. 

## <a name="route-server-limits"></a><a name = "limitations"></a>Límites de Route Server

Azure Route Server tiene los límites siguientes (por implementación).

| Resource | Límite |
|----------|-------|
| Número de emparejamientos de BGP admitidos | 8 |
| Número de rutas que puede anunciar cada emparejamiento de BGP a Azure Route Server | 1000 |
| Número de rutas que puede anunciar Azure Route Server a ExpressRoute o a la puerta de enlace de VPN | 200 |
| Número de máquinas virtuales de la red virtual (incluidas las redes virtuales emparejadas) que Azure Route Server puede admitir. | 2000 |

El número de máquinas virtuales que Azure Route Server puede admitir no tiene un límite estricto. Este depende de cómo se implemente la infraestructura del servidor de rutas dentro de una región de Azure.

Si la NVA anuncia más rutas que el límite, se descartará la sesión BGP. En caso de que se descarte la sesión BGP entre la puerta de enlace y Azure Route Server, perderá la conectividad de la red local a Azure. Para más información, consulte [Diagnóstico de problemas de enrutamiento en una máquina virtual](../virtual-network/diagnose-network-routing-problem.md).


## <a name="next-steps"></a>Pasos siguientes

Consulte [Configuración de una instancia de Azure Route Server](quickstart-configure-route-server-powershell.md).

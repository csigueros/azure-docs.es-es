---
title: Instrucciones para el planeamiento de red de Azure NetApp Files | Microsoft Docs
description: Describe las instrucciones que pueden ayudarle a diseñar una arquitectura de red eficaz con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: ramakk
ms.openlocfilehash: 09f09b5c50dd04f39f95405df9875d458a258b25
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545970"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Instrucciones para el planeamiento de red de Azure NetApp Files

La planeación de la arquitectura de red es un elemento clave del diseño de cualquier infraestructura de aplicación. En este artículo encontrará ayuda para diseñar una arquitectura de red eficaz para que sus cargas de trabajo se beneficien de las completas funcionalidades de Azure NetApp Files.

Los volúmenes de Azure NetApp Files están diseñados para incluirse en una subred de propósito especial llamada [subred delegada](../virtual-network/virtual-network-manage-subnet.md) dentro de Azure Virtual Network. Por lo tanto, puede acceder a los volúmenes directamente desde dentro de Azure mediante emparejamiento de red virtual o desde un entorno local por medio de una puerta de enlace de red virtual (ExpressRoute o VPN Gateway), según sea necesario. La subred está dedicada a Azure NetApp Files y no hay conectividad a Internet. 

## <a name="configurable-network-features"></a>Características de red configurables  

 La configuración de [**características de red Standard**](configure-network-features.md) para Azure NetApp Files está disponible en versión preliminar pública. Después de registrarse en esta característica con la suscripción, puede crear nuevos volúmenes si elije características de red *Standard* o *Basic* en las regiones admitidas. En las regiones en las que no se admiten las características de red Standard, el volumen usa de manera predeterminada las características de red Basic.  

* ***Estándar***  
    La selección de este valor permite límites de IP superiores y características de red virtual estándar, como [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md) y [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md#user-defined) en subredes delegadas, y patrones de conectividad adicionales, como se indica en este artículo.

* ***Basic***  
    Al seleccionar este valor se habilitan los patrones de conectividad selectivos y la escala de IP limitada, como se menciona en la sección [Consideraciones](#considerations). Todas las [restricciones](#constraints) se aplican en este valor. 

## <a name="considerations"></a>Consideraciones  

Debe entender algunas consideraciones al planear de red de Azure NetApp Files.

### <a name="constraints"></a>Restricciones

En la tabla siguiente se describe lo que se admite en cada configuración de características de red:

|      Características     |      Características de red Standard     |      Características de red Basic     |
|---|---|---|
|     Número de direcciones IP en uso en una red virtual con Azure NetApp Files (incluidas las redes virtuales emparejadas inmediatamente)    |     [Límites estándar como máquinas virtuales](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)    |     1000    |
|     Subredes delegadas de ANF por red virtual    |     1    |     1    |
|     [Grupos de seguridad de red](../virtual-network/network-security-groups-overview.md) (NSG) en subredes delegadas de Azure NetApp Files    |     Sí    |     No    |
|     [Rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md#user-defined) (UDR) en subredes delegadas de Azure NetApp Files    |     Sí    |     No    |
|     Conectividad a [puntos de conexión privados](../private-link/private-endpoint-overview.md)    |     No    |     No    |
|     Conectividad a [puntos de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md)    |     No    |     No    |
|     Directivas de Azure (por ejemplo, directivas de nomenclatura personalizadas) en la interfaz de Azure NetApp Files    |     No    |     No    |
|     Equilibradores de carga para el tráfico de Azure NetApp Files    |     No    |     No    |
|     Red virtual de pila dual (IPv4 e IPv6)    |     No <br> (Solo se admite IPv4)    |     No <br> (Solo se admite IPv4)    |

### <a name="supported-network-topologies"></a>Topologías de red admitidas

En la tabla siguiente se describen las topologías de red admitidas en cada configuración de características de red de Azure NetApp Files. 

|      Topologías     |      Características de red Standard     |      Características de red Basic     |
|---|---|---|
|     Conectividad al volumen en una red virtual local    |     Sí    |     Sí    |
|     Conectividad al volumen en una red virtual emparejada (misma región)    |     Sí    |     Sí    |
|     Conectividad a volumen en una red virtual emparejada (emparejamiento global o entre regiones)    |     No    |     No    |
|     Conectividad a un volumen a través de la puerta de enlace ExpressRoute    |     Sí    |     Sí    |
|     FastPath de ExpressRoute (ER)    |     Sí    |     No    |
|     Conectividad desde el entorno local a un volumen en una red virtual de radio por medio de una puerta de enlace de ExpressRoute y emparejamiento de redes virtuales con tránsito de puerta de enlace    |     Sí    |     Sí    |
|     Conectividad desde el entorno local a un volumen en una red virtual de radio por medio de una puerta de enlace de VPN    |     Sí    |     Sí    |
|     Conectividad desde el entorno local a un volumen en una red virtual de radio por medio de una puerta de enlace de VPN y emparejamiento de redes virtuales con tránsito de puerta de enlace    |     Sí    |     Sí    |
|     Conectividad mediante puertas de enlace de VPN activo/pasivo    |     Sí    |     Sí    |
|     Conectividad mediante puertas de enlace de VPN activo/activo    |     Sí    |     No    |
|     Conectividad mediante puertas de enlace con redundancia de zona activa/activa    |     No    |     No    |
|     Conectividad mediante Virtual WAN (VWAN)    |     No    |     No    |

## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Red virtual para volúmenes de Azure NetApp Files

En esta sección se explican los conceptos que le ayudarán a planear la red virtual.

### <a name="azure-virtual-networks"></a>Redes virtuales de Azure

Antes de aprovisionar un volumen de Azure NetApp Files, deberá crear una red virtual de Azure (VNet) o usar una ya existente en su suscripción. La red virtual define los límites de red del volumen.  Para más información acerca de cómo crear redes virtuales, consulte la [documentación de Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Subredes

Las subredes segmentan la red virtual en espacios de direcciones independientes utilizables por los recursos de Azure que se encuentran en ellos.  Los volúmenes de Azure NetApp Files están incluidos en una subred de propósito especial llamada [subred delegada](../virtual-network/virtual-network-manage-subnet.md). 

La delegación de la subred proporciona al servicio Azure NetApp Files permisos explícitos para crear recursos específicos del servicio en la subred.  Usa un identificador único para implementar el servicio. En este caso, se crea una interfaz de red para habilitar la conectividad a Azure NetApp Files.

Si usa una red virtual nueva, puede crear una subred y delegar la subred a Azure NetApp Files según las instrucciones de [Delegación de una subred en Azure NetApp Files](azure-netapp-files-delegate-subnet.md). También puede delegar una subred vacía existente que no esté ya delegada a otros servicios.

Si la red virtual está emparejada con otra red virtual, no puede expandir el espacio de direcciones de la red virtual. Por ese motivo, la nueva subred delegada debe crearse dentro del espacio de direcciones de la red virtual. Si necesita expandir el espacio de direcciones, debe eliminar el emparejamiento de redes virtuales antes de expandir el espacio de direcciones.

### <a name="udrs-and-nsgs"></a>UDR y NSG

Las rutas definidas por el usuario (UDR) y los grupos de seguridad de red (NSG) solo se admiten en subredes delegadas de Azure NetApp Files que tienen al menos un volumen creado con las características de red Standard.  

> [!NOTE]
> No se permite la asociación de NSG en el nivel de interfaz de red en interfaces de red de Azure NetApp Files. 

Si la subred tiene una combinación de volúmenes con las características de red Standard y Basic (o para volúmenes existentes no registrados en la versión preliminar de la característica), las UDR y los NSG aplicados en las subredes delegadas solo se aplican a los volúmenes con las características de red Standard.

La configuración de rutas definidas por el usuario (UDR) en las subredes de máquina virtual de origen con prefijo de dirección de subred delegada y próximo salto como NVA no se admite para volúmenes con las características de red Basic. Esa configuración da lugar a problemas de conectividad.

## <a name="azure-native-environments"></a>Entornos nativos de Azure

En el siguiente diagrama se ilustra un entorno nativo de Azure:

![Entorno de conexión nativo de Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Red virtual local

Un escenario básico consiste en crear o conectarse a un volumen de Azure NetApp Files desde una máquina virtual (VM) en la misma red virtual. Para la red virtual 2 en el diagrama anterior, el volumen 1 se crea en una subred delegada y puede montarse en la VM 1 en la subred predeterminada.

### <a name="vnet-peering"></a>Emparejamiento de VNET

Si en la misma región tiene redes virtuales adicionales que deben acceder a los recursos de las demás, las redes virtuales se pueden conectar mediante [emparejamiento de redes virtuales](../virtual-network/virtual-network-peering-overview.md) para habilitar la conectividad segura a través de la infraestructura de Azure. 

Piense en la red virtual 2 y la red virtual 3 en el diagrama anterior. Si la VM 1 tiene que conectarse a la VM 2 o al volumen 2, o si la VM 2 tiene que conectarse a la VM 1 o al volumen 1, tiene que habilitar el emparejamiento de redes virtuales entre la red virtual 2 y la red virtual 3. 

Además, considere un escenario donde la red virtual 1está emparejada con la red virtual 2, y la red virtual 2 está emparejada con la red virtual 3 en la misma región. Los recursos de la red virtual 1 pueden conectarse a los recursos de la red virtual 2, pero no pueden conectarse a los recursos de la red virtual 3, a menos que la red virtual 1 y la red virtual 3 estén emparejadas. 

En el diagrama anterior, aunque la VM 3 puede conectarse al volumen 1, la VM 4 no puede conectarse al volumen 2.  El motivo para esto es que las redes virtuales de radio no están emparejadas, y _no se admite el enrutamiento de tránsito a través del emparejamiento de redes virtuales_.

## <a name="hybrid-environments"></a>Entornos híbridos

En el siguiente diagrama se ilustra un entorno híbrido: 

![Entorno de conexión híbrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

En el escenario híbrido, las aplicaciones de centros de datos locales necesitan acceso a los recursos de Azure.  Esto es así si quiere expandir su centro de datos a Azure, o si quiere usar los servicios nativos de Azure o para la recuperación ante desastres. Consulte las [opciones de planeamiento de VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) para información sobre cómo conectar varios recursos locales a los recursos en Azure a través de una VPN de sitio a sitio o ExpressRoute.

En una topología de red en estrella tipo hub-and-spoke híbrida, la red virtual del centro (hub) en Azure actúa como un punto central de conectividad para la red local. Los radios (spokes) son redes virtuales emparejadas con el centro y pueden usarse para aislar las cargas de trabajo.

En función de la configuración, puede conectar los recursos locales a los recursos del centro y los radios.

En la topología que se ilustra anteriormente, la red local está conectada a una red virtual de radio en Azure, y hay 2 redes virtuales de radio en la misma región emparejadas con la red virtual del centro.  En este escenario, las opciones de conectividad admitidas para los volúmenes de Azure NetApp Files son las siguientes:

* Los recursos locales VM 1 y VM 2 pueden conectarse al volumen 1 en el centro a través de una VPN de sitio a sitio o un circuito ExpressRoute. 
* Los recursos locales VM 1 y VM 2 pueden conectarse al volumen 2 o volumen 3 a través de una VPN de sitio a sitio y emparejamiento de redes virtuales regional.
* La VM 3 en el centro puede conectarse al volumen 2 en la red virtual 1 de radio y el volumen 3 en la red virtual 2 de radio.
* La VM 4 de la red virtual 1 de radio y la VM 5 de la red virtual 2 de radio pueden conectarse al volumen 1 en la red virtual del centro.
* La VM 4 en la red virtual 1 de radio no se puede conectar al volumen 3 en la red virtual 2 de radio. Además, la VM 5 en la red virtual 2 de radio no se puede conectar al volumen 2 en la red virtual 1 de radio. Esto se debe a que las redes virtuales de radio no están emparejadas, y _no se admite el enrutamiento de tránsito a través del emparejamiento de redes virtuales_.
* En la arquitectura anterior, si también hay una puerta de enlace en la red virtual de radios, se perderá la conectividad al volumen de ANF de la conexión local a través de la puerta de enlace en el concentrador. Por diseño, se dará prioridad a la puerta de enlace de la red virtual de radios y, de este modo, solo las máquinas que se conecten a través de esa puerta de enlace podrán conectarse al volumen de ANF.

## <a name="next-steps"></a>Pasos siguientes

* [Delegación de una subred en Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
* [Configuración de las características de red de un volumen de Azure NetApp Files](configure-network-features.md) 

---
title: Azure para ingenieros de red
description: En esta página se explica a los ingenieros de red tradicionales cómo funcionan las redes en Azure.
documentationcenter: na
services: networking
author: osamazia
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: c73ea7c9e2f399e479f7d3f8bef5797feabb83ec
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228813"
---
# <a name="azure-for-network-engineers"></a>Azure para ingenieros de red
Como ingeniero de red convencional, ha trabajado con recursos físicos, como enrutadores, conmutadores, cables y firewalls, para crear infraestructuras. En una capa lógica, ha configurado la LAN virtual (VLAN), el protocolo de árbol de expansión (STP), los protocolos de enrutamiento (RIP, OSPF, BGP). Ha administrado la red mediante herramientas de administración y la CLI. Las redes en la nube son diferentes cuando los puntos de conexión de red son lógicos y el uso de los protocolos de enrutamiento es mínimo. Trabajará con la API Azure Resource Manager, la CLI de Azure y PowerShell para configurar y administrar recursos en Azure. Para comenzar el recorrido de la red en la nube, deberá comprender los inquilinos básicos de las redes de Azure. 
## <a name="virtual-network"></a>Virtual network
Al diseñar una red desde cero, recopilará información básica. Esta información podría ser el número de hosts, dispositivos de red, el número de subredes, el enrutamiento entre subredes y dominios de aislamiento, como las VLAN. Esta información ayuda a ajustar el tamaño de la red y los dispositivos de seguridad, así como a crear la arquitectura para admitir aplicaciones y servicios.

Al planear la implementación de aplicaciones y servicios en Azure, comenzará por crear un límite lógico en Azure, que se denomina "red virtual". Esta red virtual es similar a un límite de red física. Al tratarse de una red virtual, no necesita equipos físicos, pero aún tiene que planear las entidades lógicas, como las direcciones IP, las subredes IP, el enrutamiento y las directivas.

Cuando crea una red virtual en Azure, está configurada previamente con un intervalo de direcciones IP (10.0.0.0/16). Este intervalo no es fijo, puede definir su propio intervalo IP. Puede definir intervalos de direcciones IPv4 e IPv6. Los intervalos IP definidos para la red virtual no se anuncian en Internet. Puede crear varias subredes desde el intervalo IP. Estas subredes se usarán para asignar direcciones IP a interfaces de red virtual (vNIC). Las cuatro primeras direcciones IP de cada subred están reservadas y no se pueden usar para la asignación de direcciones IP. No existe el concepto de VLAN en una nube pública. Sin embargo, puede crear aislamiento dentro de una red virtual en función de las subredes definidas.

Puede crear una subred grande que abarque todo el espacio de direcciones de la red virtual o elegir crear varias subredes. Sin embargo, si usa una puerta de enlace de red virtual, Azure le exige crear una subred con el nombre "subred de puerta de enlace". Azure usará esta subred para asignar las direcciones IP a las puertas de enlace de la red virtual. 

## <a name="ip-allocation"></a>Asignación de direcciones IP

Cuando asigna una dirección IP a un host, en realidad asigna la IP a una tarjeta de interfaz de red (NIC). En Azure se pueden asignar dos tipos de direcciones IP a una NIC:

- Direcciones IP públicas: Se usan para la comunicación entrante y saliente [sin traducción de direcciones de red (NAT)] con Internet y otros recursos de Azure no conectados a una red virtual. La asignación de una dirección IP pública a una NIC es opcional. Las direcciones IP públicas pertenecen al espacio de direcciones IP de Microsoft.
- Direcciones IP privadas: Se usan para la comunicación dentro de una red virtual, una red local e Internet (con NAT). El espacio de direcciones IP que se define en la red virtual se considera privado, incluso si se configura el espacio de direcciones IP públicas. Microsoft no anuncia este espacio en Internet. Debe asignar al menos una dirección IP privada a una máquina virtual.

Al igual que con los hosts o dispositivos físicos, hay dos maneras de asignar una dirección IP a un recurso: dinámica o estática. En Azure, el método de asignación predeterminado es dinámico, donde se asigna una dirección IP cuando se crea una máquina virtual (VM) o se inicia una VM detenida. La dirección IP se libera cuando se detiene o se elimina la máquina virtual. Para asegurarse de que la dirección IP de la máquina virtual no cambia, puede establecer explícitamente el método de asignación en estático. En este caso, se asigna de inmediato una dirección IP. Solo se libera cuando se elimina la máquina virtual o cuando se cambia su método de asignación a dinámico. 

Las direcciones IP privadas se asignan desde las subredes que se han definido en una red virtual. En el caso de una VM, elija una subred para la asignación de direcciones IP. Si una VM contiene varias NIC, puede elegir una subred diferente para cada NIC.

## <a name="routing"></a>Enrutamiento
Cuando se crea una red virtual, Azure crea una tabla de enrutamiento para la red. Esta tabla de enrutamiento contiene los siguientes tipos de rutas.
- Rutas del sistema
- Rutas predeterminadas de subred
- Rutas de otras redes virtuales
- Rutas BGP
- Rutas de punto de conexión de servicio
- Rutas definidas por el usuario (UDR)

Al crear una red virtual por primera vez sin definir ninguna subred, Azure crea entradas de enrutamiento en la tabla de enrutamiento. Estas rutas se denominan "rutas del sistema". Las rutas del sistema se definen en esta ubicación. No se pueden modificar estas rutas. Sin embargo, puede configurar UDR para invalidar las rutas de los sistemas.

Al crear una o varias subredes dentro de una red virtual, Azure crea entradas predeterminadas en la tabla de enrutamiento para permitir la comunicación entre estas subredes en una red virtual. Estas rutas se pueden modificar mediante rutas estáticas, que son rutas definidas por el usuario (UDR) en Azure.

al crear un emparejamiento entre dos redes virtuales, se agrega una ruta para cada intervalo de direcciones en el espacio de direcciones de cada red virtual para la que se crea un emparejamiento.

Si la puerta de enlace de red local intercambia las rutas del protocolo Border Gateway Protocol (BGP) con una puerta de enlace de red virtual de Azure, se agrega una ruta por cada ruta que se propaga desde la puerta de enlace de red local. Estas rutas aparecen en la tabla de enrutamiento como rutas BGP.

Azure agrega las direcciones IP públicas de determinados servicios a la tabla de rutas cuando se habilita un punto de conexión para el servicio. Los puntos de conexión de servicio se habilitan para subredes individuales dentro de una red virtual. Cuando se habilita un punto de conexión de servicio, la ruta solo se agrega a la tabla de enrutamiento para la subred que pertenece a este servicio. Azure administra automáticamente las direcciones en la tabla de rutas cuando cambian.

Las rutas definidas por el usuario también se denominan "rutas personalizadas". En Azure se pueden crear UDR para reemplazar las rutas de sistema predeterminadas de Azure o para agregar rutas adicionales a una tabla de enrutamiento de una subred. En Azure, cree una tabla de enrutamiento y asóciela a las subredes de la red virtual.

Cuando haya entradas que compitan en una tabla de enrutamiento, Azure seleccionará el próximo salto en función de la coincidencia de prefijo más larga, de manera similar a la de los enrutadores tradicionales. Sin embargo, si hay varias entradas de próximo salto con el mismo prefijo de dirección, Azure selecciona las rutas en el orden siguiente.
1. Rutas definidas por el usuario (UDR)
1. Rutas BGP
1. Rutas del sistema

## <a name="security"></a>Seguridad

Puede filtrar el tráfico de red hacia y desde los recursos en una red virtual mediante los grupos de seguridad de red. También puede usar aplicaciones virtuales de red (NVA), como Azure Firewall o firewalls de otros proveedores. Puede controlar la manera en que Azure enruta el tráfico de subredes. Asimismo, también puede limitar en su organización quién puede trabajar con los recursos en redes virtuales.

Un grupo de seguridad de red (NSG) contiene una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a subredes, NIC, o ambas. Los grupos de seguridad de red se pueden asociar con subredes o con NIC individuales conectadas a una subred. Si un grupo de seguridad de red está asociado a una subred, las reglas de la ACL se aplican a todas las máquinas virtuales de dicha subred. Además, se puede limitar el tráfico que llega a una NIC individual, para lo que se debe asociar un NSG directamente a la NIC.

Contiene dos tipos de reglas: de entrada y de salida. La prioridad de una regla debe ser única dentro de cada conjunto. Cada regla tiene propiedades de protocolo, intervalos de puertos de origen y destino, prefijos de direcciones, dirección de tráfico, prioridad y tipo de acceso. Todos los grupos de seguridad de red contienen un conjunto de reglas predeterminadas. No se pueden eliminar las reglas predeterminadas, pero dado que tienen asignada la mínima prioridad, pueden reemplazarse por las reglas que cree.

## <a name="verification"></a>Comprobación
### <a name="routes-in-virtual-network"></a>Rutas en la red virtual
La combinación de rutas que cree, las rutas predeterminadas de Azure y las rutas que se propagan desde la red local a través de una puerta de enlace de VPN de Azure (si la red virtual está conectada a la red local) mediante el protocolo de puerta de enlace de borde (BGP), son la rutas eficaces para todas las interfaces de red de una subred. Para ver estas rutas eficaces, puede navegar a la NIC a través del portal, PowerShell o la CLI.
### <a name="network-security-groups"></a>Grupos de seguridad de red
Las reglas de seguridad en vigor aplicadas a una interfaz de red se agregan a las que existen en el NSG asociado a una interfaz de red y la subred en la que se encuentra la interfaz. Para ver todas las reglas de seguridad vigentes desde los NSG que se aplican a las interfaces de red de la VM, puede navegar a la NIC a través del portal, PowerShell o la CLI.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [redes virtuales][VNet].

Más información sobre el [enrutamiento de redes virtuales][vnet-routing].

Más información sobre los [grupos de seguridad de red][network-security].

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md

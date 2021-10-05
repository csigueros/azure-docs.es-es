---
title: Información general de IPv6 para Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Descripción de IPv6 de los puntos de conexión y las rutas de acceso a datos de IPv6 en una instancia de Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: b6b641f82e81e69636f418c343b592336c080335
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094697"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>¿Qué es IPv6 para Azure Virtual Network?

IPv6 para Azure Virtual Network (VNet) le permite hospedar aplicaciones en Azure con la conectividad IPv6 e IPv4, tanto en una red virtual como hacia y desde Internet. Debido al agotamiento de direcciones IPv4 públicas, las nuevas redes de movilidad e Internet de las cosas (IoT) a menudo se basan en IPv6. Incluso las redes móviles e ISP consolidadas se están transformando a IPv6. Los servicios que solo usan IPv4 pueden encontrarse en desventaja real en los mercados existentes y emergentes. La conectividad IPv4/IPv6 de pila doble permite que los servicios hospedados en Azure atraviesen esta brecha tecnológica con los servicios en pila doble disponibles globalmente que se conectan fácilmente con la conectividad IPv4 existente y con estos nuevos dispositivos y redes IPv6.

La conectividad IPv6 original de Azure permite proporcionar fácilmente conectividad a Internet (IPv4/IPv6) de doble pila para las aplicaciones hospedadas en Azure. Permite la implementación simple de VM con conectividad IPv6 con equilibrio de carga para las conexiones iniciadas tanto entrantes como salientes. Esta característica todavía está disponible y se puede encontrar más información [aquí](../load-balancer/load-balancer-ipv6-overview.md).
La conectividad IPv6 para Azure Virtual Network es mucho más completa: permite implementar arquitecturas de soluciones IPv6 completas en Azure.


El siguiente diagrama representa una implementación simple de pila doble (IPv4/IPv6) en Azure:

![Diagrama de implementación de red IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Ventajas

Ventajas de IPv6 para la red virtual de Azure:

- Ayuda a ampliar el alcance de sus aplicaciones hospedadas en Azure en los mercados móvil y de Internet de las cosas en desarrollo.
- Las VM IPv4/IPv6 con pila doble proporcionan la máxima flexibilidad de implementación del servicio. Una sola instancia del servicio se puede conectar con clientes de Internet que admitan tanto IPv4 como IPv6.
- Se basa en la consolidada y estable conectividad IPv6 de Azure Virtual Machines con Internet.
- Ofrece seguridad de forma predeterminada, puesto que la conectividad IPv6 a Internet solo se establece cuando se solicita explícitamente en la implementación.

## <a name="capabilities"></a>Capacidades

IPv6 para red virtual de Azure incluye las siguientes características:

- Los clientes de Azure pueden definir su propio espacio de direcciones de red virtual IPv6 para satisfacer las necesidades de sus aplicaciones y clientes, o bien para la integración sin problemas en su espacio de IP local.
- Las redes virtuales (IPv4 e IPv6) de pila doble con subredes de pila doble permiten a las aplicaciones conectarse con recursos IPv4 e IPv6 en su red virtual o en Internet.
    > [!IMPORTANT]
    > Las subredes para IPv6 deben tener un tamaño exactamente de /64.  Esto garantiza la compatibilidad en el futuro si habilita el enrutamiento de la subred a una red local, ya que algunos enrutadores sólo pueden aceptar rutas /64 IPv6.  
- Proteja los recursos con reglas de IPv6 para grupos de seguridad de red.
    - Y las protecciones de denegación de servicio distribuido (DDoS) de la plataforma Azure se amplían a las IP públicas con conexión a Internet.
- Personalice el enrutamiento del tráfico IPv6 en la red virtual con rutas definidas por el usuario (especialmente al utilizar aplicaciones virtuales de red para mejorar su aplicación).
- Las máquinas virtuales Linux y Windows pueden utilizar IPv6 para la red virtual de Azure.
- Compatibilidad con la instancia de [IPv6 pública de Standard Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) para crear aplicaciones resistentes y escalables, lo que incluye:
    - Sondeo de estado de IPv6 opcional para establecer qué instancias del grupo de back-end tienen un estado correcto y, por tanto, reciben flujos nuevos.
    - Reglas de salida opciones que proporcionan un control declarativo completo sobre la conectividad de salida para escalar y ajustar esta conectividad según sus necesidades específicas.
    - Distintas configuraciones de front-end opcionales que permiten que un único equilibrador de carga use varias direcciones IP públicas IPv6: el mismo protocolo y puerto de front-end pueden usarse entre las direcciones de front-end.
    - Los puertos IPv6 opcionales se pueden reutilizar en las instancias de back-end mediante la característica de *IP flotante* de las reglas de equilibrio de carga. 
    - Nota: El equilibrio de carga no realiza ninguna traducción de protocolo (sin NAT64). 
    - Nota: IPv6 solo puede ser de carga equilibrada en la interfaz de red (NIC) principal en máquinas virtuales de Azure. 
- Compatibilidad con la instancia de [IPv6 interna de Standard Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) para crear aplicaciones resistentes de varios niveles en redes virtuales de Azure.   
- Compatibilidad con la instancia de IPv6 pública de Basic Load Balancer para la compatibilidad con implementaciones heredadas.
- [Las direcciones IP públicas y los intervalos de direcciones IPv6 reservadas](./public-ip-address-prefix.md) proporcionan direcciones IPv6 estables y predecibles que facilitan el filtrado de las aplicaciones hospedadas en Azure para su empresa y sus clientes.
- La dirección IP pública a nivel de instancia proporciona conectividad IPv6 a Internet directamente a las máquinas virtuales individuales.
- [Adición de IPv6 a implementaciones de solo IPv4 existentes](ipv6-add-to-existing-vnet-powershell.md): esta característica permite agregar fácilmente conectividad IPv6 a las implementaciones existentes de solo IPv4 sin necesidad de volver a crear dichas implementaciones.  El tráfico de red IPv4 no se ve afectado durante este proceso. Por ello, en función de la aplicación y del sistema operativo, es posible que pueda agregar IPv6 incluso a servicios en directo.    
- Permita a los clientes de Internet acceder sin problemas a la aplicación de pila dual con el protocolo de su elección con compatibilidad con Azure DNS para registros IPv6 (AAAA). 
- Cree aplicaciones de pila doble que se escalen automáticamente a su carga con conjuntos de escalado de máquinas virtuales con IPv6.
- [Emparejamiento de redes virtuales](virtual-network-peering-overview.md): tanto en el emparejamiento regional como global, permite conectar redes virtuales de pila dual. Tanto los puntos de conexión de IPv4 como de IPv6 en las máquinas virtuales de las redes emparejadas podrán comunicarse entre sí. Incluso puede emparejar pilas dobles con redes virtuales solo de IPv4 mientras realiza la transición de las implementaciones de doble pila. 
- Los diagnósticos y la solución de problemas de IPv6 están disponibles con las características de alertas y métricas del equilibrador de carga, así como con las características de Network Watcher como la captura de paquetes, los registros de flujo de grupos de seguridad de red, la solución de problemas de conexión y la supervisión de conexiones.   

## <a name="scope"></a>Ámbito
IPv6 para Azure Virtual Network es un conjunto de características básicas que permite a los clientes hospedar aplicaciones de pila dual (IPv4 + IPv6) en Azure.  Tenemos previsto agregar compatibilidad con IPv6 a más características de red de Azure a lo largo del tiempo y, finalmente, ofrecer versiones de pila dual de los servicios PaaS de Azure. No obstante, mientras tanto, se puede acceder a todos los servicios PaaS de Azure a través de los puntos de conexión IPv4 en máquinas virtuales de pila dual.   

## <a name="limitations"></a>Limitaciones
La versión actual de IPv6 para Azure Virtual Network tiene las siguientes limitaciones:
- IPv6 para red virtual de Azure está disponible en todas las regiones comerciales globales de Azure y de la Administración Pública de EE. UU. mediante todos los métodos de implementación.  
- Las puertas de enlace de ExpressRoute se PUEDEN usar para el tráfico de solo IPv4 en una red virtual con IPv6 habilitado.  La compatibilidad con el tráfico IPv6 de ExpressRoute está en versión preliminar.   
- Las puertas de enlace de VPN NO SE PUEDEN usar en una red virtual que tiene IPv6 habilitado, ya sea directamente o emparejadas con "UseRemoteGateway".
- La plataforma de Azure (AKS, etc.) no admite la comunicación IPv6 para los contenedores. 
- No se admiten máquinas virtuales o conjuntos de escalado de máquinas virtuales de solo IPv6; cada NIC debe incluir al menos una configuración de IP IPv4. 
- Al agregar IPv6 a las implementaciones IPv4 existentes, los rangos IPv6 no se pueden agregar a una red virtual con vínculos de navegación de recursos existentes.  
- El reenvío de DNS para IPv6 es compatible actualmente con DNS público de Azure, pero el DNS inverso todavía no se admite.
- Aunque es posible crear reglas de NSG para IPv4 e IPv6 dentro del mismo NSG, actualmente no se puede combinar una subred IPv4 con una subred IPv6 en la misma regla al especificar prefijos IP.

## <a name="pricing"></a>Precios

El ancho de banda y los recursos de Azure de IPv6 tienen el mismo precios que para IPv4. No se aplican cargos adicionales ni diferentes para IPv6. Puede encontrar detalles sobre precios de [direcciones IP públicas](https://azure.microsoft.com/pricing/details/ip-addresses/), [ancho de banda de red](https://azure.microsoft.com/pricing/details/bandwidth/) o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [implementar una aplicación de pila doble IPv6 con Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Aprenda a [implementar una aplicación de pila doble IPv6 con la CLI de Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Aprenda a [implementar una aplicación de pila dual IPv6 mediante plantillas de Resource Manager (JSON)](ipv6-configure-standard-load-balancer-template-json.md).

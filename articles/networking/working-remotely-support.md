---
title: Trabajo remoto con los servicios de red de Azure
description: En esta página se describe cómo usar los servicios de red de Azure que están disponibles para habilitar el trabajo remoto y cómo mitigar los problemas de tráfico que surgen a raíz del aumento del número de personas que trabajan de forma remota.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 2086351f1e66cbd2ef41ac98b2ab86fe7275823b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621364"
---
# <a name="working-remotely-using-azure-networking-services"></a>Trabajo remoto con los servicios de red de Azure

>[!NOTE]
> En este artículo se describe cómo usar los servicios de red de Azure, la red de Microsoft y el ecosistema de asociados de Microsoft para trabajar de forma remota y mitigar los problemas de red que puede experimentar debido a la crisis de COVID-19.

En este artículo se describen las opciones que están disponibles para que las organizaciones configuren el acceso remoto para sus usuarios o complementen sus soluciones existentes con capacidad adicional durante los períodos de máxima utilización. Los arquitectos de red se enfrentan a los siguientes desafíos:

- Abordar un aumento del uso de la red.
- Proporcionar conectividad segura y confiable a más empleados de su empresa y sus clientes.
- Proporcionar conectividad a ubicaciones remotas en todo el mundo.

No todas las redes (por ejemplo, redes principales corporativas y WAN privadas) se congestionan por una carga máxima de trabajo remoto. Los cuellos de botella se suelen registrar solo en redes de banda ancha domésticas y en puertas de enlace VPN de redes locales corporativas.

Los planificadores de red pueden ayudar a simplificar los cuellos de botella y aliviar la congestión de la red teniendo en cuenta que los distintos tipos de tráfico necesitan diferentes prioridades de tratamiento de la red y una distribución o un redireccionamiento inteligentes de la carga. Por ejemplo, el tráfico de telemedicina en tiempo real derivado de la interacción entre médicos y pacientes es muy importante y sensible a retrasos y fluctuaciones. En cambio, la replicación del mismo tráfico entre los almacenamientos no es sensible a retrasos. El tráfico anterior se debe enrutar a través de la ruta de red más óptima con una mayor calidad de servicio; sin embargo, es aceptable enrutar el tráfico posterior a través de una ruta poco óptima.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Uso compartido de nuestros procedimientos recomendados: la red de Azure está diseñada para la elasticidad y alta disponibilidad

Azure está diseñado para resistir cambios repentinos en el uso de los recursos y puede resultar muy útil durante períodos de máxima utilización. Además, Microsoft mantiene y opera en una de las redes más grandes del mundo. La red de Microsoft se ha diseñado para ofrecer una alta disponibilidad que puede resistir distintos tipos de errores: desde el error de un único elemento de la red hasta el error de una región completa.

La red de Microsoft se ha diseñado para cumplir los requisitos y proporcionar un rendimiento óptimo de los diferentes tipos de tráfico de red, incluido el tráfico multimedia sensible a retrasos para Skype y Teams, la red CDN, el análisis de macrodatos en tiempo real, Azure Storage, Bing y Xbox. Para proporcionar un rendimiento óptimo de los diferentes tipos de tráfico, la red de Microsoft atrae todo el tráfico destinado a sus recursos, o que desea pasar por ellos, lo más cerca posible del origen del tráfico.

>[!NOTE] 
>El uso de las características de red de Azure que se describen a continuación utiliza el comportamiento de atracción del tráfico de la red global de Microsoft para ofrecer una experiencia de red mejorada para el cliente. El comportamiento de atracción del tráfico de la red de Microsoft ayuda a descargar el tráfico lo antes posible desde las redes del primer y último kilómetro que puedan experimentar congestión durante los períodos de máxima utilización.
>

## <a name="enable-employees-to-work-remotely"></a>Permitir que los empleados trabajen de forma remota

Azure VPN Gateway admite conexiones VPN de punto a sitio (P2S) y de sitio a sitio (S2S). Con Azure VPN Gateway, puede escalar las conexiones de los empleados para que accedan con seguridad tanto a los recursos implementados en Azure como a los recursos locales. Para más información, vea [Permitir que los usuarios trabajen de forma remota](../vpn-gateway/work-remotely-support.md). 

Si usa el protocolo de túnel de sockets seguros (SSTP), el número de conexiones simultáneas está limitado a 128. Para obtener un mayor número de conexiones, se recomienda realizar la transición a OpenVPN o IKEv2. Para más información, vea [Transición al protocolo OpenVPN o IKEv2 desde SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Para acceder a los recursos implementados en Azure, los desarrolladores remotos pueden usar la solución Azure Bastion, en lugar de la conexión VPN, para acceder de forma segura a shell (RDP o SSH) sin necesidad de usar direcciones IP públicas en las máquinas virtuales a las que se accede. Para más información, vea [Trabajo remoto con Azure Bastion](../bastion/work-remotely-support.md).

Puede usar Azure Virtual WAN para agregar una conexión VPN a gran escala, admitir conexiones universales entre recursos en distintas ubicaciones globales locales, en diferentes redes virtuales radiales regionales, y para optimizar la utilización de varias redes de banda ancha domésticas. Para más información, vea [¿Tiene dificultades para trabajar desde casa? Así es cómo Azure Virtual WAN puede ayudar](../virtual-wan/work-remotely-support.md).

Otra forma de respaldar a los recursos remotos consiste en implementar una infraestructura de escritorio virtual (VDI) hospedada en la red virtual de Azure, protegida con Azure Firewall. Por ejemplo, Azure Virtual Desktop (AVD) es un servicio de virtualización de escritorio y de aplicaciones que se ejecuta en Azure. Con Azure Virtual Desktop, puede configurar un entorno flexible y escalable en su suscripción de Azure sin necesidad de ejecutar ningún servidor de puerta de enlace adicional. Solo es responsable de las máquinas virtuales AVD de la red virtual. Para más información, vea [Compatibilidad con trabajo remoto de Azure Firewall](../firewall/remote-work-support.md). 

Azure también cuenta con un amplio conjunto de asociados en su ecosistema. Las aplicaciones virtuales de red de nuestros asociados en Azure también pueden ayudar a escalar la conectividad de VPN. Para más información, vea [Consideraciones sobre la aplicación virtual de red (NVA) para el trabajo remoto](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Extensión de la conexión de los empleados para acceder a los recursos distribuidos globalmente

Los siguientes servicios de Azure pueden ayudar a permitir que los empleados accedan a los recursos distribuidos globalmente. Los recursos pueden estar en cualquiera de las regiones de Azure, redes locales o incluso en otras nubes públicas o privadas. 

- **Emparejamiento de redes virtuales de Azure**: si implementa los recursos en más de una región de Azure o si agrega la conectividad de los empleados que trabajan de forma remota con varias redes virtuales, puede establecer la conectividad entre varias redes virtuales de Azure mediante el emparejamiento de redes virtuales. Para más información, consulte [Emparejamiento de redes virtuales][VNet-peer].

- **Solución basada en Azure VPN**: para los empleados remotos conectados a Azure a través de P2S o VPN S2S, puede habilitar el acceso a redes locales mediante la configuración de una VPN S2S entre las redes locales y Azure VPN Gateway. Para más información, vea [Creación de una conexión de sitio a sitio][S2S].

- **ExpressRoute**: Con el emparejamiento privado de ExpressRoute, puede habilitar la conectividad privada entre las implementaciones de Azure y la infraestructura local o la infraestructura de una instalación de ubicación compartida. ExpressRoute, mediante el emparejamiento de Microsoft, también permite acceder a los puntos de conexión públicos de Microsoft desde la red local. Las conexiones ExpressRoute no pasan por la red pública de Internet. Ofrecen conectividad segura, confiabilidad y mayor rendimiento con una latencia menor y más coherente que las conexiones a Internet típicas. Para más información, consulte [Información general sobre ExpressRoute][ExR]. Recurrir al proveedor de red existente que ya forma parte de nuestro [ecosistema de asociados de ExpressRoute][ExR-eco] puede ayudar a reducir el tiempo para obtener conexiones de ancho de banda grande a Microsoft.  Con [ExpressRoute Direct][ExR-D] puede conectar directamente su red local a la red troncal de Microsoft. ExpressRoute Direct ofrece dos opciones de velocidad de línea diferentes de 10 Gbps dual o 100 Gbps. 

- **Azure Virtual WAN**: Azure Virtual WAN permite una interoperabilidad directa entre las conexiones VPN y los circuitos ExpressRoute. Como se mencionó anteriormente, Azure Virtual WAN también admite conexiones universales entre recursos en diferentes ubicaciones globales locales, en diferentes redes virtuales radiales regionales.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Escalado de la conectividad de los clientes a recursos front-end

En los momentos en los que más personas se conectan, muchos sitios web corporativos experimentan un aumento del tráfico de los clientes. Azure Application Gateway puede ayudar a administrar este aumento de la carga de trabajo front-end. Para más información, vea [Soporte técnico para tráfico elevado en Application Gateway](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Soporte técnico de Microsoft para el tráfico entre varias nubes

En el caso de las implementaciones de otras nubes públicas, Microsoft puede proporcionar conectividad global. Azure Virtual WAN, VPN o ExpressRoute pueden servir de ayuda en este sentido. Para ampliar la conectividad de Azure a otras nubes, puede configurar VPN S2S entre las dos nubes. También puede establecer la conectividad desde Azure a otras nubes públicas mediante ExpressRoute. Oracle Cloud forma parte del ecosistema de asociados de ExpressRoute. Puede [configurar una interconexión directa entre Azure y Oracle Cloud Infrastructure][Az-OCI]. La mayoría de los proveedores de servicios que forman parte del ecosistema de asociados de ExpressRoute ofrecen también conectividad privada a otras nubes públicas. Al recurrir a estos proveedores de servicios, puede establecer una conectividad privada entre sus implementaciones en Azure y otras nubes a través de ExpressRoute.

## <a name="next-steps"></a>Pasos siguientes

En los siguientes artículos se describe cómo se pueden usar diferentes características de red de Azure para escalar a los usuarios para que trabajen de forma remota:

| **Artículo** | **Descripción** |
| --- | --- |
| [Permitir que los usuarios trabajen de forma remota](../vpn-gateway/work-remotely-support.md) | Revise las opciones disponibles para configurar el acceso remoto para los usuarios o para complementar las soluciones existentes con capacidad adicional para la organización.|
| [¿Tiene dificultades para trabajar desde casa? Así es cómo Azure Virtual WAN puede ayudar](../virtual-wan/work-remotely-support.md) | Use Azure Virtual WAN para abordar las necesidades de conectividad remota de la organización.|
| [Soporte técnico para tráfico elevado en Application Gateway](../application-gateway/high-traffic-support.md) | Use Application Gateway con el firewall de aplicaciones web (WAF) para una manera escalable y segura de administrar el tráfico a las aplicaciones web. |
| [Consideraciones sobre el trabajo remoto en la aplicación virtual de red (NVA)](../vpn-gateway/nva-work-remotely-support.md)|Revise la guía sobre cómo aprovechar las NVA en Azure para proporcionar soluciones de acceso remoto. |
| [Transición al protocolo OpenVPN o IKEv2 desde SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | Supere el límite de 128 conexiones simultáneas de SSTP mediante la transición al protocolo OpenVPN o IKEv2.|
| [Trabajo remoto con Azure Bastion](../bastion/work-remotely-support.md) | Proporcione conectividad RDP/SSH segura y sin problemas a las máquinas virtuales dentro de la red virtual de Azure, directamente en Azure Portal, sin usar una dirección IP pública. |
| [Uso de Azure ExpressRoute para crear una conectividad híbrida para admitir usuarios remotos](../expressroute/work-remotely-support.md) | Use ExpressRoute para la conectividad híbrida para permitir que los usuarios de la organización trabajen de forma remota.|
| [Compatibilidad con trabajo remoto de Azure Firewall](../firewall/remote-work-support.md)|Proteja los recursos de la red virtual de Azure mediante Azure Firewall. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md

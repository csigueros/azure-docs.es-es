---
title: 'Concepto: integración de una implementación de Azure VMware Solution en una arquitectura en estrella tipo hub-and-spoke'
description: Obtenga información sobre cómo integrar una implementación de Azure VMware Solution en una arquitectura en estrella tipo hub-and-spoke en Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 2ed815904b8bb15b9822fbc9603b65e20ccdce43
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069517"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integración de Azure VMware Solution en una arquitectura en estrella tipo hub-and-spoke

En este artículo se ofrecen recomendaciones para la integración de una implementación de Azure VMware Solution en una [arquitectura en estrella tipo hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) existente o nueva en Azure. 


El escenario radial presupone un entorno de nube híbrida con cargas de trabajo en:

* Azure nativo con servicios IaaS o PaaS
* Azure VMware Solution 
* vSphere local

## <a name="architecture"></a>Architecture

El *concentrador* (hub) es una red virtual de Azure que actúa como punto central de conectividad al entorno local y la nube privada de Azure VMware Solution. Los *radios* son redes virtuales emparejadas con el centro para permitir la comunicación entre redes virtuales.

El tráfico entre el centro de datos local, la nube privada de Azure VMware Solution y el concentrador pasa por las conexiones Azure ExpressRoute. Las redes virtuales de radios suelen contener cargas de trabajo basadas en IaaS, pero pueden tener servicios PaaS como [App Service Environment](../app-service/environment/intro.md), que tiene integración directa con Virtual Network u otros servicios PaaS con [Azure Private Link](../private-link/index.yml) habilitado.

>[!IMPORTANT]
>Puede usar una puerta de enlace de ExpressRoute existente para conectarse a Azure VMware Solution, siempre que no supere el límite de cuatro circuitos ExpressRoute por red virtual.  Sin embargo, para tener acceso a Azure VMware Solution desde el entorno local mediante ExpressRoute, debe tener Global Reach de ExpressRoute, ya que la puerta de enlace de ExpressRoute no proporciona enrutamiento transitivo entre sus circuitos conectados.

En el diagrama se muestra un ejemplo de una implementación en estrella tipo hub-and-spoke en Azure conectada al entorno local y a Azure VMware Solution a través de ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Diagrama en el que se muestra la implementación de la integración de la arquitectura en estrella tipo hub-and-spoke de Azure VMware Solution" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

La arquitectura consta de los siguientes componentes principales:

- **Sitio local:** centros de datos locales de clientes conectados a Azure a través de una conexión ExpressRoute.

- **Nube privada de Azure VMware Solution:** SDDC de Azure VMware Solution formado por uno o varios clústeres de vSphere, cada uno con un máximo de 16 hosts.

- **Puerta de enlace de ExpressRoute:** permite la comunicación entre la nube privada de Azure VMware Solution, los servicios compartidos en la red virtual de concentrador y las cargas de trabajo que se ejecutan en redes virtuales de radio.

- **ExpressRoute Global Reach:** habilita la conectividad entre la nube privada local y Azure VMware Solution. La conectividad entre Azure VMware Solution y el tejido de Azure solo se realiza a través de Global Reach de ExpressRoute. No se puede seleccionar ninguna opción más allá de la ruta de acceso rápida de ExpressRoute.  Asimismo, ExpressRoute Direct no es compatible.


- **Consideraciones sobre VPN S2S:** en el caso de las implementaciones de producción de Azure VMware Solution, no se admite la VPN de sitio a sitio de Azure debido a los requisitos de red para VMware HCX. Pero se puede usar para una implementación de PoC.


- **Red virtual de centro:** actúa como punto central de conectividad a la red local y la nube privada de Azure VMware Solution.

- **Red virtual de radios**

    - **Radio de IaaS:** hospeda las cargas de trabajo basadas en Azure IaaS, incluidos los conjuntos de disponibilidad y de escalado de máquinas virtuales, así como los componentes de red correspondientes.

    - **Radio de PaaS:** hospeda los servicios de Azure PaaS usando direccionamiento privado, gracias a un [punto de conexión privado](../private-link/private-endpoint-overview.md) y a [Private Link](../private-link/private-link-overview.md).

- **Azure Firewall:** actúa como pieza central para segmentar el tráfico entre los radios y Azure VMware Solution.

- **Application Gateway:** expone y protege las aplicaciones web que se ejecutan en Azure IaaS/PaaS o en máquinas virtuales (VM) de Azure VMware Solution. Se integra con otros servicios como API Management.

## <a name="network-and-security-considerations"></a>Consideraciones sobre red y seguridad

Las conexiones ExpressRoute permiten que el tráfico fluya entre el entorno local, Azure VMware Solution y el tejido de red de Azure. Azure VMware Solution emplea [Global Reach de ExpressRoute](../expressroute/expressroute-global-reach.md) para implementar esta conectividad.

Dado que una puerta de enlace de ExpressRoute no proporciona enrutamiento transitivo entre sus circuitos conectados, la conectividad local también debe usar ExpressRoute Global Reach para comunicarse entre el entorno de vSphere local y Azure VMware Solution. 

* **Flujo de tráfico del entorno local a Azure VMware Solution**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Diagrama en el que se muestra el flujo de tráfico del entorno local a Azure VMware Solution" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Flujo de tráfico de Azure VMware Solution a la red virtual de concentrador**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Diagrama en el que se muestra el flujo de tráfico de Azure VMware Solution a la red virtual de centro" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Para más información sobre los conceptos de redes y conectividad de Azure VMware Solution, consulte la [documentación del producto de Azure VMware Solution](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentación de tráfico

[Azure Firewall](../firewall/index.yml) es la pieza central de la topología de red en estrella tipo hub-and-spoke implementada en la red virtual de centro. Use Azure Firewall u otra aplicación virtual de red (NVA) compatible con Azure para establecer reglas de tráfico y segmentar la comunicación entre los distintos radios y las cargas de trabajo de Azure VMware Solution.

Cree tablas de rutas para dirigir el tráfico a Azure Firewall.  En el caso de las redes virtuales de radio, cree una ruta que establezca la ruta predeterminada a la interfaz interna de Azure Firewall. De este modo, cuando una carga de trabajo de la red virtual deba alcanzar el espacio de direcciones de Azure VMware Solution, el firewall puede evaluarla y aplicar la regla de tráfico correspondiente para permitirla o denegarla.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Captura de pantalla en la que se muestra cómo crear tablas de rutas para dirigir el tráfico a Azure Firewall" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> No se admite una ruta con el prefijo de dirección 0.0.0.0/0 en el valor **GatewaySubnet**.

Establezca rutas para redes específicas en la tabla de rutas correspondiente. Por ejemplo, rutas para llegar a los prefijos IP de cargas de trabajo y administración de Azure VMware Solution desde las cargas de trabajo de radios y viceversa.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Captura de pantalla en la que se muestra cómo establecer rutas para redes específicas en la tabla de rutas correspondiente" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Un segundo nivel de segmentación del tráfico que usa los grupos de seguridad de red dentro de la red radial para crear una directiva de tráfico más pormenorizada.

> [!NOTE]
> **Tráfico desde el entorno local a Azure VMware Solution:** el tráfico entre cargas de trabajo locales, ya sea basado en vSphere o en otros, se habilita mediante Global Reach, pero el tráfico no pasa por Azure Firewall en el concentrador. En este escenario, debe implementar mecanismos de segmentación de tráfico en el entorno local o en Azure VMware Solution.

### <a name="application-gateway"></a>Application Gateway

Se ha probado Azure Application Gateway V1 y V2 con aplicaciones web que se ejecutan en máquinas virtuales de Azure VMware Solution como grupo de back-end. Application Gateway es actualmente el único método admitido para exponer las aplicaciones web que se ejecutan en máquinas virtuales de Azure Application Gateway a Internet. También puede exponer las aplicaciones a los usuarios internos de forma segura.

Para más información, consulte el artículo específico de Azure VMware Solution sobre [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md).

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagrama en el que se muestra el segundo nivel de segmentación del tráfico usando los grupos de seguridad de red" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Jumpbox y Azure Bastion

Acceda al entorno de Azure VMware Solution con un jumpbox, una máquina virtual de Windows 10 o Windows Server implementada en la subred de servicio compartida dentro de la red virtual del centro.

>[!IMPORTANT]
>Azure Bastion es el servicio recomendado para conectarse al jumpbox a fin de evitar exponer Azure VMware Solution a Internet. No se puede usar Azure Bastion para conectarse a máquinas virtuales de Azure VMware Solution porque no son objetos de IaaS de Azure.  

Como práctica recomendada de seguridad, implemente el servicio [Microsoft Azure Bastion](../bastion/index.yml) dentro de la red virtual de centro. Azure Bastion proporciona acceso RDP y SSH ininterrumpido a máquinas virtuales implementadas en Azure sin necesidad de proporcionar direcciones IP públicas a dichos recursos. Una vez que aprovisione el servicio de Azure Bastion, puede acceder a la máquina virtual seleccionada desde Azure Portal. Después de establecer la conexión, se abre una pestaña nueva que muestra el escritorio de Jumpbox y, desde ese escritorio, puede acceder al plano de administración de la nube privada de Azure VMware Solution.

> [!IMPORTANT]
> No asigne una IP pública a la máquina virtual de Jumpbox ni exponga el puerto 3389/TCP a la red pública de Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Diagrama en el que se muestra la red virtual de centro de Azure Bastion" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Consideraciones sobre la resolución Azure DNS

Para la resolución de Azure DNS hay dos opciones disponibles:

-   Use los controladores de dominio implementados en el centro (descritos en [Consideraciones de identidad](#identity-considerations)) como servidores de nombres.

-   Implemente y configure una zona privada de Azure DNS.

El mejor enfoque consiste en combinar ambos para proporcionar una resolución de nombres confiable para Azure VMware Solution, el entorno local y Azure.

Como recomendación de diseño general, use el DNS existente que está integrado en Active Directory, implementado en al menos dos máquinas virtuales de Azure en la red virtual de centro y configurado en las redes virtuales de radio para usar dichos servidores de Azure DNS en la configuración de DNS.

Puede usar el DNS privado de Azure, en el que la zona de DNS privado de Azure se vincula a la red virtual.  Los servidores DNS se usan como resolución híbrida con reenvío condicional al entorno local o a la instancia de Azure VMware Solution que ejecuta DNS usando la infraestructura de DNS privado de Azure. 

Para administrar automáticamente el ciclo de vida de los registros DNS de las MV implementadas en las redes virtuales de radio, habilite el registro automático. Cuando esté habilitado, el número máximo de zonas DNS privadas será solo uno. Si está deshabilitado, el número máximo será 1000.

Los servidores locales y de Azure VMware Solution se pueden configurar con reenviadores condicionales a las máquinas virtuales de resolución de Azure para la zona de DNS privado de Azure.

## <a name="identity-considerations"></a>Consideraciones de identidad

En lo que respecta a la identidad, el mejor enfoque consiste en implementar al menos un controlador de dominio de AD en el concentrador. Use dos subredes de servicio compartido en el modo distribuido por zona o en un conjunto de disponibilidad de máquina virtual. Para obtener más información sobre cómo extender el dominio de Active Directory local (AD) a Azure, consulte [Centro de arquitectura de Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

Además, implemente otro controlador de dominio en el lado de Azure VMware Solution para que actúe como identidad y origen DNS dentro del entorno de vSphere.

Como práctica recomendada, integre el [dominio de AD con Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->

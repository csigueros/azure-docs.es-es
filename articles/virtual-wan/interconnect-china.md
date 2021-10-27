---
title: 'Arquitectura: interconexión con China mediante Azure Virtual WAN y un centro de conectividad seguro'
description: Obtenga información sobre cómo interconectarse con China mediante Azure Virtual WAN y un centro protegido.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: 0353cf0c8c98c148ac70121849d510aadc31d131
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000809"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconexión con China mediante Azure Virtual WAN y un centro de conectividad seguro

Si se examinan sectores comunes como la automoción, manufacturero, de logística u otras instituciones como embajadas, a menudo surge la pregunta de cómo mejorar la interconexión con China. Estas mejoras son relevantes principalmente para el uso de servicios en la nube como Microsoft 365, Azure Global Services o la interconexión de ramas dentro de China con una red troncal de cliente.

En la mayoría de los casos, los clientes se enfrentan a latencias altas, un ancho de banda bajo, una conexión inestable y altos costos para conectarse fuera de China (por ejemplo, Europa o Estados Unidos).

Un motivo de ello es el "Gran Cortafuegos de China", que protege la parte china de Internet y filtra el tráfico a China. Prácticamente todo el tráfico que circula de la República Popular China a fuera de China, excepto las zonas administrativas especiales, como Hong Kong y Macao, pasa por el Gran Cortafuegos. El tráfico que circula a través de Hong Kong y Macao no pasa en su totalidad por el Gran Cortafuegos, sino que lo controla un subconjunto del Gran Cortafuegos.

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="El diagrama muestra la interconexión del proveedor.":::

Con Virtual WAN, un cliente puede establecer una conexión más eficaz y estable con los servicios en la nube de Microsoft, así como una conexión a su red empresarial sin violar la legislación china en materia de ciberseguridad.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisitos y flujo de trabajo

Con el objetivo de cumplir con la legislación china en materia de ciberseguridad, debe cumplir una serie de condiciones.

En primer lugar, debe trabajar con una red y un ISP que cuente con una licencia de ICP (proveedor de contenido de Internet) para China. En la mayoría de los casos, terminará con uno de los siguientes proveedores:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

En función del proveedor y de sus necesidades, deberá adquirir uno de los siguientes servicios de conectividad de red para interconectar sus ramas en China.

* Una red MPLS/IPVPN 
* Una red WAN definida por software (SDWAN)
* Acceso a Internet dedicado

Después, deberá acordar con ese proveedor para proporcionar una salida a la red global de Microsoft y su red perimetral en Hong Kong, no en Pekín ni Shanghái. En este caso, Hong Kong es muy importante debido a su conexión y ubicación física con China.

Aunque la mayoría de los clientes creen que lo mejor es recurrir a Singapur para la interconexión porque parece que está más cerca de China al verlo en el mapa, en realidad no es cierto. Si sigue los mapas de la fibra de red, casi todas las conexiones de red pasan por Pekín, Shanghái y Hong Kong. Esto hace que Hong Kong sea una mejor opción para la interconexión con China.

En función del proveedor puede recibir distintas ofertas de servicio. En la siguiente tabla se muestra un ejemplo de proveedores y el servicio que ofrecen, en función de la información disponible en el momento en que se escribió este artículo.

| Servicio | Ejemplos de proveedor |
| --- | --- |
| Red MPLS/IPVPN |PCCW o China Telecom Global |
|SDWAN| PCCW o China Telecom Global|
| Acceso a Internet dedicado | PCCW, Hong Kong Telecom, China Mobile|

Puede acordar con su proveedor cuál de las dos siguientes soluciones debe usar para acceder a la red troncal global de Microsoft:

* Obtener una ruta de Microsoft Azure ExpressRoute que acabe en Hong Kong. Este sería el caso del uso de MPLS/IPVPN. Actualmente, el único proveedor de licencias de ICP con ExpressRoute en Hong Kong es China Telecom Global. Sin embargo, también pueden contactar con los demás proveedores por si emplean proveedores de intercambio en la nube, como Megaport o InterCloud. Para obtener más información, consulte [Proveedores de conectividad de ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Usar un acceso a Internet dedicado directamente en uno de los siguientes puntos de intercambio de Internet, o bien usar una interconexión de red privada.

En la siguiente lista se muestran los intercambios de Internet posibles en Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Al usar esta conexión, el próximo salto BGP de los servicios de Microsoft debe ser el número de sistema autónomo de Microsoft (ASN) 8075. Si usa una única ubicación o solución SDWAN, esa sería la opción de conexión.

Con los cambios actuales relacionados con las interconexiones entre China y la región administrativa especial (RAE) de Hong Kong, la mayoría de estos proveedores de red crean un puente de MPLS entre China y la RAE de Hong Kong.

Se puede ver que las conexiones VPN de sitio a sitio dentro de China están permitidas y la mayoría de ellas son estables. Lo mismo se aplica a las conexiones de sitio a sitio entre las ramas del resto del mundo. Ahora los proveedores crean una agregación de VPN/SDWAN en ambos lados y un puente mediante MPLS entre ellos.

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="En el diagrama se muestra el puente de MPLS de China.":::

En cualquier caso, seguimos recomendando que disponga de una segunda salida regular de Internet hacia China. Esto consiste en dividir el tráfico entre el tráfico empresarial a los servicios en la nube, como Microsoft 365 o Azure, y el tráfico de Internet regulado por ley.

Una arquitectura de red conforme dentro de China podría parecerse al siguiente ejemplo:

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="En el diagrama se muestran varias ramas.":::

En este ejemplo, si tiene una interconexión con la red global de Microsoft en Hong Kong, ahora puede empezar a aprovechar la [arquitectura de tránsito global de Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md) y servicios adicionales, como el centro de conectividad seguro de Azure Virtual WAN, para consumir servicios e interconectarlos a sus ramas y centros de datos que se encuentren fuera de China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicación entre centros de conectividad

En esta sección se usa la comunicación entre centros de conectividad de Virtual WAN para establecer la interconexión. En este caso se crea un recurso de centro de conectividad de Virtual WAN para conectarse a un centro de conectividad de Virtual WAN en Hong Kong, en otras regiones que prefiera, en una región en la que ya tenga recursos de Azure o donde quiera conectarse.

Una arquitectura de ejemplo podría parecerse al siguiente ejemplo:

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="En el diagrama se muestra una WAN de ejemplo.":::

En este ejemplo, las ramas de China se conectan a Azure Cloud China y entre sí mediante conexiones VPN o MPLS. Las ramas que deben conectarse a servicios globales usan MPLS o servicios basados en Internet conectados directamente con Hong Kong. Si quier usar ExpressRoute en Hong Kong y en la otra región, deberá configurar [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para interconectar ambos circuitos de ExpressRoute.

ExpressRoute Global Reach no está disponible en algunas regiones. Si necesita interconectarse con Brasil o India, por ejemplo, deberá usar [proveedores de intercambio en la nube](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) para ofrecer los servicios de enrutamiento.

En la siguiente ilustración se muestran los dos ejemplos de este caso.

:::image type="content" source="./media/interconnect-china/global.png" alt-text="En el diagrama se muestra Global Reach.":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Salida de Internet segura para Microsoft 365

Otro aspecto que se debe tener en cuenta es la seguridad de red, así como el registro del punto de entrada entre China y el componente de red troncal establecida por Virtual WAN y la red troncal del cliente. En la mayoría de los casos, debe haber una salida a Internet en Hong Kong para acceder directamente a la red perimetral de Microsoft y, con eso, a los servidores de Azure Front Door usados para los servicios de Microsoft 365.

En los dos casos con Virtual WAN se emplea el [centro de conectividad seguro de Azure Virtual WAN](../firewall-manager/secured-virtual-hub.md). Con Azure Firewall Manager puede cambiar un centro de conectividad normal de Virtual WAN a uno seguro y, después, implementar y administrar un firewall de Azure dentro de ese centro de conectividad.

En la siguiente ilustración se muestra un ejemplo de este caso:

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="en el diagrama se muestra la salida de Internet para el tráfico de servicios web y de Microsoft.":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Arquitectura y flujos de tráfico

En función de su elección con respecto a la conexión con Hong Kong, la arquitectura global puede cambiar ligeramente. En esta sección se muestran tres arquitecturas disponibles en combinaciones distintas con VPN o SDWAN y ExpressRoute.

Todas estas opciones usan el centro de conectividad seguro de Azure Virtual WAN para la conectividad directa de Microsoft 365 en Hong Kong. Estas arquitecturas también admiten los requisitos de cumplimiento de [Microsoft 365 Multi-Geo](/microsoft-365/enterprise/microsoft-365-multi-geo) y mantienen ese tráfico cerca de la siguiente ubicación de Azure Front Door. Como resultado, también se mejora el uso de Microsoft 365 fuera de China.

Al usar Azure Virtual WAN junto con conexiones a Internet, todas las conexiones pueden beneficiarse de servicios adicionales como [Microsoft Azure Peering Service (MAPS)](../peering-service/about.md). MAPS se ha creado para optimizar el tráfico que llega a la red global de Microsoft procedente de proveedores de servicios de Internet de terceros.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opción 1: SDWAN o VPN

En esta sección se describe un diseño que usa SDWAN o VPN para Hong Kong y otras ramas. Esta opción muestra el uso y el flujo de tráfico al usar una conexión a Internet pura en ambos sitios de la red troncal de Virtual WAN. En este caso, la conexión se dirige a Hong Kong mediante un acceso a Internet dedicado o una solución SDWAN del proveedor de ICP. Otras ramas también usan soluciones SDWAN o de Internet pura.

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="En el diagrama se muestra el tráfico de China a Hong Kong.":::

En esta arquitectura, cada sitio está conectado a la red global de Microsoft mediante VPN y Azure Virtual WAN. El tráfico existente entre los sitios y Hong Kong se transmite por la red de Microsoft y solo usa la conexión de Internet normal en la última milla.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opción 2: ExpressRoute y SDWAN o VPN

En esta sección se describe un diseño que usa ExpressRoute en Hong Kong y otras ramas con ramas VPN/SDWAN. Esta opción muestra el uso de una ruta de ExpressRoute que acaba en Hong Kong y otras ramas conectadas a través de SDWAN o VPN. En Hong Kong, ExpressRoute se encuentra limitado a una breve lista de proveedores, que encontrará en la lista de [asociados de ExpressRoute](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="En el diagrama se muestra el tráfico de China a Hong Kong (ExpressRoute).":::

También hay opciones para finalizar ExpressRoute de China, por ejemplo, a Corea del Sur o Japón. Sin embargo, dado el cumplimiento, la normativa y la latencia, Hong Kong es a día de hoy la mejor opción.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opción 3: Solo ExpressRoute

En esta sección se describe un diseño en el que se usa ExpressRoute para Hong Kong y otras ramas. Esta opción muestra la interconexión mediante ExpressRoute en ambos extremos. Aquí tiene un flujo de tráfico diferente al otro. El tráfico de Microsoft 365 se dirigirá al centro de conectividad seguro de Azure Virtual WAN y, de ahí, a la red perimetral de Microsoft y a Internet.

El tráfico que se dirige a las ramas interconectadas o desde ellas hasta las ubicaciones en China seguirá un enfoque diferente dentro de esa arquitectura. Actualmente, Virtual WAN no admite el tránsito de ExpressRoute a ExpressRoute. El tráfico usará ExpressRoute Global Reach o la interconexión de terceros sin pasar por el centro de conectividad de Virtual WAN. Pasará directamente de una red perimetral de Microsoft Enterprise (MSEE) a otra.

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="En el diagrama se muestra Global Reach de ExpressRoute.":::

Actualmente, ExpressRoute Global Reach no está disponible en todos los países o regiones, pero puede configurar una solución con Azure Virtual WAN.

Por ejemplo, puede configurar ExpressRoute con Microsoft Peering y conectar un túnel VPN a través de ese emparejamiento a Azure Virtual WAN. Ahora ha habilitado, otra vez, el tránsito entre la VPN y ExpressRoute sin Global Reach y un proveedor y servicio de terceros, como Megaport Cloud.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes artículos:

* [Arquitectura de red de tránsito global con Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Creación de un centro de conectividad de Virtual WAN](virtual-wan-site-to-site-portal.md)

* [Configuración de un centro de conectividad seguro de Virtual WAN](../firewall-manager/secure-cloud-network.md)

* [Información general de la versión preliminar de Azure Peering Service](../peering-service/about.md)
---
title: Control del tráfico de entrada en Azure Australia
description: Guía para controlar el tráfico de entrada en Azure Australia para cumplir los requisitos de la administración pública de Australia relativos a la seguridad de las puertas de Internet
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 3e2fcfb91a0cb514a26608a29cb8cb09722b7f32
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2020
ms.locfileid: "117029022"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Control del tráfico de entrada en Azure Australia

Un elemento fundamental de la protección de los sistemas ICT es el control del tráfico. Este se debe restringir solo al necesario para que un sistema funcione y así reducir el riesgo.

En esta guía se proporcionan detalles sobre cómo funciona el tráfico entrante (de entrada) en Azure y recomendaciones para implementar controles de seguridad de red para un sistema conectado a Internet.

Los controles de red están en línea con la guía para el consumidor del Australian Cyber Security Centre (ACSC) y la intención del manual de seguridad de la información (ISM) del ACSC.

## <a name="requirements"></a>Requisitos

Los requisitos de seguridad generales para los sistemas de la Commonwealth se definen en el manual ISM. Para ayudar a las entidades de la Commonwealth a implementar la seguridad de red, el ACSC ha publicado [ACSC Protect: implementación de la segmentación y la segregación de la red](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm) y, para ayudar en la protección de los sistemas en entornos en la nube, ha publicado [Seguridad de la informática en la nube para inquilinos](https://www.cyber.gov.au/publications/cloud-computing-security-for-tenants).

En estas guías se describe el contexto para implementar la seguridad de red y controlar el tráfico, y se incluyen recomendaciones prácticas para el diseño y la configuración de red.

La guía de [seguridad de la informática en la nube para los inquilinos de Microsoft Azure](https://aka.ms/au-irap) de Microsoft de la página australiana de del portal de confianza de servicios destaca tecnologías específicas de Microsoft que permiten llevar a cabo los consejos de las publicaciones de ACSC.

Los siguientes requisitos clave, identificados en las publicaciones de ACSC, son importantes para controlar el tráfico de entrada en Azure:

|Descripción|Source|
|---|---|
|**Implementación de la segmentación y la segregación de la red, por ejemplo, en una arquitectura de n niveles, con firewalls basados en host y controles de acceso a la red del CSP para limitar la conectividad de red entrante y saliente en las máquinas virtuales únicamente a los puertos y protocolos necesarios.**| _Cloud Computing for Tenants_ (Informática en la nube para inquilinos)|
|**Implemente una conectividad de red confiable, de baja latencia y alto ancho de banda** entre el inquilino (incluidos los usuarios remotos del inquilino) y el servicio en la nube para cumplir los requisitos de disponibilidad del inquilino.  | _Cloud Computing for Tenants_ (Informática en la nube para inquilinos)|
|**Aplique tecnologías en más de solo una capa de red**. Cada host y cada red se deben segmentar y segregar, siempre que sea posible, en el nivel más bajo que se pueda administrar de forma práctica. En la mayoría de los casos, esta segmentación y segregación abarcan desde la capa de vínculo de datos hasta la capa de aplicación (incluida); sin embargo, en entornos confidenciales, el aislamiento físico puede ser adecuado. Se deben implementar medidas basadas en el host y para toda la red de forma complementaria y se deben supervisar de manera centralizada. No basta con implementar un firewall o un dispositivo de seguridad como la única medida de seguridad. |_ACSC Protect: implementación de la segmentación y la segregación de la red_|
|**Use los principios de privilegios mínimos y necesidad de conocer**. Si un host, un servicio o una red no necesitan comunicarse con otro host, servicio o red, no se le debe permitir hacerlo. Si un host, un servicio o una red solo necesitan comunicarse con otro host, servicio o red en un puerto o protocolo específicos, los demás puertos y protocolos se deben deshabilitar. La adopción de estos principios en una red complementará a la minimización de los privilegios de usuario y elevará considerablemente la posición de seguridad general del entorno. |_ACSC Protect: implementación de la segmentación y la segregación de la red_|
|**Separe los hosts y las redes en función de su confidencialidad o importancia para las operaciones empresariales**. La separación se puede lograr mediante el uso de hardware o plataformas diferentes en función de distintas clasificaciones de seguridad, dominios de seguridad o requisitos de disponibilidad e integridad de determinados hosts o redes. En especial, separe las redes de administración y considere la posibilidad de aislar físicamente las que estén fuera de banda en entornos confidenciales. |_ACSC Protect: implementación de la segmentación y la segregación de la red_|
|**Identifique, autentique y autorice el acceso de todas las entidades a todas las demás entidades**. Todos los usuarios, hosts y servicios deben tener acceso restringido a únicamente los demás usuarios, hosts y servicios que necesiten para realizar sus tareas o funciones designadas. Todos los servicios heredados o locales que eluden o degradan la seguridad de los servicios de identificación, autenticación y autorización se deben deshabilitar y su uso se debe supervisar de forma rigurosa. |_ACSC Protect: implementación de la segmentación y la segregación de la red_|
|**Implemente la lista de permitidos del tráfico en lugar de la lista de denegados**. Permita únicamente el acceso del tráfico de red bien conocido (es decir, el identificado, autenticado y autorizado), en lugar de denegar el acceso al tráfico no conocido (por ejemplo, bloquear una dirección o un servicio específicos). El uso de una lista de remitentes aceptados da como resultado una directiva de seguridad superior a la de una lista de bloqueados y mejora considerablemente la capacidad de una organización para detectar y evaluar posibles intrusiones en la red. |_ACSC Protect: implementación de la segmentación y la segregación de la red_|
|

En este artículo se proporcionan información y recomendaciones sobre cómo se pueden cumplir estos requisitos en los sistemas implementados en Azure mediante la infraestructura como servicio (IaaS) y la plataforma como servicio (PaaS). También debe leer el artículo sobre [el control del tráfico de salida en Azure Australia](gateway-egress-traffic.md) para comprender totalmente cómo se controla el tráfico en Azure.

## <a name="architecture"></a>Architecture

Si está implicado en el diseño o la implementación de los controles de tráfico de entrada y de seguridad de red, primero debe comprender cómo funciona el tráfico en Azure con IaaS y PaaS. En esta sección se proporciona información general sobre los puntos de entrada donde el tráfico puede alcanzar un recurso hospedado en Azure y los controles de seguridad disponibles para restringir y controlar ese tráfico. Cada uno de estos componentes se describe en detalle en las secciones restantes de esta guía.

### <a name="architecture-components"></a>Componentes de la arquitectura

El diagrama de la arquitectura que se muestra aquí describe las posibles rutas que el tráfico puede tomar para conectarse a un servicio hospedado en Azure. Estos componentes se dividen en Azure, entrada de IaaS, entrada de PaaS y control de seguridad, dependiendo de la función que proporcionen para el tráfico de entrada.

![Architecture](media/ingress-traffic.png)

### <a name="azure-components"></a>Componentes de Azure

|Componente | Descripción|
|---|---|
|**Protección contra DDOS** | Los ataques de denegación de servicio distribuido (DDoS) intentan agotar los recursos de una aplicación haciendo que esta no esté disponible para los usuarios legítimos. Los ataques DDoS pueden ir dirigidos a cualquier punto de conexión que sea públicamente accesible a través de Internet. Azure incluye DDoS Protection automáticamente mediante la plataforma Azure y proporciona funcionalidades de mitigación adicionales que se pueden habilitar para aplicaciones específicas para un control más pormenorizado.|
| **Traffic Manager** | Azure Traffic Manager es un equilibrador de carga de tráfico basado en el servicio de nombres de dominio (DNS) que puede distribuir el tráfico de forma óptima a servicios de distintas regiones de Azure al tiempo que proporciona una alta disponibilidad y capacidad de respuesta. Traffic Manager usa el servicio de nombres de dominio para dirigir las solicitudes del cliente al punto de conexión de más adecuado en función de un método de enrutamiento del tráfico y el estado de los puntos de conexión.|
| **ExpressRoute** | ExpressRoute es una conexión de red dedicada para consumir los servicios en la nube de Microsoft. Se aprovisiona mediante un proveedor de conectividad y ofrece más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones normales a través de Internet. Un circuito ExpressRoute representa la conexión lógica entre la infraestructura local y los servicios en la nube de Microsoft mediante un proveedor de conectividad.|
| **Emparejamiento privado de ExpressRoute** | El emparejamiento privado de ExpressRoute es una conexión entre el entorno local y las redes virtuales privadas de Azure. El emparejamiento privado permite el acceso a servicios de Azure, como Virtual Machines, que se implementan en una red virtual. Los recursos y las redes virtuales a los que se accede mediante el emparejamiento privado se consideran una extensión de la red principal de una organización. El emparejamiento privado proporciona conectividad bidireccional entre la red local y las redes virtuales de Azure con direcciones IP privadas.|
| **Emparejamiento de Microsoft para ExpressRoute** | El emparejamiento de Microsoft para ExpressRoute es una conexión entre el entorno local y los servicios públicos de Microsoft y Azure. Esto incluye la conectividad con Microsoft 365, Dynamics 365 y los servicios de plataforma como servicio de Azure. El emparejamiento se establece mediante direcciones IP públicas propiedad de la organización o del proveedor de conectividad. No se puede acceder a ningún servicio mediante el emparejamiento de Microsoft para ExpressRoute de manera predeterminada; la organización debe elegir los servicios necesarios. Este proceso proporciona entonces conectividad a los mismos puntos de conexión que están disponibles en Internet.|
|

### <a name="iaas-ingress-components"></a>Componentes de entrada de IaaS

|Componente | Descripción|
|---|---|
|**Interfaz de red** | Una interfaz de red es un recurso que existe en Azure. Está vinculada a una máquina virtual y se le asigna una dirección IP privada que no se pueda enrutar a Internet desde la subred a la que está asociada. Esta dirección IP se asigna de forma dinámica o estática mediante Azure Resource Manager.|
|**Subred** | Una subred es un intervalo de direcciones IP que se crea dentro de una red virtual. Se pueden crear varias subredes dentro de una red virtual para la segmentación de la red.|
| **Red virtual** | Una red virtual es un recurso fundamental dentro de Azure que proporciona una plataforma y un límite para implementar recursos y permitir la comunicación. La red virtual existe en una región de Azure y define el espacio de direcciones IP y los límites de enrutamiento de los recursos integrados de red virtual, como Virtual Machines.|
| **Emparejamiento de redes virtuales** | El emparejamiento de redes virtuales es una opción de configuración de Azure que permite la comunicación directa entre dos redes virtuales sin necesidad de una puerta de enlace de red virtual. Una vez emparejadas, las dos redes virtuales pueden comunicarse directamente y la configuración adicional controla el uso de las puertas de enlace de red virtual y otras opciones de tránsito.|
| **Dirección IP pública** | Una dirección IP pública es un recurso que reserva una de las direcciones IP públicas enrutables a Internet y propiedad de Microsoft desde la región especificada para su uso en la red virtual. Se puede asociar con una interfaz de red específica, lo que permite que se pueda acceder al recurso desde Internet, ExpressRoute y sistemas PaaS.|
| **Puerta de enlace de ExpressRoute** | Una puerta de enlace de ExpressRoute es un objeto de una red virtual que proporciona conectividad y enrutamiento desde esta a redes locales mediante el emparejamiento privado en un circuito ExpressRoute.|
| **VPN Gateway** | VPN Gateway es un objeto de una red virtual que proporciona un túnel cifrado desde una red virtual a una externa. El túnel cifrado puede ser de sitio a sitio para la comunicación bidireccional con un entorno local, otra red virtual o un entorno en la nube o de punto a sitio para la comunicación con un único punto de conexión.|
| **Integración de PaaS con la red virtual** | Muchas funcionalidades de PaaS se pueden implementar en una red virtual o integrarse con ellas. Algunas funcionalidades de PaaS pueden integrarse completamente con una red virtual y ser accesibles solo mediante direcciones IP privadas. Otras, como Azure Load Balancer y Azure Application Gateway, pueden tener una interfaz externa con una dirección IP pública y una interfaz interna con una dirección IP privada dentro de la red virtual. En esta instancia, el tráfico puede entrar en la red virtual mediante la funcionalidad de PaaS.|
|

### <a name="paas-ingress-components"></a>Componentes de entrada de PaaS

|Componente | Descripción|
|---|---|
|**Nombre de host** | Las funcionalidades de PaaS de Azure se identifican mediante un nombre de host público único que se asigna al crear el recurso. Este nombre de host se registra en un dominio DNS público, donde se puede resolver en una dirección IP pública.|
|**Dirección IP pública** | A menos que se implemente en una configuración integrada de red virtual, el acceso a las funcionalidades de PaaS de Azure se realiza mediante una dirección IP pública que se puede enrutar a Internet. Esta dirección se puede dedicar a recursos específicos, como un equilibrador de carga público, o asociar a una funcionalidad específica que tenga un punto de entrada compartido para varias instancias, como Storage o SQL. A esta dirección IP pública se puede acceder desde Internet, ExpressRoute o las direcciones IP públicas de IaaS mediante la red troncal de Azure.|
|**Puntos de conexión de servicio** | Los puntos de conexión de servicio proporcionan una conexión privada directa entre una red virtual y una funcionalidad específica de PaaS. Los puntos de conexión de servicio, que solo están disponibles para un subconjunto de funcionalidades de PaaS, proporcionan un mayor rendimiento y seguridad para los recursos de una red virtual que tenga acceso a PaaS.|
|

### <a name="security-controls"></a>Controles de seguridad

|Componente | Descripción|
|---|---|
|**Grupos de seguridad de red** | Los NSG controlan el tráfico dentro y fuera de los recursos de red virtual de Azure. Los NSG aplican reglas para los flujos de tráfico que se permiten o deniegan, lo que incluye el tráfico dentro de Azure y entre Azure y redes externas, como locales o Internet. Los NSG se aplican a subredes de una red virtual o a interfaces de red individuales.|
|**Firewall de PaaS** | Muchas funcionalidades de PaaS, como Storage y SQL, tienen un firewall integrado para controlar el tráfico de entrada al recurso específico. Se pueden crear reglas para permitir o denegar la conexión desde direcciones IP o redes virtuales específicas.|
|**Autenticación de PaaS y control de acceso** | Como parte de un enfoque por niveles de seguridad, las funcionalidades de PaaS proporcionan varios mecanismos para autenticar los usuarios y controlar los privilegios y el acceso.|
|**Azure Policy** | Azure Policy es un servicio de Azure para crear, asignar y administrar directivas. Estas directivas usan reglas para controlar los tipos de recursos que se pueden implementar y la configuración de esos recursos. Se pueden usar directivas para exigir el cumplimiento e impedir la implementación de recursos si no satisfacen los requisitos; también se pueden utilizar para supervisar e informar sobre el estado de cumplimiento.|
|

## <a name="general-guidance"></a>Instrucciones generales

Para diseñar y crear soluciones seguras en Azure, es fundamental entender y controlar el tráfico de red para que solo pueda tener lugar una comunicación identificada y autorizada. La intención de estas instrucciones y las relativas a componentes específicos de las secciones posteriores es describir las herramientas y los servicios que se pueden usar para aplicar los principios descritos en _ACSC Protect: implementación de la segmentación y la segregación de la red_ en las cargas de trabajo de Azure. Esto incluye información detallada sobre cómo crear una arquitectura virtual para proteger los recursos cuando no es posible aplicar los mismos controles físicos y de red tradicionales que en un entorno local.

### <a name="specific-focus-areas"></a>Áreas de enfoque específicas

* Limite el número de puntos de entrada a las redes virtuales
* Limite el número de direcciones IP públicas
* Considere la posibilidad de usar un diseño de red de tipo hub-and-spoke para las redes virtuales, como se describe en la documentación del centro de datos virtual de Microsoft (VDC).
* Utilice productos con funcionalidades de seguridad integradas para las conexiones entrantes desde Internet (como Application Gateway, una puerta de enlace de API o aplicaciones virtuales de red).
* Restrinja los flujos de comunicación a las funcionalidades de PaaS solo a los necesarios para que funcione el sistema
* Implemente PaaS en una configuración integrada de red virtual para mayor segregación y control
* Configure los sistemas para que usen mecanismos de cifrado en línea con la guía para el consumidor de ACSC e ISM
* Use protecciones basadas en la identidad, como la autenticación y el control de acceso basado en rol de Azure, además de los controles de red tradicionales.
* Implemente ExpressRoute para la conectividad con las redes locales
* Implemente VPN para el tráfico administrativo y la integración con redes externas
* Utilice Azure Policy para restringir las regiones y los recursos solo a los necesarios para la funcionalidad del sistema.
* Utilice Azure Policy para aplicar la configuración de seguridad de línea base a los recursos accesibles desde Internet.

### <a name="additional-resources"></a>Recursos adicionales

|Recurso | Vínculo|
|---|---|
|Documentos de cumplimiento de políticas y reglamentos australianos, que incluyen una guía del consumidor|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Centro de datos virtual de Azure|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)|
|Segmentación de red de ACSC|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|Seguridad en la nube de ACSC para inquilinos| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|Manual de seguridad de la información de ACSC|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Guía de componentes

En esta sección se proporciona una guía adicional sobre los componentes individuales que son pertinentes para el tráfico de entrada de los sistemas implementados en Azure. En cada sección se describe la intención del componente específico con vínculos a documentación y guías de configuración que se pueden usar para ayudar con las actividades de diseño y compilación.

## <a name="azure"></a>Azure

Toda la comunicación con los recursos de Azure pasa por la infraestructura de red mantenida por Microsoft, que proporciona funciones de conectividad y seguridad. Microsoft implanta automáticamente una serie de protecciones para proteger la plataforma y la infraestructura de red de Azure. Existen además funcionalidades adicionales como servicios dentro de Azure para controlar el tráfico de red y establecer la segmentación y la segregación de la red.

### <a name="ddos-protection"></a>DDoS Protection

Los recursos accesibles por Internet son susceptibles de ataques de denegación de servicio distribuido. Para protegerse frente a estos ataques, Azure proporciona protecciones DDoS de nivel básico y estándar.

El nivel básico se habilita automáticamente como parte de la plataforma Azure, incluida la supervisión del tráfico Always-On y la mitigación en tiempo real de los ataques de nivel de red más comunes, y proporciona las mismas defensas utilizadas por los servicios en línea de Microsoft.  Puede usarse la escala completa de una red global de Azure para distribuir y reducir el tráfico de ataques en las distintas regiones. Se proporciona protección para direcciones IP públicas de Azure IPv4 e IPv6.

El nivel estándar ofrece funciones adicionales de reducción de ataques en comparación con el nivel de servicio básico, adaptadas específicamente a los recursos de Azure Virtual Network. Las directivas de protección se ajustan a través de la supervisión del tráfico dedicado y los algoritmos de Machine Learning. Se proporciona protección para direcciones IP públicas de Azure IPv4.

|Resource|Vínculo|
|---|---|
|Introducción a Azure DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](../ddos-protection/ddos-protection-overview.md)|
|Procedimientos recomendados de DDoS de Azure|[https://docs.microsoft.com/azure/ddos-protection/fundamental-best-practices](../ddos-protection/fundamental-best-practices.md)|
|Administración de DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager se utiliza para administrar el tráfico de entrada mediante el control de los puntos de conexión de una aplicación que reciben conexiones. Para protegerse frente a la pérdida de disponibilidad en sistemas o aplicaciones debida a ataques de ciberseguridad o para recuperar servicios después de una situación de riesgo en el sistema, Traffic Manager redirige el tráfico a las instancias de la aplicación que funcionen y estén disponibles.

|Resource|Vínculo|
|---|---|
|Información general sobre el Administrador de tráfico | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](../traffic-manager/traffic-manager-overview.md)|
|Guía de recuperación ante desastres mediante Azure DNS y Traffic Manager | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](../networking/disaster-recovery-dns-traffic-manager.md)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute se puede usar para establecer una ruta de acceso privada desde un entorno local a sistemas hospedados en Azure. Esta conexión puede proporcionar mayor confiabilidad y rendimiento garantizado con una privacidad mejorada para las comunicaciones de red. ExpressRoute permite a las entidades de la Commonwealth controlar el tráfico entrante desde el entorno local y definir direcciones dedicadas específicas de la organización para usarlas con las reglas de firewall de entrada y las listas de control de acceso.

|Resource | Vínculo|
|---|---|
|Introducción a ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](../expressroute/index.yml)|
|Modelos de conectividad de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](../expressroute/expressroute-connectivity-models.md)|
|

### <a name="expressroute-private-peering"></a>Emparejamiento privado de ExpressRoute

El emparejamiento privado proporciona un mecanismo para extender un entorno local a Azure con solo direcciones IP privadas. Esto permite a las entidades de la Commonwealth integrar redes virtuales de Azure e intervalos de direcciones con los sistemas y servicios locales existentes. El emparejamiento privado proporciona la seguridad de que la comunicación mediante ExpressRoute solo produce para las redes virtuales autorizadas por la organización. Si usa el emparejamiento privado, las entidades de la Commonwealth deben implementar aplicaciones virtuales de red (NVA) en lugar de Azure VPN Gateway para establecer la comunicación VPN segura con las redes locales según lo establecido en la guía para el consumidor de ACSC.

|Resource | Vínculo|
|---|---|
|Introducción al emparejamiento privado de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|Guía paso a paso sobre el emparejamiento privado de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](../expressroute/expressroute-howto-routing-portal-resource-manager.md#private)|
|

### <a name="expressroute-microsoft-peering"></a>Emparejamiento de Microsoft para ExpressRoute

El emparejamiento de Microsoft proporciona una conexión de baja latencia y alta velocidad a los servicios públicos de Microsoft sin necesidad de pasar por Internet. Esto proporciona mayor confiabilidad, rendimiento y privacidad para las conexiones. Mediante el uso de filtros de ruta, las entidades de la Commonwealth pueden restringir las comunicaciones a solo aquellas regiones de Azure que requieran, pero esto incluye servicios hospedados por otras organizaciones y puede exigir funcionalidades de filtrado o inspección adicionales entre el entorno local y Microsoft.

Las entidades de la Commonwealth pueden usar las direcciones IP públicas dedicadas que se establecen mediante la relación de emparejamiento para identificar de forma exclusiva el entorno local para su uso en firewalls y listas de control de acceso dentro de las funcionalidades de PaaS.

Como alternativa, las entidades de la Commonwealth pueden usar el emparejamiento de Microsoft para ExpressRoute como red subyacente para establecer la conectividad VPN mediante Azure VPN Gateway. En este modelo no hay ninguna comunicación activa desde la red local interna a los servicios públicos de Azure mediante ExpressRoute, pero la conectividad segura mediante las redes virtuales privadas se logra de acuerdo con la guía para el consumidor de ACSC.

|Resource | Vínculo|
|---|---|
|Introducción al emparejamiento de Microsoft para ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|Guía paso a paso sobre el emparejamiento de Microsoft para ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](../expressroute/expressroute-howto-routing-portal-resource-manager.md#msft)|
|

## <a name="iaas-ingress"></a>Entrada de IaaS

En esta sección se proporciona una guía para controlar el tráfico de entrada a los componentes de IaaS. IaaS incluye Virtual Machines y otros recursos de proceso que se pueden implementar y administrar dentro de una red virtual en Azure. Para que el tráfico llegue a los sistemas implementados mediante IaaS, deben tener un punto de entrada a la red virtual que se pueda establecer mediante una dirección IP pública, una puerta de enlace de red o una relación de emparejamiento de redes virtuales.

### <a name="network-interface"></a>Interfaz de red

Las interfaces de red son los puntos de entrada de todo el tráfico a una máquina virtual. Las interfaces de red permiten la configuración de direcciones IP y se pueden usar para aplicar grupos de seguridad de red o para enrutar el tráfico mediante una aplicación virtual de red. Las interfaces de red de Virtual Machines deben planearse y configurarse correctamente para que se alineen con los objetivos generales de segmentación y segregación de la red.

|Resource | Vínculo|
|---|---|
|Creación, cambio o eliminación de una interfaz de red | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md)|
|Direccionamiento IP de la interfaz de red | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/private-ip-addresses.md)|
|

### <a name="subnet"></a>Subred

Las subredes son un componente fundamental de la segmentación y la segregación de la red dentro de Azure. Se pueden usar subredes como para proporcionar la separación entre sistemas. Los grupos de seguridad de red se pueden aplicar a las subredes para restringir los flujos de comunicación de entrada solo a los necesarios para que funcione el sistema. Las subredes se pueden usar como direcciones de origen y de destino para las reglas de firewall y las listas de control de acceso, y se pueden configurar para los puntos de conexión de servicio a fin de proporcionar conectividad a las funcionalidades de PaaS.

|Resource | Vínculo|
|---|---|
|Incorporación, cambio o eliminación de una subred de red virtual | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)|
|

### <a name="virtual-network-vnet"></a>Virtual Network

Las redes virtuales son uno de los bloques de creación fundamentales de las redes en Azure. Definen un espacio de direcciones IP y un límite de enrutamiento para usar en diversos sistemas. Las redes virtuales se dividen en subredes y todas las subredes de una red virtual tienen una ruta de red directa entre ellas. Mediante el uso de puertas de enlace de red virtual (ExpressRoute o VPN), los sistemas de una red virtual pueden ser accesibles para entornos locales y externos. Para entender y controlar el tráfico de entrada es esencial comprender las redes virtuales y los parámetros de configuración asociados, así como el enrutamiento.

|Resource | Vínculo|
|---|---|
|Introducción a las redes virtuales | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md)|
|Guía paso a paso para planear redes virtuales | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|
Inicio rápido de creación de una red virtual | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)|
|

### <a name="vnet-peering"></a>Emparejamiento de redes virtuales

El emparejamiento de redes virtuales se usa para proporcionar una ruta de comunicación directa entre dos redes virtuales. Una vez establecido el emparejamiento, los hosts de una red virtual tendrán enrutamiento de alta velocidad y directa a los hosts de la otra. Se siguen aplicando grupos de seguridad de red al tráfico y los parámetros de configuración normal y avanzada sirven para definir si se permite la comunicación mediante puertas de enlace de red virtual u otros sistemas externos.

|Resource | Vínculo|
|---|---|
|Introducción al emparejamiento de redes virtuales |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md)|
|Crear, cambiar o eliminar un emparejamiento de red virtual | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="public-ip-on-vnet"></a>IP pública en la red virtual

Las direcciones IP públicas se usan para proporcionar una ruta de comunicación de entrada a los servicios implementados en una red virtual. Las entidades de la Commonwealth deben planear la asignación de direcciones IP públicas con cuidado y asignarlas únicamente a los recursos en los que exista una verdadera necesidad. Como práctica de diseño general, las direcciones IP públicas se deben asignar a recursos con funcionalidades de seguridad integradas, como Application Gateway o aplicaciones virtuales de red, para proporcionar un punto de entrada público controlado y seguro a una red virtual.

|Resource | Vínculo|
|---|---|
|Introducción a las direcciones IP públicas | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/public-ip-addresses.md#public-ip-addresses)|
|Creación, modificación o eliminación de una dirección IP pública | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/virtual-network-public-ip-address.md)|
|

### <a name="expressroute-gateway"></a>Puerta de enlace de ExpressRoute

Las puertas de enlace de ExpressRoute proporcionan un punto de entrada desde el entorno local y se deben implementar para cumplir los requisitos de seguridad, disponibilidad, económicos y de rendimiento. Las puertas de enlace de ExpressRoute proporcionan un ancho de banda de red definido y generan costos tras la implementación. Las redes virtuales solo pueden tener una puerta de enlace de ExpressRoute, pero pueden estar conectadas a varios circuitos ExpressRoute y las pueden aprovechar varias redes virtuales mediante el emparejamiento de redes virtuales, lo que las permite compartir el ancho de banda y la conectividad. Al configurar el enrutamiento entre entornos locales y redes virtuales mediante puertas de enlace de ExpressRoute, se debe tener cuidado a fin de garantizar la conectividad de un extremo a otro mediante puntos de entrada de red conocidos y controlados. Las entidades de la Commonwealth que usen la puerta de enlace de ExpressRoute también deben implementar aplicaciones virtuales de red para establecer la conectividad de VPN con el entorno local de cara al cumplimiento de la guía para el consumidor de ACSC.

|Resource | Vínculo|
|---|---|
|Introducción a la puerta de enlace de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md)|
|Configuración de una puerta de enlace de red virtual para ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway proporciona un punto de red de entrada desde una red externa para proteger las conexiones de punto a sitio o de sitio a sitio. Las puertas de enlace de VPN proporcionan un ancho de banda de red definido y generan costos de uso tras la implementación. Las entidades de la Commonwealth que utilizan VPN Gateway deben asegurarse de que se configura de acuerdo con la guía para el consumidor del ACSC. Las redes virtuales solo pueden tener una instancia de VPN Gateway, pero se pueden configurar con varios túneles y varias redes virtuales pueden aprovecharlas mediante el emparejamiento de redes virtuales, lo que permite que compartan el ancho de banda y la conectividad. Las puertas de enlace de VPN se pueden establecer a través de Internet o de ExpressRoute por medio del emparejamiento de Microsoft.

|Resource | Vínculo|
|---|---|
|Introducción a VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|Planeamiento y diseño de VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
|Configuración de VPN Gateway para las agencias de la administración pública de Australia|[Configuración de IPsec necesaria para las agencias de la administración pública de Australia](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Integración de PaaS con la red virtual

Aprovechar PaaS aporta funcionalidad y disponibilidad mejoradas y reduce la sobrecarga de administración, pero se debe proteger adecuadamente. Para aumentar el control, exigir la segmentación de la red o proporcionar un punto de entrada seguro para aplicaciones y servicios, muchas de las funcionalidades de PaaS se pueden integrar con una red virtual.

Para proporcionar un punto de entrada seguro, las funcionalidades de PaaS como Application Gateway se pueden configurar con una interfaz de acceso público externa y una interfaz privada interna para la comunicación con los servicios de aplicaciones. Esto evita la necesidad de configurar servidores de aplicaciones con direcciones IP públicas y exponerlos a redes externas.

Para usar PaaS como parte integrada de la arquitectura del sistema o de la aplicación, Microsoft proporciona varios mecanismos de implementación de PaaS en una red virtual. La metodología de implementación restringe el acceso de entrada desde las redes externas, como Internet, al tiempo que proporciona conectividad e integración con sistemas y aplicaciones internos. Algunos ejemplos son las instancias de App Service Environment, SQL Managed Instance, etc.

|Resource | Vínculo|
|---|---|
|Integración de red virtual para los servicios de Azure | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md)|
|Guía paso a paso para integrar la aplicación con una instancia de Azure Virtual Network | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/web-sites-integrate-with-vnet.md)|
|

## <a name="paas-ingress"></a>Entrada de PaaS

Las funcionalidades de PaaS proporcionan oportunidades para aumentar la capacidad y la administración simplificada, pero presentan complejidades a la hora de abordar los requisitos de segmentación y segregación de la red. Normalmente se configuran con direcciones IP públicas y son accesibles desde Internet.  Al compilar sistemas con funcionalidades de PaaS, se debe tener cuidado para identificar todos los flujos de comunicación necesarios entre los componentes del sistema y las reglas de seguridad de red creadas para permitir solo esta comunicación. Como parte de un enfoque de seguridad basado en la defensa en profundidad, las funcionalidades de PaaS deben configurarse con cifrado, autenticación y controles de acceso y permisos adecuados.  

### <a name="hostname"></a>Nombre de host

Las funcionalidades de PaaS se identifican de forma exclusiva mediante los nombres de host para permitir que varias instancias del mismo servicio se hospeden en la misma dirección IP pública. Los nombres de host únicos se especifican cuando se crean los recursos y existen dentro de los dominios DNS propiedad de Microsoft. Los nombres de host específicos de los servicios autorizados se pueden usar en herramientas de seguridad con funcionalidades de filtrado de nivel de aplicación. Algunos servicios también se pueden configurar con dominios personalizados, según sea necesario.

|Resource | Vínculo|
|---|---|
|Muchos espacios de nombres públicos utilizados por los servicios de Azure se pueden obtener mediante la ejecución del comando de PowerShell Get-AzureRMEnvironment. | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](/powershell/module/azurerm.profile/get-azurermenvironment)|
|Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure | App Services y otros pueden tener dominios personalizados [https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](../cloud-services/cloud-services-custom-domain-name-portal.md)|
|

### <a name="public-ip-for-paas"></a>IP pública para PaaS

Las direcciones IP públicas para las funcionalidades de PaaS se asignan en función de la región en la que se hospeda o implementa el servicio. Para compilar las reglas de seguridad de red y la topología de enrutamiento adecuadas para la segmentación y la segregación de la red que abarcan redes virtuales de Azure, PaaS, ExpressRoute y la conectividad a Internet, es necesario conocer las regiones y la asignación de direcciones IP públicas. Azure asigna direcciones IP de un grupo asignado a cada región de Azure. Microsoft permite que las direcciones usadas en cada región estén disponibles para su descarga, que se actualiza de forma periódica y controlada. Los servicios que están disponibles en cada región también cambian con frecuencia a medida que se publican nuevos servicios o los servicios se implementan más ampliamente. Las entidades de la Commonwealth deben revisar estos materiales periódicamente y pueden aprovechar la automatización para el mantenimiento de los sistemas según proceda. Para obtener las direcciones IP específicas de algunos servicios hospedados en cada región, puede ponerse en contacto con el servicio de soporte técnico de Microsoft.

|Resource | Vínculo|
|---|---|
|Intervalos de direcciones IP del centro de datos de Microsoft Azure | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Servicios de Azure por región | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Puntos de conexión del servicio

Los puntos de conexión de red virtual proporcionan una conexión de red de entrada privada y de alta velocidad para las subredes de una red virtual que consumen funcionalidades de PaaS específicas. Para la segmentación y la segregación completas de la funcionalidad de PaaS, esta debe configurarse de manera que acepte la conexión solo desde las redes virtuales necesarias. No todas las funcionalidades de PaaS admiten una combinación de reglas de firewall que incluya puntos de conexión de servicio y reglas tradicionales basadas en direcciones IP, por lo que se debe prestar atención para comprender el flujo de comunicaciones necesario para la funcionalidad y administración de la aplicación de modo que la implementación de estos controles de seguridad no afecte a la disponibilidad del servicio.

|Resource | Vínculo|
|---|---|
|Introducción a los puntos de conexión de servicio | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](../virtual-network/virtual-network-service-endpoints-overview.md)
|Tutorial |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

## <a name="security"></a>Seguridad

La implementación de los controles de segmentación y segregación de la red en las funcionalidades IaaS y PaaS se logra mediante la protección de las funcionalidades en sí y la implementación de rutas de comunicación controladas desde los sistemas que se comunicarán con ellas.

El diseño y la creación de soluciones en Azure es un proceso de creación de una arquitectura lógica para comprender, controlar y supervisar los recursos de red allí donde Azure esté presente. Esta arquitectura lógica es software que se define dentro de la plataforma Azure y ocupa el lugar de una topología de red física que se implementa en entornos de red tradicionales.

La arquitectura lógica que se crea debe proporcionar la funcionalidad necesaria que facilite su uso, pero también debe proporcionar la visibilidad y el control necesarios de cara a la seguridad y la integridad.

La obtención de este resultado se basa en la implementación de las herramientas de segregación y segmentación de la red necesarias, pero también en la protección y la aplicación de la topología de red y la implementación de estas herramientas.

La información proporcionada en esta guía se puede usar para ayudar a identificar los orígenes de tráfico de entrada que se deben permitir y las formas en las que el tráfico se puede controlar o restringir aún más.

### <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSG)

Los grupos de seguridad de red se usan para especificar el tráfico entrante y saliente permitido en una subred o una interfaz de red específica. Al configurar los grupos de seguridad de red, las entidades de la Commonwealth deben usar un enfoque de listas de aprobaciones en las que se configuran reglas para permitir el tráfico necesario, con una regla predeterminada configurada para denegar todo el tráfico que no coincida con una declaración de permiso específica. Al planear y configurar los grupos de seguridad de red, debe prestar atención para asegurarse de que todo el tráfico entrante y saliente necesario se capture correctamente. Esto incluye identificar y conocer todos los intervalos de direcciones IP privadas que se utilizan en las redes virtuales de Azure y el entorno local, y en servicios de Microsoft específicos, como los requisitos de administración de Azure Load Balancer y PaaS. Las personas implicadas en el diseño y la implementación de grupos de seguridad de red también deben comprender el uso de las etiquetas de servicio y los grupos de seguridad de aplicaciones para crear reglas de seguridad específicas detalladas de la aplicación y del servicio.

|Resource | Vínculo|
|---|---|
|Introducción a la seguridad de red | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)
|Crear, modificar o eliminar un grupo de seguridad de red | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

## <a name="paas-firewall"></a>Firewall de PaaS

Un firewall de PaaS es una funcionalidad de control de acceso a la red que se puede aplicar a determinados servicios de PaaS. Permite configurar el filtrado de direcciones IP o de redes virtuales específicas para restringir el tráfico de entrada a la instancia específica de PaaS. En el caso de las funcionalidades de PaaS que incluyen un firewall, deben configurarse las directivas de control de acceso a la red para permitir solo el tráfico de entrada necesario en función de los requisitos de la aplicación.  

|Resource | Vínculo|
|---|---|
|Reglas de firewall de Azure SQL Database y de Azure Synapse Analytics | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](../azure-sql/database/firewall-configure.md)|
|Seguridad de red de almacenamiento | [https://docs.microsoft.com/azure/storage/common/storage-network-security](../storage/common/storage-network-security.md)|
|

## <a name="paas-authentication-and-access-control"></a>Autenticación de PaaS y control de acceso

Dependiendo de la funcionalidad de PaaS y su propósito, los controles de red para restringir el acceso pueden resultar imposibles o poco prácticos. Como parte del modelo de seguridad por capas para PaaS, Azure proporciona una variedad de mecanismos de autenticación y control de acceso para restringir el acceso a un servicio, aunque se permita el tráfico. Los mecanismos de autenticación típicos para las funcionalidades de PaaS incluyen Azure Active Directory, la autenticación a nivel de aplicación y claves compartidas o firmas de acceso. Una vez identificado un usuario de forma segura, los roles sirven para controlar las acciones que este puede realizar. Estas herramientas se pueden utilizar como alternativa o como medida complementaria para restringir el acceso a los servicios.

|Resource | Vínculo|
|---|---|
|Control y concesión de acceso de la base de datos a SQL Database y Azure Synapse Analytics | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](../azure-sql/database/logins-create-manage.md)|
|Autorización para los servicios de almacenamiento de Azure | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy es un componente clave para aplicar y mantener la integridad de la arquitectura lógica del entorno de Azure. Dada la variedad de servicios y las rutas de acceso de tráfico entrante disponibles mediante los servicios de Azure, es fundamental que las entidades de la Commonwealth conozcan los recursos que existen en su entorno y los puntos de entrada de red disponibles. Para asegurarse de que no se creen puntos de entrada de red no autorizados en el entorno de Azure, las entidades de la Commonwealth deben aprovechar las ventajas de Azure Policy para controlar los tipos de recursos que se pueden implementar y la configuración de esos recursos. Entre los ejemplos prácticos, se incluye la restricción de los recursos a solo aquellos autorizados y aprobados para su uso, la aplicación del cifrado HTTPS en el almacenamiento y la obligación de agregar grupos de seguridad de red a las subredes.

|Resource | Vínculo|
|---|---|
|Introducción a Azure Policy | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|Directiva de ejemplo de los tipos de recursos permitidos | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)
|Comprobación de la directiva de ejemplo de la cuenta de almacenamiento HTTPS|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](../governance/policy/samples/index.md)_
|Directiva de ejemplo para forzar los NSG en una subred| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="next-steps"></a>Pasos siguientes

Revise el artículo sobre [la administración y el control del tráfico de salida de la puerta de enlace](gateway-egress-traffic.md) para más información sobre la administración de los flujos de tráfico desde el entorno de Azure a otras redes con los componentes de la puerta de enlace de Azure.
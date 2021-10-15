---
title: Control del tráfico de salida en Azure Australia
description: Principales elementos del control del tráfico de salida en Azure a fin de cumplir los requisitos de la administración pública de Australia de las puertas de enlace de Internet seguras.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yvettep
ms.openlocfilehash: 58fccb77131aac8311029cb06372201f2dd4b3bb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359559"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Control del tráfico de salida en Azure Australia

Uno de los componentes fundamentales de la protección de los sistemas ICT es el control del tráfico. Restringir la comunicación a solo al tráfico necesario para que un sistema funcione reduce los peligros. La visibilidad y el control sobre los sistemas externos con los que se comunican sus aplicaciones y servicios ayudan a detectar los peligros, así como los intentos de filtración de datos o la consecución de este tipo de ataque. En este artículo se proporciona información sobre cómo funciona el tráfico saliente (salida) dentro de Azure y se ofrecen recomendaciones para la implementación de controles de seguridad de red en sistemas conectados a Internet para que estén en consonancia con la guía del cliente de Australian Cyber Security Centre (ACSC) y la intención de su manual de seguridad de la información (ISM, por sus siglas en inglés).

## <a name="requirements"></a>Requisitos

Los requisitos de seguridad generales para los sistemas de la Commonwealth se definen en el manual ISM. Para ayudar a las entidades de la Commonwealth a implementar la seguridad de red, la ACSC ha publicado _ACSC Protect: Implementing Network Segmentation and Segregation_ (Protección de ACSC: Implementación de la segmentación y la segregación de la red) y, para facilitar la protección de los sistemas en entornos en la nube, _Cloud Computing Security for Tenants_. (Seguridad de la informática en la nube para los inquilinos).

En los documentos de ACSC se describe el contexto para implementar la seguridad de red y controlar el tráfico, y se proporcionan recomendaciones prácticas para el diseño y la configuración de red.

En ellos se han identificado los siguientes requisitos clave para controlar el tráfico de salida en Azure.

Descripción|Source
--------------- |------------------
**Implementing Network Segmentation and Segregation** (Implementación de la segmentación y la segregación de la red): por ejemplo, use una arquitectura de n niveles con firewalls basados en host y controles de acceso a la red para limitar la conectividad de red entrante y saliente únicamente a los puertos y protocolos necesarios.| [Cloud Computing for Tenants](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm) (Informática en la nube para inquilinos)
**Implemente una conectividad de red confiable, de baja latencia y alto ancho de banda** entre el inquilino (incluidos sus usuarios remotos) y el servicio en la nube para satisfacer los requisitos de disponibilidad del inquilino  | [ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (Protección de ASCS: Implementación de la segmentación y la segregación de la red)
**Aplique tecnologías en más de solo una capa de red**. Cada host y cada red se deben segmentar y segregar, siempre que sea posible, en el nivel más bajo que se pueda administrar de forma práctica. En la mayoría de los casos, abarca desde la capa de vínculo de datos hasta la capa de aplicación (incluida); sin embargo, en entornos confidenciales, el aislamiento físico puede ser adecuado. Se deben implementar medidas basadas en el host y para toda la red de forma complementaria y se deben supervisar de manera centralizada. No basta con implementar un firewall o un dispositivo de seguridad como única medida de seguridad. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (Protección de ASCS: Implementación de la segmentación y la segregación de la red)
**Use los principios de privilegios mínimos y necesidad de conocer**. Si un host, un servicio o una red no necesitan comunicarse con otro host, servicio o red, no se le debe permitir hacerlo. Si un host, un servicio o una red solo necesitan comunicarse con otro host, servicio o red en un puerto o protocolo específicos, se debe restringir el acceso solo a esos puertos y protocolos. La adopción de estos principios en una red complementará a la reducción de privilegios de usuario y aumentará considerablemente la posición de seguridad general del entorno. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (Protección de ASCS: Implementación de la segmentación y la segregación de la red)
**Separe los hosts y las redes en función de su confidencialidad o importancia para las operaciones empresariales**. Esto puede incluir el uso de hardware o plataformas diferentes en función de distintas clasificaciones de seguridad, dominios de seguridad o requisitos de disponibilidad e integridad de determinados hosts o redes. En especial, separe las redes de administración y considere la posibilidad de aislar físicamente las que estén fuera de banda en entornos confidenciales. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (Protección de ASCS: Implementación de la segmentación y la segregación de la red)
**Identifique, autentique y autorice el acceso de todas las entidades a todas las demás entidades**. Todos los usuarios, hosts y servicios deben tener su acceso a todos los demás usuarios, hosts y servicios restringido solo a aquellos que necesiten para realizar sus tareas o funciones designadas. Todos los servicios heredados o locales que eluden o degradan la seguridad de los servicios de identificación, autenticación y autorización se deben deshabilitar siempre que sea posible y supervisar estrechamente su uso. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (Protección de ASCS: Implementación de la segmentación y la segregación de la red)
**Implemente la lista de permitidos del tráfico de red, en lugar de la lista de denegados**. Solo se permite el acceso del tráfico de red bueno conocido (tráfico identificado, autenticado y autorizado), en lugar de denegar el acceso al tráfico de red malo conocido (por ejemplo, bloquear una dirección o un servicio específicos). Las listas de permitidos dan como resultado una directiva de seguridad superior a las listas de denegados y mejoran considerablemente su capacidad para detectar y evaluar posibles intrusiones de red. |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (Protección de ASCS: Implementación de la segmentación y la segregación de la red)
**La definición de una lista de sitios web permitidos y el bloqueo de todos los sitios web que no están en la lista** elimina eficazmente una de las técnicas de entrega y filtración de datos más comunes usadas por un adversario. Si los usuarios tienen la necesidad legítima de acceder a numerosos sitios web o a una lista de sitios web que cambia rápidamente, debe tener en cuenta los costos de este tipo de implementación. Incluso una lista de permitidos relativamente permisiva ofrece una mayor seguridad que confiar en las listas de denegación o en la falta total de restricciones, a la vez que se reducen los costos de implementación. Un ejemplo de una lista de permitidos permisiva podría ser permitir todo el subdominio australiano, es decir, "*.au", o permitir los 1000 primeros sitios de la clasificación de sitios de Alexa (después de filtrar los dominios del sistema de nombres de dominio dinámicos [DDNS] y otros dominios inadecuados).| [Manual de seguridad de la información (ISM) de la administración pública de Australia](https://www.cyber.gov.au/ism)
|

En este artículo se proporciona información y recomendaciones sobre cómo se controla el tráfico que sale del entorno de Azure. Abarca los sistemas implementados en Azure mediante infraestructura como servicio (IaaS) y plataforma como servicio (PaaS).

En el artículo sobre el [tráfico de entrada de la puerta enlace](gateway-ingress-traffic.md) se aborda el tráfico que entra en el entorno de Azure y es el complemento a este artículo para obtener información completa del control de red.

## <a name="architecture"></a>Architecture

Para controlar correctamente el tráfico de salida, al diseñar e implementar la seguridad de red, primero debe comprender cómo funciona en Azure en IaaS y PaaS. En esta sección se proporciona información general sobre cómo se procesa el tráfico de salida generado por un recurso hospedado en Azure, y los controles de seguridad disponibles para restringir y controlar ese tráfico.

### <a name="architecture-components"></a>Componentes de la arquitectura

El diagrama de arquitectura que se muestra aquí representa las posibles rutas que puede tomar el tráfico al salir de un sistema que se implementa en una subred de una red virtual. El tráfico de una red virtual se administra y regula en el nivel de subred, con la aplicación de reglas de enrutamiento y seguridad a los recursos que contiene. Los componentes relacionados con el tráfico de salida se dividen en sistemas, rutas eficaces, tipos de próximo salto, controles de seguridad y salida de PaaS.

![Architecture](media/egress-traffic.png)

### <a name="systems"></a>Sistemas

Los sistemas son los recursos de Azure y los componentes relacionados que generan tráfico saliente dentro de una subred IP que forma parte de una red virtual.

| Componente | Descripción |
| --- | ---|
|Virtual Network | Una red virtual es un recurso fundamental dentro de Azure que proporciona una plataforma y un límite para implementar recursos y permitir la comunicación. La red virtual existe en una región de Azure y define el espacio de direcciones IP y los límites de enrutamiento de los recursos integrados de red virtual, como Virtual Machines.|
|Subred | Una subred es un intervalo de direcciones IP que se crea dentro de una red virtual. Se pueden crear varias subredes dentro de una red virtual para la segmentación de la red.|
|Interfaz de red| Una interfaz de red es un recurso que existe en Azure. Está vinculada a una máquina virtual y se le asigna una dirección IP privada que no se pueda enrutar a Internet desde la subred a la que está asociada. Esta dirección IP se asigna de forma dinámica o estática mediante Azure Resource Manager.|
|Direcciones IP públicas| Una dirección IP pública es un recurso que reserva una de las direcciones IP públicas enrutables a Internet y propiedad de Microsoft desde la región especificada para su uso en la red virtual. Se puede asociar a una interfaz de red o un recurso de PaaS específicos, lo que permite que el recurso se comunique con Internet, ExpressRoute y otros sistemas PaaS.|
|

### <a name="routes"></a>Rutas

La ruta de acceso que toma el tráfico de salida depende de las rutas eficaces de ese recurso, que es el conjunto resultante de rutas que se determina mediante la combinación de las rutas obtenidas de todos los orígenes posibles y la aplicación de la lógica de enrutamiento de Azure.

| Componente | Descripción |
|--- | ---|
|Rutas del sistema| Azure crea automáticamente rutas del sistema y las asigna a todas las subredes de una red virtual. No se pueden crear ni quitar rutas del sistema, pero algunas se pueden reemplazar por rutas personalizadas. Azure crea rutas del sistema predeterminadas para cada subred y agrega rutas predeterminadas opcionales adicionales a determinadas subredes, o a todas ellas, al usar funcionalidades específicas de Azure.|
|Puntos de conexión de servicio| Los puntos de conexión de servicio proporcionan una conexión de salida privada directa entre una subred y una funcionalidad de PaaS específica. Los puntos de conexión de servicio, que solo están disponibles para un subconjunto de funcionalidades de PaaS, proporcionan un mayor rendimiento y seguridad para los recursos de una red virtual que tenga acceso a PaaS.|
|Tablas de ruta| Una tabla de rutas es un recurso de Azure que se puede crear para especificar rutas definidas por el usuario (UDR) que pueden complementar o reemplazar a las rutas del sistemas o a las rutas obtenidas a través del Protocolo de puerta de enlace de borde. Cada UDR especifica una red, una máscara de red y un próximo salto. Una tabla de rutas se puede asociar a una subred y la misma tabla de rutas se puede asociar a varias subredes, pero una subred solo puede tener una o ninguna tabla de rutas.|
|Protocolo de puerta de enlace de borde (BGP)| BGP es un protocolo de enrutamiento entre sistemas autónomos. Un sistema autónomo es una red o grupo de redes cono una administración común y con directivas de enrutamiento comunes. BGP se usa para intercambiar información de enrutamiento entre sistemas autónomos. Se puede integrar en redes virtuales mediante puertas de enlace de red virtual.|
|

### <a name="next-hop-types-defined"></a>Tipos de próximo salto definidos

Cada ruta de Azure incluye el intervalo de red, la máscara de subred asociada y el próximo salto, que determina cómo se procesa el tráfico.

Tipo de próximo salto | Descripción
---- | ----
**Virtual Network** | Enruta el tráfico entre los intervalos de direcciones del espacio de direcciones de una red virtual. Azure crea una ruta con un prefijo de dirección que corresponde a cada intervalo de direcciones definido en el espacio de direcciones de una red virtual. Si el espacio de direcciones de la red virtual tiene varios intervalos de direcciones definidos, Azure crea una ruta individual para cada intervalo de direcciones. Azure enruta automáticamente el tráfico entre subredes de una red virtual mediante las rutas creadas para cada intervalo de direcciones.
**Emparejamiento de VNET** | Cuando se crea un emparejamiento de redes virtuales entre dos redes virtuales, se agrega una ruta para cada intervalo de direcciones de cada red virtual a la red virtual a la que está emparejada. El tráfico se enruta entre las redes virtuales emparejadas de la misma manera que las subredes de una red virtual.
**Puerta de enlace de red virtual** | Cuando se agrega una puerta de enlace de red virtual a una red virtual, se agregan también una o varias rutas en las que la puerta de enlace de red virtual aparece como el tipo de próximo salto. Las rutas incluidas son las que están configuradas en el recurso de puerta de enlace de red local y las rutas aprendidas mediante BGP.
**Aplicación virtual** | Normalmente, una aplicación virtual ejecuta una aplicación de red, como un firewall. La aplicación virtual permite el procesamiento adicional del tráfico que se produce, como el filtrado, la inspección o la traducción de direcciones. Cada ruta con el tipo de salto de aplicación virtual debe especificar también una dirección IP de próximo salto.
**VirtualNetworkServiceEndpoint** | Las direcciones IP públicas de un servicio específico se agregan como rutas a una subred con un próximo salto de VirtualNetworkServiceEndpoint cuando se habilita un punto de conexión de servicio. Los puntos de conexión de servicio se habilitan para servicios individuales en subredes individuales dentro de una red virtual. Las direcciones IP públicas de los servicios de Azure cambian periódicamente. Azure administra automáticamente las direcciones en la tabla de rutas cuando cambian.
**Internet** | El tráfico con el próximo salto de Internet saldrá de la red virtual y se traducirá automáticamente a una dirección IP pública desde un grupo dinámico disponible en la región de Azure asociada, o por medio de una dirección IP pública configurada para ese recurso. Si la dirección de destino es para uno de los servicios de Azure, el tráfico se enruta directamente al servicio a través de la red troncal de Azure, en lugar de enrutarlo a Internet. El tráfico entre los servicios de Azure no atraviesa Internet, independientemente de la región de Azure en que exista la red virtual o de la región de Azure en que se implementa una instancia del servicio de Azure.
**Ninguno** | Se quita el tráfico con un próximo salto de ninguno. Azure crea rutas predeterminadas del sistema para los prefijos de direcciones reservadas con el tipo de próximo salto de ninguno. También se pueden agregar rutas con un próximo salto de ninguno mediante tablas de rutas para evitar que el tráfico se enrute a redes específicas.
|

### <a name="security-controls"></a>Controles de seguridad

Control | Descripción
----- | -----
**Grupos de seguridad de red** | Los NSG controlan el tráfico dentro y fuera de los recursos de red virtual de Azure. Los NSG aplican reglas para los flujos de tráfico que se permiten o deniegan, lo que incluye el tráfico dentro de Azure y entre Azure y redes externas, como locales o Internet. Los NSG se aplican a subredes de una red virtual o a interfaces de red individuales.
**Azure Firewall** | Azure Firewall es un servicio de seguridad de red administrado y basado en la nube que protege los recursos de red virtual de Azure. Se trata de un firewall como servicio con estado completo que incorpora alta disponibilidad y escalabilidad a la nube sin restricciones. Azure Firewall se pueden configurar con reglas de filtrado de red tradicionales basadas en direcciones IP, protocolos y puertos, pero también admite el filtrado basado en nombres de dominio completos (FQDN), etiquetas de servicio e información de amenazas integrada.
**Aplicación virtual de red (NVA)** | Las aplicaciones virtuales de red son medios de máquina virtual que pueden proporcionar funciones de red, seguridad, etc., a Azure. Las NVA admiten servicios y funcionalidades de red en forma de máquinas virtuales en redes virtuales e implementaciones. Se pueden usar para abordar requisitos específicos, realizar la integración con herramientas de administración y operativas, o para proporcionar coherencia con los productos existentes. Azure admite una amplia lista de aplicaciones virtuales de red de terceros, como firewalls de aplicaciones web (WAF), firewalls, puertas de enlace o enrutadores, controladores de entrega de aplicaciones (ADC) y optimizadores WAN.
**Directivas de punto de conexión de servicio (versión preliminar)** | Las directivas de punto de conexión de servicio de red virtual le permiten filtrar el tráfico de red virtual a los servicios de Azure, de forma que solo recursos de servicio de Azure específicos pueden pasar por los puntos de conexión de servicio. Las directivas de punto de conexión ofrecen un control de acceso pormenorizado para el tráfico de red virtual a los servicios de Azure.
**Azure Policy** | Azure Policy es un servicio de Azure para crear, asignar y administrar directivas. Estas directivas usan reglas para controlar los tipos de recursos que se pueden implementar y la configuración de esos recursos. Se pueden usar directivas para exigir el cumplimiento e impedir la implementación de recursos si no satisfacen los requisitos; también se pueden utilizar para supervisar e informar sobre el estado de cumplimiento.
|

### <a name="paas-egress"></a>Salida de PaaS

La mayoría de los recursos de PaaS no generan tráfico de salida, sino que responden a las solicitudes entrantes (por ejemplo, de Application Gateway, Storage, SQL Database, etc.) o retransmiten los datos de otros recursos (como Service Bus y Azure Relay). Los flujos de comunicación de red entre recursos de PaaS, por ejemplo, de App Services a Storage o a bases de datos SQL, se controlan y contienen en Azure y se protegen mediante la identidad y otros controles de configuración de recursos en lugar de la segmentación o la segregación de red.

Los recursos de PaaS implementados en una red virtual reciben direcciones IP dedicadas y están sujetos a controles de enrutamiento y NSG de la misma manera que otros recursos de la red virtual. Los recursos de PaaS que no existen dentro de una red virtual usarán un grupo de direcciones IP que se comparten entre todas las instancias del recurso. Estas direcciones se publican mediante la documentación de Microsoft o se pueden determinar por medio de Azure Resource Manager.

## <a name="general-guidance"></a>Instrucciones generales

Para diseñar y compilar soluciones seguras en Azure, es fundamental entender y controlar el tráfico de red para que solo pueda tener lugar la comunicación identificada y autorizada. La intención de estas instrucciones, y las de componentes específicos de las secciones posteriores, es describir las herramientas y los servicios que se pueden usar para aplicar los principios descritos en [ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) entre cargas de trabajo de Azure. Esto incluye información detallada sobre cómo crear una arquitectura virtual para proteger los recursos cuando no es posible aplicar los mismos controles físicos y de red tradicionales que en un entorno local.

### <a name="guidance"></a>Guía

* Limitar el número de puntos de salida de las redes virtuales
* Invalidar la ruta predeterminada del sistema en todas las subredes que no necesiten comunicación de salida directa a Internet
* Diseñar e implementar una arquitectura de red completa para identificar y controlar todos los puntos de entrada y salida de los recursos de Azure
* Considerar la posibilidad de usar un diseño de red de tipo hub-and-spoke para las redes virtuales, como se explica en la documentación del centro de datos virtual de Microsoft (VDC)
* Utilizar productos con funcionalidades de seguridad integradas en las conexiones salientes a Internet (por ejemplo, Azure Firewall, aplicaciones virtuales de red o proxies web)
* Usar controles de identidad, como el control de acceso basado en rol de Azure, el acceso condicional y Multi-Factor Authentication (MFA) para limitar los privilegios de configuración de red
* Implementar bloqueos para impedir la modificación o eliminación de elementos clave de la configuración de red
* Implemente PaaS en una configuración integrada de red virtual para mayor segregación y control
* Implemente ExpressRoute para la conectividad con las redes locales
* Implementar VPN para la integración con redes externas
* Utilice Azure Policy para restringir las regiones y los recursos solo a los necesarios para que funcione el sistema
* Utilizar Azure Policy para aplicar la configuración de seguridad de línea de base a los recursos
* Aprovechar Network Watcher y Azure Monitor para registrar y auditar el tráfico de red en Azure y obtener visibilidad sobre él

### <a name="resources"></a>Recursos

Elemento | Vínculo
-----------| ---------
_Documentos de cumplimiento de políticas y reglamentos australianos, que incluyen una guía del consumidor_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Centro de datos virtual de Azure_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)
_Segmentación de red de ACSC_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_Seguridad en la nube de ACSC para inquilinos_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_Manual de seguridad de la información de ACSC_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Guía de componentes

En esta sección se proporciona una guía adicional sobre los componentes individuales que son pertinentes para el tráfico de salida de los sistemas implementados en Azure. En cada sección se describe la intención del componente específico con vínculos a documentación y guías de configuración que se pueden usar para ayudar con las actividades de diseño y compilación.

### <a name="systems-security"></a>Seguridad de los sistemas

Toda la comunicación con los recursos de Azure pasa por la infraestructura de red mantenida por Microsoft, que proporciona funciones de conectividad y seguridad. Microsoft implanta automáticamente una serie de protecciones para proteger la plataforma y la infraestructura de red de Azure. Existen además funcionalidades adicionales como servicios dentro de Azure para controlar el tráfico de red y establecer la segmentación y la segregación de la red.

### <a name="virtual-network-vnet"></a>Virtual Network

Las redes virtuales son uno de los bloques de creación fundamentales de las redes en Azure. Definen un espacio de direcciones IP y un límite de enrutamiento para usar en diversos sistemas. Las redes virtuales se dividen en subredes y todas las subredes de una red virtual tienen una ruta de red directa entre ellas. Gracias a las puertas de enlace de red virtual (ExpressRoute o VPN), los sistemas de una red virtual se pueden integrar con entornos locales y externos, y mediante la traducción de direcciones de red (NAT) y la asignación de direcciones IP públicas proporcionadas por Azure, los sistemas pueden conectarse a Internet o a otras regiones y servicios de Azure. Para entender y controlar el tráfico de salida es esencial comprender las redes virtuales y los parámetros de configuración asociados, así como el enrutamiento.

Como las redes virtuales constituyen el espacio de direcciones base y el límite de enrutamiento dentro de Azure, se pueden usar como bloque de creación principal de la segmentación y la segregación de la red.

| Resource | Vínculo |
| --- | --- |
| *Introducción a las redes virtuales* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md) |
| *Guía paso a paso para planear las redes virtuales*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md) |
| *Inicio rápido de creación de una red virtual* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)
|

### <a name="subnet"></a>Subred

Las subredes son un componente fundamental de la segmentación y la segregación de la red dentro de Azure. Se pueden usar subredes para proporcionar la separación entre sistemas. Una subred es el recurso dentro de una red virtual donde se aplican los NSG, las tablas de rutas y los puntos de conexión de servicio. Se pueden usar subredes como direcciones de origen y de destino para las reglas de firewall y las listas de control de acceso.

Las subredes de una red virtual deben planearse para que cumplan los requisitos de las cargas de trabajo y los sistemas. Las personas que intervienen en el diseño o la implementación de subredes deben consultar las directrices de ACSC sobre la segmentación de la red con el fin de determinar cómo se deben agrupar los sistemas en una subred.

|Resource|Vínculo|
|---|---|
|*Incorporación, cambio o eliminación de una subred de red virtual* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)
|

### <a name="network-interface"></a>Interfaz de red

Las interfaces de red son el origen de todo el tráfico de salida procedente de una máquina virtual. Permiten la configuración de direcciones IP y se pueden usar para aplicar NSG o para enrutar el tráfico a través de una NVA. Las interfaces de red de las máquinas virtuales deben planearse y configurarse correctamente para que se alineen con los objetivos generales de segmentación y segregación de la red.

|Resource|Vínculo|
|---|---|
|*Creación, cambio o eliminación de una interfaz de red* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md) |
|*Direccionamiento IP de la interfaz de red*               | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/private-ip-addresses.md)
|

### <a name="vnet-integrated-paas"></a>PaaS integrada de VNet

PaaS puede proporcionar funcionalidad y disponibilidad mejoradas y reducir la sobrecarga de administración, pero se debe proteger adecuadamente. Para aumentar el control, exigir la segmentación de la red o proporcionar un punto de salida seguro para aplicaciones y servicios, muchas de las funcionalidades de PaaS se pueden integrar con una red virtual.

Con PaaS como parte integrada de la arquitectura del sistema o de la aplicación, Microsoft proporciona varios mecanismos para implementar PaaS en una red virtual. La metodología de implementación puede ayudar a restringir el acceso, a la vez que proporciona conectividad e integración con sistemas y aplicaciones internos. Algunos ejemplos son las instancias de App Service Environment, SQL Managed Instance, etc.

Al implementar PaaS en una red virtual donde se han implementado controles de enrutamiento y NSG, es fundamental comprender los requisitos de comunicación específicos del recurso, como el acceso de administración desde los servicios de Microsoft y la ruta de acceso que tomará el tráfico de comunicaciones al responder a las solicitudes entrantes procedentes de estos servicios.

| Resource  | Vínculo  |
| --- | --- |
| *Integración de red virtual para los servicios de Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md) |
| *Guía paso a paso para integrar la aplicación con una instancia de Azure Virtual Network* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/web-sites-integrate-with-vnet.md)
|

### <a name="public-ip"></a>Dirección IP pública

Las direcciones IP públicas se usan para la comunicación fuera de una red virtual. Esto incluye los recursos de PaaS y cualquier ruta con un próximo salto de Internet. Las entidades de la Commonwealth deben planear la asignación de direcciones IP públicas con cuidado y asignarlas únicamente a los recursos en los que exista una verdadera necesidad. Como práctica de diseño general, las direcciones IP públicas deben asignarse a puntos de salida controlados de la red virtual, como Azure Firewall, VPN Gateway o aplicaciones virtuales de red.

|Resource|Vínculo|
|---|---|
|*Introducción a las direcciones IP públicas*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/public-ip-addresses.md#public-ip-addresses) |
|*Creación, modificación o eliminación de una dirección IP pública* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/virtual-network-public-ip-address.md)
|

## <a name="effective-routes"></a>Rutas eficaces

Las rutas eficaces son el conjunto resultante de rutas determinadas por la combinación de rutas del sistema, puntos de conexión de servicio, tablas de rutas y BGP y la aplicación de la lógica de enrutamiento de Azure. Cuando se envía tráfico saliente desde una subred, Azure selecciona una ruta en función de la dirección IP de destino y se usa el algoritmo de coincidencia de prefijo más largo. Si varias rutas contienen el mismo prefijo de dirección, Azure selecciona el tipo de ruta, en función de la siguiente prioridad:

1. Ruta definida por el usuario
2. Ruta BGP
3. Ruta del sistema

Es importante advertir que las rutas del sistema para el tráfico relacionado con la red virtual, los emparejamientos de la red virtual o los puntos de conexión de servicio de red virtual son las rutas preferidas, aunque las rutas BGP sean más específicas.

Las personas que intervienen en el diseño o la implementación de topologías de enrutamiento en Azure deben comprender cómo Azure enruta el tráfico y desarrollar una arquitectura que busque el equilibrio entre la funcionalidad necesaria de los sistemas y la seguridad y visibilidad requeridas. A la hora de planear el entorno, hay que ser precavido, a fin de evitar un número excesivo de intervenciones y excepciones a los comportamientos de enrutamiento, puesto que esta situación puede aumentar la complejidad y convertir la solución de problemas y la detección de errores en un proceso difícil y lento.

| Resource | 
| --- | 
| [Visualización de las rutas eficaces](../virtual-network/manage-route-table.md#view-effective-routes)

### <a name="system-routes"></a>Rutas del sistema

En el caso de las [rutas del sistema](../virtual-network/virtual-networks-udr-overview.md#system-routes), las personas que intervienen en el diseño o la implementación de redes virtuales deben comprender las rutas del sistema predeterminadas y las opciones disponibles para complementar o invalidar esas rutas.

### <a name="service-endpoints"></a>Puntos de conexión del servicio

Al habilitar los [puntos de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md) en una subred, se proporciona una ruta de comunicación directa al recurso de PaaS asociado. Con ello, se puede conseguir un rendimiento y una seguridad mayores al restringir la ruta de acceso de comunicación disponible a solo ese servicio. El uso de puntos de conexión de servicio introduce una posible ruta de acceso a la filtración de datos, ya que la configuración predeterminada permite el acceso a todas las instancias del servicio PaaS en lugar de a las instancias específicas que necesita una aplicación o un sistema.

Las entidades de la Commonwealth deben evaluar el riesgo asociado con el hecho de proporcionar acceso directo al recurso de PaaS, como la probabilidad y la consecuencia del uso incorrecto de la ruta de acceso.

Para reducir los posibles riesgos asociados con los puntos de conexión de servicio, implemente directivas de punto de conexión de servicio siempre que sea posible; también, considere la posibilidad de habilitar puntos de conexión de servicio en una subred de Azure Firewall o NVA y enrutar el tráfico a través de ella desde subredes específicas donde se puedan aplicar filtrado, supervisión o inspección adicionales.

|Resource|Vínculo|
|---|---|
|*Tutorial: Restricción del acceso de la red a los recursos de PaaS mediante puntos de conexión de servicio de red virtual mediante Azure Portal.* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

### <a name="route-tables"></a>Tablas de ruta

Las tablas de rutas proporcionan un mecanismo configurado por el administrador para controlar el tráfico dentro de Azure. Se pueden usar para reenviar el tráfico a través de una instancia de Azure Firewall o una NVA, conectarse directamente a recursos externos o invalidar las rutas del sistema de Azure. Las tablas de rutas también se pueden usar para evitar que las redes obtenidas mediante una puerta de enlace de red virtual estén disponibles para los recursos de una subred; para ello, se debe deshabilitar la propagación de rutas de puerta de enlace de red virtual.

|Resource|Vínculo|
|---|---|
|*Conceptos de enrutamiento: rutas personalizadas* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes)|
|*Tutorial: Enrutamiento del tráfico de red* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](../virtual-network/tutorial-create-route-table-portal.md)|
|

### <a name="border-gateway-protocol-bgp"></a>Protocolo de puerta de enlace de borde (BGP)

BGP se puede usar en las puertas de enlace de red virtual para el intercambio dinámico de información de enrutamiento con redes locales o u otras redes externas. BGP se aplica a una red virtual cuando se configura mediante una puerta de enlace de red virtual de ExpressRoute o por medio del emparejamiento privado de ExpressRoute y cuando se habilita en una instancia de Azure VPN Gateway.

Las personas que intervienen en el diseño o la implementación de redes virtuales y puertas de enlace de red virtual de Azure deben dedicar tiempo a entender las opciones de configuración y comportamiento disponibles para BGP en Azure.

|Resource|Vínculo|
|---|---|
|*Conceptos de enrutamiento: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools)|
|*Requisitos de enrutamiento de ExpressRoute* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](../expressroute/expressroute-routing.md)|
|*Acerca de BGP con Azure VPN Gateway* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](../vpn-gateway/vpn-gateway-bgp-overview.md)|
|*Tutorial: Configuración de una VPN de sitio a sitio por medio del emparejamiento de Microsoft de ExpressRoute* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](../expressroute/site-to-site-vpn-over-microsoft-peering.md)|
|

## <a name="next-hop-types"></a>Tipos de próximo salto

### <a name="virtual-network"></a>Virtual Network

Las rutas con un próximo salto de Virtual Network se agregan automáticamente como rutas del sistema, pero también se pueden agregar a rutas definidas por el usuario para dirigir el tráfico de vuelta a la red virtual en aquellos casos en los que se haya invalidado la ruta del sistema.

### <a name="vnet-peering"></a>Emparejamiento de VNET

El emparejamiento de VNet permite la comunicación entre dos redes virtuales distintas. La configuración del emparejamiento de VNet debe estar habilitada en cada red virtual, pero no es necesario que las redes virtuales estén en la misma región o suscripción ni que estén asociadas al mismo inquilino de Azure Active Directory (Azure AD).

Al configurar el emparejamiento de VNet, es fundamental que las personas que intervienen en su diseño o implementación conozcan los cuatro parámetros de configuración asociados y cómo se aplican a cada red:

1. **Permitir acceso a red virtual:** seleccione **Habilitado** (valor predeterminado) para habilitar la comunicación entre las dos redes virtuales. Al habilitar la comunicación entre las redes virtuales, permite que los recursos conectados a cualquier red virtual se comuniquen entre sí con el mismo ancho de banda y latencia que si estuvieran conectados a la misma red virtual.
2. **Permitir tráfico reenviado:** active esta casilla para permitir que el tráfico *reenviado* por una red virtual (el que no se ha originado en la red virtual) fluya a esta red virtual mediante un emparejamiento. Esta configuración es fundamental para implementar una topología de red de tipo hub-and-spoke.
3. **Permitir tránsito de puerta de enlace:** active esta casilla para permitir que la red virtual emparejada pueda usar la puerta de enlace de red virtual asociada a esta red virtual. *Permitir tránsito de puerta de enlace* está habilitada en la red virtual con el recurso de puerta de enlace de red virtual, pero solo se aplica si *Usar puertas de enlace remotas* está habilitada en la otra red virtual.
4. **Usar puertas de enlace remotas:** active esta casilla para permitir que el tráfico procedente de esta red virtual fluya por una puerta de enlace de red virtual asociada a la red virtual con la que se realiza el emparejamiento. *Usar puertas de enlace remotas* está habilitada en la red virtual sin una puerta de enlace de red virtual y solo se aplica si la opción *Permitir tránsito de puerta de enlace* está habilitada en la otra red virtual.

|Resource|Vínculo|
|---|---|
| Conceptos: emparejamiento de red virtual                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md) |
| Crear, cambiar o eliminar un emparejamiento de red virtual | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

Las puertas de enlace de red virtual proporcionan un mecanismo para integrar las redes virtuales con redes externas, como entornos locales, entornos de asociados y otras implementaciones de nube. Los dos tipos de puerta de enlace de red virtual son ExpressRoute y VPN.

#### <a name="expressroute-gateway"></a>Puerta de enlace de ExpressRoute

Las puertas de enlace de ExpressRoute proporcionan un punto de salida de la red virtual a un entorno local y se deben implementar para cumplir los requisitos de seguridad, disponibilidad, económicos y de rendimiento. Las puertas de enlace de ExpressRoute proporcionan un ancho de banda de red definido y generan costos tras la implementación. Las redes virtuales solo pueden tener una puerta de enlace de ExpressRoute, pero pueden estar conectadas a varios circuitos ExpressRoute y las pueden aprovechar varias redes virtuales mediante el emparejamiento de VNet, lo que permite compartir el ancho de banda y la conectividad. Al configurar el enrutamiento entre entornos locales y redes virtuales mediante puertas de enlace de ExpressRoute, se debe tener cuidado a fin de garantizar la conectividad de un extremo a otro mediante puntos de salida de red conocidos y controlados. Las entidades de la Commonwealth que usan la puerta de enlace de ExpressRoute a través del emparejamiento privado de ExpressRoute también deben implementar aplicaciones virtuales de red (NVA) para establecer la conectividad de VPN con el entorno local de cara al cumplimiento de la guía del consumidor de ACSC.

Es importante tener en cuenta que las puertas de enlace de ExpressRoute tienen restricciones sobre los intervalos de direcciones, las comunidades y otros elementos de configuración específicos intercambiados a través de BGP.

| Resource  | Vínculo  |
|---|---|
| Introducción a la puerta de enlace de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md) |
| Configuración de una puerta de enlace de red virtual para ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway proporciona un punto de red de salida desde la red virtual hasta una red externa para proteger la conectividad de sitio a sitio. Las puertas de enlace de VPN proporcionan un ancho de banda de red definido y generan costos de uso tras la implementación. Las entidades de la Commonwealth que utilizan VPN Gateway deberían asegurarse de que se configura de acuerdo con la guía para el consumidor de ACSC. Las redes virtuales solo pueden tener una instancia de VPN Gateway, pero se pueden configurar con varios túneles y varias redes virtuales pueden aprovecharlas mediante el emparejamiento de VNet, lo que permite que compartan el ancho de banda y la conectividad. Las puertas de enlace de VPN se pueden establecer a través de Internet o de ExpressRoute por medio del emparejamiento de Microsoft.

| Resource  | Vínculo |
| --- | --- |
| Introducción a VPN Gateway| [https://docs.microsoft.com/azure/vpn-gateway](../vpn-gateway/index.yml)|
| Planeamiento y diseño de VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
| Azure VPN Gateway en Azure Australia | [Azure VPN Gateway en Azure Australia](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Próximo salto de aplicación virtual

El próximo salto de aplicación virtual ofrece la posibilidad de procesar el tráfico de red fuera de la topología de red y enrutamiento de Azure que se aplica a las redes virtuales. Las aplicaciones virtuales pueden aplicar reglas de seguridad, traducir direcciones, establecer redes VPN, enviar tráfico mediante proxy y otras diversas funcionalidades. El próximo salto de aplicación virtual se aplica mediante los UDR de una tabla de rutas y se puede usar para dirigir el tráfico a Azure Firewall, NVA individuales o Azure Load Balancer y proporcionar disponibilidad en varias NVA. Para usar una aplicación virtual para el enrutamiento, las interfaces de red asociadas deben estar habilitadas para el reenvío IP.

| Resource  | Vínculo |
| --- | ---|
| Conceptos de enrutamiento: rutas personalizadas | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes) |
| Habilitación o deshabilitación del reenvío IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](../virtual-network/virtual-network-network-interface.md#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Próximo salto de VirtualNetworkServiceEndpoint

Las rutas con un tipo de próximo salto de VirtualNetworkServiceEndpoint solo se agregan cuando se configura un punto de conexión de servicio en una subred; no se pueden configurar manualmente mediante tablas de rutas.

### <a name="next-hop-of-internet"></a>Próximo salto de Internet

El próximo salto de Internet se usa para acceder a los recursos que usan una dirección IP pública, lo que incluye Internet, así como los servicios PaaS y de Azure en las regiones de Azure. No requiere una ruta predeterminada (0.0.0.0/0) que abarque todas las redes, pero se puede usar para habilitar rutas de acceso de enrutamiento a servicios públicos específicos. Se debe usar para agregar rutas a servicios autorizados y funcionalidades necesarias para el sistema, como las direcciones de administración de Microsoft.

Algunos de los servicios que se pueden agregar con el próximo salto de Internet son:

1. Servicios de administración de claves para la activación de Windows
2. Administración de App Service Environment

|Resource|Vínculo|
|---|---|
| Conexiones salientes en Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](../load-balancer/load-balancer-outbound-connections.md) |
| Uso de rutas personalizadas de Azure para habilitar la activación de KMS | [https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation) |
| Bloqueo de una instancia de App Service Environment  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](../app-service/environment/firewall-integration.md) |
|

### <a name="next-hop-of-none"></a>Próximo salto de ninguno

El próximo salto de ninguno se puede usar para impedir la comunicación con una red específica. A diferencia de NSG, que controla si se permite o impide que el tráfico pase por una ruta de acceso de red disponible, el uso de un próximo salto de ninguno quita completamente la ruta de acceso de red. Hay que tener cuidado al crear rutas con un próximo salto de ninguno, en especial cuando se aplica a una ruta predeterminada de 0.0.0.0/0, ya que puede haber consecuencias indeseadas y convertir la solución de problemas del sistema en un proceso complejo y lento.

## <a name="security"></a>Seguridad

La implementación de los controles de segmentación y segregación de la red en las funcionalidades IaaS y PaaS se logra mediante la protección de las funcionalidades en sí y la implementación de rutas de comunicación controladas desde los sistemas que se comunicarán con ellas.

El diseño y la creación de soluciones en Azure es un proceso de creación de una arquitectura lógica para comprender, controlar y supervisar los recursos de red allí donde Azure esté presente. Esta arquitectura lógica es software que se define dentro de la plataforma Azure y ocupa el lugar de una topología de red física que se implementa en entornos de red tradicionales.

La arquitectura lógica que se crea debe proporcionar la funcionalidad necesaria que facilite su uso, pero también debe proporcionar la visibilidad y el control necesarios de cara a la seguridad y la integridad.

La obtención de este resultado se basa en la implementación de las herramientas de segregación y segmentación de la red necesarias, pero también en la protección y la aplicación de la topología de red y la implementación de estas herramientas.

### <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSG)

Los grupos de seguridad de red se usan para especificar el tráfico entrante y saliente permitido en una subred o una interfaz de red específica. Al configurar los grupos de seguridad de red, las entidades de la Commonwealth deben usar un enfoque de listas de aprobaciones en las que se configuran reglas para permitir el tráfico necesario, con una regla predeterminada configurada para denegar todo el tráfico que no coincida con una declaración de permiso específica. Al planear y configurar los NSG, debe tener cuidado para asegurarse de que todo el tráfico entrante y saliente necesario se capture correctamente. Esto incluye identificar y comprender todos los intervalos de direcciones IP privadas que se utilizan en las redes virtuales y el entorno local, y en servicios de Microsoft específicos, como los requisitos de administración de Azure Load Balancer y PaaS. Las personas que intervienen en el diseño y la implementación de NSG también deben comprender el uso de etiquetas de servicio y grupos de seguridad de aplicaciones para crear reglas de seguridad específicas detalladas de la aplicación y del servicio.

Es importante advertir que la configuración predeterminada de un NSG permite el tráfico saliente a todas las direcciones de la red virtual y a todas las direcciones IP públicas.

|Resource|Vínculo|
|---|---|
|Introducción a la seguridad de red | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|Crear, modificar o eliminar un grupo de seguridad de red | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall puede usarse para crear una topología de red de tipo hub-and-spoke y aplicar controles de seguridad de red centralizados. También se puede usar para cumplir los requisitos necesarios del ISM con respecto al tráfico de salida mediante la implementación de un enfoque de lista de permitidos donde solo se autoricen las direcciones IP, los protocolos, los puertos y los FQDN necesarios para la funcionalidad del sistema. Las entidades de la Commonwealth deben adoptar un enfoque basado en el riesgo para determinar si las funcionalidades de seguridad proporcionadas por Azure Firewall son suficientes para sus necesidades. En escenarios en los que se necesitan funcionalidades de seguridad adicionales a las proporcionadas por Azure Firewall, las entidades de la Commonwealth deben considerar la implementación de NVA.

|Resource|Vínculo|
|---|---|
|*Documentación sobre Azure Firewall* | [https://docs.microsoft.com/azure/firewall](../firewall/index.yml)|
|*Tutorial: Implementación y configuración de Azure Firewall en una red híbrida con Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](../firewall/tutorial-hybrid-ps.md)|
|

### <a name="network-virtual-appliances-nvas"></a>Aplicaciones virtuales de red (NVA)

Las NVA se pueden usar para crear una topología de red de tipo hub-and-spoke, proporcionar funcionalidades de red mejoradas o complementarias, o bien se pueden emplear como alternativa a los mecanismos de red de Azure por su familiaridad y compatibilidad continuada y la administración con servicios de red locales. Las NVA se pueden implementar para satisfacer requisitos de seguridad específicos, por ejemplo, escenarios en los que haya una necesidad de conocer la identidad asociada al tráfico de red, el descifrado HTTPS, la inspección de contenido, el filtrado u otras funcionalidades de seguridad. Las NVA se deben implementar en una configuración de alta disponibilidad y las personas que intervienen en el diseño o la implementación de NVA deben consultar la documentación del proveedor correspondiente para obtener instrucciones sobre la implementación en Azure.

|Resource|Vínculo|
|---|---|
|*Implementación de aplicaciones virtuales de red de alta disponibilidad* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Directivas de punto de conexión de servicio (versión preliminar)

Configure directivas de punto de conexión de servicio basadas en la disponibilidad del servicio y en una evaluación de riesgos de seguridad de la probabilidad y el efecto de la filtración de datos. Las directivas de punto de conexión de servicio se deben tener en cuenta en Azure Storage y se deben administrar según el caso en otros servicios en función del perfil de riesgo asociado.

| Resource | Vínculo  |
| --- | --- |
| *Directivas de punto de conexión de servicio (versión preliminar)* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](../virtual-network/virtual-network-service-endpoint-policies-overview.md) |
| *Creación, cambio o eliminación de la directiva de punto de conexión de servicio mediante Azure Portal* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](../virtual-network/virtual-network-service-endpoint-policies-portal.md)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy es un componente clave para aplicar y mantener la integridad de la arquitectura lógica del entorno de Azure. Hay una serie de servicios y rutas de acceso del tráfico de salida disponibles mediante los servicios de Azure. Es esencial que las entidades de la Commonwealth conozcan los recursos que existen en su entorno y los puntos de salida de red disponibles. Para asegurarse de que no se crean puntos de salida de red no autorizados en el entorno de Azure, las entidades de la Commonwealth deben usar Azure Policy para controlar los tipos de recursos que se pueden implementar y la configuración de esos recursos. Entre los ejemplos prácticos se incluye la restricción de recursos a solo los autorizados y aprobados para su uso y la necesidad de agregar NSG a las subredes.

|Resource | Vínculo|
|---|---|
|*Introducción a Azure Policy* | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|*Directiva de ejemplo de tipos de recursos permitidos* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)|
|*Directiva de ejemplo para forzar los NSG en una subred*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="paas-egress-capabilities"></a>Funcionalidades de salida de PaaS

Las funcionalidades de PaaS proporcionan oportunidades para mejorar la funcionalidad y simplificar la administración, pero presentan complejidades a la hora de abordar los requisitos de segmentación y segregación de la red. Normalmente se configuran con direcciones IP públicas y son accesibles desde Internet.  Si va a usar funcionalidades de PaaS en sus sistemas y soluciones, debe tener cuidado para identificar los flujos de comunicación entre los componentes y crear reglas de seguridad de red para permitir solo esa comunicación. Como parte de un enfoque de seguridad basado en la defensa en profundidad, las funcionalidades de PaaS deben configurarse con cifrado, autenticación y controles de acceso y permisos adecuados.  

### <a name="public-ip-for-paas"></a>IP pública para PaaS

Las direcciones IP públicas para las funcionalidades de PaaS se asignan en función de la región en la que se hospeda o implementa el servicio. Si va a compilar las reglas de seguridad de red y la topología de enrutamiento adecuadas para la segmentación y la segregación de la red que abarcan redes virtuales de Azure, PaaS y ExpressRoute y la conectividad a Internet, es necesario conocer las regiones y la asignación de direcciones IP públicas. Azure asigna direcciones IP de un grupo asignado a cada región de Azure. Microsoft permite que las direcciones usadas en cada región estén disponibles para su descarga, que se actualiza de forma periódica y controlada. Los servicios que están disponibles en cada región también cambian con frecuencia a medida que se publican nuevos servicios o los servicios se implementan más ampliamente. Las entidades de la Commonwealth deben revisar estos materiales con regularidad y pueden utilizar la automatización para mantener los sistemas si es necesario. Para obtener las direcciones IP específicas de algunos servicios hospedados en cada región, puede ponerse en contacto con el servicio de soporte técnico de Microsoft.

| Resource | Vínculo |
| --- | --- |
| *Intervalos de direcciones IP del centro de datos de Microsoft Azure*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Servicios de Azure por región*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Direcciones IP de entrada y salida en Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](../app-service/overview-inbound-outbound-ips.md)
|

## <a name="next-steps"></a>Pasos siguientes

Compare la arquitectura y el diseño generales con los [planos protegidos publicados de aplicaciones web IaaS y PaaS](https://aka.ms/au-protected).

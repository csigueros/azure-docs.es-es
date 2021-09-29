---
title: Seguridad de red para Azure Service Bus
description: En este artículo se describen las características de seguridad de red, como las etiquetas de servicio, las reglas de firewall de IP, los puntos de conexión de servicio y los puntos de conexión privados.
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: db09ca367d228100eb344c63cca31047fd5e3624
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575960"
---
# <a name="network-security-for-azure-service-bus"></a>Seguridad de red para Azure Service Bus 
En este artículo se describe cómo usar las siguientes características de seguridad con Azure Service Bus: 

- Etiquetas de servicio
- Reglas de firewall de IP
- Puntos de conexión de servicio de red
- Puntos de conexión privados


## <a name="service-tags"></a>Etiquetas de servicio
Una etiqueta de servicio representa un grupo de prefijos de direcciones IP de un servicio de Azure determinado. Microsoft administra los prefijos de direcciones que la etiqueta de servicio incluye y actualiza automáticamente dicha etiqueta a medida que las direcciones cambian, lo que minimiza la complejidad de las actualizaciones frecuentes en las reglas de seguridad de red. Para más información sobre las etiquetas de servicio, consulte [Introducción a las etiquetas de servicio](../virtual-network/service-tags-overview.md).

Puede usar etiquetas de servicio para definir controles de acceso a la red en [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md#security-rules) o [Azure Firewall](../firewall/service-tags.md). Utilice etiquetas de servicio en lugar de direcciones IP específicas al crear reglas de seguridad. Al especificar el nombre de la etiqueta de servicio (por ejemplo, **ServiceBus**) en el campo de *origen* o *destino* apropiado de una regla, puede permitir o denegar el tráfico para el servicio correspondiente.

| Etiqueta de servicio | Propósito | ¿Se puede usar para tráfico entrante o saliente? | ¿Puede ser regional? | ¿Se puede usar con Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Service Bus** | Tráfico de Azure Service Bus que usa el nivel de servicio Premium. | Salida | Sí | Sí |


> [!NOTE]
> Solo puede usar etiquetas de servicio para los espacios de nombres **Premium**. Si usa un espacio de nombres **estándar**, use la dirección IP que aparece al ejecutar el siguiente comando: `nslookup <host name for the namespace>`. Por ejemplo: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>Firewall de dirección IP 
De forma predeterminada, los espacios de nombres de Service Bus son accesibles desde Internet, siempre que la solicitud venga con una autenticación y una autorización válidas. Con el firewall de IP, puede restringirlo aún más a solo un conjunto de direcciones o intervalos de direcciones IPv4 en notación [CIDR (Enrutamiento de interdominios sin clases)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esta característica es útil en escenarios en los que Azure Service Bus debe ser accesible únicamente desde ciertos sitios conocidos. Las reglas de firewall permiten configurar reglas para aceptar el tráfico procedente de direcciones IPv4 concretas. Por ejemplo, si usa Service Bus con [Azure ExpressRoute][express-route], puede crear una **regla de firewall** para permitir el tráfico procedente únicamente de las direcciones IP de la infraestructura local o de las direcciones de una puerta de enlace de NAT corporativa. 

Las reglas de firewall de IP se aplican en el nivel de espacio de nombres de Service Bus. Por lo tanto, las reglas se aplican a todas las conexiones de clientes que usan cualquier protocolo admitido. Cualquier intento de conexión desde una dirección IP que no coincida con una regla IP admitida en el espacio de nombres de Service Bus se rechaza como no autorizado. La respuesta no menciona la regla IP. Las reglas de filtro IP se aplican en orden y la primera regla que coincida con la dirección IP determina la acción de aceptar o rechazar.

Para más información, consulte [Configuración de reglas de firewall de IP para un espacio de nombres de Service Bus](service-bus-ip-filtering.md).

## <a name="network-service-endpoints"></a>Puntos de conexión de servicio de red
La integración de Service Bus con los [puntos de conexión de servicio de una red virtual (VNet)](service-bus-service-endpoints.md) permite el acceso seguro a las funcionalidades de mensajería de cargas de trabajo como las de máquinas virtuales que están enlazadas a redes virtuales, con una ruta de acceso del tráfico de red que está protegida en ambos extremos.

Una vez realizada la configuración para enlazarse con al menos un punto de conexión de servicio de subred de red virtual, el espacio de nombres respectivo de Service Bus solo aceptará ya el tráfico procedente de redes virtuales autorizadas. Desde la perspectiva de la red virtual, el enlace de un espacio de nombres de Service Bus a un punto de conexión de servicio configura un túnel de redes aislado desde la subred de la red virtual al servicio de mensajería.

El resultado es una relación privada y aislada entre las cargas de trabajo enlazadas a la subred y el espacio de nombres respectivo de Service Bus, a pesar de que la dirección de red que se puede observar en el punto de conexión de servicio de mensajería esté en un intervalo IP público.

> [!IMPORTANT]
> Solo se admiten redes virtuales en espacios de nombres de Service Bus del [nivel Premium](service-bus-premium-messaging.md).
> 
> Al utilizar los puntos de conexión de servicio de la red virtual con Service Bus, no debería habilitar estos puntos de conexión en aplicaciones que combinan espacios de nombres de Service Bus de los niveles Estándar y Premium. Dado que el nivel Estándar no admite redes virtuales, el punto de conexión está restringido solo a los espacios de nombres de nivel Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Escenarios de seguridad avanzados que habilita la integración de VNet 

En el caso de soluciones que requieren una estricta seguridad compartimentalizada y en las que las subredes de redes virtuales proporcionan la segmentación entre los servicios compartimentalizados, estas siguen necesitando, por lo general, rutas de comunicación entre los servicios que residen en esos compartimientos.

Cualquier ruta IP inmediata entre compartimientos, incluidos aquellos que transportan HTTPS a través de TCP/IP, tienen el riesgo de sufrir ataques por puntos vulnerables en la capa de red o en capas superiores. Los servicios de mensajería proporcionan rutas de comunicación completamente aisladas, donde los mensajes incluso se escriben en discos cuando se trasladan entre entidades. Las cargas de trabajo de dos redes virtuales distintas enlazadas a la misma instancia de Service Bus pueden comunicarse de forma eficaz y confiable mediante mensajes, al tiempo que se preserva la integridad de los respectivos límites de aislamiento de red.
 
Esto significa que sus soluciones confidenciales en la nube no solo obtienen acceso a las funcionalidades de mensajería asíncrona líderes del sector en fiabilidad y escalabilidad, sino que también pueden usar ahora la mensajería para crear rutas de comunicación entre compartimientos seguros de la solución que son intrínsecamente más seguros de lo que se puede lograr con cualquier otro modo de comunicación entre iguales, incluido HTTPS y los protocolos de socket protegidos por TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Enlace de Service Bus a Virtual Networks

Las *reglas de red virtual* son una característica de firewall que controla si el servidor de Azure Service Bus acepta las conexiones de una subred determinada de una red virtual.

Enlazar un espacio de nombres de Service Bus a una red virtual es un proceso de dos pasos. Primero debe crear un **punto de conexión de servicio de red virtual** en una subred de red virtual y habilitarlo para **Microsoft.ServiceBus**, tal como se explicó en la [introducción a los puntos de conexión de servicio](service-bus-service-endpoints.md). Una vez que ha agregado el punto de conexión de servicio, enlácelo con el espacio de nombres de Service Bus con una **regla de red virtual**.

La regla de red virtual es una asociación del espacio de nombres de Service Bus con una subred de red virtual. Mientras exista la regla, se les concederá acceso a todas las cargas de trabajo enlazadas a la subred al espacio de nombres de Service Bus. Service Bus no establece nunca por sí mismo conexiones de salida, no necesita obtener acceso y, por tanto, nunca se le concede acceso a la subred habilitando esta regla.

Para más información, consulte [Configuración de puntos de conexión de servicio de red virtual para un espacio de nombres de Service Bus](service-bus-service-endpoints.md).

## <a name="private-endpoints"></a>Puntos de conexión privados

El servicio Azure Private Link le permite acceder a los servicios de Azure (por ejemplo, Azure Service Bus, Azure Storage y Azure Cosmos DB) y a los servicios de asociados o clientes hospedados por Azure mediante un **punto de conexión privado** de la red virtual.

Un punto de conexión privado es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. El punto de conexión privado usa una dirección IP privada de la red virtual para incorporar el servicio de manera eficaz a su red virtual. Todo el tráfico dirigido al servicio se puede enrutar mediante el punto de conexión privado, por lo que no se necesita ninguna puerta de enlace, dispositivos NAT, conexiones de ExpressRoute o VPN ni direcciones IP públicas. El tráfico entre la red virtual y el servicio atraviesa la red troncal de Microsoft, eliminando la exposición a la red pública de Internet. Puede conectarse a una instancia de un recurso de Azure, lo que le otorga el nivel más alto de granularidad en el control de acceso.

Para más información, consulte [¿Qué es Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Esta característica es compatible con el nivel **premium** de Azure Service Bus. Para más información sobre el nivel premium, consulte el artículo [Niveles de mensajería Premium y Estándar de Service Bus](service-bus-premium-messaging.md).


Para más información, consulte [Configuración de puntos de conexión privados para un espacio de nombres de Service Bus](private-link-service.md).


## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Configuración del firewall de IP para un espacio de nombres de Service Bus](service-bus-ip-filtering.md)
- [Configuración de puntos de conexión de servicio de red virtual para un espacio de nombres de Service Bus](service-bus-service-endpoints.md)
- [Configuración de puntos de conexión privados para un espacio de nombres de Service Bus](private-link-service.md)
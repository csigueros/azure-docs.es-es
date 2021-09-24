---
title: Azure API Management con una red virtual de Azure
description: Obtenga información sobre los escenarios y requisitos para conectar la instancia de API Management a una red virtual de Azure.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 9d021eb435eb74612f7716fb6d790e6878b9d9d5
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544393"
---
# <a name="use-a-virtual-network-with-azure-api-management"></a>Uso de una red virtual con Azure API Management

Con Azure Virtual Network (redes virtuales), puede colocar los recursos de Azure en una red distinta de Internet que se pueda enrutar y a la que controle el acceso. Después, puede conectar estas redes a sus redes locales mediante diversas tecnologías de VPN. Para más información sobre las redes virtuales de Azure, empiece con la información que se muestra en [Información general sobre Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

En este artículo se explican las opciones de conectividad de red virtual, los requisitos y los aspectos a tener en cuenta en relación con la instancia de API Management. Puede usar Azure Portal, la CLI de Azure, plantillas de Azure Resource Manager u otras herramientas para la implementación. Puede controlar el tráfico entrante y saliente en la subred en la que se implementa API Management mediante [grupos de seguridad de red][NetworkSecurityGroups].

Para conocer los pasos detallados de implementación y la configuración de red, consulte:

* [Conexión a una red virtual externa mediante Azure API Management](./api-management-using-with-vnet.md).
* [Conexión a una red virtual interna mediante Azure API Management](./api-management-using-with-internal-vnet.md).

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="access-options"></a>Opciones de acceso

De forma predeterminada, una instancia de API Management debe ser accesible desde Internet. Mediante una red virtual, puede configurar el portal para desarrolladores, la puerta de enlace de API y otros puntos de conexión de API Management para que sean accesibles desde Internet (modo externo) o solo dentro de la red virtual (modo interno). 

* **Externo**: los puntos de conexión de API Management son accesibles públicamente desde Internet con un equilibrador de carga externo. La puerta de enlace puede acceder a recursos dentro de la red virtual.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-external.png" alt-text="Conexión a una red virtual externa":::

    Use API Management en modo externo para acceder a los servicios back-end implementados en la red virtual.

* **Interno**: los puntos de conexión de API Management solo son accesibles desde la red virtual con un equilibrador de carga interno. La puerta de enlace puede acceder a recursos dentro de la red virtual.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-internal.png" alt-text="Conexión a una red virtual interna":::

    Use API Management en modo interno para:

    * Conseguir que las API hospedadas en el centro de datos privado sean accesibles para terceros de forma segura desde este centro mediante conexiones de VPN de Azure o Azure ExpressRoute.
    * Puede permitir escenarios de nube híbrida exponiendo las API basadas en la nube y las API locales a través de una puerta de enlace común.
    * Administrar las API hospedadas en diversas ubicaciones geográficas, mediante un único punto de conexión de puerta de enlace.


## <a name="network-resource-requirements"></a>Requisitos de recursos de la red.

Los siguientes son los requisitos de recursos de la red virtual de API Management. Algunos requisitos varían en función de la versión (`stv2` o `stv1`) de la [plataforma de proceso](compute-infrastructure.md) que hospeda la instancia de API Management.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Se requiere una red virtual de Azure Resource Manager.
* Debe proporcionar una [dirección IPv4 pública](../virtual-network/public-ip-addresses.md#standard) de SKU estándar además de especificar una red virtual y una subred.
* La subred usada para conectarse a la instancia de API Management puede contener otros tipos de recursos de Azure.
* El servicio API Management, la red virtual y la subred, y el recurso de dirección IP pública deben estar en la misma región y suscripción.
* En el caso de las implementaciones de API Management en varias regiones, configure los recursos de la red virtual por separado para cada ubicación.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Se requiere una red virtual de Azure Resource Manager.
* La subred utilizada para conectarse a la instancia de API Management debe estar dedicada a API Management. No puede contener otros tipos de recursos de Azure.
* Los recursos del servicio API Management, de la red virtual y de la subred deben estar en la misma región y suscripción.
* En el caso de las implementaciones de API Management en varias regiones, configure los recursos de la red virtual por separado para cada ubicación.

---

## <a name="subnet-size"></a>Tamaño de la subred

El tamaño mínimo de la subred en la que se puede implementar API Management es /29, lo que proporciona tres direcciones IP utilizables. Cada unidad de escalado adicional de API Management requiere dos direcciones IP más. El requisito de tamaño mínimo se basa en las siguientes consideraciones:

* Azure reserva algunas direcciones IP dentro de cada subred que no se pueden usar. La primera y la última dirección IP de las subredes están reservadas para la conformidad con el protocolo. Se usan tres direcciones más para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

* Además de las direcciones IP que usa la infraestructura de Azure VNET, cada instancia de API Management de la subred usa:
    * dos direcciones IP por unidad de SKU Premium o 
    * una dirección IP adicional para la SKU de desarrollador. 

* Cada instancia reserva una dirección IP adicional para el equilibrador de carga externo. Cuando se implementa una [red virtual interna](./api-management-using-with-internal-vnet.md), la instancia requiere una dirección IP adicional para el equilibrador de carga interno.

## <a name="routing"></a>Enrutamiento

Consulte la guía de enrutamiento al implementar la instancia de API Management en una [red virtual externa](./api-management-using-with-vnet.md#routing) o una [interna](./api-management-using-with-internal-vnet.md#routing).

Obtenga más información sobre las [direcciones IP de API Management](api-management-howto-ip-addresses.md).

## <a name="dns"></a>DNS

En el modo externo, la red virtual habilita la [resolución de nombres proporcionada por Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) para los puntos de conexión de API Management y otros recursos de Azure. No proporciona resolución de nombres para los recursos locales. 

En el modo interno, debe proporcionar su propia solución DNS para garantizar la resolución de nombres de los puntos de conexión de API Management y otros recursos de Azure necesarios. Es recomendable que configure una [zona DNS privada](../dns/private-dns-overview.md) de Azure.

Para más información, consulte: 
* [Resolución de nombres de recursos en redes virtuales de Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)  
* [Creación de una zona DNS privada de Azure](../dns/private-dns-getstarted-portal.md)

> [!IMPORTANT]
> Si planea utilizar una solución DNS personalizada para la red virtual, configúrela **antes** de implementar en ella un servicio API Management. En caso contrario, deberá actualizar el servicio API Management cada vez que cambie los servidores DNS mediante la ejecución de la [operación Aplicar configuración de red](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates).

## <a name="limitations"></a>Limitaciones

Algunas limitaciones varían en función de la versión (`stv2` o `stv1`) de la [plataforma de proceso](compute-infrastructure.md) que hospeda la instancia de API Management.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Una subred que contenga instancias de API Management no se puede mover a otras suscripciones.
* Para implementaciones de API Management de varias regiones configuradas en el modo de red virtual interna, los usuarios son responsables de administrar el equilibrio de carga a través de varias regiones.
* Para importar una API a API Management desde una [especificación de OpenAPI](import-and-publish.md), la dirección URL de la especificación debe hospedarse en una dirección de Internet con acceso público.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Una subred que contenga instancias de API Management no se puede mover a otras suscripciones.
* Para implementaciones de API Management de varias regiones configuradas en el modo de red virtual interna, los usuarios son responsables de administrar el equilibrio de carga a través de varias regiones.
* Para importar una API a API Management desde una [especificación de OpenAPI](import-and-publish.md), la dirección URL de la especificación debe hospedarse en una dirección de Internet con acceso público.
* Debido a las limitaciones de la plataforma, la conectividad de un recurso en una VNET emparejada globalmente a otra región y a un servicio API Management en modo interno no funciona. Para más información, consulte la [documentación sobre redes virtuales](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).

---

## <a name="next-steps"></a>Pasos siguientes

Más información sobre:

* [Conexión de una red virtual al back-end mediante VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Conexión de redes virtuales a partir de diferentes modelos de implementación con PowerShell](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Preguntas más frecuentes acerca de Azure Virtual Network](../virtual-network/virtual-networks-faq.md)

Conexión a una red virtual:
* [Conexión a una red virtual externa mediante Azure API Management](./api-management-using-with-vnet.md).
* [Conexión a una red virtual interna mediante Azure API Management](./api-management-using-with-internal-vnet.md).

Consulte los siguientes temas.

* [Conexión de una red virtual a back-end mediante VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Conexión a una red virtual a partir de diferentes modelos de implementación](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Uso de API Inspector para hacer un seguimiento de las llamadas en Azure API Management](api-management-howto-api-inspector.md)
* [Preguntas más frecuentes acerca de Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Etiquetas de servicio](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/virtual-network-concepts/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/virtual-network-concepts/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

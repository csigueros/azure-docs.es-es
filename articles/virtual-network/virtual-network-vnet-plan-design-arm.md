---
title: Planear redes virtuales de Azure | Microsoft Docs
description: Obtenga información sobre cómo planear redes virtuales según los requisitos de aislamiento, conectividad y ubicación.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: fff105eadfffb8b38ebe5e95b3cfb0c79df0929b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248951"
---
# <a name="plan-virtual-networks"></a>Planear redes virtuales

Crear una red virtual con la cual experimentar es bastante sencillo, pero es probable que, con el tiempo, implemente varias redes virtuales para satisfacer las necesidades de producción que tiene su organización. Con cierto planeamiento, podrá implementar redes virtuales y conectar los recursos que necesita de manera más eficaz. La información de este artículo es más útil si ya está familiarizado con las redes virtuales y tiene cierta experiencia al trabajar con ellas. Si no está familiarizado con las redes virtuales, le recomendamos que lea la [información general sobre Virtual Network](virtual-networks-overview.md).

## <a name="naming"></a>Nomenclatura

Todos los recursos de Azure tienen un nombre. El nombre debe ser único dentro de un ámbito, que puede variar para cada tipo de recurso. Por ejemplo, el nombre de una red virtual debe ser único dentro de un [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), pero puede haber duplicados del nombre dentro de una [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) o [región](https://azure.microsoft.com/regions/#services) de Azure. Definir una convención de nomenclatura que pueda usar de forma consistente al asignar nombres a recursos le será de utilidad al administrar varios recursos de red con el tiempo. Consulte [Naming conventions](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork) (Convenciones de nomenclatura) para obtener sugerencias.

## <a name="regions"></a>Regions

Todos los recursos de Azure se crean en una suscripción y una región de Azure. Un recurso solo se puede crear en una red virtual que exista en la misma región y suscripción de ese recurso. No obstante, puede conectar redes virtuales que ya existan a diferentes suscripciones y regiones. Para obtener más información, consulte[Conectividad](#connectivity). Al decidir en qué regiones implementar los recursos, debe tener en cuenta dónde se encuentran físicamente los consumidores de esos recursos:

- Los consumidores de recursos normalmente quieren obtener la latencia de red más baja para sus recursos. Para determinar las latencias relativas entre una ubicación específica y las regiones de Azure, consulte [View relative latencies](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Ver latencias relativas).
- ¿Debe cumplir con requisitos de residencia, soberanía, cumplimiento o resistencia de datos? Si es así, es fundamental elegir la región que mejor se ajuste a sus requisitos. Para obtener más información, consulte [Zonas geográficas de Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- ¿Necesita configurar la resistencia en Azure Availability Zones dentro de la misma región de Azure para los recursos que está implementando? Puede implementar recursos como máquinas virtuales (VM) en diferentes zonas de disponibilidad dentro de la misma red virtual. Sin embargo, no todas las regiones de Azure admiten zonas de disponibilidad. Para obtener más información sobre las zonas de disponibilidad y las regiones que las admiten, consulte [Zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Suscripciones

Puede implementar tantas redes virtuales como sea necesario en cada suscripción, hasta el [límite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) que se haya establecido. Por ejemplo, algunas organizaciones tienen suscripciones distintas para diferentes departamentos. Para obtener más información y detalles en torno a las suscripciones, consulte [Gobernanza de suscripción](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentación

Puede crear varias redes virtuales por suscripción y por región. Puede crear varias subredes en cada red virtual. Los detalles siguientes le ayudarán a determinar cuántas redes virtuales y subredes necesita:

### <a name="virtual-networks"></a>Redes virtuales

Una red virtual es una parte virtual y aislada de la red pública de Azure. Cada red virtual está dedicada a su suscripción. Qué debe tener en cuenta al decidir si quiere crear una o varias redes virtuales en una suscripción:

- ¿Hay algún requisito de seguridad en la organización para aislar el tráfico en redes virtuales diferentes? Puede elegir conectar la redes virtuales o no. Si conecta las redes virtuales, puede implementar una aplicación virtual de red (como un firewall) para controlar el flujo de tráfico entre las redes virtuales. Para obtener más información, consulte [seguridad](#security) y [conectividad](#connectivity).
- ¿Hay algún requisito en la organización para aislar las redes virtuales en diversas [suscripciones](#subscriptions) o [regiones](#regions)?
- Una [interfaz de red](virtual-network-network-interface.md) permite que una máquina virtual se comunique con otros recursos. Cada interfaz de red puede tener una o varias direcciones IP privadas asignadas. ¿Cuántas interfaces de red y [direcciones IP privadas](./ip-services/private-ip-addresses.md) se necesitan en una red virtual? Hay [límites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) en el número de interfaces de red y direcciones IP privadas que se pueden tener en una red virtual.
- ¿Quiere conectar la red virtual a otra red virtual o red local? Puede conectar algunas redes virtuales entre sí o a redes locales, pero no a otras redes. Para obtener más información, consulte[Conectividad](#connectivity). Cada red virtual que se conecta a otra red virtual o local debe tener un espacio de direcciones único. Cada red virtual tiene uno o más intervalos de direcciones públicas o privadas asignados a su espacio de direcciones. Un intervalo de direcciones se especifica en un formato propio del enrutamiento de interdominios sin clases (CIDR), como 10.0.0.0/16. Obtenga más información sobre los [intervalos de direcciones](manage-virtual-network.md#add-or-remove-an-address-range) para redes virtuales.
- ¿Tiene algún requisito de administración de la organización para recursos en diferentes redes virtuales? De ser así, puede separar los recursos en una red virtual a parte para simplificar la [asignación de permisos](#permissions) a los usuarios de la organización, o para asignar diferentes directivas a diferentes redes virtuales.
- Cuando implementa algunos recursos del servicio de Azure en una red virtual, estos se encargan de crear su propia red virtual. Para determinar si un servicio de Azure ha creado su propia red virtual, consulte la información de cada [servicio de Azure que se puede implementar en una red virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Subredes

Una red virtual se puede segmentar en una o más subredes hasta el [límite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) que se haya establecido. Qué debe tener en cuenta al decidir si quiere crear una subred o varias redes virtuales en una suscripción:

- Cada subred debe tener un intervalo de direcciones único, especificado en formato CIDR, en el espacio de direcciones de la red virtual. Este intervalo de direcciones no puede superponerse con otras subredes de la red virtual.
- Si planea implementar algunos recursos del servicio de Azure en una red virtual, seguramente necesiten crear su propia subred, por lo que debe haber suficiente espacio no asignado para que puedan hacerlo. Para determinar si un servicio de Azure ha creado su propia subred, consulte la información de cada [servicio de Azure que se puede implementar en una red virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Por ejemplo, si conecta una red virtual a una red local mediante Azure VPN Gateway, la red virtual debe tener una subred dedicada para la puerta de enlace. Obtenga más información sobre las [subredes de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- De forma predeterminada, Azure enruta el tráfico de red entre todas las subredes de una red virtual. Por ejemplo, puede invalidar el enrutamiento predeterminado de Azure para evitar el enrutamiento de Azure entre subredes, o para enrutar el tráfico entre subredes a través de una aplicación virtual de red. Si necesita que el tráfico entre recursos de la misma red virtual fluya a través de una aplicación virtual de red (NVA), implemente los recursos en diferentes subredes. Obtenga más información en el apartado [Seguridad](#security).
- Puede limitar el acceso a recursos de Azure como, por ejemplo, una cuenta de almacenamiento de Azure o Azure SQL Database, a subredes específicas con un punto de conexión de servicio de red virtual. Además, puede denegar el acceso a los recursos desde Internet. Puede crear varias subredes y habilitar un punto de conexión de servicio para algunas subredes, pero no para otras. Obtenga más información sobre los [puntos de conexión de servicio](virtual-network-service-endpoints-overview.md) y los recursos de Azure puede habilitar.
- Puede asociar un grupo de seguridad de red o ninguno, a cada subred de una red virtual. Puede asociar el mismo grupo de seguridad de red u otro diferente a cada subred. Cada grupo de seguridad de red contiene reglas que permiten o niegan el paso del tráfico hacia y desde los orígenes y destinos. Más información sobre los [grupos de seguridad de red](#traffic-filtering).

## <a name="security"></a>Seguridad

Puede filtrar el tráfico de red hacia y desde los recursos en una red virtual mediante los grupos de seguridad de red y las aplicaciones virtuales de red. Puede controlar la manera en que Azure enruta el tráfico de subredes. Asimismo, también puede limitar en su organización quién puede trabajar con los recursos en redes virtuales.

### <a name="traffic-filtering"></a>Filtrado de tráfico

- Puede filtrar el tráfico de red entre recursos de una red virtual mediante un grupo de seguridad de red, una NVA que filtra el tráfico de red o ambos. Para implementar una NVA como un cortafuegos para filtrar el tráfico de red, consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Al usar una NVA, también crea rutas personalizadas para enrutar el tráfico de las subredes a la NVA. Obtenga más información acerca el [enrutamiento del tráfico](#traffic-routing).
- Un grupo de seguridad de red contiene varias reglas de seguridad predeterminadas que permiten o deniegan el tráfico hacia o desde los recursos. Un grupo de seguridad de red se puede asociar a una interfaz de red, a la subred que se encuentra en ella o a ambas. Siempre que sea posible y para simplificar la administración de reglas de seguridad, le recomendamos que asocie un grupo de seguridad de red a las subredes individuales, en lugar de asociarlo a las interfaces de red individuales de la subred.
- Si debe aplicar reglas de seguridad a varias máquinas virtuales de una subred, puede asociar la interfaz de red en la máquina virtual a uno o varios grupos de seguridad de aplicaciones. Una regla de seguridad puede especificar un grupo de seguridad de aplicaciones en el origen, en el destino o en ambos. A continuación, solo se aplica dicha regla a las interfaces de red que forman parte del grupo de seguridad de aplicaciones. Obtenga más información sobre los [grupos de seguridad de red](./network-security-groups-overview.md) y los [grupos de seguridad de aplicaciones](./network-security-groups-overview.md#application-security-groups).
- Azure crea de forma predeterminada varias reglas de seguridad en cada grupo de seguridad de red. Una regla predeterminada permite que todo el tráfico fluya entre todos los recursos de una red virtual. Para invalidar este comportamiento, use los grupos de seguridad de red, el enrutamiento personalizado para enrutar el tráfico a una NVA o ambas opciones. Le recomendamos que se familiarice con todas las [reglas de seguridad predeterminadas](./network-security-groups-overview.md#default-security-rules) de Azure, y con la forma de aplicar las reglas del grupo de seguridad de red a un recurso.

Puede ver los diseños de ejemplo para implementar una red perimetral (también conocida como DMZ) entre Azure e Internet mediante [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Enrutamiento del tráfico

Azure crea varias rutas predeterminadas para el tráfico saliente desde una subred. Puede invalidar el enrutamiento predeterminado de Azure si crea una tabla de rutas y la asocia a una subred. Los motivos más comunes para invalidar el enrutamiento predeterminado de Azure son:
- Porque quiere que el tráfico entre subredes fluya a través de una NVA. Más información sobre cómo [configurar las tablas de rutas para forzar el tráfico a través de una NVA](tutorial-create-route-table-portal.md).
- Porque quiere forzar todo el tráfico de Internet a través de una NVA o de forma local, a través de Azure VPN Gateway. Forzar el tráfico de Internet de forma local para su inspección y registro a menudo se conoce como "tunelización forzada". Obtenga más información sobre la configuración de la [tunelización forzada](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Si tiene que implementar el enrutamiento personalizado, le recomendamos que se familiarice con el [enrutamiento en Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Conectividad

Puede conectar una red virtual a otras redes virtuales mediante el emparejamiento de redes virtuales, o puede conectarla a una red local mediante Azure VPN Gateway.

### <a name="peering"></a>Emparejamiento

Al usar [el emparejamiento de redes virtuales](virtual-network-peering-overview.md), las redes virtuales pueden estar en las mismas regiones de Azure compatibles o en diferentes regiones. Las redes virtuales pueden estar en las mismas suscripciones de Azure (incluso suscripciones que pertenezcan a diferentes inquilinos de Azure Active Directory), o en otras diferentes. Le recomendamos que se familiarice con los [requisitos y restricciones](virtual-network-manage-peering.md#requirements-and-constraints) de emparejamiento antes de crear un emparejamiento. El ancho de banda entre recursos de redes virtuales emparejadas en la misma región es el mismo que si los recursos estuvieran en la misma red virtual.

### <a name="vpn-gateway"></a>puerta de enlace de VPN

Puede usar Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para conectar una red virtual a la red local mediante un [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), o mediante una conexión dedicada con Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Puede combinar el emparejamiento y una puerta de enlace de VPN para crear [redes de tipo hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), donde las redes virtuales denominadas "radios" (spoke) se conectan a una red virtual denominada "concentrador" (hub) y, a su vez, ese concentrador se conecta a una red local, por ejemplo.

### <a name="name-resolution"></a>Resolución de nombres

Los recursos de una red virtual no pueden resolver los nombres de los recursos de una red virtual emparejada mediante el [DNS integrado](virtual-networks-name-resolution-for-vms-and-role-instances.md) de Azure. Para resolver nombres en una red virtual emparejada, [despliegue su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), o use los [dominios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de Azure DNS. Recuerde que la resolución de nombres entre recursos en una red virtual y en redes locales también requiere que implemente su propio servidor DNS.

## <a name="permissions"></a>Permisos

Azure usa el [control de acceso basado en rol de Azure](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC de Azure) para los recursos. Se asignan permisos a un [ámbito](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) según la siguiente jerarquía: grupo de administración, suscripción, grupo de recursos y recurso individual. Para obtener más información acerca de la jerarquía, consulte [Organización de los recursos](../governance/management-groups/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para trabajar con redes virtuales de Azure y todas sus capacidades relacionadas, como el emparejamiento, los grupos de seguridad de red, los puntos de conexión de servicio y las tablas de rutas, puede asignar miembros de la organización a los roles integrados [Propietario](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor) o [Colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) y, a continuación, asignar uno de esos roles a un ámbito adecuado. Si quiere asignar permisos específicos para un subconjunto de capacidades de red virtual, cree un [rol personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y asigne al rol los permisos específicos necesarios para [redes virtuales](manage-virtual-network.md#permissions), [subredes y puntos de conexión de servicio](virtual-network-manage-subnet.md#permissions), [interfaces de red](virtual-network-network-interface.md#permissions), [emparejamientos](virtual-network-manage-peering.md#permissions), [grupos de seguridad de aplicaciones y red](manage-network-security-group.md#permissions) o [tablas de rutas](manage-route-table.md#permissions).

## <a name="policy"></a>Directiva

Azure Policy le permite crear, asignar y administrar las definiciones de directivas. Las definiciones de directivas aplican distintas reglas en los recursos, para que estos sigan siendo compatibles con los estándares de la organización y los contratos de nivel de servicio. Azure Policy ejecuta una evaluación de los recursos, para detectar los que no son compatibles con las definiciones de directivas que tiene. Por ejemplo, puede definir y aplicar una directiva que permita la creación de redes virtuales en un solo grupo de recursos o región específicos. Otra directiva puede requerir que cada subred tenga un grupo de seguridad de red asociado a ella. Estas directivas se evalúan al crear y actualizar los recursos.

Igualmente, las directivas se aplican según la jerarquía siguiente: grupo de administración, suscripción y grupo de recursos. Obtenga más información sobre [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o acerca de cómo implementar algunas [definiciones de Azure Policy](./policy-reference.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de todas las tareas, configuración y opciones para una [red virtual](manage-virtual-network.md), [subred y punto de conexión de servicio](virtual-network-manage-subnet.md), [interfaz de red](virtual-network-network-interface.md), [emparejamiento](virtual-network-manage-peering.md), [grupo de seguridad de red y aplicaciones](manage-network-security-group.md) o [una tabla de rutas](manage-route-table.md).
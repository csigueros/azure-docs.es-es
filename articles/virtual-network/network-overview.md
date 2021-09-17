---
title: Redes virtuales y máquinas virtuales en Azure
description: Información sobre redes relativa a las máquinas virtuales en Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: allensu
ms.openlocfilehash: 27c4340071e80cdcac806b80c28873e0955cd586
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444714"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Redes virtuales y máquinas virtuales en Azure

Al crear una máquina virtual, hay que crear una [red virtual](../virtual-network/virtual-networks-overview.md) o usar una ya existente. Decida la forma en que pretende acceder a las máquinas virtuales de la red virtual. Es importante [planear antes de crear recursos](../virtual-network/virtual-network-vnet-plan-design-arm.md) y asegurarse de que se conocen los [límites de los recursos de red](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

En la siguiente imagen, las máquinas virtuales se representan como servidores web y servidores de aplicaciones. Cada conjunto de máquinas virtuales se asigna a subredes independientes en la red virtual.

:::image type="content" source="./media/network-overview/load-balancer.png" alt-text="Diagrama de una configuración con varias capas y varias subredes" border="true":::

Se puede crear una red virtual antes de crear una máquina virtual o mientras se crea. 

Para que se pueda establecer comunicación con una máquina virtual, hay que crear estos recursos:

- Interfaces de red
- Direcciones IP
- Red virtual y subredes

Considere igualmente estos recursos opcionales:

- Grupos de seguridad de red
- Equilibradores de carga

## <a name="network-interfaces"></a>Interfaces de red

Una [interfaz de red (NIC)](../virtual-network/virtual-network-network-interface.md) es la interconexión entre una máquina virtual y una red virtual. Una máquina virtual debe tener como mínimo una NIC, y puede tener más de una en función del tamaño de la máquina virtual que se cree. Para obtener información sobre el número de NIC aceptable en cada tamaño de máquina virtual, consulte [Tamaños de las máquinas virtuales](../virtual-machines/sizes.md).

Puede crear una máquina virtual con varias NIC y agregarlas o quitarlas a lo largo del ciclo de vida de una máquina virtual. Cuando hay varias NIC, una máquina virtual se puede conectar a diferentes subredes.

Todas las NIC conectadas a una máquina virtual deben existir en la misma ubicación y suscripción que esta. Cada NIC debe estar conectada a una red virtual que exista en la misma ubicación y suscripción de Azure en la que se encuentre la NIC. Puede cambiar la subred a la que una máquina virtual está conectada después de crearla. La red virtual no se puede cambiar. A cada NIC conectada a una máquina virtual se le asigna una dirección MAC que no cambia hasta que se elimine la máquina virtual.

En esta tabla se enumeran los métodos que se pueden usar para crear una interfaz de red.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](https://portal.azure.com) | Cuando se crea una máquina virtual en Azure Portal, se crea automáticamente una interfaz de red. El portal crea una máquina virtual con una sola NIC. Si desea crear una máquina virtual con más de una, debe usar otro método para hacerlo. |
| [Azure PowerShell](../virtual-machines/windows/multiple-nics.md) | Use [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) con el parámetro **`-PublicIpAddressId`** para proporcionar el identificador de la dirección IP pública que creó anteriormente. |
| [CLI de Azure](../virtual-machines/linux/multiple-nics.md) | Para proporcionar el identificador de la dirección IP pública que creó anteriormente, use [az network nic create](/cli/azure/network/nic) con el parámetro **`--public-ip-address`** . |
| [Plantilla](../virtual-network/template-samples.md) | Para obtener información sobre cómo implementar una interfaz de red usando una plantilla, consulte [Interfaz de red en una red virtual con dirección IP pública](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet). |

## <a name="ip-addresses"></a>Direcciones IP

En Azure, estos tipos de [direcciones IP](../virtual-network/public-ip-addresses.md) se pueden asignar a una NIC:

- **Direcciones IP públicas**: se usan para la comunicación entrante y saliente (sin traducción de direcciones de red, o NAT) con Internet y otros recursos de Azure no conectados a una red virtual. La asignación de una dirección IP pública a una NIC es opcional. Las direcciones IP públicas tienen un cargo nominal y se puede usar un número máximo de ellas por suscripción.
- **Direcciones IP privadas**: se usan para la comunicación dentro de una red virtual, una red local e Internet (con NAT). Una máquina debe tener asignada al menos una dirección IP privada. Para más información acerca de NAT en Azure, lea [Información sobre las conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md).

Se pueden asignar direcciones IP públicas a lo siguiente:

* Máquinas virtuales
* Equilibradores de carga públicos

Se pueden asignar direcciones IP privadas a lo siguiente:

* Máquinas virtuales
* Equilibradores de carga internos

Las direcciones IP se asignan a una máquina virtual mediante una interfaz de red.

Existen dos métodos en los que una dirección IP se asigna a un recurso: dinámico o estático. El método predeterminado con el que Azure asigna direcciones IP es el dinámico. Una dirección IP no se asigna cuando se crea, sino al crear una máquina virtual o al iniciar una máquina virtual detenida. La dirección IP se libera cuando se detiene o se elimina la máquina virtual.

Para asegurarse de que la dirección IP de la máquina virtual no cambia, puede establecer explícitamente el método de asignación en estático. En este caso, se asigna de inmediato una dirección IP. Solo se libera cuando se elimina la máquina virtual o cuando se cambia su método de asignación a dinámico.

En esta tabla se enumeran los métodos que se pueden usar para crear una dirección IP.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Las direcciones IP públicas son dinámicas de forma predeterminada. La dirección IP puede cambiar cuando la máquina virtual se detiene o elimina. Para garantizar que la máquina virtual siempre usa la misma dirección IP pública, cree una dirección IP pública estática. De manera predeterminada, el portal asigna una dirección IP privada dinámica a una NIC al crear una máquina virtual. Una vez creada esta, puede cambiar la dirección IP a estática.|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Se usa [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) con el parámetro **`-AllocationMethod`** como "Dynamic" o "Static". |
| [CLI de Azure](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Se usa [az network public-ip create](/cli/azure/network/public-ip) con el parámetro **`--allocation-method`** como "Dynamic" o "Static". |
| [Plantilla](../virtual-network/template-samples.md) | Para obtener más información sobre cómo implementar una dirección IP pública usando una plantilla, consulte [Interfaz de red en una red virtual con dirección IP pública](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet). |

Después de crear una dirección IP pública, se puede asociar a una máquina virtual. Para ello, es preciso asignarla a una NIC.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="virtual-network-and-subnets"></a>Red virtual y subredes

Una subred es un intervalo de direcciones IP en la red virtual. Una red virtual se puede dividir en varias subredes para facilitar su organización o por motivos de seguridad. Cada una de las NIC de una máquina virtual está conectada a una subred de una red virtual. Las NIC conectadas a subredes (iguales o distintas) dentro de una red virtual pueden comunicarse entre sí sin ninguna configuración adicional.

Al configurar una red virtual, especifique la topología, incluidos los espacios de direcciones y las subredes disponibles. Si la red virtual está conectada a otras redes virtuales o redes locales, seleccione intervalos de direcciones que no se solapen. Las direcciones IP son privadas y no se puede acceder a ellas desde Internet. Azure trata cualquier intervalo de direcciones como parte del espacio de direcciones IP de la red virtual privada. El intervalo de direcciones solo es accesible dentro de la red virtual, dentro de redes virtuales interconectadas y desde la ubicación local.

Si trabaja en una organización en la que otra persona es responsable de las redes internas, póngase en contacto con ella antes de seleccionar el espacio de direcciones. Asegúrese de que no hay solapamientos en el espacio de direcciones e indique el espacio que quiere usar para que no intenten usar el mismo intervalo de direcciones IP.

De forma predeterminada, no hay límites de seguridad entre subredes. Las máquinas virtuales de cada una de estas subredes pueden comunicarse. Si la implementación necesita límites de seguridad, use **grupos de seguridad de red (NSG)** , que controlan el flujo de tráfico que llega y sale tanto de las subredes como de las máquinas virtuales.

En esta tabla se muestran los métodos que se pueden usar para crear una red virtual y subredes.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](../virtual-network/quick-create-portal.md) | Si deja que Azure cree una red virtual cuando cree una máquina virtual, el nombre será una combinación del nombre del grupo de recursos que contiene la red virtual y **`-vnet`** . El espacio de direcciones será 10.0.0.0/24, el nombre de subred requerida será **default** y el intervalo de direcciones de la subred será 10.0.0.0/24. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | Para crear una subred y una red virtual se usan [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) y [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). También se puede usar [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) para agregar una subred a una red virtual existente. |
| [CLI de Azure](../virtual-network/quick-create-cli.md) | La subred y la red virtual se crean al mismo tiempo. Especifique un parámetro **`--subnet-name`** en [az network vnet create](/cli/azure/network/vnet) con el nombre de la subred. |
| [Plantilla](../virtual-network/template-samples.md) | Para obtener más información sobre cómo usar una plantilla para crear una red virtual y subredes, consulte [Red virtual con dos subredes](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/vnet-two-subnets). |

## <a name="network-security-groups"></a>Grupos de seguridad de red

Un [grupo de seguridad de red (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) contiene una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a subredes, NIC, o ambas. Los grupos de seguridad de red se pueden asociar con subredes o con NIC individuales conectadas a una subred. Si un grupo de seguridad de red está asociado a una subred, las reglas de la ACL se aplican a todas las máquinas virtuales de dicha subred. El tráfico que llega a una NIC individual se puede restringir asociando un grupo de seguridad de red directamente a la NIC.

Los grupos de seguridad de red contienen dos tipos de reglas: entrada y salida. La prioridad de una regla debe ser única dentro de cada conjunto. 

Cada regla tiene propiedades relativas a lo siguiente:

* Protocolo
* Intervalos de puertos de origen y destino
* Prefijos de dirección
* Dirección del tráfico
* Prioridad
* Tipo de acceso

Todos los grupos de seguridad de red contienen un conjunto de reglas predeterminadas. Las reglas predeterminadas no se pueden eliminar. Tienen asignada la prioridad más baja y ninguna de las reglas que creemos pueden reemplazarlas.

Al asociar un grupo de seguridad de red a una NIC, las reglas de acceso de red del grupo de seguridad de red se aplican solo a esa NIC. Si se asigna un grupo de seguridad de red a una sola NIC en una máquina virtual con varias NIC, el tráfico de las restantes no se verá afectado. Se pueden asociar distintos grupos de seguridad de red a una NIC (o a una máquina virtual, según el modelo de implementación) y a la subred a la que una NIC o una máquina virtual están enlazadas. La prioridad depende de la dirección del tráfico.

Procure [planear](../virtual-network/virtual-network-vnet-plan-design-arm.md) los grupos de seguridad de red al planear las máquinas virtuales y la red virtual.

En esta tabla se enumeran los métodos que se pueden usar para crear un grupo de seguridad de red.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](../virtual-network/tutorial-filter-network-traffic.md) | Cuando se crea una máquina virtual en Azure Portal, se crea automáticamente un grupo de seguridad de red, que se asocia a la NIC que crea el portal. El nombre de dicho grupo es una combinación del nombre de la máquina virtual y **`-nsg`** . </br> Este grupo de seguridad de red contiene una regla de entrada: </br> Tiene una prioridad de 1000. </br> El servicio está establecido en RDP. </br> El protocolo está establecido en TCP. </br> El puerto está establecido en 3389. </br> La acción está establecida en **Permitir**. </br> Si quiere permitir algún otro tráfico entrante en la máquina virtual, cree otra regla o reglas. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) y especifique la información de las reglas requerida. Use [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) para crear el grupo de seguridad de red. Use [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) para configurar el grupo de seguridad de red para la subred. Use [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para agregar el grupo de seguridad de red a la red virtual. |
| [CLI de Azure](../virtual-network/tutorial-filter-network-traffic-cli.md) | Use [az network nsg create](/cli/azure/network/nsg) para crear inicialmente el grupo de seguridad de red. Use [az network nsg rule create](/cli/azure/network/nsg/rule) para agregar reglas al grupo de seguridad de red. Use [az network vnet subnet update](/cli/azure/network/vnet/subnet) para agregar el grupo de seguridad de red a la subred. |
| [Plantilla](../virtual-network/template-samples.md) | Use [Create a Network Security Group](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/security-group-create) (Creación de un grupo de seguridad de red) como guía para la implementación de un grupo de seguridad de red mediante una plantilla. |

## <a name="load-balancers"></a>Equilibradores de carga

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) proporciona una alta disponibilidad y un elevado rendimiento de red a las aplicaciones. Se puede configurar un equilibrador de carga para [equilibrar el tráfico entrante de Internet](../load-balancer/components.md#frontend-ip-configurations) a las máquinas virtuales o [equilibrar el tráfico entre las máquinas virtuales de una red virtual](../load-balancer/components.md#frontend-ip-configurations). Un equilibrador de carga también puede equilibrar el tráfico entre los equipos locales y las máquinas virtuales de una red entre entornos, o reenviar el tráfico externo a una máquina virtual concreta.

El equilibrador de carga asigna tráfico entrante y saliente entre lo siguiente:  

* La dirección IP pública y el puerto en el equilibrador de carga

* La dirección IP privada y el puerto de la máquina virtual

Al crear un equilibrador de carga, también es preciso tener en cuenta estos elementos de configuración:

- **Configuración de IP de front-end**: un equilibrador de carga puede incluir una o varias direcciones IP de front-end. Estas direcciones IP sirven como entrada para el tráfico.
- **Grupo de direcciones de back-end**: direcciones IP que están asociadas a la NIC a la que se distribuye la carga.
- **[Enrutamiento de puerto](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** : define el flujo de tráfico entrante a través de la dirección IP de front-end y su distribución a la dirección IP de back-end mediante reglas NAT de entrada.
- **Reglas del equlibrador de carga**: asigna una combinación dada de IP de front-end y puerto a una combinación de un conjunto de direcciones IP de back-end y puerto. Un solo equilibrador de carga puede tener varias reglas de equilibrio de carga. Cada regla tiene una combinación de una IP de front-end y un puerto y una IP de back-end asociados a las máquinas virtuales.
- **[Sondeos](../load-balancer/load-balancer-custom-probe-overview.md)** : supervisa el estado de las máquinas virtuales. Si un sondeo no responde, el equilibrador de carga deja de enviar nuevas conexiones a la máquina virtual que no funciona correctamente. Las conexiones existentes no se ven afectadas y las nuevas conexiones se envían a las máquinas virtuales que están en buen estado.
- **[Reglas de salida](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** : una regla de salida configura una traducción de direcciones de red (NAT) de salida para todas las máquinas virtuales o instancias identificadas por el grupo de back-end de la instancia de Standard Load Balancer que se van a traducir para el front-end.

En esta tabla se enumeran los métodos que se pueden usar para crear un equilibrador de carga con acceso a Internet.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure |  Puede [equilibrar la carga del tráfico de Internet en máquinas virtuales mediante Azure Portal](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | Para proporcionar el identificador de la dirección IP pública que creó anteriormente, use [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) con el parámetro **`-PublicIpAddress`** . Use [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para crear la configuración del grupo de direcciones de back-end. Use [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) para crear reglas NAT de entrada asociadas a la configuración de la IP de front-end que ha creado. Use [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) para crear los sondeos que necesite. Use [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) para crear la configuración del equilibrador de carga. Use [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) para crear el equilibrador de carga.|
| [CLI de Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Use [az network lb create](/cli/azure/network/lb) para crear la configuración inicial del equilibrador de carga. Use [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) para agregar la dirección IP pública que creó anteriormente. Use [az network lb address-pool create](/cli/azure/network/lb/address-pool) para agregar la configuración del grupo de direcciones de back-end. Use [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) para agregar reglas NAT. Use [az network lb rule create](/cli/azure/network/lb/rule) para agregar las reglas del equilibrador de carga. Use [az network lb probe create](/cli/azure/network/lb/probe) para agregar los sondeos. |
| [Plantilla](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Use [tres máquinas virtuales en una instancia de Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/load-balancer-standard-create) como guía para implementar un equilibrador de carga mediante una plantilla. |

En esta tabla se enumeran los métodos que se pueden usar para crear un equilibrador de carga interno.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | Puede [equilibrar la carga de tráfico interno con un equilibrador de carga en Azure Portal](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Para proporcionar una dirección IP privada en la subred de la red, use [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) con el parámetro **`-PrivateIpAddress`** . Use [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para crear la configuración del grupo de direcciones de back-end. Use [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) para crear reglas NAT de entrada asociadas a la configuración de la IP de front-end que ha creado. Use [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) para crear los sondeos que necesite. Use [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) para crear la configuración del equilibrador de carga. Use [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) para crear el equilibrador de carga.|
| [CLI de Azure](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Use el comando [az network lb create](/cli/azure/network/lb) para crear la configuración inicial del equilibrador de carga. Para definir la dirección IP privada, use [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) con el parámetro **`--private-ip-address`** . Use [az network lb address-pool create](/cli/azure/network/lb/address-pool) para agregar la configuración del grupo de direcciones de back-end. Use [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) para agregar reglas NAT. Use [az network lb rule create](/cli/azure/network/lb/rule) para agregar las reglas del equilibrador de carga. Use [az network lb probe create](/cli/azure/network/lb/probe) para agregar los sondeos.|
| [Plantilla](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Use [dos máquinas virtuales en una instancia de Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer) como guía para implementar un equilibrador de carga mediante una plantilla. |

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escalado de máquinas virtuales

Para más información sobre el equilibrador de carga y los conjuntos de escalado de máquinas virtuales, consulte [Redes para conjuntos de escalado de máquinas virtuales de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="virtual-machines"></a>Máquinas virtuales

Las máquinas virtuales se pueden crear en la misma red virtual, y se pueden conectar entre sí mediante direcciones IP privadas. También pueden conectarse entre sí si están en subredes diferentes. Se conectan sin que sea necesario configurar una puerta de enlace ni usar direcciones IP públicas. Para colocar máquinas virtuales en una red virtual, hay que crear la red virtual. Al ir creando cada máquina virtual, se asigna a la red virtual y a la subred. Las máquinas virtuales obtienen su configuración de red durante la implementación o el inicio.

Las máquinas virtuales reciben una dirección IP cuando se implementan. Si se implementan varias máquinas virtuales en una red virtual o una subred, se les asignarán direcciones IP cuando se inicien. También les puede asignar una dirección IP estática. Si lo hace, conviene plantearse la posibilidad de usar una subred específica para evitar reutilizar una dirección IP estática en otra máquina virtual por error.

Si crea una máquina virtual y, posteriormente, quiere migrarla a una red virtual, esto no es un simple cambio de configuración. Vuelva a implementar la máquina virtual en la red virtual. La manera más fácil de volver a implementarla es eliminar la máquina virtual, pero no los discos conectados a ella y, después, volver a crear la máquina virtual con los discos originales en la red virtual.

En esta tabla se enumeran los métodos que se pueden usar para crear una máquina virtual en una red virtual.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](../virtual-machines/windows/quick-create-portal.md) | Utiliza la configuración de red predeterminada que se mencionó anteriormente para crear una máquina virtual con una NIC única. Para crear una máquina virtual con varias NIC, es preciso usar otro método. |
| [Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md) | Incluye el uso de [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) para agregar la NIC que creó anteriormente a la configuración de la VM. |
| [CLI de Azure](../virtual-machines/linux/create-cli-complete.md) | Cree una máquina virtual y conéctela a una red virtual, subred y NIC creadas mediante pasos individuales. |
| [Plantilla](../virtual-machines/windows/ps-template.md) | Use [Very simple deployment of a Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows) (Implementación muy simple de una máquina virtual Windows) como guía para la implementación de una máquina virtual mediante una plantilla. |

## <a name="virtual-network-nat"></a>Virtual Network NAT

La NAT (traducción de direcciones de red) de Virtual Network simplifica la conectividad a Internet de solo salida para redes virtuales. Cuando se configura en una subred, todas las conexiones salientes usan las direcciones IP públicas estáticas que se hayan especificado. La conectividad saliente es posible sin que el equilibrador de carga ni las direcciones IP públicas estén conectados directamente a máquinas virtuales. La NAT está totalmente administrada y es muy resistente.

Se puede definir la conectividad de salida para todas las subredes con NAT. Distintas subredes dentro de la misma red virtual pueden tener diferentes NAT. Para configurar una subred, es preciso especificar qué recurso de puerta de enlace de NAT se debe usar. Los flujos de salida UDP y TCP de todas las instancias de máquina virtual usarán NAT.
NAT es compatible con los recursos de dirección IP pública o los recursos de prefijos IP públicos, o una combinación de ambos de una SKU estándar. Puede usar un prefijo de IP pública directamente o distribuir las direcciones IP públicas del prefijo entre varios recursos de puerta de enlace de NAT. NAT limpiará todo el tráfico hacia el intervalo de direcciones IP del prefijo. Ahora, filtrar direcciones IP de las implementaciones es más fácil.

Todo el tráfico de salida para la subred lo procesa NAT automáticamente sin que el cliente tenga que configurar nada. No se necesitan rutas definidas por el usuario. NAT tiene prioridad sobre otros escenarios de salida y reemplaza el destino de Internet predeterminado de una subred.

Para obtener más información sobre el recurso NAT Gateway, consulte [¿Qué es Azure Virtual Network NAT?](./nat-gateway/nat-overview.md)

En esta tabla se muestran los métodos que se pueden usar para crear un recurso de puerta de enlace NAT.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](./nat-gateway/tutorial-create-nat-gateway-portal.md) | Crea una red virtual, una subred, una dirección IP pública, una puerta de enlace NAT y una máquina virtual para probar el recurso de puerta de enlace NAT. |
| [Azure PowerShell](./nat-gateway/tutorial-create-nat-gateway-powershell.md) | Incluye el uso de [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) para crear un recurso de puerta de enlace NAT. Crea una red virtual, una subred, una dirección IP pública, una puerta de enlace NAT y una máquina virtual para probar el recurso de puerta de enlace NAT. |
| [CLI de Azure](./nat-gateway/tutorial-create-nat-gateway-cli.md) | Incluye el uso de [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) para crear un recurso de puerta de enlace NAT. Crea una red virtual, una subred, una dirección IP pública, una puerta de enlace NAT y una máquina virtual para probar el recurso de puerta de enlace NAT. |
| [Plantilla](./nat-gateway/quickstart-create-nat-gateway-template.md) | Crea una red virtual, una subred, una dirección IP pública y un recurso de puerta de enlace NAT. |

## <a name="azure-bastion"></a>Azure Bastion 

Azure Bastion se implementa para proporcionar conectividad de administración segura a las máquinas virtuales de una red virtual. El servicio Azure Bastion permite conectarse sin fisuras mediante RDP y SSH a las máquinas virtuales de la red virtual. Asimismo, permite conectarse sin exponer una dirección IP pública en la máquina virtual. Las conexiones se realizan directamente desde Azure Portal, sin necesidad de un software o un cliente/agente adicional. Azure Bastion admite direcciones IP públicas de SKU estándar.

Para más información sobre Azure Bastion, consulte [¿Qué es Azure Bastion?](../bastion/bastion-overview.md)

En esta tabla se muestran los métodos que se pueden usar para crear una implementación de Azure Bastion.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](../bastion/quickstart-host-portal.md) | Crea una red virtual, subredes, una dirección IP pública, un host bastión y máquinas virtuales. |
| [Azure PowerShell](../bastion/bastion-create-host-powershell.md) | Crea una red virtual, subredes, una dirección IP pública y un host bastión. Incluye el uso de [New-AzBastion](/powershell/module/az.network/new-azbastion) para crear un host bastión. |
| [CLI de Azure](../bastion/create-host-cli.md) | Crea una red virtual, subredes, una dirección IP pública y un host bastión. Incluye el uso de [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para crear un host bastión. |
| [Plantilla](../virtual-network/template-samples.md) | Para ver un ejemplo de una implementación de plantilla que integra un host de Azure Bastion con una implementación de ejemplo, consulte [Inicio rápido: Creación de un equilibrador de carga público para equilibrar la carga de las máquinas virtuales mediante una plantilla de Resource Manager](../load-balancer/quickstart-load-balancer-standard-public-template.md). |

## <a name="next-steps"></a>Pasos siguientes
Para conocer los pasos específicos para máquinas virtuales sobre cómo administrar redes virtuales de Azure en máquinas virtuales, consulte los tutoriales de [Windows](../virtual-machines/windows/tutorial-virtual-network.md) o [Linux](../virtual-machines/linux/tutorial-virtual-network.md).

También hay tutoriales sobre cómo equilibrar la carga de las máquinas virtuales y crear aplicaciones de alta disponibilidad para [Windows](../virtual-machines/windows/tutorial-load-balancer.md) o [Linux](../virtual-machines/linux/tutorial-load-balancer.md).

- Aprenda a configurar [rutas definidas por el usuario y el reenvío IP](../virtual-network/virtual-networks-udr-overview.md).
- Aprenda a configurar [conexión de red virtual a red virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Aprenda a [solucionar problemas de rutas](../virtual-network/diagnose-network-routing-problem.md).
- Más información sobre [Ancho de banda de la red de máquinas virtuales](../virtual-network/virtual-machine-network-throughput.md).

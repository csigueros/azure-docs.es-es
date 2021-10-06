---
title: Creación, modificación o eliminación de una dirección IP pública de Azure
titleSuffix: Azure Virtual Network
description: Administrar direcciones IP públicas. Obtenga información sobre cómo una dirección IP pública es un recurso con valores de configuración.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 116a36875c63e515725c90edc3d6fe2a86c9374c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367882"
---
# <a name="create-change-or-delete-an-azure-public-ip-address"></a>Creación, modificación o eliminación de una dirección IP pública de Azure

Obtenga información sobre una dirección IP pública y cómo crearla, modificarla y eliminarla. Una dirección IP pública es un recurso con valores de configuración. Asignar una dirección IP pública a un recurso de Azure que admita direcciones IP públicas permite:

- La comunicación entrante desde Internet a los recursos, como Azure Virtual Machines, Azure Application Gateway, Azure Load Balancer, Azure VPN Gateway y otros.
- La conectividad saliente a Internet usa una dirección IP predecible.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Para obtener instrucciones sobre cómo crear direcciones IP públicas mediante las plantillas del portal, PowerShell, la CLI o Resource Manager, consulte las siguientes páginas:

 * [Creación de direcciones IP públicas: portal](../../virtual-network/create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Creación de direcciones IP públicas: PowerShell](../../virtual-network/create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Creación de direcciones IP públicas: CLI de Azure](../../virtual-network/create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)
 * [Creación de direcciones IP públicas: plantilla](../../virtual-network/create-public-ip-template.md)

>[!NOTE]
>Si bien el portal proporciona la opción de crear dos recursos de direcciones IP públicas (una IPv4 y una IPv6), los comandos de PowerShell y la CLI crean un recurso con una dirección para una versión de dirección IP o la otra. Si desea dos recursos de direcciones IP públicas, uno para cada versión de dirección IP, debe ejecutar dos veces el comando y especificar distintos nombres y versiones de dirección IP para los recursos de direcciones IP públicas.

Para obtener más detalles sobre los atributos específicos de una dirección IP pública durante la creación, consulte la tabla siguiente:

   |Configuración|¿Necesario?|Detalles|
   |---|---|---|
   |Versión de la dirección IP|Sí| Seleccione IPv4 o IPv6 o ambas. Seleccionar ambas se traducirá en la creación de dos direcciones IP públicas: una dirección IPv4 y una dirección IPv6. Obtenga más información sobre [IPv6 en las redes virtuales de Azure](ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Sí|Todas las direcciones IP públicas creadas antes de la introducción de SKU son direcciones IP públicas de SKU **básica**. No se puede cambiar la SKU después de crear la dirección IP pública. Una máquina virtual independiente, las máquinas virtuales dentro de un conjunto de disponibilidad o los conjuntos de escalado de máquinas virtuales pueden usar SKU básicas o estándar. No se permite la mezcla de SKU entre máquinas virtuales dentro de conjuntos de disponibilidad, conjuntos de escalado o VM independientes. SKU **básico**: si va a crear una dirección IP pública en una región que admite zonas de disponibilidad, el ajuste **Zona disponibilidad** se establece en *Ninguno* de forma predeterminada. Las IP públicas básicas no admiten zonas de disponibilidad. SKU **estándar**: una dirección IP pública de SKU estándar se puede asociar a una máquina virtual o una front-end de equilibrador de carga. Si va a crear una dirección IP pública en una región que admite zonas de disponibilidad, el ajuste **Zona disponibilidad** se establece en *Con redundancia de zona* de forma predeterminada. Para obtener información sobre las zonas de disponibilidad, consulte el ajuste **Zona de disponibilidad**. La SKU estándar es necesaria si se asocia la dirección a un equilibrador de carga estándar. Para obtener más información sobre equilibradores de carga estándar, consulte [Azure load balancer standard SKU](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SLU estándar para Azure Load Balancer). Cuando asigna una dirección IP pública de SKU estándar a una interfaz de red de una máquina virtual, debe permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](../../virtual-network/network-security-groups-overview.md#network-security-groups). Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.|
   |Nivel|Sí|Indica si la dirección IP está asociada a una región (**regional**) o si se trata de una "difusión por proximidad (anycast)" desde varias regiones (**global**). *Tenga en cuenta que una IP de "nivel global" es una funcionalidad de versión preliminar para las direcciones IP estándar y actualmente solo se emplea para Load Balancer entre regiones*.|
   |Nombre|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione.|
   |Asignación de dirección IP|Sí|**Dinámica:** las direcciones dinámicas se asignan después de que una dirección IP pública se asocie a un recurso de Azure y el recurso se inicie por primera vez. Las direcciones dinámicas pueden cambiar si un recurso como una máquina virtual se detiene (desasigna) y luego se reinicia. La dirección sigue siendo la misma si una máquina virtual se reinicia o se detiene. Cuando se quita un recurso de dirección IP pública de un recurso, se libera la dirección dinámica.<br> **Estática:** las direcciones estáticas se asignan cuando se crea la dirección IP pública. Las direcciones estáticas no se liberan hasta que se elimina un recurso de dirección IP pública. <br>Nota: Si selecciona *IPv6* como la **versión de dirección IP**, el método de asignación debe ser *Dinámica* para la SKU básica.  Las direcciones de SKU estándar son *estáticas* tanto para IPv4 como para IPv6. |
  |Preferencia de enrutamiento |Sí| De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico a través de la red de área extensa global de Microsoft al usuario.  La selección de "Internet" minimiza el viaje a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad optimizada para costos.  Una vez creada, no se puede cambiar una preferencia de enrutamiento de direcciones IP públicas.  Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](routing-preference-overview.md)   |
   |Tiempo de espera de inactividad (minutos)|No|Cantidad de minutos para mantener una conexión TCP o HTTP abierta sin que dependa del envío de mensajes de mantenimiento de los clientes. Si selecciona IPv6 como la **versión de dirección IP**, este valor se establece en 4 minutos y no se puede cambiar. |
   |Etiqueta de nombre DNS|No|Debe ser único dentro de la ubicación de Azure en la que cree el nombre (entre todas las suscripciones y todos los clientes). Azure registra automáticamente el nombre y la dirección IP en el DNS para que pueda conectarse a un recurso con el nombre. Azure anexa una subred predeterminada, como *location.cloudapp.azure.com*, al nombre que proporcione para crear el nombre DNS completo. Si elige crear ambas versiones de direcciones, el mismo nombre DNS se asigna tanto a la dirección IPv4 como a la IPv6. El servicio DNS predeterminado de Azure contiene registros de nombre IPv4 A e IPv6 AAAA. El servicio DNS predeterminado responde con ambos registros durante la búsqueda de DNS. El cliente elige con qué dirección (IPv4 o IPv6) comunicarse. Puede usar el servicio Azure DNS para configurar un nombre DNS con un sufijo personalizado que se resuelva en la dirección IP pública. Para obtener más información, vea los detalles relativos al [uso de Azure DNS con una dirección IP pública de Azure](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nombre (solo visible si selecciona la versión de IP de **ambas**)|Sí, si selecciona la versión de IP de **ambas**|El nombre debe ser distinto del nombre que escribe para el **nombre** de esta lista. Si elige crear tanto una dirección IPv4 como IPv6, el portal crea dos recursos de direcciones IP públicas independientes, uno con cada versión de dirección IP asignada.|
   |Asignación de direcciones IP (solo visible si selecciona la versión de IP de **ambas**)|Sí, si selecciona la versión de IP de **ambas**| Las mismas restricciones que la asignación de direcciones IP anterior. |
   |Subscription|Sí|Debe existir en la misma [suscripción](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que el recurso al cual asociará las direcciones IP públicas.|
   |Resource group|Sí|Puede existir en el mismo [grupo de recursos](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) o en otro diferente como recurso al que asociará las direcciones IP públicas.|
   |Ubicación|Sí|Debe existir en la misma [ubicación](https://azure.microsoft.com/regions), a la que también se hace referencia como región, que el recurso al que asociará las direcciones IP públicas.|
   |Zona de disponibilidad| No | Este ajuste solo aparece si selecciona una ubicación y un tipo de dirección IP admitidos. Las direcciones IP públicas de SKU **básica** y las direcciones IP públicas de nivel **global** no admiten Availability Zones. Puede seleccionar Ninguna zona (opción predeterminada), una zona específica o con redundancia de zona. La opción dependerá de los requisitos de error de dominio específicos. Para obtener una lista de ubicaciones admitidas y más información sobre Availability Zones, consulte el artículo de [introducción a Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Visualización, cambio de la configuración o eliminación de una dirección IP pública

   - **Ver o enumerar**: revise la configuración de una dirección IP pública, incluida la SKU, la dirección y cualquier asociación. Las asociaciones pueden ser servidores front-end del equilibrador de carga, máquinas virtuales y otros recursos de Azure.
   - **Modificar**: modifique la configuración mediante la información en [Creación de una dirección IP pública](#create-a-public-ip-address). Configuración como el tiempo de espera de inactividad, la etiqueta de nombre DNS o el método de asignación. Para obtener el proceso completo de actualización de una SKU de IP pública de Básica a Estándar, consulte [Actualización de direcciones IP públicas de Azure](../../virtual-network/public-ip-upgrade-portal.md).
   
   >[!WARNING]
   >Quite la dirección de cualquier configuración de IP aplicable (consulte la sección **Eliminar**) para cambiar la asignación de una dirección IP pública de estática a dinámica. Al cambiar el método de asignación de estático a dinámico, perderá la dirección IP que se asignó al recurso de dirección IP pública. Aunque los servidores DNS públicos de Azure mantienen una asignación entre las direcciones estáticas o dinámicas y cualquier etiqueta de nombre DNS (si se han definido), las direcciones IP dinámicas pueden cambiar cuando se inicia la máquina virtual desde el estado detenido (desasignado). Para evitar que la dirección cambie, asigne una dirección IP estática.
   
|Operación|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Ver | En la sección de **Información general** de una dirección IP pública. |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar un objeto de dirección IP pública y ver su configuración.| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) para mostrar la configuración.|
|List | En la categoría de **Direcciones IP públicas**. |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar uno o varios objetos de dirección IP pública y ver su configuración.|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) para enumerar las direcciones IP públicas.|
|Modificar | En el caso de una dirección IP desasociada, seleccione **Configuración** para: </br> Modificar el tiempo de espera de inactividad. </br> Etiqueta de nombre DNS. </br> Cambiar la asignación de una dirección IP de estática a dinámica. </br> Actualizar una dirección IP Básica a Estándar. |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para actualizar la configuración. |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) para actualizar. |

   - **Eliminar**: la eliminación de direcciones IP públicas requiere que el objeto de dirección IP pública no esté asociado a ninguna configuración de IP o interfaz de red de máquina virtual. Para más información, vea la tabla siguiente.

|Resource|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Máquina virtual](../../virtual-network/remove-public-ip-address-vm.md)|Seleccione **Desasociar** para desasociar la dirección IP de la configuración de NIC y, a continuación, seleccione **Eliminar**.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para desasociar la dirección IP de la configuración de NIC; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminarla.|[az network public-ip update con el parámetro "--remove"](/cli/azure/network/public-ip#az_network_public_ip_update) para quitar la dirección IP de la configuración de NIC. Use [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) para eliminar la dirección IP pública. |
|Front-end del equilibrador de carga | Vaya a una dirección IP pública no utilizada y seleccione **Asociar**. Elija el equilibrador de carga con la configuración de IP de front-end pertinente para reemplazar la dirección IP. La dirección IP antigua se puede eliminar con el mismo método que una máquina virtual.  | Use [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) para asociar una nueva configuración de IP de front-end con un equilibrador de carga público. Use[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar una dirección IP pública. También puede usar [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) para quitar una configuración de IP de front-end si hay más de una. | Use [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) para asociar una nueva configuración de IP de front-end a un equilibrador de carga público. Use [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar una dirección IP pública. También puede usar [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) para quitar una configuración de IP de front-end si hay más de una. |
|Firewall|N/D| [Desasignar](../../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) para desasignar el firewall y quitar todas las configuraciones de IP | Use [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) para quitar la dirección IP. Use PowerShell para desasignarla primero. |

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Al usar un conjunto de escalado de máquinas virtuales con direcciones IP públicas, no hay objetos de IP pública independientes asociados a las instancias de máquina virtual individuales. Sin embargo, se puede usar un objeto de prefijo de dirección IP pública [para generar las direcciones IP de la instancia](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).

Para enumerar las direcciones IP públicas de un conjunto de escalado de máquinas virtuales, puede usar PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) o la CLI ([az virtual machine scale set list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips)).

Para más información, consulte [Redes para conjuntos de escalado de máquinas virtuales de Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Asignación de una dirección IP pública

Aprenda a asignar una dirección IP pública a los siguientes recursos:

- Una máquina virtual [Windows](../../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en el momento de la creación. Agregue una dirección IP a una [máquina virtual existente](../../virtual-network/virtual-network-network-interface-addresses.md#add-ip-addresses).
- [Equilibrador de carga público](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexión de sitio a sitio con VPN Gateway](../../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de escalado de máquinas virtuales](../../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Bastion](../../bastion/quickstart-host-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Firewall](../../firewall/tutorial-firewall-deploy-portal-policy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Equilibrador de carga entre regiones](../../load-balancer/tutorial-cross-region-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permisos

Para administrar direcciones IP públicas, su cuenta debe asignarse al rol [Colaborador de red](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor). Un rol [personalizado](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) también se admite. Al rol personalizado se le deben asignar las acciones adecuadas que se muestran en la tabla siguiente:

| Acción                                                             | Nombre                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Leer una dirección IP pública                                          |
| Microsoft.Network/publicIPAddresses/write                          | Crear o actualizar una dirección IP pública                           |
| Microsoft.Network/publicIPAddresses/delete                         | Eliminar una dirección IP pública                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Asociar una dirección IP pública a un recurso                    |

## <a name="next-steps"></a>Pasos siguientes

Las direcciones IP públicas tienen un precio simbólico. Para ver los precios, consulte la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses).

- Crear una dirección IP pública con scripts de ejemplo de [PowerShell](../../virtual-network/powershell-samples.md) o [CLI de Azure](../../virtual-network/cli-samples.md) o con [plantillas de Azure Resource Manager](../../virtual-network/template-samples.md)
- Crear y asignar [definiciones de Azure Policy](../../virtual-network/policy-reference.md) para direcciones IP públicas
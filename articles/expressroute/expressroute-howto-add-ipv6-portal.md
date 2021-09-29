---
title: 'Azure ExpressRoute: Adición de compatibilidad con IPv6 mediante Azure Portal'
description: Aprenda a agregar compatibilidad con IPv6 para conectarse a implementaciones de Azure mediante Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: ffb713ac2a2f60b6ea046691169f4775bff726e3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647487"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Adición de compatibilidad con IPv6 para el emparejamiento privado mediante Azure Portal (versión preliminar)

En este artículo se describe cómo agregar compatibilidad con IPv6 para conectarse a través de ExpressRoute a los recursos de Azure mediante Azure Portal. 

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

En un explorador, vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adición de emparejamiento privado IPv6 al circuito ExpressRoute

1. [Cree un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) o navegue hasta el circuito existente que quiere cambiar.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Captura de pantalla de la lista de circuitos ExpressRoute.":::

1. Seleccione la configuración de emparejamiento **Azure privado**.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Captura de pantalla de la página de información general de ExpressRoute.":::

1. Agregue un emparejamiento privado IPv6 a la configuración de emparejamiento privado IPv4 existente; para ello, seleccione "Ambas" en **Subredes**, o seleccione "IPv6" para habilitar el emparejamiento privado IPv6 en el nuevo circuito. Proporcione un par de subredes IPv6 /126 que posea para el vínculo principal y los vínculos secundarios. Desde cada una de estas subredes, asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador. Seleccione **Guardar** la configuración de emparejamiento una vez que haya especificado todos los parámetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Captura de pantalla de la adición de IPv6 en la página de emparejamiento privado.":::

1. Una vez que la configuración se haya aceptado correctamente, debería ver algo similar al siguiente ejemplo.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Captura de pantalla de IPv6 configurada para el emparejamiento privado.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Actualización de la conexión a una red virtual existente

Siga los pasos que se indican a continuación si tiene un entorno existente de recursos de Azure con el que quisiera usar el emparejamiento privado IPv6.

1. Navegue a la red virtual a la que está conectado el circuito ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Captura de pantalla de la lista de redes virtuales.":::

1. Vaya a la pestaña **Espacio de direcciones** y agregue un espacio de direcciones IPv6 a la red virtual. Seleccione **Guardar** el espacio de direcciones.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Captura de pantalla de la adición del espacio de direcciones IPv6 a la red virtual.":::

1. Vaya a la pestaña **Subredes** y seleccione **GatewaySubnet**. Active **Agregar un espacio de direcciones IPv6** y proporcione un espacio de direcciones IPv6 para la subred. La subred IPv6 de la puerta de enlace debe ser /64 o superior. Seleccione **Guardar** la configuración una vez que haya especificado todos los parámetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Captura de pantalla de la adición del espacio de direcciones IPv6 a la subred.":::
    
1. Si tiene una puerta de enlace con redundancia de zona existente, ejecute lo siguiente en PowerShell para habilitar la conectividad IPv6 (tenga en cuenta que los cambios pueden tardar hasta una hora en reflejarse). De lo contrario, [cree la puerta de enlace de red virtual](./expressroute-howto-add-gateway-portal-resource-manager.md) con cualquier SKU y una dirección IP pública estática Estándar. Si tiene previsto usar FastPath, use UltraPerformance o ErGw3AZ (tenga en cuenta que esta opción solo está disponible para los circuitos mediante ExpressRoute Direct).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    
>[!NOTE]
> If you have an existing gateway that is not zone-redundant (meaning it is Standard, High Performance, or Ultra Performance SKU), you will need to delete and [recreate the gateway](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) using any SKU and a Standard, Static public IP address.

## Create a connection to a new virtual network

Follow the steps below if you plan to connect to a new set of Azure resources using your IPv6 Private Peering.

1. Create a dual-stack virtual network with both IPv4 and IPv6 address space. For more information, see [Create a virtual network](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Create the dual-stack gateway subnet](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Create the virtual network gateway](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) using any SKU and a Standard, Static public IP address. If you plan to use FastPath, use UltraPerformance or ErGw3AZ (note that this option is only available for circuits using ExpressRoute Direct).

1. [Link your virtual network to your ExpressRoute circuit](expressroute-howto-linkvnet-portal-resource-manager.md).

## Limitations
While IPv6 support is available for connections to deployments in Public Azure regions, it doesn't support the following use cases:

* Connections to *existing* ExpressRoute gateways that are not zone-redundant. Note that *newly* created ExpressRoute gateways of any SKU (both zone-redundant and not) using  a Standard, Static IP address can be used for dual-stack ExpressRoute connections
* Global Reach connections between ExpressRoute circuits
* Use of ExpressRoute with virtual WAN
* FastPath with non-ExpressRoute Direct circuits
* FastPath with circuits in the following peering locations: Dubai
* Coexistence with VPN Gateway

## Next steps

To troubleshoot ExpressRoute problems, see the following articles:

* [Verifying ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
* [Troubleshooting network performance](expressroute-troubleshooting-network-performance.md)

---
title: 'Azure ExpressRoute: Adición de compatibilidad con IPv6 mediante Azure PowerShell'
description: Aprenda a agregar compatibilidad con IPv6 para conectarse a implementaciones de Azure mediante Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: f1af7fd0dc4b1e790a120c8fb9086d886487c34d
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113302795"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Adición de compatibilidad con IPv6 para el emparejamiento privado mediante Azure PowerShell (versión preliminar)

En este artículo se describe cómo agregar compatibilidad con IPv6 para conectarse a través de ExpressRoute a los recursos de Azure mediante Azure PowerShell.

## <a name="working-with-azure-powershell"></a>Trabajo con Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adición de emparejamiento privado IPv6 al circuito ExpressRoute

1. [Cree un circuito ExpressRoute](./expressroute-howto-circuit-arm.md) o use un circuito existente. Para recuperar el circuito, ejecute el comando **Get-AzExpressRouteCircuit**:

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Para recuperar la configuración de emparejamiento privado para el circuito, ejecute **Get-AzExpressRouteCircuitPeeringConfig**:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Agregue un emparejamiento privado IPv6 a la configuración de emparejamiento privado IPv4 existente. Proporcione un par de subredes IPv6 /126 que posea para el vínculo principal y los vínculos secundarios. Desde cada una de estas subredes, asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador.

    > [!Note]
    > PeerASN y VlanId deben coincidir con los de la configuración de emparejamiento privado IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Una vez que la configuración se haya guardado correctamente, ejecute el comando **Get-AzExpressRouteCircuit** para volver a obtener el circuito. La respuesta debe ser similar a la del siguiente ejemplo:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Actualización de la conexión a una red virtual existente

Siga los pasos que se indican a continuación si tiene un entorno existente de recursos de Azure con el que quisiera usar el emparejamiento privado IPv6.

1. Recupere la red virtual a la que está conectado el circuito ExpressRoute.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Agregue un espacio de direcciones IPv6 a la red virtual.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Agregue el espacio de direcciones IPv6 a la subred de la puerta de enlace. La subred IPv6 de la puerta de enlace debe ser /64 o superior.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Si tiene una puerta de enlace con redundancia de zona existente, ejecute lo siguiente para habilitar la conectividad IPv6 (tenga en cuenta que los cambios pueden tardar hasta una hora en reflejarse). De lo contrario, [cree la puerta de enlace de red virtual](./expressroute-howto-add-gateway-resource-manager.md) mediante cualquier SKU. Si tiene previsto usar FastPath, use UltraPerformance o ErGw3AZ (tenga en cuenta que esto solo está disponible para los circuitos mediante ExpressRoute Direct).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Creación de una conexión a una nueva red virtual

Siga los pasos que se indican a continuación si tiene previsto conectarse a un nuevo conjunto de recursos de Azure mediante el emparejamiento privado IPv6.

1. Cree una red virtual de doble pila con espacios de direcciones IPv4 e IPv6. Para obtener más información, consulte [Creación de una red virtual](../virtual-network/quick-create-portal.md#create-a-virtual-network).

2. [Creación de la subred de puerta de enlace de doble pila](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. [Creación de la puerta de enlace de red virtual](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) mediante cualquier SKU. Si tiene previsto usar FastPath, use UltraPerformance o ErGw3AZ (tenga en cuenta que esto solo está disponible para los circuitos mediante ExpressRoute Direct).

4. [Vincule la red virtual a su circuito ExpressRoute](./expressroute-howto-linkvnet-arm.md).

## <a name="limitations"></a>Limitaciones
Aunque la compatibilidad con IPv6 está disponible para las conexiones a las implementaciones en regiones públicas de Azure, no se admiten los siguientes casos de uso:

* Conexiones a puertas de enlace de ExpressRoute existentes que *no tienen* redundancia de zona
* Conexiones Global Reach entre circuitos ExpressRoute
* Uso de ExpressRoute con WAN virtual
* FastPath con circuitos que no son de ExpressRoute Direct
* FastPath con circuitos en las siguientes ubicaciones de emparejamiento: Dubái
* Coexistencia con VPN Gateway

## <a name="next-steps"></a>Pasos siguientes

Para solucionar problemas de ExpressRoute, consulte los siguientes artículos:

* [Comprobación de la conectividad de ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solución de problemas de rendimiento de red](expressroute-troubleshooting-network-performance.md)

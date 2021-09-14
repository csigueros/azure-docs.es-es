---
title: 'Inicio rápido: Creación y configuración de una instancia de Route Server mediante Azure PowerShell'
description: En esta guía de inicio rápido, aprenderá a crear y configurar una instancia de Route Server mediante Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 09/01/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: 65f5957d52dcf510601f4a4773cde4c8a477dc97
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475627"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Inicio rápido: Creación y configuración de una instancia de Route Server mediante Azure PowerShell

Este artículo le ayuda a configurar Azure Route Server para su emparejamiento con una aplicación virtual de red (NVA) en su red virtual mediante Azure PowerShell. Route Server aprenderá las rutas de la NVA y las programará en las máquinas virtuales de la red virtual. Azure Route Server también anunciará las rutas de la red virtual a la NVA. Para más información, consulte [Azure Route Server](overview.md).

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Diagrama del entorno de implementación de Route Server mediante Azure PowerShell." border="false":::

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Asegúrese de que tiene los módulos más recientes de PowerShell, o bien, puede usar Azure Cloud Shell en el portal.
* Consulte [Límites de servicio de Azure Route Server](route-server-faq.md#limitations).
* Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="create-resource-group-and-a-virtual-network"></a>Creación de un grupo de recursos y una red virtual

### <a name="create-a-resource-group"></a>Crear un grupo de recursos 

Para poder crear una instancia de Azure Route Server, debe crear un grupo de recursos para hospedarla. Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). En este ejemplo se crea un grupo de recursos denominado **myRouteServerRG** en la ubicación **westus**:

```azurepowershell-interactive
$rg = @{
    Name = 'myRouteServerRG'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-a-virtual-network"></a>Creación de una red virtual

Cree una red virtual con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). En este ejemplo se crea una red virtual predeterminada denominada **myVirtualNetwork** en la ubicación **WestUS**: si ya tiene una red virtual, puede ir directamente a la sección siguiente.

```azurepowershell-interactive
$vnet = @{
    Name = 'myVirtualNetwork'
    ResourceGroupName = 'myRouteServerRG'
    Location = 'WestUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-dedicated-subnet"></a>Adición de una subred dedicada

Azure Route Server requiere una subred dedicada denominada *RouteServerSubnet*. El tamaño de subred debe ser de al menos /27 o un prefijo corto (como /26 o /25) o recibirá un mensaje de error al implementar Route Server. Cree una configuración de subred denominada **RouteServerSubnet** con [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig).

```azurepowershell-interactive
$subnet = @{
    Name = 'RouteServerSubnet'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet

$virtualnetwork | Set-AzVirtualNetwork
```

## <a name="create-the-route-server"></a>Creación de la instancia de Route Server

1. Para garantizar la conectividad con el servicio back-end que administra la configuración de Route Server, es necesario asignar una dirección IP pública. Cree una dirección IP pública estándar denominada **RouteServerIP** con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

    ```azurepowershell-interactive
    $ip = @{
        Name = 'myRouteServerIP'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        AllocationMethod = 'Static'
        IpAddressVersion = 'Ipv4'
        Sku = 'Standard'
    }
    $publicIp = New-AzPublicIpAddress @ip
    ```
    
2. Cree la instancia de Azure Route Server con [New-AzRouteServer](/powershell/module/az.network/new-azrouteserver). En este ejemplo se crea una instancia de Azure Route Server denominada **myRouteServer** en la ubicación **WestUS**. *HostedSubnet* es el identificador de recurso de RouteServerSubnet creado en la sección anterior.

    ```azurepowershell-interactive
    $rs = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        HostedSubnet = $subnetConfig.Id
        PublicIP = $publicIp
    }
    New-AzRouteServer @rs 
    ```

## <a name="create-bgp-peering-with-an-nva"></a>Creación del emparejamiento de BGP con una NVA

Para establecer el emparejamiento BGP desde el servidor de rutas a la aplicación virtual de red, use [Add-AzRouteServerPeer](/powershell/module/az.network/add-azrouteserverpeer):

"your_nva_ip" es la dirección IP de la red virtual asignada a la NVA. "your_nva_asn" es el número de sistema autónomo (ASN) configurado en la NVA. El ASN puede ser cualquier número de 16 bits que no se encuentre en el intervalo del 65515 al 65520. Este intervalo de ASN está reservado por Microsoft.

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA"
    PeerIp = '192.168.0.1'
    PeerAsn = '65501'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = myRouteServerRG'
}
Add-AzRouteServerPeer @peer
```

Para configurar el emparejamiento con una NVA diferente u otra instancia de la misma NVA con fines de redundancia, use el mismo comando que antes con valores diferentes de *PeerName*, *PeerIp* y *PeerAsn*.

## <a name="complete-the-configuration-on-the-nva"></a>Finalización de la configuración en la NVA

Para completar la configuración en la NVA y habilitar las sesiones de BGP, necesita la dirección IP y el ASN de la instancia de Azure Route Server. Para obtener esta información, utilice [Get-AzRouteServer](/powershell/module/az.network/get-azrouteserver):

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
} 
Get-AzRouteServer @routeserver
```

El resultado tendrá un aspecto similar al siguiente:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Configuración del intercambio de rutas

Si tiene una instancia de ExpressRoute y otra de Azure VPN Gateway en la misma red virtual y quiere que intercambien rutas, puede habilitar el intercambio de rutas en Azure Route Server.

1. Para habilitar el intercambio de rutas entre Azure Route Server y las puertas de enlace, use [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) con la marca *-AllowBranchToBranchTraffic*:

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Update-AzRouteServer @routeserver 
```

2. Para deshabilitar el intercambio de rutas entre Azure Route Server y las puertas de enlace, use [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver) sin la marca *-AllowBranchToBranchTraffic*:

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
}  
Update-AzRouteServer @routeserver 
```

## <a name="troubleshooting"></a>Solución de problemas

Use [Get-AzRouteServerPeerAdvertisedRoute](/powershell/module/az.network/get-azrouteserverpeeradvertisedroute) para ver las rutas anunciadas por Azure Route Server.

```azurepowershell-interactive
$remotepeer = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
    PeerName = 'myNVA'
}
Get-AzRouteServerPeerAdvertisedRoute @routeserver
```

Use [Get-AzRouteServerPeerLearnedRoute](/powershell/module/az.network/get-azrouteserverpeerlearnedroute) para ver las rutas aprendidas por la instancia de Azure Route Server.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourcGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Get-AzRouteServerPeerLearnedRoute @routeserver
```
## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita la instancia de Azure Route Server, use el primer comando para quitar el emparejamiento de BGP y, a continuación, el segundo para quitar la instancia de Route Server. 

1. Elimine el emparejamiento de BGP entre Azure Route Server y una NVA con [Remove-AzRouteServerPeer](/powershell/module/az.network/remove-azrouteserverpeer):

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServerPeer @peer
```

2. Quite la instancia de Azure Route Server con [Remove-AzRouteServer](/powershell/module/az.network/remove-azrouteserver):

```azurepowershell-interactive 
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServer @routeserver
```

## <a name="next-steps"></a>Pasos siguientes

Después de crear la instancia de Azure Route Server, continúe para obtener información sobre cómo interactúa Azure Route Server con las puertas de enlace de ExpressRoute y VPN Gateway: 

> [!div class="nextstepaction"]
> [Compatibilidad de Azure Route Server (versión preliminar) con ExpressRoute y VPN de Azure](expressroute-vpn-support.md)

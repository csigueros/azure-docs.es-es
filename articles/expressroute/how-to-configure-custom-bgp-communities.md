---
title: Configuración de las comunidades de BGP personalizadas para el emparejamiento privado de Azure ExpressRoute (versión preliminar)
description: Obtenga información sobre cómo aplicar o actualizar el valor de la comunidad de BGP para una red virtual nueva o existente.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/18/2021
ms.author: duau
ms.openlocfilehash: da3be1f9fbfc2872c31ed4f40d01a1d38ef300ef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478133"
---
# <a name="configure-custom-bgp-communities-for-azure-expressroute-private-peering-preview"></a>Configuración de las comunidades de BGP personalizadas para el emparejamiento privado de Azure ExpressRoute (versión preliminar)

Las comunidades de BGP son agrupaciones de prefijos IP etiquetados con un valor de comunidad. Este valor se puede usar para tomar decisiones de enrutamiento en la infraestructura del enrutador. Puede aplicar filtros o especificar preferencias de enrutamiento para el tráfico enviado a su entorno local desde Azure con etiquetas de la comunidad de BGP. En este artículo se explica cómo aplicar un valor personalizado de la comunidad de BGP para las redes virtuales mediante Azure PowerShell. Una vez configurado, puede ver el valor de la comunidad de BGP regional y el valor personalizado de la comunidad de la red virtual. Este valor se usará para el tráfico saliente enviado a través de ExpressRoute cuando se origine desde esa red virtual.

## <a name="prerequisites"></a>Requisitos previos

* Revise los [requisitos previos](expressroute-prerequisites.md), los [requisitos de enrutamiento](expressroute-routing.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.

* Tiene que tener un circuito ExpressRoute activo. 
  * Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite el circuito mediante el proveedor de conectividad. 
  * Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-arm.md) para obtener instrucciones sobre el enrutamiento. 
  * Asegúrese de que el emparejamiento privado de Azure se configure y establezca el emparejamiento BGP entre la red y Microsoft para la conectividad de un extremo a otro.
  
### <a name="working-with-azure-powershell"></a>Trabajo con Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="apply-a-custom-bgp-community-value-for-a-new-virtual-network"></a>Aplicación de un valor personalizado de la comunidad de BGP para una nueva red virtual

1. Para iniciar la configuración, inicie sesión en su cuenta de Azure y seleccione la suscripción que quiere usar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

1. Cree un grupo de recursos para almacenar la nueva red virtual.

    ```azurepowershell-interactive
    $rg = @{
        Name = 'myERRG'
        Location = 'WestUS'
    }
    New-AzResourceGroup @rg
    ```

1. Cree una red virtual con la marca`-BgpCommunity` para aplicar un valor de comunidad de BGP.

    ```azurepowershell-interactive
    $vnet = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
        Location = 'WestUS'
        AddressPrefix = '10.0.0.0/16'
        BgpCommunity = '12076:20001'    
    }
    New-AzVirtualNetwork @vnet
    ```
    
    > [!NOTE]
    > `12076:` es necesario antes del valor de comunidad personalizado.
    >

1. Recupere la red virtual y revise sus propiedades. Observará una sección *BgpCommunities* que contiene un valor **RegionalCommunity** y un valor **VirtualNetworkCommunity**. El valor *RegionalCommunity* está predefinido en función de la región de Azure de la red virtual. El valor *VirtualNetworkCommunity* debe coincidir con la definición personalizada.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNewtork @virtualnetwork
    ```

## <a name="applying-or-updating-the-custom-bgp-value-for-an-existing-virtual-network"></a>Aplicación o actualización del valor BGP personalizado para una red virtual existente

1. Obtenga la red virtual que quiere aplicar o actualice el valor de comunidad de BGP y almacénelo en una variable.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    $vnet = Get-AzVirtualNewtork @virtualnetwork
    ```

1. Actualice el valor `VirtualNetworkCommunity` para la red virtual.

    ```azurepowershell-interactive
    $vnet.BgpCommunities.VirtualNetworkCommunity = '12076:20002'
    $vnet | Set-AzVirtualNetwork
    ```

    > [!NOTE]
    > `12076:` es necesario antes del valor de comunidad personalizado.
    >

1. Recupere la red virtual y revise sus propiedades actualizadas. El valor **RegionalCommunity** está predefinido en función de la región de Azure de la red virtual. El valor **VirtualNetworkCommunity** debe coincidir con la definición personalizada.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNetwork @virtualnetwork
    ```

> [!IMPORTANT]
>  Si la red virtual existente ya está conectada a un circuito ExpressRoute, deberá eliminar y volver a crear la conexión ExpressRoute después de aplicar el valor personalizado de la comunidad de BGP. Vea [Conexión de una red virtual con un circuito de ExpressRoute](expressroute-howto-linkvnet-arm.md) para saber cómo hacerlo.
>

## <a name="next-steps"></a>Pasos siguientes

- [Comprobación de la conectividad de ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
- [Solución de problemas de rendimiento de la red](expressroute-troubleshooting-network-performance.md)

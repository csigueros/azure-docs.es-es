---
title: 'Creación de una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager (versión preliminar): Azure PowerShell'
description: Aprenda a crear una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager mediante Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 8762a43608c52ceec3f8cb92e08f88a94c244e30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093404"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Creación de una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager (versión preliminar): Azure PowerShell

En este artículo, aprenderá a crear una topología en estrella tipo hub-and-spoke con Azure Virtual Network Manager. Con esta configuración, debe seleccionar una red virtual para que actúe como concentrador y todas las redes virtuales radiales tendrán de forma predeterminada una emparejamiento bidireccional solo con el centro de conectividad. También puede habilitar la conectividad directa entre redes virtuales radiales y permitir que estas usen la puerta de enlace de red virtual en el centro de conectividad.

> [!IMPORTANT]
> *Azure Virtual Network Manager* se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [**Condiciones de uso complementarias de las versiones preliminares de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

* Obtenga información sobre las topologías de red en estrella tipo [hub-and-spoke](concept-connectivity-configuration.md#hub-and-spoke-topology).
* Ha creado una [instancia de Azure Virtual Network Manager](create-virtual-network-manager-powershell.md#create-virtual-network-manager).
* Identifique las redes virtuales que quiere usar en la configuración de tipo hub-and-spoke o cree nuevas [redes virtuales](../virtual-network/quick-create-powershell.md). 

## <a name="create-a-network-group"></a>Creación de un grupo de red

Esta sección le ayudará a crear un grupo de redes que contenga las redes virtuales que va a usar para la topología en estrella tipo hub-and-spoke.

### <a name="static-membership"></a>Pertenencia estática

1. Cree un miembro de red virtual estática con [New-AzNetworkManagerGroupMembersItem](/powershell/module/az.network/new-aznetworkmanagergroupmembersitem).

    ```azurepowershell-interactive
    $member = New-AzNetworkManagerGroupMembersItem –ResourceId "/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA"
    ```

1. Agregue el miembro estático al grupo de pertenencia estática con los siguientes comandos:

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerGroupMembersItem]]$groupMembers = @()  
    $groupMembers.Add($member)
    ```

### <a name="dynamic-membership"></a>Pertenencia dinámica

1. Defina la instrucción condicional y almacénela en una variable:

    ```azurepowershell-interactive
    $conditionalMembership = '{ 
        "allof":[ 
            { 
            "field": "name", 
            "contains": "VNet" 
            } 
        ] 
    }' 
    ```

1. Cree el grupo de red mediante el grupo de pertenencia estática (GroupMember) o el grupo de pertenencia dinámica (ConditionalMembership) definido anteriormente mediante [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetworks
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Creación de una configuración de conectividad de tipo hub-and-spoke

Esta sección le guiará a través de los detalles para crear una configuración de tipo hub-and-spoke con el grupo de red que creó en la sección anterior.

1. Cree un elemento de grupo de conectividad radial para agregar un grupo de red con [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem). Puede habilitar la conectividad directa con la marca `-GroupConnectivity`, la malla global con la marca `-IsGlobal` o la marca `-UseHubGateway` para usar la puerta de enlace en la red virtual del centro de conectividad:

    ```azurepowershell-interactive
    $spokes = @{
        NetworkGroupId = $networkgroup.Id
    }
    $spokesGroup = New-AzNetworkManagerConnectivityGroupItem @gi -UseHubGateway -GroupConnectivity 'None' -IsGlobal
    ```

1. Cree un grupo de conectividad radial y agregue el elemento de grupo del paso anterior.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($spokesGroup) 
    ```

1. Cree un elemento de grupo de conectividad de centro de conectividad y defina la red virtual que usará como centro de conectividad con [New-AzNetworkManagerHub](/powershell/module/az.network/new-aznetworkmanagerhub).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerHub]]$hubList = @()
    
    $hub = @{
        ResourceId = '/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA'
        ResourceType = 'Microsoft.Network/virtualNetworks'
    } 
    $hubvnet = New-AzNetworkManagerHub @hub

    $hubList.Add($hubvnet)
    ```

1. Cree la configuración de conectividad con [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'HubAndSpoke'
        Hub = $hubList
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config -DeleteExistingPeering -IsGlobal
     ```

## <a name="deploy-the-hub-and-spoke-configuration"></a>Implementación de la configuración de tipo hub-and-spoke

Confirme la configuración en las regiones de destino con [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>Confirmación de la implementación

1. Vaya a una de las redes virtuales en el portal y seleccione **Emparejamientos** en *Configuración*. Verá una nueva conexión de emparejamiento entre el centro de conectividad y la red virtual radial con *AVNM* en el nombre.

1. Para probar la *conectividad directa* entre radios, implemente una máquina virtual en cada red virtual radial. A continuación, inicie una solicitud ICMP de una máquina virtual a la otra.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [reglas de administración de seguridad](concept-security-admins.md).
- Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-powershell.md).

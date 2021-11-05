---
title: 'Creación de una topología de red de malla con Azure Virtual Network Manager (versión preliminar): Azure PowerShell'
description: Descubra cómo crear una topología de red de malla con Azure Virtual Network Manager mediante Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d763a0914a624d21d3845a05c1f2c0e6ef7e1ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091197"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Creación de una topología de red de malla con Azure Virtual Network Manager (versión preliminar): Azure PowerShell

En este artículo, descubrirá cómo crear una topología de red de malla con Azure Virtual Network Manager mediante Azure PowerShell. Con esta configuración, todas las redes virtuales de la misma región en el mismo grupo de red pueden comunicarse entre sí. Puede permitir la conectividad entre regiones habilitando la configuración de malla global en la configuración de conectividad.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Obtenga información sobre la topología de red de [malla](concept-connectivity-configuration.md#mesh-network-topology).
* Ha creado una [instancia de Azure Virtual Network Manager](create-virtual-network-manager-powershell.md#create-virtual-network-manager).
* Identifique las redes virtuales que quiere usar en la configuración de red o cree [redes virtuales](../virtual-network/quick-create-powershell.md).

## <a name="create-a-network-group"></a>Creación de un grupo de red

Esta sección le ayudará a crear un grupo de redes que contenga las redes virtuales que va a usar para la topología en estrella tipo hub-and-spoke.

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

## <a name="create-a-mesh-connectivity-configuration"></a>Creación de una configuración de conectividad de malla

En esta sección se le guiará por el proceso de cómo crear una configuración de malla con el grupo de red que se ha creado en la sección anterior.

1. Cree un elemento de grupo de conectividad al que agregar un grupo de red con [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem).

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = $networkgroup.Id
    }
    $groupItem = New-AzNetworkManagerConnectivityGroupItem @gi
    ```

1. Cree un grupo de configuración y agregue el elemento de grupo del paso anterior.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($groupItem)
    ```

1. Cree la configuración de conectividad con [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'Mesh'
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config
     ```

## <a name="deploy-the-mesh-configuration"></a>Implementación de la configuración de malla

Confirme la configuración en las regiones de destino con [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$target = @()   
$target.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $target
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>Confirmación de la implementación

1. Vaya a una de las redes virtuales en el portal y seleccione **Network Manager** (Administrador de red) en *Configuración*. Debería ver la configuración mostrada en esa página.

1. Para probar la conectividad entre redes virtuales, implemente una máquina virtual de prueba en cada red virtual e inicie una solicitud ICMP entre ellas.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [reglas de administración de seguridad](concept-security-admins.md).
- Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-powershell.md).

---
title: 'Inicio rápido: crear una red de malla con Azure Virtual Network Manager mediante Azure PowerShell'
description: Use este inicio rápido para aprender a crear una red de malla con Virtual Network Manager mediante Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: effbdd0482e40535793e2c60dcdce3b4cdb518e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466472"
---
# <a name="quickstart-create-a-mesh-network-with-azure-virtual-network-manager-using-azure-powershell"></a>Inicio rápido: crear una red de malla con Azure Virtual Network Manager mediante Azure PowerShell

Introducción a Azure Virtual Network Manager mediante el uso de la Azure PowerShell para administrar la conectividad de las redes virtuales.

En este inicio rápido, implementará tres redes virtuales y usará Azure Virtual Network Manager para crear una topología de red de malla.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Asegúrese de que tiene los módulos más recientes de PowerShell, o bien, puede usar Azure Cloud Shell en el portal.
* Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="register-subscription-for-public-preview"></a>Registro de la suscripción en la versión preliminar pública

Use el siguiente comando para registrar la suscripción de Azure para la versión preliminar pública de Azure Virtual Network Manager:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowAzureNetworkManager -ProviderNamespace Microsoft.Network
```

## <a name="install-azure-powershell-module"></a>Instalación del módulo de Azure PowerShell

Instale el módulo *Az.Network* de Azure PowerShell más reciente mediante este comando:

```azurepowershell-interactive
Install-Module -Name Az.Network -AllowPrerelease
```

## <a name="create-virtual-network-manager"></a>Creación de un administrador de redes virtuales

1. Defina el ámbito y el tipo de acceso que tendrá esta instancia de Azure Virtual Network Manager. Puede optar por crear el ámbito con el grupo de suscripciones o el grupo de administración o una combinación de ambos. Cree el ámbito mediante [New-AzNetworkManagerScope](/powershell/module/az.network/new-aznetworkmanagerscope).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$subGroup = @()  
    $group.Add("/subscriptions/abcdef12-3456-7890-abcd-ef1234567890")
    [System.Collections.Generic.List[string]]$mgGroup = @()  
    $group.Add("/managementGroups/abcdef12-3456-7890-abcd-ef1234567890")
    
    [System.Collections.Generic.List[String]]$access = @()  
    $access.Add("Connectivity");  
    $access.Add("Security"); 
 
    $scope = New-AzNetworkManagerScope -Subscription $subGroup  -ManagementGroup $mgGroup
    ```

1. Cree el Virtual Network Manager con [New-AzNetworkManager](/powershell/module/az.network/new-aznetworkmanager). En este ejemplo se crea una instancia de Azure Virtual Network Manager llamada **myAVNM** en la ubicación de Oeste de EE. UU.

    ```azurepowershell-interactive
    $avnm = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerScope = $scope.id
        NetworkManagerScopeAccess = $access
        Location = 'West US'
    }
    $networkmanager = New-AzNetworkManager @avnm
    ```

## <a name="create-resource-group-and-virtual-networks"></a>Creación de un grupo de recursos y de redes virtuales

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para poder crear una instancia de Azure Virtual Network Manager, debe crear un grupo de recursos para hospedar el administrador de red. Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). En el ejemplo siguiente se crea un grupo de recursos denominado **myAVNMResourceGroup** en la ubicación de **WestUS**.

```azurepowershell-interactive
$rg = @{
    Name = 'myAVNMResourceGroup'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-three-virtual-networks"></a>Crear tres redes virtuales

Cree tres redes virtuales con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). En este ejemplo se crean redes virtuales denominadas **VNetA**, **VNetB** y **VNetC** en la ubicación **Oeste de EE. UU.** Si ya tiene redes virtuales con las que desea crear una red de malla, puede ir directamente a la sección siguiente.

```azurepowershell-interactive
$vnetA = @{
    Name = 'VNetA'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetworkA = New-AzVirtualNetwork @vnetA

$vnetB = @{
    Name = 'VNetB'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.1.0.0/16'    
}
$virtualNetworkB = New-AzVirtualNetwork @vnetB

$vnetC = @{
    Name = 'VNetC'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.2.0.0/16'    
}
$virtualNetworkC = New-AzVirtualNetwork @vnetC
```

### <a name="add-a-subnet-to-each-virtual-network"></a>Agregue una subred a cada una de las redes virtuales.

Para completar la configuración de las redes virtuales, agregue una subred /24 a cada una. Cree una configuración de subred denominada **default** con [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig).

```azurepowershell-interactive
$subnetA = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkA
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigA = Add-AzVirtualNetworkSubnetConfig @subnetA
$virtualnetworkA | Set-AzVirtualNetwork

$subnetB = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkB
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetB
$virtualnetworkB | Set-AzVirtualNetwork

$subnetC = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkC
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetC
$virtualnetworkC | Set-AzVirtualNetwork
```

## <a name="create-a-network-group"></a>Creación de un grupo de red

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

1. Cree el grupo de red mediante la instrucción condicional definida en el último paso mediante [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetwork'
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-configuration"></a>Creación de una configuración

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

## <a name="commit-deployment"></a>Confirmación de la implementación

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

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita Azure Virtual Network Manager, debe asegurarse de que todos los elementos siguientes se cumplen antes de poder eliminar el recurso:

* No hay implementaciones de configuraciones en ninguna región.
* Se han eliminado todas las configuraciones.
* Se han eliminado todos los grupos de red.

1. Quite la implementación de conectividad mediante la implementación de una configuración vacía con [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$configIds = @()
    [System.Collections.Generic.List[string]]$target = @()   
    $target.Add("westus")     
    $removedeployment = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        ConfigurationId = $configIds
        Target = $target
        CommitType = 'Connectivity'
    }
    Deploy-AzNetworkManagerCommit @removedeployment
    ```

1. Quite la configuración de conectividad con [Remove-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/remove-aznetworkmanagerconnectivityconfiguration)

    ```azurepowershell-interactive
    $removeconfig = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerConnectivityConfiguration @removeconfig   
    ```

1. Quite el grupo de red con [Remove-AzNetworkManagerGroup](/powershell/module/az.network/remove-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $removegroup = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerGroup @removegroup
    ```

1. Elimine la instancia del administrador de red con [Remove-AzNetworkManager](/powershell/module/az.network/remove-aznetworkmanager).

    ```azurepowershell-interactive
    $removenetworkmanager = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
    }
    Remove-AzNetworkManager @removenetworkmanager
    ```

1. Si ya no necesita los recursos que ha creado, elimine el grupo de recursos con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell-interactive
    Remove-AzResourceGroup -Name 'myAVNMResourceGroup'
    ```

## <a name="next-steps"></a>Pasos siguientes

Después de crear la instancia de Azure Virtual Network Manager, siga avanzando para aprender a bloquear el tráfico mediante la configuración del administrador de seguridad:

> [!div class="nextstepaction"]
> [Bloquear el tráfico con reglas de administración de seguridad](how-to-block-network-traffic-powershell.md)

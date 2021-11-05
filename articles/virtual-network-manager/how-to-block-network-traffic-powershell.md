---
title: 'Bloqueo del tráfico de red con Azure Virtual Network Manager (versión preliminar): Azure PowerShell'
description: Aprenda a bloquear el tráfico de red mediante reglas de seguridad en Azure Virtual Network Manager con Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d2e1a79891c0061dd49749fa2e27a2725a0b922f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093655"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-powershell"></a>Bloqueo del tráfico de red con Azure Virtual Network Manager (versión preliminar): Azure PowerShell

En este artículo se muestra cómo crear una regla de seguridad para bloquear el tráfico de red saliente para los puertos 80 y 443 que puede agregar a las colecciones de reglas. Para más información, consulte [Reglas de administración de seguridad](concept-security-admins.md).

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar a configurar reglas de seguridad, confirme los pasos siguientes:

* Comprende todos los elementos de una [regla de administración de seguridad](concept-security-admins.md).
* Ha creado una [instancia de Azure Virtual Network Manager](create-virtual-network-manager-powershell.md).

## <a name="create-a-securityadmin-configuration"></a>Creación de una configuración de SecurityAdmin

1. Cree una nueva configuración de SecurityAdmin con [New-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/new-aznetworkmanagersecurityadminconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'SecurityConfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $securityconfig = New-AzNetworkManagerSecurityAdminConfiguration @config 
    ```

1. Almacene el grupo de red en una variable con [Get-AzNetworkManagerGroup](/powershell/module/az.network/get-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResoureceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $networkgroup = Get-AzNetworkManagerGroup @ng   
    ```

1. Cree un elemento de grupo de conectividad al que agregar un grupo de red con [New-AzNetworkManagerSecurityGroupItem](/powershell/module/az.network/new-aznetworkmanagersecuritygroupitem).

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = '$networkgroup.Id'
    }
    $groupItem = New-AzNetworkManagerSecurityGroupItem @gi
    ```

1. Cree un grupo de configuración y agregue el elemento de grupo del paso anterior.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerSecurityGroupItem]]$configGroup = @()  
    $configGroup.Add($groupItem) 
    ```

1. Cree una colección de reglas de administración de seguridad con [New-AzNetworkManagerSecurityAdminRuleCollection](/powershell/module/az.network/new-aznetworkmanagersecurityadminrulecollection).

    ```azurepowershell-interactive
    $collection = @{
        Name = 'myRuleCollection'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManager = 'myAVNM'
        ConfigName = 'SecurityConfig'
        AppliesToGroup = $configGroup
    }
    $rulecollection = New-AzNetworkManagerSecurityAdminRuleCollection @collection
    ```

1. Defina las variables para los puertos y prefijos de dirección de origen y destino con [New-AzNetworkManagerAddressPrefixItem](/powershell/module/az.network/new-aznetworkmanageraddressprefixitem).

    ```azurepowershell-interactive
    $sourceip = @{
        AddressPrefix = 'Internet'
        AddressPrefixType = 'ServiceTag'
    }
    $sourceprefix = New-AzNetworkManagerAddressPrefixItem @sourceip

    $destinationip = @{
        AddressPrefix = '10.0.0.0/24'
        AddressPrefixType = 'IPPrefix'
    }
    $destinationprefix = New-AzNetworkManagerAddressPrefixItem @destinationip

    [System.Collections.Generic.List[string]]$sourcePortList = @() 
    $sourcePortList.Add("65500”) 

    [System.Collections.Generic.List[string]]$destinationPortList = @() 
    $destinationPortList.Add("80”)
    $destinationPortList.Add("443”)
    ```

1. Cree una regla de seguridad con [New-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/new-aznetworkmanagersecurityadminrule).

    ```azurepowershell-interactive
    $rule = @{
        Name = 'Block_HTTP_HTTPS'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        SecurityAdminConfigurationName = 'SecurityConfig'
        RuleCollectionName = 'myRuleCollection'
        Protocol = 'TCP'
        Access = 'Deny'
        Priority = '100'
        Direction = 'Outbound'
        Source = $sourceprefix
        SourcePortRange = $sourcePortList
        Destination = $destinationprefix
        DestinationPortRange = $destinationPortList
    }
    $securityrule = New-AzNetworkManagerSecurityAdminRule @rule
    ```

## <a name="commit-deployment"></a>Confirmación de la implementación

Confirme la configuración de seguridad en las regiones de destino con [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($securityconfig.id) 
[System.Collections.Generic.List[string]]regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="delete-security-configuration"></a>Eliminación de la configuración de seguridad

Si ya no necesita la configuración de seguridad, deberá asegurarse de que se cumplen los siguientes criterios antes de poder eliminar la propia configuración de seguridad:

* No hay implementaciones de configuraciones en ninguna región.
* Elimine todas las reglas de seguridad de una colección de reglas asociada a la configuración de seguridad.

### <a name="remove-security-configuration-deployment"></a>Eliminación de la implementación de configuración de seguridad

Quite la implementación de seguridad mediante la implementación de una configuración con [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     
$removedeployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @removedeployment
```

### <a name="remove-security-rules"></a>Eliminación de reglas de seguridad

Quite las reglas de seguridad con [Remove-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/remove-aznetworkmanagersecurityadminrule).

```azurepowershell-interactive
$removerule = @{
    Name = 'Block80'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecurityConfig'
}
Remove-AzNetworkManagerSecurityAdminRule @removerule
```

### <a name="remove-security-rule-collections"></a>Eliminación de colecciones de reglas de seguridad

```azurepowershell-interactive
$removecollection = @{
    Name = 'myRuleCollection'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecuritConfig'
}
Remove-AzNetworkManagerSecurityAdminRuleCollection @removecollection
```

### <a name="delete-configuration"></a>Eliminar configuración

Elimine la configuración de seguridad con [Remove-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/remove-aznetworkmanagersecurityadminconfiguration).

```azurepowershell-interactive
$removeconfig = @{
    Name = 'SecurityConfig'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
}
Remove-AzNetworkManagerSecurityAdminConfiguration @removeconfig
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las [reglas de administración de seguridad](concept-security-admins.md).

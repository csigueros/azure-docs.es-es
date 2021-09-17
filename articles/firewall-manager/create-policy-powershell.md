---
title: 'Inicio rápido: Creación y actualización de una directiva de Azure Firewall mediante Azure PowerShell'
description: En este inicio rápido aprenderá a crear y actualizar una directiva de Azure Firewall mediante Azure PowerShell.
services: firewall-manager
author: vaboya
ms.author: victorh
ms.date: 08/16/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.openlocfilehash: 3e7eef6eed76f2b6e3e122c1a373580e05556f03
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254379"
---
# <a name="quickstart-create-and-update-an-azure-firewall-policy-using-azure-powershell"></a>Inicio rápido: Creación y actualización de una directiva de Azure Firewall mediante Azure PowerShell

En este inicio rápido, se usa Azure PowerShell para crear una directiva de Azure Firewall con reglas de red y aplicación. También puede actualizar la directiva existente si agrega reglas de red y de aplicación.

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="set-up-the-network-and-policy"></a>Configuración de la red y la directiva

Primero, cree un grupo de recursos y una red virtual. Después, cree una directiva de Azure Firewall.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El grupo de recursos contiene todos los recursos que se utilizan en este procedimiento.

```azurepowershell
New-AzResourceGroup -Name Test-FWpolicy-RG -Location "East US"
```

### <a name="create-a-virtual-network"></a>Creación de una red virtual

```azurepowershell
$ServerSubnet = New-AzVirtualNetworkSubnetConfig -Name subnet-1 -AddressPrefix 10.0.0.0/24
$testVnet = New-AzVirtualNetwork -Name Test-FWPolicy-VNET -ResourceGroupName Test-FWPolicy-RG -Location "East US" -AddressPrefix 10.0.0.0/8 -Subnet $ServerSubnet
```
### <a name="create-a-firewall-policy"></a>Crear una directiva de firewall

```azurepowershell
New-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG -Location "EAST US"
```

## <a name="create-a-network-rule-collection-group-and-add-new-rules"></a>Creación de un grupo de colecciones de reglas de red e incorporación de reglas nuevas

En primer lugar, cree el grupo de colecciones de reglas y agregue la colección con las reglas.

### <a name="create-the-network-rule-collection-group"></a>Creación del grupo de colecciones de reglas de red

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newnetworkrulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "NetworkRuleCollectionGroup" -Priority 200 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
```
### <a name="create-network-rules"></a>Creación de reglas de red

```azurepowershell
$networkrule1= New-AzFirewallPolicyNetworkRule -Name NwRule1 -Description testRule1  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.1/32 -DestinationPort 22 
$networkrule2= New-AzFirewallPolicyNetworkRule -Name NWRule2 -Description TestRule2  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.10/32 -DestinationPort 1434
```
### <a name="create-a-network-rule-collection-and-add-new-rules"></a>Creación de una colección de reglas de red e incorporación de reglas nuevas

```azurepowershell
$newrulecollectionconfig=New-AzFirewallPolicyFilterRuleCollection -Name myfirstrulecollection -Priority 1000 -Rule $networkrule1,$networkrule2 -ActionType Allow
$newrulecollection = $networkrulecollectiongroup.Properties.RuleCollection.Add($newrulecollectionconfig)
```

### <a name="update-the-network-rule-collection-group"></a>Actualización del grupo de colecciones de reglas de red

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -Priority "200" -FirewallPolicyObject $firewallpolicy -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```

### <a name="output"></a>Resultados

Vea la nueva colección de reglas y sus reglas:

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-Output  $output
```

## <a name="add-network-rules-to-an-existing-rule-collection"></a>Incorporación de reglas de red a una colección de reglas existente

Ahora que tiene una colección de reglas existente, puede agregarle más reglas.

### <a name="get-existing-network-rule-group-collection"></a>Obtención de la colección de reglas de red existente en el grupo

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy 
```

### <a name="create-new-network-rules"></a>Creación de reglas de red

```azurepowershell
$newnetworkrule1 = New-AzFirewallPolicyNetworkRule -Name newNwRule01 -Description testRule01  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.5/32 -DestinationPort 3389
$newnetworkrule2 = New-AzFirewallPolicyNetworkRule -Name newNWRule02 -Description TestRule02  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.15/32 -DestinationPort 1434
```
### <a name="update-the-network-rule-collection-and-add-new-rules"></a>Actualización de la colección de reglas de red e incorporación de reglas nuevas

```azurepowershell
$getexistingrullecollection = $networkrulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myfirstrulecollection"}
$getexistingrullecollection.RuleS.Add($newnetworkrule1)
$getexistingrullecollection.RuleS.Add($newnetworkrule2)
```
### <a name="update-network-rule-collection-group"></a>Actualización del grupo de colecciones de reglas de red

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 200 -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>Resultados

Vea las reglas que acaba de agregar:

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-output $output
```
## <a name="create-an-application-rule-collection-and-add-new-rules"></a>Creación de una colección de reglas de aplicación e incorporación de reglas nuevas

En primer lugar, cree el grupo de colecciones de reglas y agregue la colección con las reglas.

### <a name="create-the-application-rule-collection-group"></a>Creación del grupo de colecciones de reglas de aplicación

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newapprulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "ApplicationRuleCollectionGroup" -Priority 300 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
```
### <a name="create-new-application-rules"></a>Creación de reglas de aplicación

```azurepowershell
$apprule1 = New-AzFirewallPolicyApplicationRule -Name apprule1 -Description testapprule1 -SourceAddress 192.168.0.1/32 -TargetFqdn "*.contoso.com" -Protocol HTTPS
$apprule2 = New-AzFirewallPolicyApplicationRule -Name apprule2 -Description testapprule2  -SourceAddress 192.168.0.10/32 -TargetFqdn "www.contosoweb.com" -Protocol HTTPS
```
### <a name="create-a-new-application-rule-collection-with-rules"></a>Creación de una colección de reglas de aplicación con reglas

```azurepowershell
$apprulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
$apprulecollection = New-AzFirewallPolicyFilterRuleCollection -Name myapprulecollection -Priority 1000 -Rule $apprule1,$apprule2 -ActionType Allow 
$newapprulecollection = $apprulecollectiongroup.Properties.RuleCollection.Add($apprulecollection) 
```

### <a name="update-the-application-rule-collection-group"></a>Actualización de la colección de reglas de aplicación del grupo

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection 
```

### <a name="output"></a>Output

Examine el nuevo grupo de colecciones de reglas y sus reglas nuevas:

```azurepowershell
$output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="add-application-rules-to-an-existing-rule-collection"></a>Incorporación de reglas de aplicación a una colección de reglas existente

Ahora que tiene una colección de reglas existente, puede agregarle más reglas.

```azurepowershell 
#Create new Application Rules for exist Rule collection
$newapprule1 = New-AzFirewallPolicyApplicationRule -Name newapprule01 -Description testapprule01 -SourceAddress 192.168.0.5/32 -TargetFqdn "*.contosoabc.com" -Protocol HTTPS
$newapprule2 = New-AzFirewallPolicyApplicationRule -Name newapprule02 -Description testapprule02  -SourceAddress 192.168.0.15/32 -TargetFqdn "www.contosowebabcd.com" -Protocol HTTPS
```

### <a name="update-the-application-rule-collection"></a>Actualización de la colección de reglas de aplicación

```azurepowershell
$apprulecollection = $apprulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myapprulecollection"}
$apprulecollection.Rules.Add($newapprule1)
$apprulecollection.Rules.Add($newapprule2)

# Update Application Rule collection Group  
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>Resultados

Visualice las reglas nuevas:

```azurepowershell
$Output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite los recursos que ha creado, elimine el grupo de recursos. De este modo se eliminarán todos los recursos creados.

Para eliminar el grupo de recursos, use el cmdlet `Remove-AzResourceGroup`:

```azurepowershell
Remove-AzResourceGroup -Name Test-FWpolicy-RG
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la implementación de Azure Firewall Manager](deployment-overview.md)
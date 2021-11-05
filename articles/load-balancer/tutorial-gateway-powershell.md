---
title: 'Tutorial: Creación de un equilibrador de carga de puerta de enlace: Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Use este tutorial para aprender a crear un equilibrador de carga de puerta de enlace mediante Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 97b8b428d71da312c7cfc0ed9da057003ac68ee7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093170"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-azure-powershell"></a>Tutorial: Creación de un equilibrador de carga de puerta de enlace mediante Azure PowerShell

Azure Load Balancer consta de SKU estándar, básicas y de puerta de enlace. La instancia de Load Balancer de puerta de enlace se usa para la inserción transparente de aplicaciones virtuales de red (NVA). Use la instancia de Load Balancer de puerta de enlace en escenarios que requieran alto rendimiento y alta escalabilidad de NVA.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Registre la característica en vista previa.
> * Cree una red virtual.
> * Cree un grupo de seguridad de red.
> * Cree un equilibrador de carga de puerta de enlace.
> * Encadene un front-end de equilibrador de carga a un equilibrador de carga de puerta de enlace.

> [!IMPORTANT]
> La instancia de Azure Load Balancer para puerta de enlace se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa: [cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una SKU estándar pública existente en Azure Load Balancer. Para obtener más información sobre cómo crear un equilibrador de carga, consulte **[Creación de un equilibrador de carga público mediante Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)** .
    - Para los fines detallados en este tutorial, el equilibrador de carga existente en los ejemplos se denomina **myLoadBalancer**.
- Azure PowerShell instalado localmente o Azure Cloud Shell

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable Az` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="register-preview-feature"></a>Registrar la característica en vista previa

Como parte de la versión preliminar pública del equilibrador de carga de puerta de enlace, el proveedor debe estar registrado en su suscripción de Azure.

Para registrar la característica de proveedor **AllowGatewayLoadBalancer**, use [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature):

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

Para registrar el proveedor de recursos **Microsoft.Network**, use [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider):

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorGwLB-rg' -Location 'eastus'

```

## <a name="create-virtual-network"></a>Creación de una red virtual

Se necesita una red virtual para los recursos que se encuentran en el grupo de back-end del equilibrador de carga de puerta de enlace. Cree la red virtual mediante el comando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Use [New-AzBastion](/powershell/module/az.network/new-azbastion) para implementar un host bastión para la administración segura de recursos en la red virtual.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="create-nsg"></a>Creación de NSG

Use el ejemplo siguiente para crear un grupo de seguridad de red. Configurará las reglas de NSG necesarias para el tráfico de red en la red virtual creada anteriormente.

Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) para crear reglas para NSG. Use [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) para crear el grupo de seguridad de red.

```azurepowershell-interactive
## Create rule for network security group and place in variable. ##
$nsgrule1 = @{
    Name = 'myNSGRule-AllowAll'
    Description = 'Allow all'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule1

$nsgrule2 = @{
    Name = 'myNSGRule-AllowAll-TCP-Out'
    Description = 'Allow all TCP Out'
    Protocol = 'TCP'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Outbound'
}
$rule2 = New-AzNetworkSecurityRuleConfig @nsgrule2

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    SecurityRules = $rule1,$rule2
}
New-AzNetworkSecurityGroup @nsg
```

## <a name="create-gateway-load-balancer"></a>Creación de un equilibrador de carga de puerta de enlace

En esta sección, creará la configuración e implementará el equilibrador de carga de puerta de enlace. Cree la configuración de la IP de front-end para el equilibrador de carga mediante [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). 

Usará [New-AzLoadBalancerTunnelInterface](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) para crear dos interfaces de túnel para el equilibrador de carga. 

Cree un grupo de back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para las NVA. 

Es necesario realizar un sondeo de estado para supervisar el estado de las instancias de back-end en el equilibrador de carga. Use [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) para crear los sondeos de estado que necesite.

El tráfico destinado a las instancias de back-end se enruta con una regla de equilibrio de carga. Use [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) para crear la regla del equilibrador de carga.

Use [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) para crear la implementación del equilibrador de carga.

```azurepowershell-interactive
## Place virtual network configuration in a variable for later use. ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
}
$vnet = Get-AzVirtualNetwork @net

## Create load balancer frontend configuration and place in variable. ## 
$fe = @{
    Name = 'myFrontend'
    SubnetId = $vnet.subnets[0].id
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create backend address pool configuration and place in variable. ## 
$int1 = @{
    Type = 'Internal'
    Protocol = 'Vxlan'
    Identifier = '800'
    Port = '10800'
}
$tunnelInterface1 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int1

$int2 = @{
    Type = 'External'
    Protocol = 'Vxlan'
    Identifier = '801'
    Port = '10801'
}
$tunnelInterface2 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int2

$pool = @{
    Name = 'myBackendPool'
    TunnelInterface = $tunnelInterface1,$tunnelInterface2
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @pool

## Create the health probe and place in variable. ## 
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ## 
$para = @{
    Name = 'myLBRule'
    Protocol = 'All'
    FrontendPort = '0'
    BackendPort = '0'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
$rule = New-AzLoadBalancerRuleConfig @para

## Create the load balancer resource. ## 
$lb = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
    Location = 'eastus'
    Sku = 'Gateway'
    LoadBalancingRule = $rule
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
New-AzLoadBalancer @lb

```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Adición de aplicaciones virtuales de red al grupo de back-end del equilibrar la carga de la puerta de enlace
Implemente las NVA a través de Azure Marketplace. Una vez implementadas, agregue las máquinas virtuales al grupo de back-end con [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface).

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Encadene un front-end del equilibrador de carga a un equilibrador de carga de puerta de enlace.

En este ejemplo, encadenará el front-end de un equilibrador de carga estándar al equilibrador de carga de puerta de enlace. 

Asimismo, agregará el front-end a la dirección IP de front-end de un equilibrador de carga existente en la suscripción.

Use [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) para encadenar el front-end del equilibrador de carga de puerta de enlace al equilibrador de carga existente.

```azurepowershell-interactive
## Place the gateway load balancer configuration into a variable. ##
$par1 = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
}
$gwlb = Get-AzLoadBalancer @par1

## Place the existing load balancer into a variable. ##
$par2 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @par2

## Place the existing public IP for the existing load balancer into a variable.
$par3 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}
$publicIP = Get-AzPublicIPAddress @par3

## Chain the gateway load balancer to your existing load balancer frontend. ##
$par4 = @{
    Name = 'myFrontEndIP'
    PublicIPAddress = $publicIP
    LoadBalancer = $lb
    GatewayLoadBalancerId = $gwlb.FrontendIpConfigurations.Id
}
$config = Set-AzLoadBalancerFrontendIpConfig @par4

$config | Set-AzLoadBalancer

```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede usar el comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para quitar el grupo de recursos, el equilibrador de carga y el resto de los recursos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorGwLB-rg'
```

## <a name="next-steps"></a>Pasos siguientes

Cree aplicaciones virtuales de red en Azure. 

Al crear las aplicaciones virtuales de red, elija los recursos creados en este tutorial:

* Virtual network

* Subnet

* Grupo de seguridad de red

* Equilibrador de carga de puerta de enlace

Pase al siguiente artículo para aprender a crear una instancia de Azure Load Balancer entre regiones.
> [!div class="nextstepaction"]
> [Equilibrador de carga entre regiones](tutorial-cross-region-powershell.md)

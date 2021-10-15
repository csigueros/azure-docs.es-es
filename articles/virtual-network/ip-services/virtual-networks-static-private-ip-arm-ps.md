---
title: 'Creación de una VM con una dirección IP privada estática: Azure PowerShell'
description: Obtenga información sobre cómo crear una máquina virtual con una dirección IP privada estática mediante Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: a98fab6f07e2616e8f3732f28446c24e9051bf09
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367673"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-azure-powershell"></a>Creación de una máquina virtual con una dirección IP privada estática mediante Azure PowerShell

A una máquina virtual (VM) se le asigna automáticamente una dirección IP privada de un intervalo que el usuario especifique. Este intervalo se basa en la subred en la que se implementa la máquina virtual. La VM conserva la dirección hasta que se elimina. Azure asigna dinámicamente la siguiente dirección IP privada disponible desde la subred en la que se crea una máquina virtual. Asigne una dirección IP estática a la máquina virtual si quiere disponer de una dirección IP específica de la subred.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente o Azure Cloud Shell

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable Az` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) con el nombre **myResourceGroup** en la ubicación **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg

```

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Cree una máquina virtual con [New-AzVM](/powershell/module/az.compute/new-azvm). 

Con el comando siguiente, se crea una máquina virtual con Windows Server. Cuando se le solicite, proporcione un nombre de usuario y una contraseña que se usarán como credenciales para la máquina virtual:

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'myResourceGroup'
    Location = 'East US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="change-private-ip-address-to-static"></a>Cambio de la dirección IP privada a estática

En esta sección, cambiará la dirección IP privada de **dinámica** a **estática** para la máquina virtual que creó anteriormente. 

Use [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) para colocar la configuración de red virtual en una variable. Use [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) para colocar la configuración de subred en una variable. Use [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) para obtener la configuración de la interfaz de red y colocarla en una variable. Use [Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig) para establecer la configuración de la interfaz de red. Por último, use [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface) para establecer la configuración de la máquina virtual.

El comando siguiente cambia la dirección IP privada de la máquina virtual a estática:

```azurepowershell-interactive
## Place virtual network configuration into a variable. ##
$net = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

## Place subnet configuration into a variable. ##
$sub = @{
    Name = 'myVM'
    VirtualNetwork = $vnet
}
$subnet = Get-AzVirtualNetworkSubnetConfig @sub

## Get name of network interface and place into a variable ##
$int1 = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vm = Get-AzVM @int1

## Place network interface configuration into a variable. ##
$nic = Get-AzNetworkInterface -ResourceId $vm.NetworkProfile.NetworkInterfaces.Id

## Set interface configuration. ##
$config =@{
    Name = 'myVM'
    PrivateIpAddress = '192.168.1.4'
    Subnet = $subnet
}
$nic | Set-AzNetworkInterfaceIpConfig @config -Primary

## Save interface configuration. ##
$nic | Set-AzNetworkInterface
```

> [!WARNING]
> Aunque puede agregar la configuración de dirección IP privada al sistema operativo, se recomienda no hacerlo hasta después de haber leído el tema sobre la [incorporación de una dirección IP privada a un sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para quitar el grupo de recursos y todos los recursos que contiene:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas](public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Obtener más información acerca de las [direcciones IP privadas](private-ip-addresses.md) y la asignación de una [dirección IP privada estática](virtual-network-network-interface-addresses.md#add-ip-addresses) a una máquina virtual de Azure.
- Obtener más información acerca de cómo crear máquinas virtuales [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
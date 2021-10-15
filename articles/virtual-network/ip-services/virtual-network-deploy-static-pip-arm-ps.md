---
title: 'Creación de una máquina virtual con una dirección IP pública estática: Azure PowerShell'
titlesuffix: Azure Virtual Network
description: Creación de una máquina virtual con una dirección IP pública estática mediante Azure PowerShell. Las direcciones IP públicas estáticas nunca cambian.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 60c9d4827a83475b56abcc769bb1fa7f68467a24
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367847"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-azure-powershell"></a>Creación de una máquina virtual con una dirección IP pública estática mediante Azure PowerShell

En este artículo, creará una máquina virtual con una dirección IP pública estática. Una dirección IP pública permite la comunicación con una máquina virtual desde Internet. Asigne una dirección IP pública estática, en lugar de una dirección dinámica, para garantizar que la dirección no cambie nunca. 

Las direcciones IP públicas tienen un [precio simbólico](https://azure.microsoft.com/pricing/details/ip-addresses). Existe un [límite](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para el número de direcciones IP públicas que puede usar por suscripción.

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

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear direcciones IPv4 públicas estándar.

El comando siguiente crea una dirección IP pública con redundancia de zona denominado **myPublicIP** en **myResourceGroupSLB**.

```azurepowershell-interactive
## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Cree una máquina virtual con [New-AzVM](/powershell/module/az.Compute/new-azvm). 

Con el comando siguiente, se crea una máquina virtual con Windows Server. Escribirá el nombre de la dirección IP pública creada anteriormente en el parámetro **`-PublicIPAddressName`** . Cuando se le solicite, proporcione un nombre de usuario y una contraseña que se usarán como credenciales para la máquina virtual:

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

Para más información sobre las SKU de IP pública, consulte [SKU de dirección IP pública](public-ip-addresses.md#sku). Se puede agregar una máquina virtual al grupo de back-end de una instancia de Azure Load Balancer. La SKU de la dirección IP pública debe coincidir con la SKU de la dirección IP pública de un equilibrador de carga. Para obtener más información, consulte [Equilibrador de carga de Azure](../../load-balancer/skus.md).

Vea la dirección IP pública asignada y confirme que se creó como una dirección estática, con [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

```azurepowershell-interactive
## Retrieve public IP address settings. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
Get-AzPublicIpAddress @ip | Select "IpAddress","PublicIpAllocationMethod" | Format-Table

```

> [!WARNING]
> No modifique la configuración de direcciones IP dentro del sistema operativo de la máquina virtual. El sistema operativo no conoce las direcciones IP públicas de Azure. Aunque puede agregar la configuración de dirección IP privada al sistema operativo, se recomienda no hacerlo a menos que sea necesario y no hasta después de haber leído [Incorporación o eliminación de direcciones IP privadas a un sistema operativo](virtual-network-network-interface-addresses.md#private).

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

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
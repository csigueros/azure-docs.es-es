---
title: 'Tutorial: Configuración de la preferencia de enrutamiento de una máquina virtual (Azure PowerShell)'
description: En este tutorial, aprenderá a crear una máquina virtual con una dirección IP pública con preferencia de enrutamiento mediante Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5442bbd177feab409daa7ed5eb5bad95aeed9e04
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369685"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-azure-powershell"></a>Tutorial: Configuración de la preferencia de enrutamiento de una máquina virtual mediante Azure PowerShell

En este tutorial se muestra cómo configurar las preferencias de enrutamiento para una máquina virtual. El tráfico de Internet enlazado desde la máquina virtual se enrutará a través de la red del ISP si elige **Internet** como opción de preferencia de enrutamiento. El enrutamiento predeterminado se hará a través de la red global de Microsoft.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una dirección IP pública con la preferencia de enrutamiento de **Internet**.
> * Cree una máquina virtual.
> * Comprobar que la dirección IP pública está establecida en la preferencia de enrutamiento de **Internet**.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente o Azure Cloud Shell

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable Az` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) con el nombre **TutorVMRoutePref-rg** en la ubicación **westus2**.

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorVMRoutePref-rg' -Location 'westus2'

```

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IP pública estándar con redundancia de zona llamada **myPublicIP** en **TutorVMRoutePref-rg**. La **etiqueta** de **Internet** se aplica a la dirección IP pública como un parámetro en el comando de PowerShell que habilita la preferencia de enrutamiento de **Internet**.

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'westus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

## <a name="create-virtual-machine"></a>Crear máquina virtual

Use [New-AzVM](/powershell/module/az.compute/new-azvm) para crear una máquina virtual. La dirección IP pública creada en la sección anterior se agrega como parte del comando de PowerShell y se adjunta a la máquina virtual durante la creación.

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'West US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="verify-internet-routing-preference"></a>Comprobación de la preferencia de enrutamiento de Internet

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para comprobar que la preferencia de enrutamiento de **Internet** está configurada para la dirección IP pública.

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select -ExpandProperty IpTags

```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con la máquina virtual y la dirección IP pública, elimine el grupo de recursos y todos los recursos que contiene con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>Pasos siguientes

En el siguiente artículo aprenderá a crear una máquina virtual con preferencia de enrutamiento mixto:
> [!div class="nextstepaction"]
> [Configuración de ambas opciones de preferencias de enrutamiento para una máquina virtual](routing-preference-mixed-network-adapter-portal.md)


---
title: 'Creación de un prefijo de dirección IP pública: PowerShell'
titlesuffix: Azure Virtual Network
description: Obtenga información sobre cómo crear un prefijo de dirección IP pública mediante PowerShell.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 07/12/2021
ms.author: allensu
ms.openlocfilehash: 9e3c939574dcbccc775de3db8330a33a21ae4a6a
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667358"
---
# <a name="create-a-public-ip-address-prefix-using-powershell"></a>Creación de un prefijo de dirección IP pública mediante PowerShell

Obtenga información sobre el prefijo de una dirección IP pública y cómo crearlo, modificarlo y eliminarlo. Un prefijo de dirección IP pública es un rango continuo de direcciones IP públicas de SKU estándar. 

Al crear un recurso de dirección IP pública, puede asignar una dirección IP pública estática a partir del prefijo y asociar la dirección a máquinas virtuales, equilibradores de carga u otros recursos. Para más información, consulte [Prefijo de dirección IP pública](public-ip-address-prefix.md).

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

## <a name="create-a-public-ip-address-prefix"></a>Creación del prefijo de una dirección IP pública

En esta sección, creará un prefijo de dirección IP pública con redundancia de zona, zonal y no zonal mediante Azure PowerShell. 

Los prefijos de los ejemplos son los siguientes:

* **IPv4**: /28 (16 direcciones)

* **IPv6**: /124 (16 direcciones)

Para obtener más información sobre los tamaños de prefijo disponibles, consulte [Tamaños de prefijo](public-ip-address-prefix.md#prefix-sizes).

Cree un prefijo de dirección IP pública con [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) llamado **myPublicIpPrefix** en la ubicación **eastus2**.

### <a name="zone-redundant-ipv4-prefix"></a>Prefijo IPv4 con redundancia de zona

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**. Para crear un prefijo IPv4 con redundancia de zona, escriba **1,2,3** en el parámetro **Zone**.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv4
```

### <a name="zonal-ipv4-prefix"></a>Prefijo IPv4 zonal

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**. Escriba **2** en el parámetro **Zone** para crear un prefijo de dirección IP zonal en la zona 2.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpPrefix @ipv4
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="non-zonal-ipv4-prefix"></a>Prefijo IPv4 no zonal

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**. Quite el parámetro **Zone** para crear un prefijo de dirección IP no zonal.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpPrefix @ipv4
```

La eliminación del parámetro **zone** en el comando es válida en todas las regiones.  

La eliminación del parámetro **zone** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="zone-redundant-ipv6-prefix"></a>Prefijo IPv6 con redundancia de zona

Para crear un prefijo de dirección IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**. Para crear un prefijo IPv6 con redundancia de zona, escriba **1,2,3** en el parámetro **Zone**.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv6
```

### <a name="zonal-ipv6-prefix"></a>Prefijo IPv6 zonal

Para crear un prefijo de dirección IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**. Escriba **2** en el parámetro **Zone** para crear un prefijo de dirección IP zonal en la zona 2.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 2
}
New-AzPublicIpPrefix @ipv6
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="non-zonal-ipv6-prefix"></a>Prefijo IPv6 no zonal

Para crear un prefijo de dirección IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**. Quite el parámetro **Zone** para crear un prefijo de dirección IP no zonal.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
}
New-AzPublicIpPrefix @ipv6
```

La eliminación del parámetro **zone** en el comando es válida en todas las regiones.  

La eliminación del parámetro **zone** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creación de una dirección IP pública estática a partir de un prefijo

Una vez que cree un prefijo, debe crear las direcciones IP estáticas a partir del prefijo. En esta sección, creará una dirección IP estática a partir del prefijo que ha creado antes.

Cree una dirección IP pública con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) en el prefijo **myPublicIpPrefix**.

### <a name="ipv4-address"></a>Dirección IPv4

Para crear una dirección IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**.

```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv4 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv4
```

### <a name="ipv6-address"></a>Dirección IPv6

Para crear un prefijo de dirección IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**.
```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv6 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv6'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv6
```

>[!NOTE]
>Solo se pueden asignar direcciones IP públicas estáticas del intervalo del prefijo creadas con la SKU Estándar. Para más información sobre las SKU de direcciones IP públicas, consulte [Direcciones IP publicas](./public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Eliminación de un prefijo

En esta sección, aprenderá a eliminar un prefijo.

Use [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) para eliminar un prefijo de dirección IP pública.

```azurepowershell-interactive
$pr =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
Remove-AzPublicIpPrefix @pr
```

>[!NOTE]
>Si las direcciones dentro del prefijo están asociadas a recursos de dirección IP pública, primero debe eliminar dichos recursos de dirección IP pública. Consulte [Eliminar una dirección IP pública](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Limpieza de recursos

En este artículo, ha creado un prefijo de dirección IP pública y una dirección IP pública a partir de ese prefijo. 

Cuando haya terminado con el prefijo de dirección IP pública, elimine el grupo de recursos y todos los recursos que contiene:

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName 'myResourceGroup'
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre los escenarios y las ventajas de usar un [prefijo de dirección IP pública](public-ip-address-prefix.md).

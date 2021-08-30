---
title: 'Creación de una dirección IP pública: Azure PowerShell'
titleSuffix: Azure Virtual Network
description: Aprenda a crear una dirección IP pública mediante Azure PowerShell
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/03/2021
ms.author: allensu
ms.openlocfilehash: dc9a6aa398a8cab2927bc8890ceaeb4d0026714c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434814"
---
# <a name="create-a-public-ip-address-using-azure-powershell"></a>Creación de una dirección IP pública mediante Azure PowerShell

En este artículo se muestra cómo crear un recurso de dirección IP pública mediante Azure PowerShell. 

Para más información sobre los recursos que admiten direcciones IP públicas, consulte [Direcciones IP públicas](./public-ip-addresses.md).
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
## <a name="create-standard-sku-public-ip-with-zones"></a>Creación de una dirección IP pública de SKU estándar con zonas

En esta sección, creará una dirección IP pública con zonas. Las direcciones IP públicas pueden tener redundancia de zona o ser zonales.

### <a name="zone-redundant"></a>Redundancia de zona

>[!NOTE]
>El siguiente comando funciona con la versión 4.5.0 o posterior del módulo de Az.Network.  Para más información sobre los módulos de PowerShell que se usan actualmente, consulte la [documentación de PowerShellGet](/powershell/module/powershellget/).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IP pública estándar con redundancia de zona llamada **myStandardZRPublicIP** en **myResourceGroup**. 

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZRPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpAddress @ip
```
> [!IMPORTANT]
> En el caso de los módulos de Az.Network anteriores a 4.5.0, ejecute el comando anterior sin especificar un parámetro de zona para crear una dirección IP con redundancia de zona. 
>

### <a name="zonal"></a>Zonal

Para crear una dirección IPv4 pública zonal estándar en la Zona 2 llamada **myStandardZonalPublicIP** en **myResourceGroup**, use el siguiente comando:

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZonalPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-standard-public-ip-without-zones"></a>Creación de una dirección IP pública estándar sin zonas

En esta sección, creará una dirección IP no zonal.  

>[!NOTE]
>El siguiente comando funciona con la versión 4.5.0 o posterior del módulo de Az.Network.  Para más información sobre los módulos de PowerShell que se usan actualmente, consulte la [documentación de PowerShellGet](/powershell/module/powershellget/).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IPv4 pública estándar como un recurso no de zona llamada **myStandardPublicIP** en **myResourceGroup**. 

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
La eliminación del parámetro **zone** en el comando es válida en todas las regiones.  

La eliminación del parámetro **zone** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-basic-public-ip"></a>Creación de una dirección IP pública básica

En esta sección, creará una IP básica. Las direcciones IP públicas básicas no admiten zonas de disponibilidad.

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IPv4 pública estática básica llamada **myBasicPublicIP** en **myResourceGroup**.  

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**. 

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **dinámica** cambiando el valor de AllocationMethod a **Dynamic**. 

>[!NOTE]
> Una dirección IPv6 básica siempre debe ser "dinámica".

## <a name="routing-preference-and-tier"></a>Preferencia y nivel de enrutamiento

Las direcciones IPv4 públicas estáticas de SKU estándar admiten preferencia de enrutamiento o la característica de nivel global.

### <a name="routing-preference"></a>Preferencia de enrutamiento

De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico al usuario través de la red de área extensa global de Microsoft.  

La selección de **Internet** reduce el recorrido a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad más rentable.  

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](./routing-preference-overview.md)

El comando crea una dirección IPv4 pública con redundancia de zona estándar con una preferencia de enrutamiento de tipo **Internet**:

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = ${
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
### <a name="tier"></a>Nivel

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../load-balancer/cross-region-overview.md).

El comando siguiente crea una dirección IPv4 global. Esta dirección se puede asociar al servidor front-end de un equilibrador de carga entre regiones.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Tier = 'Global'
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Las direcciones de nivel global no admiten Availability Zones.

## <a name="additional-information"></a>Información adicional 

Para más información sobre los parámetros individuales enumerados en este procedimiento, consulte [Administración de direcciones IP públicas](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Pasos siguientes
- Asocie una [dirección IP pública a una máquina virtual](./associate-public-ip-address-vm.md#azure-portal).
- Obtenga más información acerca de las [direcciones IP públicas](./public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).

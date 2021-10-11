---
title: 'Inicio rápido: Creación de una dirección IP pública mediante PowerShell'
titleSuffix: Azure Virtual Network
description: En este inicio rápido, aprenda a crear una dirección IP pública mediante Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 51a59615d0f87943e195a3f189d96eccaf67623f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369742"
---
# <a name="quickstart-create-a-public-ip-address-using-powershell"></a>Inicio rápido: Creación de una dirección IP pública mediante PowerShell

En este inicio rápido aprenderá a crear una dirección IP pública de Azure. Las direcciones IP públicas de Azure se usan para las conexiones públicas a recursos de Azure. Las direcciones IP públicas están disponibles en dos SKU: básica y estándar. Hay dos niveles de direcciones IP públicas disponibles: regionales y globales. La preferencia de enrutamiento de una dirección IP pública se establece cuando se crea. El enrutamiento de Internet y el enrutamiento de Microsoft Network son las opciones disponibles.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente o Azure Cloud Shell

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable Az` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) con el nombre **QuickStartCreateIP-rg** en la ubicación **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-public-ip"></a>Creación de una IP pública

# <a name="standard-sku"></a>[**SKU Estándar**](#tab/create-public-ip-standard)

>[!NOTE]
>Se recomienda usar la dirección IP pública de SKU estándar para cargas de trabajo de producción.  Para obtener más información sobre las SKU, consulte **[Direcciones IP públicas](public-ip-addresses.md)** .
>
>El siguiente comando funciona con la versión 4.5.0 o posterior del módulo de Az.Network.  Para más información sobre los módulos de PowerShell que se usan actualmente, consulte la [documentación de PowerShellGet](/powershell/module/powershellget/).

En esta sección, creará una dirección IP pública con zonas. Las direcciones IP públicas pueden tener redundancia de zona o ser zonales.

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IPv4 pública estándar con redundancia de zona llamada **myStandardPublicIP** en **QuickStartCreateIP-rg**. 

Para crear una dirección IPv6, modifique el parámetro **`--IpAddressVersion`** a **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

# <a name="basic-sku"></a>[**SKU básica**](#tab/create-public-ip-basic)

>[!NOTE]
>Se recomienda usar la dirección IP pública de SKU estándar para cargas de trabajo de producción.  Para obtener más información sobre las SKU, consulte **[Direcciones IP públicas](public-ip-addresses.md)** .

En esta sección, creará una IP básica. Las direcciones IP públicas básicas no admiten zonas de disponibilidad.

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IPv4 pública estática básica llamada **myBasicPublicIP** en **QuickStartCreateIP-rg**.  

Para crear una dirección IPv6, modifique el parámetro **`--IpAddressVersion`** a **IPv6**. 

```azurepowershell-interactive
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **dinámica** cambiando el valor de **`-AllocationMethod`** a **Dynamic**. 

>[!NOTE]
> Una dirección IPv6 básica siempre debe ser "dinámica".

---

## <a name="create-a-zonal-or-no-zone-public-ip-address"></a>Creación de una dirección IP pública zonal o no zonal

En esta sección, aprenderá a crear una dirección IP pública zonal o no zonal.

# <a name="zonal"></a>[**Zonal**](#tab/create-public-ip-zonal)

Para crear una dirección IPv4 pública zonal estándar en la Zona 2 llamada **myStandardPublicIP-zonal** en **QuickStartCreateIP-rg**, use el siguiente comando.

Para crear una dirección IPv6, modifique el parámetro **`--IpAddressVersion`** a **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-zonal'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal"></a>[**No zonal**](#tab/create-public-ip-non-zonal)

En esta sección, creará una dirección IP no zonal.  

>[!NOTE]
>El siguiente comando funciona con la versión 4.5.0 o posterior del módulo de Az.Network.  Para más información sobre los módulos de PowerShell que se usan actualmente, consulte la [documentación de PowerShellGet](/powershell/module/powershellget/).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para crear una dirección IPv4 pública estándar como un recurso no de zona llamada **myStandardPublicIP-nozone** en **QuickStartCreateIP-rg**. 

Para crear una dirección IPv6, modifique el parámetro **`-IpAddressVersion`** a **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-nozone'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
La eliminación del parámetro **`-Zone`** en el comando es válida en todas las regiones.  

La eliminación del parámetro **`-Zone`** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="routing-preference-and-tier"></a>Preferencia y nivel de enrutamiento

Las direcciones IPv4 públicas estáticas de SKU estándar admiten preferencia de enrutamiento o la característica de nivel global.

# <a name="routing-preference"></a>[**Preferencia de enrutamiento**](#tab/routing-preference)

De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico al usuario través de la red de área extensa global de Microsoft.  

La selección de **Internet** reduce el recorrido a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad más rentable.  

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](routing-preference-overview.md)

El comando crea una dirección IPv4 pública con redundancia de zona estándar con una preferencia de enrutamiento de tipo **Internet**:

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP-RP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

# <a name="tier"></a>[**Nivel**](#tab/tier)

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../../load-balancer/cross-region-overview.md).

El comando siguiente crea una dirección IPv4 global. Esta dirección se puede asociar al servidor front-end de un equilibrador de carga entre regiones.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

---

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con la máquina virtual y la dirección IP pública, elimine el grupo de recursos y todos los recursos que contiene con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un prefijo de dirección IP pública:
> [!div class="nextstepaction"]
> [Creación de un prefijo de IP pública mediante PowerShell](create-public-ip-prefix-powershell.md)
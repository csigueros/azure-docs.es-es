---
title: Creación de una red virtual mediante Azure PowerShell
titlesuffix: Azure Virtual Network
description: En este inicio rápido, creará una red virtual mediante Azure Portal. Una red virtual permite que los recursos de Azure se comuniquen entre sí y con Internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 76561e0b19d3d42421a73ddc178ad12cd83d5e25
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2021
ms.locfileid: "114297609"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Inicio rápido: Creación de una red virtual mediante PowerShell

Una red virtual permite que los recursos de Azure, como las máquinas virtuales (VM), se comuniquen entre sí de forma privada y con Internet. 

En esta guía de inicio rápido aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en la red virtual. Luego, conéctese a las máquinas virtuales desde Internet y, de manera privada, comuníquese a través de la red virtual.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente o Azure Cloud Shell

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable Az` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Creación de un grupo de recursos y una red virtual

Debe llevar a cabo una serie de pasos para configurar el grupo de recursos y la red virtual.

### <a name="create-the-resource-group"></a>Creación del grupo de recursos

Para poder crear una red virtual, debe crear un grupo de recursos que hospede la red virtual. Cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). En este ejemplo se crea un grupo de recursos llamado **CreateVNetQS-rg** en la ubicación **Eastus**:

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Crear la red virtual

Cree una red virtual con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). En este ejemplo se crea una red virtual predeterminada llamada **myVNet** en la ubicación **EastUS**:

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Incorporación de una subred

Azure implementa recursos en una subred dentro de una red virtual, por lo que es necesario crear una subred. Cree una configuración de subred denominada **default** con [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Asociación de la subred a la red virtual

Puede escribir la configuración de la subred en la red virtual con [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando crea la subred:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual.

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

Cree la primera máquina virtual con [New-AzVM](/powershell/module/az.compute/new-azvm). Cuando ejecute el comando siguiente, se le pedirán las credenciales. Escriba un nombre de usuario y una contraseña para la máquina virtual:

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

La opción `-AsJob` crea la máquina virtual en segundo plano. Puede continuar al paso siguiente.

Cuando Azure empieza a crear la máquina virtual en segundo plano, verá algo como esto:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

Cree la segunda máquina virtual con este comando:

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Tendrá que crear otro usuario y otra contraseña. Azure tarda unos minutos en crear la máquina virtual.

> [!IMPORTANT]
> No vaya al próximo paso hasta que Azure haya terminado.  Sabrá que terminó cuando devuelva la salida a PowerShell.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-a-vm-from-the-internet"></a>Conexión a una máquina virtual desde Internet

Para obtener la dirección IP pública de la máquina virtual, use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

Este ejemplo devuelve la dirección IP pública de la máquina virtual **myVm1**:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

Abra un símbolo del sistema en el equipo local. Ejecute el comando `mstsc`. Reemplace `<publicIpAddress>` por la dirección IP pública que se devolvió en el último paso:

> [!NOTE]
> Si ejecutó estos comandos desde un símbolo del sistema de PowerShell en el equipo local y usa la versión 1.0 o posterior del módulo Az de PowerShell, puede seguir en esa interfaz.

```cmd
mstsc /v:<publicIpAddress>
```
1. Cuando se le pida, seleccione **Conectar**.

1. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual.

    > [!NOTE]
    > Es posible que tenga que seleccionar **Más opciones** > **Usar otra cuenta** para especificar las credenciales que escribió al crear la máquina virtual.

1. Seleccione **Aceptar**.

1. Puede que reciba una advertencia de certificado. Si la recibe, seleccione **Sí** o **Continuar**.

## <a name="communicate-between-vms"></a>Comunicarse entre máquinas virtuales

1. En el Escritorio remoto de **myVm1**, abra PowerShell.

1. Escriba `ping myVm2`.

    Verá algo similar a lo siguiente:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Se produce un error de ping, porque usa el Protocolo de mensajes de control de Internet (ICMP). De manera predeterminada, el protocolo ICMP no puede atravesar el Firewall de Windows.

1. Para permitir que **myVm2** haga ping a **myVm1** en un paso posterior, escriba este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ese comando permite una conexión entrante de ICMP a través del Firewall de Windows.

1. Cierre la conexión de Escritorio remoto a **myVm1**.

1. Repita los pasos que aparecen en [Conexión a una máquina virtual desde Internet](#connect-to-a-vm-from-the-internet). Esta vez, conéctese a **myVm2**.

1. En un símbolo del sistema de la máquina virtual **myVm2**, escriba `ping myvm1`.

    Verá algo similar a lo siguiente:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Recibe respuestas de **myVm1**, porque permitió ICMP a través del Firewall de Windows en la máquina virtual **myVm1** en un paso anterior.

1. Cierre la conexión de Escritorio remoto a **myVm2**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con la red virtual y las máquinas virtuales, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para quitar el grupo de recursos y todos los recursos que tiene:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido: 

* Ha creado una red virtual predeterminada y dos máquinas virtuales. 
* Se ha conectado a una máquina virtual desde Internet y se ha comunicado de forma privada entre las dos máquinas virtuales.

La comunicación privada entre máquinas virtuales no está restringida en una red virtual. 

Para más información sobre cómo configurar distintos tipos de comunicaciones de red de VM, vaya al siguiente artículo:
> [!div class="nextstepaction"]
> [Filtrado del tráfico de red](tutorial-filter-network-traffic.md)

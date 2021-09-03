---
title: Creación de un nuevo conmutador virtual en Azure Stack Edge a través de PowerShell
description: Se describe cómo crear un conmutador virtual en un dispositivo Azure Stack Edge mediante Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 9910ac4d817879812803cd41f6b184846e1b02be
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106252"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Creación de un nuevo conmutador virtual en Azure Stack Edge Pro con GPU a través de PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo crear un nuevo conmutador virtual en un dispositivo Azure Stack Edge Pro con GPU. Por ejemplo, si desea que las máquinas virtuales se conecten a través de un puerto de red físico diferente, debe crear un nuevo conmutador virtual.

## <a name="vm-deployment-workflow"></a>Flujo de trabajo de implementación de una máquina virtual

1. Conéctese a la interfaz de PowerShell del dispositivo.
2. Consulte las interfaces de red físicas disponibles.
3. Crear un conmutador virtual.
4. Compruebe la red virtual y la subred que se crean automáticamente.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que:

- Tiene acceso a un equipo cliente que puede tener acceso a la interfaz de PowerShell del dispositivo. Consulte [Conectarse a la interfaz de PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    El equipo cliente debe ejecutar un [sistema operativo compatible](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).

- Usa la interfaz de usuario local para habilitar el proceso en una de las interfaces de red físicas en el dispositivo según las instrucciones de [Habilitación de la red de proceso](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) en el dispositivo. 


## <a name="connect-to-the-powershell-interface"></a>Conectarse a la interfaz de PowerShell

[Conéctese a la interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Consulta de las interfaces de red disponibles

1. Use el siguiente comando para mostrar una lista de interfaces de red físicas en las que puede crear un nuevo conmutador virtual. Seleccionará una de estas interfaces de red.

    ```powershell
    Get-NetAdapter -Physical
    ```
    A continuación, se incluye una salida de ejemplo:
    
    ```output
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Elija una interfaz de red que:

    - Esté en estado **Activo**. 
    - No se use en ningún conmutador virtual existente. Actualmente, solo se puede configurar un conmutador virtual por cada interfaz de red. 
    
    Para comprobar el conmutador virtual existente y la asociación de la interfaz de red, ejecute el comando `Get-HcsExternalVirtualSwitch`.
 
    Esta es una salida de ejemplo.

    ```output
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    En esta instancia, el puerto 2 está asociado a un conmutador virtual existente y no debe usarse.

## <a name="create-a-virtual-switch"></a>Crear un conmutador virtual

Use el siguiente cmdlet para crear un nuevo conmutador virtual en la interfaz de red especificada. Una vez completada esta operación, las instancias de proceso pueden usar la nueva red virtual.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Use el comando `Get-HcsExternalVirtualSwitch` para identificar el conmutador recién creado. El nuevo conmutador que se ha creado se denomina `vswitch-<InterfaceAlias>`. 

A continuación, se incluye una salida de ejemplo:

```output
[10.100.10.10]: PS> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet-for-switch"></a>Comprobación de la red y la subred para el conmutador

Después de crear el nuevo conmutador virtual, Azure Stack Edge Pro con GPU crea automáticamente una red virtual y una subred que le corresponde. Puede usar esta red virtual al crear máquinas virtuales.

Para identificar la red virtual y la subred asociada al nuevo conmutador que ha creado, use el cmdlet `Get-HcsVirtualNetwork`. 

## <a name="create-virtual-lans"></a>Creación de redes LAN virtuales

Para agregar una configuración de red de área local virtual (LAN) en un conmutador virtual, use el cmdlet siguiente.

```powershell
Add-HcsVirtualNetwork-VirtualSwitchName <Virtual Switch name> -VnetName <Virtual Network Name> –VlanId <Vlan Id> –AddressSpace <Address Space> –GatewayIPAddress <Gateway IP>–DnsServers <Dns Servers List> -DnsSuffix <Dns Suffix name>
``` 

Puede usar los parámetros siguientes con el cmdlet `Add-HcsVirtualNetwork-VirtualSwitchName`.


|Parámetros  |Descripción  |
|---------|---------|
|VNetName     |Nombre de la red LAN virtual         |
|VirtualSwitchName    |Nombre del conmutador virtual donde quiere agregar la configuración de LAN virtual         |
|AddressSpace     |Espacio de direcciones de subred para la red LAN virtual         |
|GatewayIPAddress     |Puerta de enlace para la red virtual         |
|DnsServers     |Lista de las direcciones IP de servidores DNS         |
|DnsSuffix     |Nombre DNS sin el elemento host para la subred de red LAN virtual         |



Esta es una salida de ejemplo.

```output
[10.100.10.10]: PS> Add-HcsVirtualNetwork -VirtualSwitchName vSwitch1 -VnetName vlanNetwork100 -VlanId 100 -AddressSpace 5.5.0.0/16 -GatewayIPAddress 5.5.0.1 -DnsServers "5.5.50.50&quot;,&quot;5.5.50.100&quot; -DnsSuffix &quot;name.domain.com"

[10.100.10.10]: PS> Get-HcsVirtualNetwork
 
Name             : vnet2015
AddressSpace     : 10.128.48.0/22
SwitchName       : vSwitch1
GatewayIPAddress : 10.128.48.1
DnsServers       : {}
DnsSuffix        :
VlanId           : 2015
 
Name             : vnet3011
AddressSpace     : 10.126.64.0/22
SwitchName       : vSwitch1
GatewayIPAddress : 10.126.64.1
DnsServers       : {}
DnsSuffix        :
VlanId           : 3011
```
 
> [!NOTE]
> - Puede configurar varias redes LAN virtuales en el mismo conmutador virtual. 
> - La dirección IP de puerta de enlace debe estar en la misma subred que el parámetro que se haya pasado como espacio de direcciones.
> - No se puede quitar un conmutador virtual si hay redes LAN virtuales configuradas. Para eliminar este conmutador virtual, primero debe eliminar la LAN virtual y después el conmutador virtual.

## <a name="verify-network-subnet-for-virtual-lan"></a>Comprobación de la red y la subred para la LAN virtual

Después de crear la LAN virtual, se crean automáticamente una red virtual y una subred correspondiente. Puede usar esta red virtual al crear máquinas virtuales.

Para identificar la red virtual y la subred asociada al nuevo conmutador que ha creado, use el cmdlet `Get-HcsVirtualNetwork`.


## <a name="next-steps"></a>Pasos siguientes

- [Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU a través de Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)

---
title: Creación de un conmutador virtual en Azure IoT Edge para Linux en Windows | Microsoft Docs
description: Instalaciones para crear un conmutador virtual para Azure IoT Edge para Linux en Windows
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5db37717356cd06b257867615623e24a1c36a335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780579"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Creación de un conmutador virtual de Azure IoT Edge para Linux en Windows
Azure IoT Edge para Linux en Windows usa un conmutador virtual en la máquina host para comunicarse con la máquina virtual. Las versiones de escritorio de Windows incluyen un conmutador predeterminado que se puede usar, pero Windows Server no. Para poder implementar IoT Edge para Linux en Windows en un dispositivo Windows Server, debe crear un conmutador virtual. Además, puede usar esta guía para crear un conmutador virtual personalizado, si es necesario. 

En este artículo se explica cómo crear un conmutador virtual en un dispositivo Windows para instalar IoT Edge para Linux en Windows con los pasos siguientes:
- Crear un conmutador virtual
- Crear una tabla NAT
- Instalar y configurar un servidor DHCP

## <a name="prerequisites"></a>Requisitos previos
- Un dispositivo Windows. Para ver las versiones de Windows compatibles, consulte [Sistemas operativos](support.md#operating-systems).
- El rol de Hyper-V instalado en el dispositivo Windows. Para obtener más información sobre cómo habilitar Hyper-V, consulte [Instalación y aprovisionamiento de Azure IoT Edge para Linux en un dispositivo Windows](./how-to-install-iot-edge-on-windows.md?tabs=powershell#prerequisites).

## <a name="create-virtual-switch"></a>Creación de un conmutador virtual 
Los pasos de esta sección son una guía genérica para crear un conmutador virtual. Asegúrese de que la configuración del conmutador virtual se alinee con el entorno de red.

1. Abra PowerShell en una sesión con privilegios elevados.

2. Compruebe los conmutadores virtuales del host de Windows y asegúrese de que no haya ninguno que se pueda usar. Consulte [Get-VMSwitch (Hyper-V)](/powershell/module/hyper-v/get-vmswitch) para obtener información completa. 

   ```powershell
   Get-VMSwitch
   ```

   Si ya se ha creado un conmutador virtual denominado **Conmutador predeterminado** y no necesita un conmutador virtual personalizado, debería poder instalar IoT Edge para Linux en Windows sin necesidad de seguir el resto de pasos de esta guía.

3. Cree un conmutador de máquina virtual con un nombre y escriba **Interno** o **Privado**. Para crear un conmutador virtual **Externo**, especifique los parámetros **NetAdapterInterfaceDescription** o **NetAdapterName**, que establecen implícitamente el tipo de conmutador virtual en **Externo**. Consulte [New-VMSwitch (Hyper-V)](/powershell/module/hyper-v/new-vmswitch) y [Creación de un conmutador virtual para máquinas virtuales de Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) para obtener información completa e instrucciones adicionales.
   ```powershell
   New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
   ```

4. Obtenga el índice de la interfaz del conmutador creado. Consulte [Get-NetAdapter (NetAdapter)](/powershell/module/netadapter/get-netadapter) para obtener información completa. 
   ```powershell
   (Get-NetAdapter -Name '*{switchName}*').ifIndex
   ```

5. Con el índice de la interfaz del paso anterior, obtenga el octeto de la dirección IP del adaptador de red del conmutador creado. Consulte [Get-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/get-netipaddress) para obtener información completa. 
   ```powershell
   Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {ifIndex}
   ```

6. Con la familia de direcciones IP y el índice de la interfaz de los pasos anteriores, cree y establezca la nueva dirección IP de puerta de enlace.  Por ejemplo, si la dirección IPv4 del adaptador de conmutador de red virtual es xxx.xxx.xxx.yyy, puede establecer el valor de gatewayIp en xxx.xxx.xxx.1. Consulte [New-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/new-netipaddress) para obtener información completa.
   ```powershell
   New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {ifIndex}
   ```

7. Cree un objeto de traducción de direcciones de red (NAT) que traduzca una dirección de red interna en una red externa. Use la misma dirección de la familia IPv4 de los pasos anteriores. Por ejemplo, si la dirección IPv4 del adaptador de conmutador de red virtual es xxx.xxx.xxx.yyy, puede establecer el valor de natIp en xxx.xxx.xxx.0. Consulte [New-NetNat (NetNat)](/powershell/module/netnat/new-netnat) para obtener información completa. 
   ```powershell
   New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
   ```

## <a name="create-dhcp-server"></a>Creación de un servidor DHCP 

>[!WARNING]
>Es posible que se requiera autorización para implementar un servidor DHCP en un entorno de red corporativa. Compruebe si la configuración del conmutador virtual cumple las directivas de la red corporativa. Para obtener más información, consulte la guía [Implementación de DHCP mediante Windows PowerShell](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps). 

1. Compruebe si la característica Servidor DHCP está instalada en el dispositivo. Busque la columna **Instalar estado**.
   ```powershell
   Get-WindowsFeature -Name 'DHCP'
   ```

2. Si no está instalado, instálelo con el comando siguiente.
   ```powershell
   Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
   ```

3. Agregue el servidor DHCP a los grupos de seguridad locales predeterminados y reinicie el servidor.
   ```powershell
   netsh dhcp add securitygroups
   Restart-Service dhcpserver
   ```

4. Configure el ámbito del servidor DHCP. Consulte [Add-DhcpServerv4Scope (DhcpServer)](/powershell/module/dhcpserver/add-dhcpserverv4scope) para obtener información completa.  El intervalo de direcciones IP del servidor DHCP está determinado por **startIp** y **endIp**. Por ejemplo, si 100 direcciones quieren estar disponibles, según la dirección IPv4 xxx.xxx.xxx.yyy del adaptador de conmutador de red virtual del paso 5, startIp = xxx.xxx.xxx.100, endIp = xxx.xxx.xxx.200 y subnetMask = 255.255.255.0.
   ```powershell
   Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask {subnetMask} -State Active
   ```

5. Por último, asigne el objeto NAT y gatewayIp al servidor DHCP y reinicie el servidor para cargar la configuración.
   ```powershell
   Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
   Restart-service dhcpserver
   ```

## <a name="next-steps"></a>Pasos siguientes
Siga los pasos descritos en [Instalación y aprovisionamiento de Azure IoT Edge para Linux en un dispositivo Windows](how-to-install-iot-edge-on-windows.md) para configurar un dispositivo con IoT Edge para Linux en Windows.
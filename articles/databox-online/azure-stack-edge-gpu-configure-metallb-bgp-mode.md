---
title: Configuración de MetalLB a través de BGP en Azure Stack Edge
description: Describe cómo configurar MetalLB a través del Protocolo de puerta de enlace de borde para el equilibrio de carga en el dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/24/2021
ms.author: alkohli
ms.openlocfilehash: df4f0170f6dde0995576b0244733dea4ab01515e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017602"
---
# <a name="configure-load-balancing-with-metallb-on-your-azure-stack-edge"></a>Configuración del equilibrio de carga con MetalLB en Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo configurar el equilibrio de carga en el dispositivo Azure Stack Edge mediante MetalLB a través del Protocolo de puerta de enlace de borde (BGP). 

## <a name="about-metallb-and-load-balancing"></a>Acerca de MetalLB y el equilibrio de carga

MetalLB es una implementación del equilibrador de carga para clústeres de Kubernetes sin sistema operativo. MetalLB ofrece dos funciones: asigna direcciones IP a los servicios del equilibrador de carga de Kubernetes desde un grupo configurado de direcciones IP y, a continuación, anuncia la dirección IP a la red externa. MetalLB consigue estas funciones a través de protocolos de enrutamiento estándar, como el Protocolo de resolución de direcciones (ARP), el Protocolo de detección de equipos cercanos (NDP) o el Protocolo de puerta de enlace de borde (BGP). 

Para obtener más información, vea [Modo BGP para MetalLB](https://metallb.universe.tf/configuration/#bgp-configuratioN).

## <a name="metallb-on-azure-stack-edge"></a>MetalLB en Azure Stack Edge

Hay varios componentes de red, como Calico, MetalLB y Core DNS instalados en el dispositivo Azure Stack Edge. MetalLB se conecta al clúster de Kubernetes que se ejecuta en el dispositivo Azure Stack Edge y permite crear servicios de Kubernetes de tipo `LoadBalancer` en el clúster.

En el modo BGP, todas las máquinas del clúster establecen sesiones de emparejamiento de BGP con enrutadores cercanos que controla el usuario, e indican a estos enrutadores cómo reenviar el tráfico a las IP del servicio. MetalLB con el Protocolo de puerta de enlace de borde (BGP) no es el modo de red predeterminado para el clúster de Kubernetes que se ejecuta en el dispositivo. Para configurar MetalLB a través de BGP, debe designar el conmutador de la parte superior del bastidor (ToR) como equilibrador de carga y configurar sesiones del mismo nivel. 

MetalLB en modo BGP se puede configurar para lograr tiempos de conmutación por error bajos si usa dispositivos de 2 nodos. Esta configuración es más complicada que la configuración estándar, ya que es posible que no tenga acceso al conmutador de la parte superior del bastidor.

## <a name="configure-metallb"></a>Configuración de MetalLB

Puede configurar MetalLB en modo BGP si se conecta a la interfaz de PowerShell del dispositivo y, a continuación, ejecuta cmdlets específicos.

### <a name="prerequisites"></a>Prerrequisitos

Antes de comenzar, asegúrese de que:
- El proceso está habilitado en un puerto del dispositivo. Esto crea un conmutador virtual en ese puerto. 
    - Para habilitar el proceso, en la interfaz de usuario local del dispositivo, vaya a la página **Redes avanzadas** y seleccione un puerto en el que quiera habilitar el proceso. 
    - En la página **Configuración de red**, habilite el puerto para el proceso. **Aplique** la configuración.
- Tiene direcciones IP disponibles en la misma subred que el puerto que ha habilitado para el proceso en el dispositivo. 

### <a name="configuration"></a>Configuración

Para una configuración básica de MetalLB mediante la sesión BGP, necesita la siguiente información:

- Dirección IP del mismo nivel a la que debe conectarse MetalLB.
- Número de sistema autónomo (ASN) del elemento del mismo nivel. BGP requiere que las rutas se anuncien con un ASN para las sesiones del mismo nivel.
- El ASN que debe usar MetalLB. Los ASN son números de 16 bits entre 1 y 65534 y números de 32 bits entre 131072 y 4294967294.

> [!IMPORTANT]
> Para que MetalLB funcione en modo BGP, se deben especificar elementos del mismo nivel. Si no se especifica ningún elemento del mismo nivel de BGP, MetalLB funcionará en el modo de nivel 2 predeterminado. Para obtener más información, vea [Modo de nivel 2 en MetalLB](https://metallb.universe.tf/concepts/layer2/). 


Siga estos pasos para configurar MetalLB en modo BGP:

1. [Conexión a la interfaz de PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) del dispositivo.
 
1. Ejecute el cmdlet `Get-HcsExternalVirtualSwitch` para obtener el nombre del conmutador virtual externo que usará para el modo BGP. Este conmutador virtual se crea cuando se habilita el puerto para el proceso.

    ```powershell
    Get-HcsExternalVirtualSwitch
    ```
1. Ejecute el cmdlet `Set-HcsBGPPeer` para establecer una sesión de elementos del mismo nivel de BGP.

    ```powershell
    Set-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -PeerAsn <ASN for the peer> -SelfAsn <Your ASN> -SwitchName <Name of virtual switch on the port enabled for compute> -HoldTimeInSeconds <Optional hold time in seconds> 
    ```
1. Una vez establecida la sesión, ejecute el cmdlet `Get-HcsBGPPeers` para obtener las sesiones del mismo nivel que existen en un conmutador virtual.

    ```powershell
    Get-HcsBGPPeers -SwitchName <Name of virtual switch that you enabled for compute>
    ```
1. Ejecute el cmdlet `Remove-HcsBGPPeer` para quitar la sesión del mismo nivel. 

    ```powershell
    Remove-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -SwitchName <Name of virtual switch on the port enabled for compute>
    ```
1. Ejecute `Get-HcsBGPPeers` para comprobar que se ha quitado la sesión del mismo nivel.

A continuación, se incluye una salida de ejemplo: 

```powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $Name = "dbe-1csphq2.microsoftdatabox.com"
PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to
be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface
to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft
Support could result in an unsupported configuration.
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : False
EnabledForMgmt                : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 3cb2d0ae-6a7b-44cc-8a5d-8eac2d1c0436
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

Name                          : vSwitch2
InterfaceAlias                : {Port3, Port4}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : True
EnabledForMgmt                : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 8dd480c0-8f22-42b1-8621-d2a43f70690d
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

[dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsBGPPeer -PeerAddress 10.126.77.125 -PeerAsn 64512 -SelfAsn 64513 -SwitchName vSwitch1 -HoldTimeInSeconds 15
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1

PeerAddress   PeerAsn SelfAsn HoldTime
-----------   ------- ------- --------
10.126.77.125 64512   64513         15

[dbe-1csphq2.microsoftdatabox.com]: PS>Remove-HcsBGPPeer -PeerAddress 10.126.77.125 -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre las [redes en el clúster de Kubernetes en el dispositivo Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).
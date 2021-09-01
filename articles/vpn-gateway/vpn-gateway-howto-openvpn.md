---
title: Habilitación de OpenVPN para instancias de VPN Gateway de punto a sitio
titleSuffix: Azure VPN Gateway
description: Obtenga información sobre cómo habilitar el protocolo OpenVPN en instancias de VPN Gateway para configuraciones de punto a sitio.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ab7826a89dc879c1bad62e8c56415047d164c6f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729492"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Configuración de OpenVPN para instancias de VPN Gateway de punto a sitio

En este artículo le ayudamos a configurar el **protocolo OpenVPN®** en Azure VPN Gateway. Puede usar las instrucciones del portal o de PowerShell.

## <a name="prerequisites"></a>Prerrequisitos

* Se supone que ya tiene un entorno de trabajo punto a sitio. Si no es así, cree uno con uno de los métodos siguientes. Al crear la puerta de enlace, tenga en cuenta que la SKU **Básica** no admite el tipo de túnel OpenVPN.

  * [Portal: Creación de VPN de punto a sitio](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell: Creación de VPN de punto a sitio](vpn-gateway-howto-point-to-site-rm-ps.md)

* Si ya tiene una puerta de enlace de VPN, compruebe que no usa la SKU **Básica**. La SKU de nivel Básico no es compatible con OpenVPN. Para más información sobre las SKU, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Para cambiar el tamaño de una SKU básica, consulte [Cambio de tamaño de una puerta de enlace heredada](vpn-gateway-about-skus-legacy.md#resource-manager). 

## <a name="portal"></a>Portal

1. En el portal, vaya a **Puerta de enlace de red virtual -> Configuración de punto a sitio**.
1. En **Tipo de túnel**, seleccione **OpenVPN (SSL)** en la lista desplegable.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Selección de OpenVPN SSL en la lista desplegable":::
1. Guarde los cambios y continúe con los **pasos siguientes**.

## <a name="powershell"></a>PowerShell

1. Habilite OpenVPN en la puerta de enlace mediante el ejemplo siguiente, ajustando los valores según sea necesario.

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName TestRG1 -name VNet1GW
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Continúe con los **pasos siguientes**.

## <a name="next-steps"></a>Pasos siguientes

Para configurar clientes para OpenVPN, consulte [Configuración de clientes de OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" es una marca comercial de OpenVPN Inc.**

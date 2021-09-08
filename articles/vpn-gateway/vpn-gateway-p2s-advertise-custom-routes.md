---
title: Anuncio de rutas personalizadas para clientes VPN Gateway de punto a sitio
titleSuffix: Azure VPN Gateway
description: Obtenga información sobre cómo anunciar rutas personalizadas a los clientes VPN Gateway de punto a sitio. En este artículo se incluyen los pasos para la tunelización forzada del cliente VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/21/2021
ms.author: cherylmc
ms.openlocfilehash: cefbd6d014dda28a5e88a41a0131eeb92fc5f311
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440728"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anuncio de rutas personalizadas para clientes VPN de conexión de punto a sitio

Es posible que quiera anunciar rutas personalizadas a todos sus clientes VPN de punto a sitio. Por ejemplo, cuando tiene puntos de conexión de almacenamiento habilitados en su red virtual y quiere que los usuarios remotos puedan tener acceso a estas cuentas de almacenamiento a través de la conexión VPN. Puede anunciar la dirección IP del punto de conexión de almacenamiento a todos sus usuarios remotos de modo que el tráfico a la cuenta de almacenamiento pase por el túnel VPN y no por la red pública de Internet. También puede usar rutas personalizadas para configurar la tunelización forzada para los clientes VPN.

:::image type="content" source="./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png" alt-text="Diagrama para anunciar las rutas personalizadas.":::

## <a name="advertise-custom-routes"></a><a name="advertise"></a>Anuncio de rutas personalizadas

Para anunciar rutas personalizadas, use `Set-AzVirtualNetworkGateway cmdlet`. En el siguiente ejemplo se le muestra cómo anunciar la dirección IP para las [tablas de cuentas de almacenamiento de Contoso](https://contoso.table.core.windows.net).

1. Haga ping en *contoso.table.core.windows.net* y tenga en cuenta la dirección IP. Por ejemplo:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Ejecute los comandos de PowerShell siguientes:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Para agregar varias rutas personalizadas, use una coma y espacios para separar las direcciones. Por ejemplo:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```

## <a name="advertise-custom-routes---forced-tunneling"></a><a name="forced-tunneling"></a>Anuncio de rutas personalizadas: tunelización forzada

Puede dirigir todo el tráfico al túnel VPN si anuncia 0.0.0.0/1 y 128.0.0.0/1 como rutas personalizadas a los clientes. El motivo para dividir 0.0.0.0/0 en dos subredes más pequeñas es que estos prefijos más pequeños son más específicos que la ruta predeterminada que posiblemente ya esté configurada en el adaptador de red local y, como tal, se preferirán al enrutar el tráfico.

> [!NOTE]
> No se proporciona conectividad a Internet a través de VPN Gateway. Como resultado, se anula todo el tráfico enlazado a Internet.
>

1. Para habilitar la tunelización forzada, use estos comandos:

    ```azurepowershell-interactive    
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 0.0.0.0/1 , 128.0.0.0/1
    ```

## <a name="view-custom-routes"></a><a name="view"></a>Visualización de rutas personalizadas

Use el siguiente ejemplo para ver rutas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="delete-custom-routes"></a><a name="delete"></a>Eliminación de rutas personalizadas

Use el siguiente ejemplo para eliminar rutas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el enrutamiento de conexión de punto a sitio, consulte [Acerca del enrutamiento de punto a sitio](vpn-gateway-about-point-to-site-routing.md).

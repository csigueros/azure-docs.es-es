---
title: Administración de una dirección IP pública con una puerta de enlace de VPN
titleSuffix: Azure Virtual Network
description: Conozca las formas en que se usa una dirección IP pública con una puerta de enlace de VPN y cómo cambiar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 729e02427b823550a8621380261b7f7db4ff2fe5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367705"
---
# <a name="manage-a-public-ip-address-with-a-vpn-gateway"></a>Administración de una dirección IP pública con una puerta de enlace de VPN

Las direcciones IP públicas están disponibles en dos SKU: estándar y básica. La selección de la SKU determina las características de la dirección IP. La SKU determina los recursos a los que se puede asociar la dirección IP. 

Una puerta de enlace de VPN es una puerta de enlace de red virtual que se usa para enviar tráfico cifrado entre una red virtual de Azure y una ubicación local por medio de la red pública de Internet. También puede usar una instancia de VPN Gateway para enviar tráfico cifrado entre las redes virtuales de Azure a través de la red de Microsoft. Cada red virtual solo puede tener una instancia de VPN Gateway. VPN Gateway admite direcciones IP públicas de SKU estándar y básicas en función de la SKU de VPN Gateway. No se admiten prefijos de direcciones IP públicas.

Una puerta de enlace VPN Gateway requiere una dirección IP pública para su configuración. Se usa una dirección IP pública como punto de conexión externo de la VPN. 

En este artículo, aprenderá a crear una instancia de VPN Gateway mediante una dirección IP pública existente en su suscripción. 

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Una dirección IP pública de SKU estándar en la suscripción. La dirección IP no puede estar asociada a ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](../../virtual-network/create-public-ip-portal.md).
    - Para los fines de los ejemplos de este artículo, asigne a las nuevas direcciones IP públicas el nombre **myStandardPublicIP**.

## <a name="create-vpn-gateway-existing-public-ip"></a>Creación de una dirección IP pública existente de VPN Gateway

En esta sección, creará una instancia de VPN Gateway. Seleccionará la dirección IP que creó en los requisitos previos como dirección IP pública para VPN Gateway.

### <a name="create-virtual-network"></a>Creación de una red virtual

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**.

3. En los resultados de la búsqueda, seleccione **Redes virtuales**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear red virtual**, escriba o seleccione esta información.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroupVPN**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myVNet**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    
6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**.

7. Seleccione **Crear**.

8. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**.

9. En los resultados de la búsqueda, seleccione **Redes virtuales**.

10. Seleccione **myVNET** en **Redes virtuales**.

11. Seleccione **Subredes** en **Configuración** de **myVNET.**

12. Seleccione **+ Subred de puerta de enlace**.

13. En **Agregar subred**, cambie el **Intervalo de direcciones de subred** de **/24** a **/27**.

14. Seleccione **Guardar**.

### <a name="create-vpn-gateway"></a>Creación de una puerta de enlace de VPN

2. En el cuadro de búsqueda de la parte superior del portal, escriba **Puerta de enlace de red virtual**.

3. En los resultados de la búsqueda, seleccione **Puertas de enlace de red virtual**.

4. Seleccione **+ Create** (+ Crear).

5. En el cuadro de diálogo **Crear puerta de enlace de red virtual**, escriba o seleccione la siguiente información.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Subscription | Seleccione su suscripción. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myVPNGateway**. |
    | Region | Seleccione **Oeste de EE. UU. 2**. |
    | Tipo de puerta de enlace | Deje el valor predeterminado **VPN**. |
    | Tipo de VPN | Deje el valor predeterminado **Basada en rutas**. |
    | SKU | Seleccione **VpnGw1AZ**. |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | La entrada seleccionará automáticamente la opción **GatewaySubnet** que creó anteriormente. |
    | **Dirección IP pública** |   |
    | Dirección IP pública | Seleccione **Usar existente**. |
    | Elección de una IP pública | Seleccione **myStandardPublicIP** o su dirección IP pública. |
    | Habilitación del modo activo-activo | Deje el valor predeterminado, **Deshabilitado**. |
    | Configuración de BGP | Deje el valor predeterminado, **Deshabilitado**. |

6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**.

7. Seleccione **Crear**.

> [!NOTE]
> Se trata de una implementación sencilla de VPN Gateway. Para información sobre la configuración avanzada, consulte [Tutorial: Creación y administración de una puerta de enlace VPN Gateway mediante Azure Portal](../../vpn-gateway/tutorial-create-gateway-portal.md).
>
> Para más información sobre Azure VPN Gateway, consulte [¿Qué es VPN Gateway?](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="change-or-remove-public-ip-address"></a>Cambio o eliminación de IP pública

VPN Gateway no permite cambiar la IP pública después de la creación.

## <a name="caveats"></a>Advertencias

* Las direcciones IPv6 públicas no se admiten para las puertas de enlace VPN Gateway en este momento.
 
## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear un a instancia de VPN Gateway y a usar una dirección IP pública existente. 

- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](../../virtual-network/public-ip-addresses.md).
- Para más información sobre las instancias de VPN Gateway, consulte [¿Qué es VPN Gateway?](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
---
title: 'Modificación de la configuración de la dirección IP de puerta de enlace: CLI de Azure'
titleSuffix: Azure VPN Gateway
description: Aprenda a cambiar los prefijos de dirección IP de la puerta de enlace de red local mediante la CLI de Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/28/2021
ms.author: cherylmc
ms.openlocfilehash: 5a11221cc11e99cf989f1fa50f851b51754c7071
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474276"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificación de la configuración de la puerta de enlace de red local mediante la CLI de Azure

A veces, cambia la configuración Prefijo de dirección o Dirección IP de la puerta de enlace de la puerta de enlace de red local. Este artículo muestra cómo modificar la configuración de puerta de enlace de red local. También puede modificar estas configuraciones mediante un método diferente, si selecciona una opción diferente de la lista siguiente:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI de Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

>[!NOTE]
> La realización de cambios en una puerta de enlace de red local que tiene una conexión puede provocar desconexiones de túneles y tiempo de inactividad.
>

## <a name="before-you-begin"></a><a name="before"></a>Antes de empezar

Instale la versión más reciente de los comandos de la CLI (2.0 o posteriores). Para obtener más información sobre la instalación de los comandos de la CLI, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificación de los prefijos de direcciones IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificación de la dirección IP de la puerta de enlace

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Puede comprobar la conexión de la puerta de enlace. Consulte [Comprobación de una conexión de puerta de enlace](vpn-gateway-verify-connection-resource-manager.md).
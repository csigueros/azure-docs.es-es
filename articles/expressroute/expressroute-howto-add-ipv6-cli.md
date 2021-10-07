---
title: 'Azure ExpressRoute: Adición de compatibilidad con IPv6 mediante la CLI de Azure'
description: Aprenda a agregar compatibilidad con IPv6 para conectarse a implementaciones de Azure mediante la CLI de Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: c93d56cb7f8cc6ffe897cb8a9321806b4a2c40ed
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094888"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-cli-preview"></a>Adición de compatibilidad con IPv6 para el emparejamiento privado mediante la CLI de Azure (versión preliminar)

En este artículo se describe cómo agregar compatibilidad con IPv6 para conectarse a través de ExpressRoute a los recursos de Azure mediante la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

* Revise los [Requisitos previos y lista de comprobación de ExpressRoute](expressroute-prerequisites.md) y los [Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute](expressroute-workflows.md) antes de comenzar la configuración.
* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale la versión más reciente de los comandos de la CLI (2.0 o posteriores). Para más información acerca de la instalación de los comandos de la CLI, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli.md) y [Introducción a la CLI de Azure](/cli/azure/get-started-with-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adición de emparejamiento privado IPv6 al circuito ExpressRoute

1. [Cree un circuito ExpressRoute](howto-circuit-cli.md) o use un circuito existente. Para ver los detalles del circuito, ejecute el siguiente comando:

    ```azurecli-interactive
    az network express-route show --resource-group "<ExpressRouteResourceGroup>" --name "<MyCircuit>"
    ```

2. Para ver la configuración del emparejamiento privado del circuito, ejecute el siguiente comando:

    ```azurecli-interactive
    az network express-route peering show -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering
    ```

3. Agregue un emparejamiento privado IPv6 a la configuración de emparejamiento privado IPv4 existente. Proporcione un par de subredes IPv6 /126 que posea para el vínculo principal y los vínculos secundarios. Desde cada una de estas subredes, asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador.

    ```azurecli-interactive
    az network express-route peering update -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering --ip-version ipv6 --primary-peer-subnet "<X:X:X:X/126>" --secondary-peer-subnet "<Y:Y:Y:Y/126>"
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Actualización de la conexión a una red virtual existente

Siga los pasos que se indican a continuación si tiene un entorno existente de recursos de Azure con el que quisiera usar el emparejamiento privado IPv6.

1. Agregue un espacio de direcciones IPv6 a la red virtual a la que está conectado el circuito ExpressRoute.

    ```azurecli-interactive
    az network vnet update -g "<MyResourceGroup>" -n "<MyVNet>" --address-prefixes "X:X:X:X::/64"
    ```

3. Agregue el espacio de direcciones IPv6 a la subred de la puerta de enlace. La subred IPv6 de la puerta de enlace debe ser /64 o superior.

    ```azurecli-interactive
    az network vnet subnet update -g "<MyResourceGroup>" -n "<MySubnet>" -vnet-name "<MyVNet>" --address-prefixes "10.0.0.0/26", "X:X:X:X::/64"
    ```

4. Si tiene una puerta de enlace con redundancia de zona existente, ejecute lo siguiente para habilitar la conectividad IPv6 (tenga en cuenta que los cambios pueden tardar hasta una hora en reflejarse). De lo contrario, [cree la puerta de enlace de red virtual](expressroute-howto-add-gateway-resource-manager.md) mediante cualquier SKU. Si tiene previsto usar FastPath, use UltraPerformance o ErGw3AZ (tenga en cuenta que esto solo está disponible para los circuitos mediante ExpressRoute Direct).

    ```azurecli-interactive
    az network vnet-gateway update --name "<GatewayName>" --resource-group "<MyResourceGroup>"
    ```
>[!NOTE]
> Si tiene una puerta de enlace existente sin redundancia de zona (es decir, una SKU estándar, de alto rendimiento o de ultrarrendimiento) y usa una dirección IP pública de SKU básica, tendrá que eliminar y [volver a crear la puerta de enlace](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) con cualquier SKU y una dirección IP pública estándar y estática.

## <a name="create-a-connection-to-a-new-virtual-network"></a>Creación de una conexión a una nueva red virtual

Siga los pasos que se indican a continuación si tiene previsto conectarse a un nuevo conjunto de recursos de Azure mediante el emparejamiento privado IPv6.

1. Cree una red virtual de doble pila con espacios de direcciones IPv4 e IPv6. Para obtener más información, consulte [Creación de una red virtual](../virtual-network/quick-create-cli.md).

2. [Creación de la subred de puerta de enlace de doble pila](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. [Creación de la puerta de enlace de red virtual](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) mediante cualquier SKU. Si tiene previsto usar FastPath, use UltraPerformance o ErGw3AZ (tenga en cuenta que esto solo está disponible para los circuitos mediante ExpressRoute Direct).

4. [Vincule la red virtual a su circuito ExpressRoute](howto-linkvnet-cli.md).

## <a name="limitations"></a>Limitaciones
Aunque la compatibilidad con IPv6 está disponible para las conexiones a las implementaciones en regiones globales de Azure, no se admiten los siguientes casos de uso:

* Conexiones a puertas de enlace de ExpressRoute *existentes* que no tienen redundancia de zona. Tenga en cuenta que las puertas de enlace de ExpressRoute *recién* creadas de cualquier SKU (con y sin redundancia de zona) mediante una dirección IP estándar y estática se pueden usar para las conexiones de ExpressRoute de doble pila
* Conexiones Global Reach entre circuitos ExpressRoute
* Uso de ExpressRoute con WAN virtual
* FastPath con circuitos que no son de ExpressRoute Direct
* FastPath con circuitos en las siguientes ubicaciones de emparejamiento: Dubái
* Coexistencia con VPN Gateway

## <a name="next-steps"></a>Pasos siguientes

Para solucionar problemas de ExpressRoute, consulte los siguientes artículos:

* [Comprobación de la conectividad de ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solución de problemas de rendimiento de red](expressroute-troubleshooting-network-performance.md)

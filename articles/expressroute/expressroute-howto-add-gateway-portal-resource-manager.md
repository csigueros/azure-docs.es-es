---
title: 'Tutorial: Azure ExpressRoute - Agregar una puerta de enlace a una red virtual (Azure Portal)'
description: En este tutorial, se le guiará para agregar una puerta de enlace de red virtual a una red virtual para ExpressRoute mediante Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ab04c21cb904baf7ede72fd69f6d6b8375b07cc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289882"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Tutorial: Configuración de una puerta de enlace de red virtual para ExpressRoute con Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager: Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clásico: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

En este tutorial se muestran los pasos para agregar una puerta de enlace de red virtual para una red virtual existente. Este artículo lo guía por los pasos para agregar, cambiar el tamaño y quitar una puerta de enlace de red virtual (VNet) para una red virtual existente. Los pasos de esta configuración son específicos para las redes virtuales que se crearon con el modelo de implementación de Resource Manager que se utilizarán en una configuración ExpressRoute. Para obtener más información acerca de las puertas de enlace de red virtual y la configuración de puerta de enlace para ExpressRoute, consulte [Acerca de las puertas de enlace de red virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md). 

En este tutorial, obtendrá información sobre cómo:
> [!div class="checklist"]
> - Cree una subred de puerta de enlace.
> - Cree una puerta de enlace de red virtual.

## <a name="prerequisites"></a>Requisitos previos

Los pasos de esta tarea usan una red virtual que se basa en los valores de la siguiente lista de referencia de configuración. Usamos esta lista en nuestros pasos de ejemplo. Puede copiar la lista para utilizarla como referencia y reemplazar los valores por los suyos propios.

**Lista de referencia de configuración**

* Nombre de red virtual = "TestVNet"
* Espacio de direcciones de red virtual = 192.168.0.0/16
* Nombre de subred = "FrontEnd" 
    * Espacio de direcciones de subred = "192.168.1.0/24"
* Grupo de recursos: "TestRG"
* Ubicación: = "Este de EE. UU."
* Nombre de subred de puerta de enlace: "GatewaySubnet" (siempre debe asignar a las subredes de puerta de enlace el nombre *GatewaySubnet*).
    * Espacio de direcciones de subred de puerta de enlace = "192.168.200.0/26"
* Nombre de puerta de enlace = "ERGW"
* Nombre de IP pública de puerta de enlace = "MyERGWVIP"
* Tipo de puerta de enlace = "ExpressRoute" (Este tipo es obligatorio para una configuración de ExpressRoute).

Puede ver un [vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) de estos pasos antes de comenzar la configuración.

> [!IMPORTANT]
> La compatibilidad de IPv6 con el emparejamiento privado se encuentra actualmente en **versión preliminar pública**. Si desea conectar la red virtual a un circuito ExpressRoute con el emparejamiento privado basado en IPv6 configurado, asegúrese de que la red virtual es de doble pila y sigue las instrucciones de [IPv6 para Azure Virtual Network](../virtual-network/ipv6-overview.md).
> 
> 

## <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace

1. En el [portal](https://portal.azure.com), navegue a la red virtual de Resource Manager para la que desea crear una puerta de enlace de red virtual.
1. En la sección **Configuración** de la red virtual, seleccione **Subredes** para expandir la configuración de las subredes.
1. En la configuración de **Subredes**, seleccione **+ Subred de puerta de enlace** para agregar una subred de puerta de enlace. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Agregue la subred de puerta de enlace":::

1. El **nombre** de la subred se rellena automáticamente con el valor "GatewaySubnet". Este valor es necesario para que Azure reconozca que se trata de subred de puerta de enlace. Modifique los valores de **Intervalo de direcciones** rellenados automáticamente para ajustarlos a sus requisitos de configuración. Se recomienda crear una subred de puerta de enlace con un /27 o superior (/ 26, / 25, etc.). Si planea conectar 16 circuitos ExpressRoute a la puerta de enlace, **debe** crear una subred de puerta de enlace de tamaño /26 o mayor.

    Si usa una red virtual de pila dual y planea usar el emparejamiento privado basado en IPv6 a través de ExpressRoute, haga clic en **Agregar un espacio de direcciones IPv6** e indique los valores del **Intervalo de direcciones IPv6**.

    A continuación, seleccione **Aceptar** para guardar los valores y crear la subred de puerta de enlace.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Adición de la subred":::

## <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

1. En el portal, a la izquierda, seleccione **Crear un recurso** y escriba "Virtual Network Gateway" en la búsqueda. Busque **Puerta de enlace de red virtual** en los resultados de la búsqueda y seleccione la entrada. En la página **Puerta de enlace de red virtual**, seleccione **Crear**.
1. En la página **Crear puerta de enlace de red virtual**, escriba o seleccione estos valores:

    | Parámetro | Value |
    | --------| ----- |
    | Suscripción | compruebe que se selecciona la suscripción correcta. |
    | Grupo de recursos | El grupo de recursos se elegirá automáticamente una vez seleccionada la red virtual. | 
    | Nombre | Asigne un nombre a la puerta de enlace. Esta acción no es igual a la de asignación de un nombre a una subred de puerta de enlace. Este es el nombre del objeto de puerta de enlace que va a crear.|
    | Region | Cambie el campo **Región** para que apunte a la ubicación en la que se encuentra la red virtual. Si la ubicación no apunta a la región en que está la red virtual, esta no aparece en la lista desplegable "Elegir una red virtual". |
    | Tipo de puerta de enlace | Seleccione **ExpressRoute**.|
    | SKU | seleccione la SKU de puerta de enlace en la lista desplegable. |
    | Virtual network | Seleccione *TestVNet*. |
    | Dirección IP pública | Seleccione **Crear nuevo**.|
    | Nombre de la dirección IP pública | Proporcione un nombre para la dirección IP pública. |

    > [!IMPORTANT]
    > Si planea usar el emparejamiento privado basado en IPv6 a través de ExpressRoute, consulte la [documentación de PowerShell](./expressroute-howto-add-gateway-resource-manager.md) para crear la puerta de enlace con una dirección IP pública de tipo Estándar, Estática.
    > 
    > 

1. Seleccione **Revisar y crear** y, después, **Crear** para empezar a crear la puerta de enlace. Se valida la configuración y se implementa la puerta de enlace. La creación de una puerta de enlace de red virtual puede tardar en completarse hasta 45 minutos.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Campos de la página Crear puerta de enlace de red virtual":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita la puerta de enlace de ExpressRoute, localice la puerta de enlace en el grupo de recursos de la red virtual y seleccione **Eliminar**. Asegúrese de que la puerta de enlace no tiene ninguna conexión con un circuito.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Eliminar puerta de enlace de red virtual":::

## <a name="next-steps"></a>Pasos siguientes
Después de crear la puerta de enlace de red virtual, puede vincular la red virtual a un circuito ExpressRoute. 

> [!div class="nextstepaction"]
> [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
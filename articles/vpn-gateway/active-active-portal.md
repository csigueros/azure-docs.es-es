---
title: 'Configuración de puertas de enlace de VPN activo-activo: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Aprenda a configurar puertas de enlace de red virtual activo-activo mediante Azure Portal.
services: vpn-gateway
author: JackStromberg
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/22/2021
ms.author: jstrom
ms.openlocfilehash: 1a5ca58b9e56826c7173c499b64ebabc80ddf11e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746180"
---
# <a name="configure-active-active-vpn-gateways-using-the-portal"></a>Configuración de puertas de enlace de VPN activo-activo mediante Azure Portal

Este artículo le ayuda a crear puertas de enlace de VPN activo-activo de alta disponibilidad mediante el [modelo de implementación de Resource Manager](../azure-resource-manager/management/deployment-models.md) y Azure Portal. También puede configurar una puerta de enlace activo-activo mediante [PowerShell](vpn-gateway-activeactive-rm-powershell.md).

Para lograr una alta disponibilidad en la conectividad de red virtual a red virtual y entre entornos locales, debe implementar varias puertas de enlace VPN y establecer varias conexiones en paralelo entre sus redes y Azure. Consulte [Conectividad de alta disponibilidad entre locales y de red virtual a red virtual](vpn-gateway-highlyavailable.md) para obtener información general de las opciones de conectividad y la topología.

> [!IMPORTANT]
> El modo activo-activo está disponible para todas las SKU, excepto Basic o Standard. Para obtener más información, consulte [Opciones de configuración](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
>

Los pasos de este artículo le ayudarán a configurar una puerta de enlace de VPN en modo activo-activo. Hay algunas diferencias entre los modos activo-activo y activo-en espera. Las demás propiedades son las mismas que las de las puertas de enlace que no son activo-activo. 

* Las puertas de enlace activo-activo tienen dos configuraciones IP de puerta de enlace y dos direcciones IP públicas.
* Las puertas de enlace activo-activo tienen habilitada la configuración activo-activo.
* La SKU de puerta de enlace de red virtual no puede ser Basic ni Standard.

Si ya tiene una puerta de enlace de VPN, puede [actualizar una puerta de enlace de VPN](#update) existente de modo activo-en espera a activo-activo, o de modo activo-activo a modo activo-en espera.

## <a name="create-a-vnet"></a><a name="vnet"></a>Creación de una red virtual

Si aún no tiene una red virtual que quiera usar, cree una con los valores siguientes:

* **Grupo de recursos:** TestRG1
* **Nombre:** VNet1
* **Región:** (EE. UU.) Este de EE. UU.
* **Espacio de direcciones IPv4:** 10.1.0.0/16
* **Nombre de subred:** FrontEnd
* **Espacio de direcciones de subred:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-an-active-active-vpn-gateway"></a><a name="gateway"></a>Creación de una puerta de enlace de VPN activo-activo

En este paso, se crea una puerta de enlace de red virtual activo-activo (puerta de enlace VPN) para la red virtual. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada.

Cree una puerta de enlace de red virtual con los siguientes valores:

* **Nombre:** VNet1GW
* **Región:** Este de EE. UU.
* **Tipo de puerta de enlace:** VPN
* **Tipo de VPN:** basada en rutas
* **SKU:** VpnGw2
* **Generación:** Generación 2
* **Red virtual:** VNet1
* **Intervalo de direcciones de subred de puerta de enlace:** 10.1.255.0/27
* **Dirección IP pública**: Crear nuevo
* **Dirección IP pública:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-active-portal-include.md)]

Puede ver el estado de implementación en la página Información general de la puerta de enlace. Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparece como un dispositivo conectado.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="update-an-existing-vpn-gateway"></a><a name ="update"></a> Actualización de una instancia de VPN Gateway existente

En esta sección, le ayudamos a cambiar una instancia de Azure VPN Gateway existente del modo activo-en espera al modo activo-activo o viceversa. Al cambiar una puerta de enlace de activo-en espera a activo-activo, crea otra dirección IP pública y después agrega una segunda configuración IP de puerta de enlace. 

### <a name="change-active-standby-to-active-active"></a>Cambio del modo activo-en espera a activo-activo

Use los pasos siguientes para convertir la puerta de enlace del modo activo-en espera al modo activo-activo. Si la puerta de enlace se creó con el [modelo de implementación de Resource Manager](../azure-resource-manager/management/deployment-models.md), también puede actualizar la SKU en esta página.

1. Vaya a la página de la puerta de enlace de red virtual.

1. En el menú de la izquierda, seleccione **Configuración**.

1. En la página **Configuración**, defina las siguientes opciones: 

   * Cambie el modo activo-activo a **Habilitado**.
   * Haga clic en **Crear otra configuración de IP de puerta de enlace**.

   :::image type="content" source="./media/active-active-portal/configuration.png" alt-text="Captura de pantalla que muestra la página Configuración.":::

1. En la página **Elegir dirección IP pública** especifique una dirección IP pública existente que cumpla los criterios, o seleccione **+Crear nuevo** para crear una nueva dirección IP pública que se usará para la segunda instancia de VPN Gateway.

1. En la página **Crear dirección IP pública**, seleccione la SKU **Basic** y, luego, haga clic en, **Aceptar**.

1. En la parte superior de la página **Configuración**, haga clic en **Guardar**. Esta actualización puede tardar unos 30-45 minutos en completarse.

### <a name="change-active-active-to-active-standby"></a>Cambio del modo activo-activo a activo-en espera

Use los pasos siguientes para convertir la puerta de enlace del modo activo-activo al modo activo-en espera.

1. Vaya a la página de la puerta de enlace de red virtual.

1. En el menú de la izquierda, seleccione **Configuración**.

1. En la página **Configuración**, cambie el modo activo-activo a **Deshabilitado**.

1. En la parte superior de la página **Configuración**, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

Para configurar las conexiones, consulte los artículos siguientes:

* [Conexiones VPN de sitio a sitio](./tutorial-site-to-site-portal.md)
* [Conexiones de red virtual a red virtual](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md#configure-the-vnet1-gateway-connection)
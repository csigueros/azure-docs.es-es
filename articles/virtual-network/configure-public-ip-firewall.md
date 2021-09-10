---
title: Administración de una dirección IP pública con Azure Firewall
titleSuffix: Azure Virtual Network
description: Conozca las formas en que se usa una dirección IP pública con Azure Firewall y cómo cambiar la configuración.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 1ffb1242dfded6af9c869a53367016d30dd61f49
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439525"
---
# <a name="manage-a-public-ip-address-with-azure-firewall"></a>Administración de una dirección IP pública con Azure Firewall

Azure Firewall es un servicio de seguridad de red basado en la nube que protege los recursos de Azure Virtual Network. Azure Firewall requiere que se configure al menos una dirección IP estática pública. Esta dirección IP o conjunto de direcciones IP se usan como punto de conexión externo al firewall. Azure Firewall admite direcciones IP públicas de SKU estándar. No se admiten la dirección IP pública de SKU básica ni los prefijos de IP pública. 

En este artículo, aprenderá a crear una instancia de Azure Firewall mediante una dirección IP pública existente en su suscripción. Cambiará la configuración IP del firewall. Por último, agregará una configuración de IP al firewall.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Tres direcciones IP públicas de SKU estándar en la suscripción. La dirección IP no puede estar asociada a ningún recurso. Para más información sobre cómo crear una IP pública de SKU estándar, consulte [Creación de una dirección IP pública: Azure Portal](create-public-ip-portal.md).
    - Para los fines de los ejemplos de este artículo, nombre las nuevas direcciones IP públicas **myStandardPublicIP-1**, **myStandardPublicIP-2** y **myStandardPublicIP-3**.

## <a name="create-azure-firewall-existing-public-ip"></a>Creación de una instancia de Azure Firewall con una dirección IP pública existente

En esta sección, creará una instancia de Azure Firewall. Seleccionará la dirección IP que creó en los requisitos previos como dirección IP pública del firewall.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Firewall**.

3. En los resultados de la búsqueda, seleccione **Firewalls**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear firewall**, especifique o seleccione la información siguiente.

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroupFW**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myFirewall**. |
    | Region | Seleccione **Oeste de EE. UU. 2**. |
    | Zona de disponibilidad | Deje el valor predeterminado de **No**. |
    | Nivel de firewall  | Deje el valor predeterminado **Estándar**. |
    | Administración del firewall | Deje el valor predeterminado de **Use a Firewall Policy to manage this firewall** (Usar una directiva de firewall para administrar este firewall).|
    | Directiva de firewall | Seleccione **Agregar nuevo**. </br> Escriba **myFirewallPolicy** en **Nombre de directiva**. </br> En **Región**, seleccione **Oeste de EE. UU. 2**. </br> Seleccione **Sí**. |
    | Nombre de la red virtual | Escriba **myVNet**. |
    | Espacio de direcciones | Escriba **10.0.0.0/16**. |
    | Espacio de direcciones de subred | Escriba **10.0.0.0/26**. |
    | Dirección IP pública | Seleccione **myStandardPublicIP-1** o su dirección IP pública. |
    | Tunelización forzada | Deje el valor predeterminado, **Deshabilitado**. |
    
 
6. Seleccione la pestaña **Revisar y crear** o seleccione el botón **Revisar y crear**.

7. Seleccione **Crear**.

> [!NOTE]
> Esta es una implementación sencilla de Azure Firewall. Para información sobre configuración e instalación avanzadas, consulte [Tutorial: Implementación y configuración de Azure Firewall y directivas mediante Azure Portal](../firewall/tutorial-firewall-deploy-portal-policy.md).
>
> Para más información sobre Azure Firewall, consulte [¿Qué es Azure Firewall?](../firewall/overview.md)

## <a name="change-public-ip-address"></a>Cambio de la dirección IP pública

En esta sección, cambiará la dirección IP pública asociada al firewall. Un firewall debe tener al menos una dirección IP pública asociada a su configuración. 

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Firewall**.

2. En los resultados de la búsqueda, seleccione **Firewalls**.

3. En **Firewalls**, seleccione **myFirewall**.

4. En **Configuración**, en **myFirewall**, seleccione **Configuración de IP pública**.

5. En **Configuración de IP pública**, seleccione **myStandardPublicIP-1** o su dirección IP.

6. En **Dirección IP pública** de **Editar la configuración de una IP pública**, seleccione **myStandardPublicIP-2**.

7. Seleccione **Guardar**.

## <a name="add-public-ip-configuration"></a>Adición de una configuración de IP pública

En esta sección, agregará una configuración de IP pública a Azure Firewall. Para más información sobre varias direcciones IP, consulte [Varias direcciones IP públicas](../firewall/features.md#multiple-public-ip-addresses).  

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Firewall**.

2. En los resultados de la búsqueda, seleccione **Firewalls**.

3. En **Firewalls**, seleccione **myFirewall**.

4. En **Configuración**, en **myFirewall**, seleccione **Configuración de IP pública**.

5. Seleccione **+ Add public IP configuration** (+ Agregar una configuración de IP pública).

6. En **Nombre**, escriba **myNewPublicIPconfig**.

7. En **Dirección IP pública**, seleccione **myStandardPublicIP-3**.

8. Seleccione **Agregar**.

## <a name="more-information"></a>Más información

* Una instancia de Azure Firewall se puede integrar con un equilibrador de carga de SKU estándar para proteger los recursos del grupo de back-end.  Si asocia el firewall a un equilibrador de carga público, configure el tráfico de entrada para que se dirija a la dirección IP pública del firewall. Configure la salida a través de una ruta definida por el usuario a la dirección IP pública del firewall.  Para más información e instrucciones de instalación, consulte [Integración de Azure Firewall con Azure Standard Load Balancer](../firewall/integrate-lb.md). 

* Una instancia de Azure Firewall también se puede asociar a una puerta de enlace NAT para ampliar la extensibilidad de la traducción de direcciones de red de origen (SNAT). Una puerta de enlace NAT evita configuraciones que permiten el tráfico procedente de un gran número de direcciones IP públicas asociadas al firewall. Con esta configuración, todo el tráfico entrante usará la dirección IP pública o las direcciones de la puerta de enlace NAT. El tráfico sale por la dirección o las direcciones IP públicas de Azure Firewall.  Para más información, consulte [Escalado de puertos SNAT con Azure NAT Gateway](../firewall/integrate-with-nat-gateway.md).

## <a name="caveats"></a>Advertencias

* Azure Firewall usa el equilibrador de carga de SKU estándar. No se admiten protocolos distintos de TCP y UDP en las reglas de filtro de red para la traducción de direcciones de red de origen en la dirección IP pública del firewall. 
## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear una instancia de Azure Firewall y a usar una dirección IP pública existente. Ha cambiado la IP pública de la configuración IP predeterminada. Por último, ha agregado una configuración de IP pública al firewall.

- Para más información sobre las IP públicas en Azure, consulte [Direcciones IP públicas](public-ip-addresses.md).
- Para más información sobre Azure Firewall, consulte [¿Qué es Azure Firewall?](../firewall/overview.md)

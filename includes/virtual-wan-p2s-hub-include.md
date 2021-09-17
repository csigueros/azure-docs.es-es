---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 6e09f3d115ad11bc6876092b8c4e87cc56c3c22a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779913"
---
1. En la página de **Virtual WAN**, en el panel de la izquierda, seleccione **Centros de conectividad**. En la página **Centros**, seleccione **+Nuevo centro de conectividad**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="Captura de pantalla del nuevo centro de conectividad.":::

1. En la página **Crear centro de conectividad virtual**, vea la pestaña **Información básica**.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="Captura de pantalla de la creación de un centro de conectividad.":::

1. En la pestaña **Información básica**, configure los siguientes valores:

   * **Región**: seleccione la región en la que quiere implementar el centro de conectividad virtual.
   * **Nombre**: nombre por el que desea que se conozca el centro de conectividad virtual.
   * **Espacio de direcciones privadas del centro de conectividad**: intervalo de direcciones del centro de conectividad en la notación CIDR.

1. Haga clic en la pestaña **Punto a sitio** para abrir la página de configuración correspondiente. Para ver la configuración de punto a sitio, haga clic en **Sí**.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="Captura de pantalla de la configuración del centro de conectividad virtual con la opción de punto a sitio seleccionada.":::

1. Configure las siguientes opciones:

   * **Unidades de escalado de puerta de enlace**: estas representan la capacidad agregada de la puerta de enlace de VPN del usuario. Si selecciona 40 o más unidades de escalado de puerta de enlace, planee el grupo de direcciones de cliente en consecuencia. Para más información sobre cómo esta configuración afecta al grupo de direcciones de cliente, consulte [Acerca de los grupos de direcciones de cliente](../articles/virtual-wan/about-client-address-pools.md). Para obtener información sobre las unidades de escalado de puerta de enlace, vea [Preguntas más frecuentes](../articles/virtual-wan/virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Configuración de punto a sitio**: seleccione la configuración de VPN de usuario que creó en un paso anterior.
   * **Preferencia de enrutamiento**: las preferencias de enrutamiento de Azure le permiten elegir cómo se enruta el tráfico entre Azure e Internet. Puede optar por enrutar el tráfico a través de la red de Microsoft o a través de una red de ISP (Internet público). Estas opciones también se conocen como enrutamiento de tipo "cold-potato" y enrutamiento de tipo "hot-potato" respectivamente. El servicio asigna la IP pública de Virtual WAN en función de la opción de enrutamiento seleccionada. Para obtener más información sobre las preferencias de enrutamiento mediante la red de Microsoft o el ISP, vea el artículo [Preferencias de enrutamiento](../articles/virtual-network/routing-preference-overview.md).
   * **Grupo de direcciones de cliente**: el grupo de direcciones desde el que las direcciones IP se asignarán automáticamente a los clientes VPN. Para más información, consulte [Acerca de los grupos de direcciones de cliente](../articles/virtual-wan/about-client-address-pools.md).
   * **Servidores DNS personalizados**: la dirección IP de los servidores DNS que usarán los clientes. Puede especificar hasta 5.

1. Seleccione **Revisar y crear** para validar la configuración.

1. Cuando se supera la validación, seleccione **Crear**. La creación de un centro puede tardar 30 minutos o más en completarse.
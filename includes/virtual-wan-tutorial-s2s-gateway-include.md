---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 1169909f0cae9b8bcee5939dd00f497c5e1ab2df
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515228"
---
1. En la página **Crear centro de conectividad virtual**, haga clic en **Sitio a sitio** para abrir la pestaña **Sitio a sitio**.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/hub-site-to-site.png" alt-text="Captura de pantalla que muestra el panel Crear centro de conectividad virtual con Site to site (De sitio a sitio) seleccionado.":::

1. En la pestaña **Sitio a sitio**, rellene los campos siguientes:

   * Seleccione **Sí** para crear una VPN de sitio a sitio.
   * **Número AS**: el campo Número AS no se puede modificar.
   * **Unidades de escalado de puerta de enlace**: en la lista desplegable, seleccione el valor **Unidades de escalado de puerta de enlace**. La unidad de escalado permite elegir el rendimiento agregado de la puerta de enlace de VPN que se va a crear en el centro de conectividad virtual para conectar los sitios. 

     Si elige 1 unidad de escalado = 500 Mbps, esto implica que se crearán dos instancias de redundancia, cada una con un rendimiento máximo de 500 Mbps. Por ejemplo, si tuviera cinco ramas, cada una con 10 Mbps en la rama, necesitará un agregado de 50 Mbps en el extremo principal. El planeamiento de la capacidad agregada de la puerta de enlace de VPN de Azure debe realizarse después de evaluar la capacidad necesaria para admitir el número de ramas en el centro de conectividad.
   * **Preferencia de enrutamiento**: las preferencias de enrutamiento de Azure le permiten elegir cómo se enruta el tráfico entre Azure e Internet. Puede optar por enrutar el tráfico a través de la red de Microsoft o a través de una red de ISP (Internet público). Estas opciones también se conocen como enrutamiento de tipo "cold-potato" y enrutamiento de tipo "hot-potato" respectivamente. 

     El servicio asigna la dirección IP pública de Virtual WAN en función de la opción de enrutamiento seleccionada. Para obtener más información sobre las preferencias de enrutamiento mediante la red de Microsoft o el ISP, vea el artículo [Preferencias de enrutamiento](../articles/virtual-network/routing-preference-overview.md).
1. Seleccione **Revisar y crear** para validar.
1. Seleccione **Crear** para crear el centro de conectividad y la puerta de enlace. Esta operación puede tardar hasta treinta minutos. Pasados 30 minutos haga clic en **Actualizar** para ver el centro de conectividad en la página **Centros**. Seleccione **Ir al recurso** para ir al recurso.
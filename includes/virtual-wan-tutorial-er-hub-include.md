---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: f5d641a1d017f40081b311e9b9b610b7fcc7c13c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820841"
---
1. Ubique la red WAN virtual que ha creado. En la página de Virtual WAN, en la sección **Conectividad**, seleccione **Centros**. Haga clic en **Nuevo centro de conectividad** para abrir la página **Crear centro de conectividad virtual**.

   :::image type="content" source="media/virtual-wan-empty-hub/new-hub.jpg" alt-text="Captura de pantalla que muestra el cuadro de diálogo Configuración de centros de conectividad con la opción Nuevo centro de conectividad seleccionada.":::

1. En la página **Crear centro de conectividad virtual**, complete los campos.

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/create-hub.png" alt-text="Captura de pantalla en la que se muestra la pestaña Aspectos básicos donde puede especificar valores.":::

   * **Región**: seleccione la región en la que quiere implementar el centro de conectividad virtual.
   * **Nombre**: nombre por el que desea que se conozca el centro de conectividad virtual.
   * **Espacio de direcciones privadas del centro de conectividad**: intervalo de direcciones del centro de conectividad en la notación CIDR.

1. Seleccione la **pestaña ExpressRoute**. Haga clic en **Sí** para mostrar la configuración y rellenar el campo. Para obtener información sobre las unidades de escalado de puerta de enlace, vea las [Preguntas más frecuentes](../articles/virtual-wan/virtual-wan-faq.md#what-are-virtual-wan-gateway-scale-units).

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/expressroute.png" alt-text="Captura de pantalla en la que se muestra la pestaña ExpressRoute donde puede especificar valores.":::

1. Seleccione **Revisar y crear** para validar.
1. Seleccione **Crear** para crear el centro de conectividad con una puerta de enlace de ExpressRoute. Un centro de conectividad puede tardar unos 30 minutos en completarse. Pasados 30 minutos haga clic en **Actualizar** para ver el centro de conectividad en la página **Centros**. Seleccione **Ir al recurso** para ir al recurso.
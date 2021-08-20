---
title: Archivo de inclusión
description: Archivo de inclusión
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1abf76ee8e6c44eaa61d9de38b163f230327edd
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362349"
---
1. En la página de la red WAN virtual, en el panel de la izquierda, seleccione **Centros**. En la página **Centros**, seleccione **+Nuevo centro de conectividad**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="Captura de pantalla del nuevo centro de conectividad.":::

1. En la página **Crear centro de conectividad virtual**, complete los siguientes campos:

   * **Región**: seleccione la región en la que quiere implementar el centro de conectividad virtual.
   * **Nombre**: escriba el nombre que quiere asignar al centro de conectividad virtual.
   * **Espacio de direcciones privadas del centro de conectividad**: intervalo de direcciones del centro de conectividad en la notación CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="Captura de pantalla de la creación de un centro de conectividad.":::

1. En la pestaña **De punto a sitio**, rellene los campos siguientes:

   * **Unidades de escalado de puerta de enlace**: que representa la capacidad agregada de la puerta de enlace de VPN del usuario.
   * **Configuración de punto a sitio**: la que creó en el paso anterior.
   * **Grupo de direcciones de clientes**: para los usuarios remotos.
   * **Dirección IP de servidor DNS personalizado**.
   * **Preferencia de enrutamiento**: seleccione la preferencia de enrutamiento adecuada. Las preferencias de enrutamiento de Azure le permiten elegir cómo se enruta el tráfico entre Azure e Internet. Puede optar por enrutar el tráfico a través de la red de Microsoft o a través de una red de ISP (Internet público). Estas opciones también se conocen como enrutamiento de tipo "cold-potato" y enrutamiento de tipo "hot-potato" respectivamente. El servicio asigna la IP pública de Virtual WAN en función de la opción de enrutamiento seleccionada. Para obtener más información sobre las preferencias de enrutamiento mediante la red de Microsoft o el ISP, vea el artículo [Preferencias de enrutamiento](../articles/virtual-network/routing-preference-overview.md).

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="Captura de pantalla de la configuración del centro de conectividad virtual con la opción de punto a sitio seleccionada.":::

1. Seleccione **Revisar + crear**.
1. En la página **Validación superada**, seleccione **Crear**.
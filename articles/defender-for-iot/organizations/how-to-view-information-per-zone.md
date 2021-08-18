---
title: Más información sobre dispositivos en zonas específicas
description: Usar la consola de administración local para obtener una información de vista completa por zona específica
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019378"
---
# <a name="view-information-per-zone"></a>Ver información por zona


## <a name="view-a-device-map-for-a-zone"></a>Ver un mapa de dispositivos para una zona

Ver un mapa de dispositivo para una zona seleccionada en un sensor. Esta vista muestra todos los elementos de red relacionados con la zona seleccionada, incluidos los sensores, los dispositivos conectados a ellos y otra información.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Captura de pantalla del mapa de la zona.":::


- En la ventana **Administración de sitio**, seleccione **View Zone Map** (Ver mapa de zonas) en la barra que contiene el nombre de la zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Región predeterminada a unidad de negocio predeterminada.":::

Aparece la ventana **Mapa del dispositivo**. Las siguientes herramientas están disponibles para ver los dispositivos y la información del dispositivo desde el mapa. Para más información sobre cada una de estas características, consulte la *guía del usuario de la plataforma Defender para IoT*.

- **Vistas de zoom del mapa**: vista simplificada, vista de conexiones y vista detallada. La vista del mapa que se muestra varía en función del nivel de zoom del mapa. Puede cambiar entre las vistas del mapa ajustando los niveles de zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Herramientas de diseño y búsqueda del mapa**: herramientas que se usan para mostrar diversos segmentos de red, dispositivos, grupos de dispositivos o capas.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Captura de pantalla de la vista de herramientas de diseño y búsqueda.":::

- **Etiquetas e indicadores en dispositivos:** por ejemplo, el número de dispositivos agrupados en una subred de una red de TI. En este ejemplo, es 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Captura de pantalla de etiquetas e indicadores.":::

- **Visualización de las propiedades del dispositivo**: por ejemplo, el sensor que supervisa el dispositivo y las propiedades básicas de este. Haga clic con el botón derecho en el dispositivo para ver las propiedades del dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Captura de pantalla de la vista de propiedades del dispositivo.":::

- **Alertas asociadas con un dispositivo:** haga clic con el botón derecho en el dispositivo para ver las alertas relacionadas.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Captura de pantalla de la ventana Mostrar alertas.":::

## <a name="view-alerts-associated-with-a-zone"></a>Visualización de las alertas asociadas con una zona

Para ver las alertas asociadas con una zona específica:

- Seleccione el icono de alerta de la ventana **Zona**. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Vista de la unidad de negocio predeterminada con ejemplos.":::

Para más información, consulte [Introducción a la continuidad Uso de alertas](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Visualización del inventario de dispositivos de una zona

Para ver el inventario de dispositivos asociado a una zona específica:

- Seleccione **View Device Inventory** (Ver inventario de dispositivos) en la ventana **Zona**.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Aparecerá la pantalla de inventario de dispositivos.":::

Para más información, consulte [Investigación de todas las detecciones de los sensores de empresa en un inventario de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Visualización de información adicional de la zona

La siguiente información adicional de la zona está disponible:

- **Detalles de la zona**: vea el número de dispositivos, alertas y sensores asociados a la zona.

- **Detalles del sensor**: vea el nombre, la dirección IP y la versión de cada uno de los sensores asignados a la zona.

- **Estado de conectividad:** si un sensor está desconectado, conéctelo desde el sensor. Consulte [Conexión de los sensores a una consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Progreso de la actualización**: si el sensor conectado se está actualizando, se mostrarán los estados de actualización. Durante la actualización, la consola de administración local no recibe del sensor la información del dispositivo.

## <a name="next-steps"></a>Pasos siguientes

[Obtención de información sobre amenazas globales, regionales y locales](how-to-gain-insight-into-global-regional-and-local-threats.md)

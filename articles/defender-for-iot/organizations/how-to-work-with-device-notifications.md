---
title: Uso de las notificaciones del dispositivo
description: Las notificaciones proporcionan información sobre la actividad de red que puede requerir su atención, junto con recomendaciones para tratar con esta actividad.
ms.date: 12/12/2020
ms.topic: how-to
ms.openlocfilehash: c0c2fc5a4c01a8a31512cd43c340bf3fadc259b1
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019105"
---
# <a name="work-with-device-notifications"></a>Uso de las notificaciones del dispositivo

Las notificaciones proporcionan información sobre la actividad de red que puede requerir su atención, junto con recomendaciones para tratar con esta actividad. Por ejemplo, puede que reciba una notificación sobre:

- Un dispositivo inactivo. Si el dispositivo ya no forma parte de la red, puede quitarlo. Si el dispositivo está inactivo, por ejemplo, porque se desconectó de la red por error, vuelva a conectarlo y descarte la notificación.

- Se detectó una dirección IP en un dispositivo que actualmente solo está identificado por una dirección MAC. Responda autorizando la dirección IP del dispositivo.

La respuesta a las notificaciones mejora la información proporcionada en el mapa de dispositivos, el inventario de dispositivos y las consultas e informes de minería de datos. También proporciona información sobre los cambios de red legítimos y los posibles errores de configuración de la red.

**Notificaciones frente a alertas**

Además de recibir notificaciones sobre la actividad de red, es posible que reciba *alertas*. Las notificaciones proporcionan información sobre los cambios de red o las propiedades del dispositivo no resueltas que no presentan una amenaza. Las alertas proporcionan información sobre las desviaciones de red y los cambios que podrían presentar una amenaza para la red.

Para ver las notificaciones:

1. En el menú izquierdo de la consola, seleccione **Mapa del dispositivo**.

2. Seleccione el icono **Notificaciones**. El número rojo situado encima del icono indica el número de notificaciones.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Icono de notificación.":::

   La ventana **Notificaciones** muestra todas las notificaciones que el sensor ha detectado.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Notificaciones"::::

## <a name="filter-the-notifications-window"></a>Filtrado de la ventana Notificaciones

Use filtros de búsqueda para mostrar las notificaciones que le interesen.

| Filtrar por | Descripción |
|--|--|
| Filtrar por tipo | Vea las notificaciones que cubren un área específica de interés. Por ejemplo, solo las que proceden de dispositivos inactivos. |
| Filtrar por intervalo de fechas | Muestre las notificaciones que cubren un intervalo de tiempo específico. Por ejemplo, aquellas enviadas solo en la última semana. |
| Búsqueda de información específica | Busque notificaciones específicas. |

## <a name="notification-events-and-responses"></a>Eventos de notificación y respuestas

En la tabla siguiente se describen los tipos de evento de notificación que puede recibir, junto con las opciones para administrarlos. Puede actualizar la información del dispositivo con un valor recomendado o descartar la notificación. Al descartar una notificación, la información del dispositivo no se actualiza con la información recomendada. Si se vuelve a detectar el tráfico, se volverá a enviar la notificación.

| Tipos de eventos de notificación | Descripción | Respuestas |
|--|--|--|
| Nueva dirección IP detectada | Hay una nueva dirección IP asociada con el dispositivo. Pueden detectarse cinco escenarios: <br /><br /> Se asoció una dirección IP adicional a un dispositivo. Este dispositivo también está asociado a una dirección MAC existente.<br /><br /> Se detectó una nueva dirección IP para un dispositivo que usa una dirección MAC existente. Actualmente, el dispositivo no se comunica mediante una dirección IP.<br /> <br /> Se detectó una nueva dirección IP para un dispositivo que utiliza un nombre NetBIOS. <br /><br /> Se detectó una dirección IP como interfaz de administración de un dispositivo asociado a una dirección MAC. <br /><br /> Se detectó una nueva dirección IP para un dispositivo que usa una dirección IP virtual. | **Establecer IP adicional en el dispositivo** (mezcla de dispositivos) <br /> <br />**Reemplazar IP existente** <br /> <br /> **Descartar**<br /> Quite la notificación. |
| Dispositivos inactivos | No se ha detectado tráfico en un dispositivo durante más de 60 días. | **Eliminar** <br /> Si este dispositivo no forma parte de la red, quítelo. <br /><br />**Descartar** <br /> Quite la notificación si el dispositivo forma parte de la red. Si el dispositivo está inactivo (por ejemplo, porque se ha desconectado por error de la red), descarte la notificación y vuelva a conectarlo. |
| Nuevos dispositivos de OT | Una subred incluye un dispositivo de OT que no está definido en una subred de ICS. <br /><br /> Cada subred que contiene al menos un dispositivo de OT puede definirse como una subred de ICS. Esto ayuda a diferenciar entre los dispositivos de OT y TI en el mapa. | **Establecer como subred de ICS** <br /> <br /> **Descartar** <br />Quite la notificación si el dispositivo no forma parte de la subred. |
| No hay subredes configuradas | No hay subredes configuradas actualmente en la red. <br /><br /> Para una mejor representación en el mapa y poder diferenciar entre los dispositivos de OT y TI, configure subredes. | **Abra Subnets Configuration** (Configuración de subredes) y configure las subredes. <br /><br />**Descartar** <br /> Quite la notificación. |
| Cambios en el sistema operativo | Uno o más sistemas operativos nuevos se han asociado al dispositivo. | Seleccione el nombre del nuevo sistema operativo que quiere asociar al dispositivo.<br /><br /> **Descartar** <br /> Quite la notificación. |
| Nuevas subredes | Se detectaron nuevas subredes. | **Learn**<br />Agregue automáticamente la subred.<br />**Abrir Subnet Configuration** (Configuración de subredes)<br />Agregue toda la información de subred que falta.<br />**Descartar**<br />Quite la notificación. |
| Cambios de tipo de dispositivo | Se ha asociado un nuevo tipo de dispositivo al dispositivo. | **Establecer como {…}**<br />Asocie el nuevo tipo al dispositivo.<br />**Descartar**<br />Quite la notificación. |

## <a name="respond-to-many-notifications-simultaneously"></a>Respuesta a muchas notificaciones a la vez

Es posible que deba tratar con varias notificaciones a la vez. Por ejemplo:

- Si los responsables de TI realizaron una actualización del sistema operativo en un gran conjunto de servidores de red, puede indicar al sensor que aprenda las nuevas versiones de todos los servidores actualizados. 

- Si un grupo de dispositivos de una determinada línea se ha eliminado gradualmente y ya no está activo, puede indicar al sensor que quite estos dispositivos de la consola.

Puede indicar al sensor que aplique la información recién detectada a varios dispositivos o que la omita.   

Para mostrar notificaciones y administrarlas:

1. Use las opciones **filter by type, date range** (filtrar por tipo, intervalo de fechas) o **Seleccionar todo**. Anule la selección de las notificaciones según sea necesario.

2. Indique al sensor que aplique la información recién detectada a los dispositivos seleccionados; para ello, seleccione **APRENDER**. O bien, indique al sensor que omita la información recién detectada seleccionando **DESCARTAR**. Se muestra el número de notificaciones que puede aprender y descartar simultáneamente, junto con el número de notificaciones que debe tratar de forma individual.

Los eventos configurados de **New IPs** (Nuevas direcciones IP) y **No Subnets** (No hay subredes) no se pueden administrar a la vez. Requieren confirmación manual.

## <a name="see-also"></a>Consulte también

[Visualización de alertas](how-to-view-alerts.md)

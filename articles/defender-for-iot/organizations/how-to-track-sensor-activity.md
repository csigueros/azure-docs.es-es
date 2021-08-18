---
title: Seguimiento de la actividad del sensor
description: La escala de tiempo de eventos presenta una escala de tiempo de actividad detectada en la red, incluidas las alertas y las acciones de administración de alertas, los eventos de red y las operaciones de usuario como el inicio de sesión y la eliminación del usuario.
ms.date: 12/10/2020
ms.topic: article
ms.openlocfilehash: 195908001fbd247ed2e0fa007bc8dcd5ebf28e60
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019442"
---
# <a name="track-sensor-activity"></a>Seguimiento de la actividad del sensor

## <a name="event-timeline"></a>Escala de tiempo de eventos

La escala de tiempo de eventos presenta una escala de tiempo de actividad detectada por el sensor. Por ejemplo:

  - Alertas y acciones de administración de alertas

  - Eventos de red

  - Operaciones de usuario como el inicio de sesión y la eliminación de los usuarios

La escala de tiempo de eventos proporciona una vista cronológica de los eventos que se han producido en la red. La escala de tiempo de eventos permite comprender y analizar la cadena de eventos anteriores y posteriores a un ataque o un incidente, que ayuda en la investigación y el análisis forense.

> [!NOTE]
> Los *administradores* y los *analistas de seguridad* pueden llevar a cabo los procedimientos descritos en esta sección.

Para ver los registros de eventos:

- Seleccione **Escala de tiempo de eventos** en el menú lateral.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Vea los eventos en la escala de tiempo de eventos.":::

Además de ver los eventos detectados por el sensor, puede agregar manualmente los eventos a la escala de tiempo. Este proceso resulta útil si el evento se ha producido en un sistema externo pero tiene un impacto en su red, y es importante registrar el evento y presentarlo como parte de la escala de tiempo.

Para agregar eventos manualmente:

- Seleccione **Crear evento**.

Para exportar información del registro de eventos en un archivo CSV:

- Seleccione **Exportar**.

## <a name="filter-the-event-timeline"></a>Filtrar la escala de tiempo de eventos

Filtre la escala de tiempo para mostrar los dispositivos y los eventos que le interesen.

Para filtrar la escala de tiempo:

1. Seleccione **Filtros avanzados**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Use la ventana Filtros de eventos avanzados para filtrar los eventos.":::

2. Establezca filtros de eventos como se indica a continuación:

   - **Incluir dirección**: muestra eventos de dispositivos concretos.

   - **Excluir dirección**: oculta eventos de dispositivos concretos.

   - **Incluir tipos de eventos**: muestra tipos de eventos concretos.

   - **Excluir tipos de eventos**: oculta tipos de eventos concretos.

   - **Grupo de dispositivos**: Seleccione un grupo de dispositivos tal y como se definió en el mapa del dispositivo. Solo se presentan los eventos de este grupo.

3. Seleccione **Borrar todo** para borrar todos los filtros seleccionados.

4. Buscar por **Solo alertas**, **Alertas y avisos** o **Todos los eventos**.

5. Seleccione **Seleccionar fecha** para elegir una fecha concreta. Elija un día, una hora y un minuto. Se muestran los eventos del intervalo de tiempo seleccionado.

6.  Seleccione **Operaciones de usuario** para incluir o excluir eventos de operación de usuario.

7.  Seleccione la flecha (**V**) para obtener más información sobre el evento:

    - Seleccione las alertas relacionadas (si las hubiere) para mostrar una descripción detallada de la alerta.

    - Seleccione el dispositivo para mostrarlo en el mapa.

    - Seleccione **Filtrar eventos por dispositivos relacionados** si desea filtrar por dispositivos relacionados.

    - Seleccione **archivo PCAP** para descargar el archivo PCAP (si existe) que contiene una captura de paquetes de toda la red en un momento determinado. 
    
      El archivo PCAP contiene información técnica que puede ayudar a los ingenieros de redes a determinar los parámetros exactos del evento. Puede analizar el archivo PCAP con un analizador de protocolos de red como Wireshark, una aplicación de código abierto.

## <a name="see-also"></a>Consulte también

[Visualización de alertas](how-to-view-alerts.md)

---
title: Análisis de los datos del dispositivo en la aplicación de Azure IoT Central | Microsoft Docs
description: Analice los datos del dispositivo en la aplicación de Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 08/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1a664ac69ba05cc44aba9d18ffc436a98dbab154
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431122"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Cómo usar análisis para analizar los datos del dispositivo

Azure IoT Central ofrece funcionalidad de análisis enriquecida para analizar tendencias históricas y correlacionar la telemetría de los dispositivos. Para comenzar, visite **Analytics** en el panel izquierdo.

## <a name="understand-the-analytics-ui"></a>Descripción de la interfaz de usuario de Analytics

La interfaz de usuario de Analytics consta de tres componentes principales:

- **Panel de configuración de datos:** en el panel de configuración de datos, seleccione el grupo de dispositivos cuyos datos quiere analizar. A continuación, seleccione la telemetría que quiere analizar y el método de agregación para cada telemetría. El control **Group By** (Agrupar por) ayuda a agrupar los datos al usar las propiedades del dispositivo como dimensiones.

- **Time control** (Control de tiempo): utilice el control de tiempo para seleccionar la duración para la que desea analizar los datos. Puede arrastrar cualquier extremo del control deslizante de tiempo para seleccionar el intervalo de tiempo. El control de tiempo también tiene un control deslizante de **Tamaño de intervalo** que controla el cubo o el tamaño del intervalo usado para agregar los datos.

- **Chart control** (Control de gráficos): el control de gráficos visualiza los datos como un gráfico de líneas. Puede alternar la visibilidad de líneas específicas interactuando con la leyenda del gráfico.

  :::image type="content" source="media/howto-create-analytics/analytics-ui.png" alt-text="Captura de pantalla que muestra las tres áreas de la interfaz de usuario de Analytics.":::

## <a name="query-your-data"></a>Consulta de los datos

Para empezar, elija un **Grupo de dispositivos** y, a continuación, la telemetría que desea analizar. Cuando haya terminado, seleccione **Analizar** para comenzar a visualizar los datos:

- **Grupo de dispositivos:** Un [grupo de dispositivos](tutorial-use-device-groups.md) es un conjunto de dispositivos definido por el usuario. Por ejemplo, **Todos los refrigeradores de Oakland** o **Todas las turbinas eólicas versión 2.0**.

- **Telemetría**: Seleccione la telemetría que quiere analizar y explorar. Puede seleccionar varios tipos de telemetría para analizarlas juntas. El método de agregación predeterminado se establece en **Average** para el tipo de datos numérico y como **Count** para cadenas. Los métodos de agregación admitidos para los tipos de datos numéricos son **Average**, **Maximum**, **Minimum**, **Count** y **Sum**. **Count** es el único método de agregación para las cadenas.

    > [!NOTE]
    > Los puntos de datos históricos solo se muestran cuando se cumplen las condiciones de la consulta. Por ejemplo, supongamos que ayer se actualizó un dispositivo de **Template1** a **Template2**. Si consulta hoy grupos de dispositivos que contengan **Template1**, verá los datos de dispositivos de ayer y anteriores. Si consulta grupos de dispositivos que contengan **Template2**, verá el dispositivo y los datos desde el momento de la actualización en adelante.

- **Group by** (Agrupar por): el control **Group by** (Agrupar por) ayuda a agrupar los datos al usar las propiedades del dispositivo como dimensiones. La telemetría y las propiedades del dispositivo se combinan con las propiedades de la nube cuando el dispositivo envía datos. Si la propiedad de la nube o del dispositivo se ha actualizado, verá la telemetría agrupada por diferentes valores en el gráfico.

    > [!TIP]
    > Para ver los datos de cada dispositivo por separado, seleccione **id. de dispositivo** en el control **Group by** (Agrupar por).

## <a name="interact-with-your-data"></a>Interactúe con los datos

Cuando haya consultado los datos, podrá comenzar a visualizarlos en el gráfico de líneas. Puede mostrar u ocultar la telemetría, cambiar la duración del tiempo o ver los datos en una cuadrícula.

- **Panel del editor de tiempo:** de manera predeterminada, verá los datos del último día. Puede arrastrar cualquier extremo del control deslizante para cambiar el intervalo de tiempo. También puede utilizar el control de calendario para seleccionar uno de los cubos de hora predefinidos o seleccionar un intervalo de tiempo personalizado. El control de tiempo también tiene un control deslizante de **tamaño de intervalo** que controla el tamaño del intervalo usado para agregar los datos.

  :::image type="content" source="media/howto-create-analytics/time-editor-panel.png" alt-text="Captura de pantalla que muestra el panel del editor de tiempo.":::

  - **Herramienta de control deslizante de intervalo de fechas interno**: use los dos controles de punto de conexión para resaltar el intervalo de tiempo que desee. El control deslizante del intervalo de fechas externas se encarga de restringir el intervalo de fechas internas.
  
  - **Control deslizante del intervalo de fechas externas**: use los controles del punto de conexión para seleccionar el intervalo de fechas externas que está disponible para el control de intervalos de fechas internas.

  - **Aumentar y disminuir los botones de intervalo de fechas**: puede aumentar o disminuir el intervalo de tiempo si selecciona cualquiera de los botones del intervalo que quiera.

  - **Control deslizante de tamaño del intervalo**: utilice el control deslizante para acercar y alejar los intervalos del mismo intervalo de tiempo. Este control le proporciona un control más preciso del movimiento entre grandes segmentos de tiempo. Puede usar esta opción para tener vistas pormenorizadas de alta resolución de los datos, incluso en pocos milisegundos. El punto inicial predeterminado del control deslizante proporciona una vista óptima de los datos de la selección. Esta vista equilibra la resolución, la velocidad de consulta y la granularidad.
  
  - **Selector de intervalo de fechas**: use este control para seleccionar los intervalos de fecha y hora que desee. También puede usar el control para cambiar entre zonas horarias diferentes. Después de realizar los cambios para aplicarlos al área de trabajo actual, seleccione **Guardar**.

  > [!TIP]
  > El tamaño del intervalo se determina dinámicamente según el intervalo de tiempo seleccionado. Los intervalos de tiempo más pequeños permiten agregar los datos en intervalos muy pormenorizados de hasta unos segundos.

- **Leyenda de gráfico**: la leyenda de gráfico muestra la telemetría seleccionada en el gráfico. Deslice el mouse sobre un elemento de la leyenda para destacarlo en el gráfico. Cuando se usa **Group by** (Agrupar por), la telemetría se agrupa por los valores de la dimensión seleccionada. Puede alternar la visibilidad de cada tipo de telemetría o hacer clic en el nombre del grupo para alternar la visibilidad del grupo.  

- **Control de formato del eje Y:** el modo del eje Y recorre las opciones disponibles de la vista del eje Y. Este control solo está disponible cuando se visualizan varios tipos de telemetría. Los tres modos son:

  - **Apilado:** se apila un gráfico para cada tipo de telemetría y cada gráfico tiene su propio eje Y. Este es el modo predeterminado.
  - **Compartido:** se traza un gráfico para cada tipo de telemetría en el mismo eje Y.
  - **Superponer:** use este modo para apilar varias líneas en el mismo eje Y para que los datos del eje Y cambien en función de la línea seleccionada.

  :::image type="content" source="media/howto-create-analytics/y-axis-control.png" alt-text="Captura de pantalla que resalta el control del eje Y.":::

- **Control de zoom:** el control de zoom le permite profundizar más en los datos. Si encuentra un período de tiempo en el que desea centrarse en el conjunto de resultados, use el puntero del mouse para resaltar el área. A continuación, haga clic con el botón derecho en el área seleccionada y luego en **Zoom**.

  :::image type="content" source="media/howto-create-analytics/zoom.png" alt-text="Captura de pantalla que muestra el uso del control de zoom.":::

Seleccione los puntos suspensivos para visualizar más controles de gráfico:

- **Display Grid** (Mostrar cuadrícula): permite mostrar los resultados en un formato de tabla que le permita ver el valor de cada punto de datos.

- **Download as CSV** (Descargar como CSV): permite exportar los resultados como un archivo de valores separados por comas (CSV). El archivo .csv contiene datos para cada dispositivo. Los resultados se exportan mediante el intervalo y el período de tiempo especificados.

- **Drop a Marker** (Colocar un marcador): el control **Colocar un marcador** permite delimitar determinados puntos de datos en el gráfico. Resulta útil cuando se intentan comparar datos de varias líneas en distintos períodos de tiempo.

  :::image type="content" source="media/howto-create-analytics/additional-chart-controls.png" alt-text="Captura de pantalla que muestra cómo acceder a los controles de gráfico adicionales.":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a visualizar los datos con las funcionalidades de análisis integradas, le sugerimos que aprenda a [exportar datos de IoT a destinos en la nube mediante la exportación de datos](howto-export-data.md).

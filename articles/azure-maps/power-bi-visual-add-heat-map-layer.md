---
title: Incorporación de una capa de mapa térmico al objeto visual de Azure Maps para Power BI | Microsoft Azure Maps
description: En este artículo, aprenderá a usar la capa de mapa térmico en el objeto visual de Microsoft Azure Maps para Power BI.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.custom: ''
ms.openlocfilehash: 95d35c3f0ed7ab70ca64249323b22a11eca8f5a9
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007138"
---
# <a name="add-a-heat-map-layer-to-the-azure-maps-visual-for-power-bi"></a>Incorporación de una capa de mapa térmico al objeto visual de Azure Maps para Power BI

En este artículo, aprenderá a agregar una capa de mapa térmico a un objeto visual de Azure Maps en Power BI.

:::image type="content" source="media/power-bi-visual/heat-map.png" alt-text="Capa de mapa térmico en el objeto visual de Azure Maps para Power BI.":::

Los mapas térmicos, también conocidos como mapas de densidad, son un tipo de superposición en un mapa utilizado para representar la densidad de los datos mediante diferentes colores. A menudo, los mapas térmicos se usan para mostrar las "zonas activas" de datos en un mapa. Los mapas térmicos son una excelente manera de representar conjuntos de datos con una gran cantidad de puntos. Mostrar un gran número de puntos de datos en un mapa provocará una degradación del rendimiento y puede cubrirlo con símbolos superpuestos, lo que hace que no se pueda usar. La representación de los datos como un mapa térmico no solo da como resultado un mejor rendimiento, sino que le ayuda a comprender mejor los datos al facilitar la visualización de la densidad relativa de cada punto de datos.

Un mapa térmico es útil cuando los usuarios desean visualizar datos comparativos enormes:

- Comparar las tasas de satisfacción del cliente o el rendimiento de las tiendas entre regiones o países.
- Medir la frecuencia con la que los clientes visitan centros comerciales en diferentes ubicaciones.
- Visualizar grandes conjuntos de datos estadísticos y geográficos.

## <a name="prerequisites"></a>Requisitos previos

- [Introducción al objeto visual de Azure Maps para Power BI](/power-bi-visual-getting-started.md).
- [Información sobre las capas en el objeto visual de Azure Maps para Power BI](/power-bi-visual-understanding-layers.md).

## <a name="add-the-heat-map-layer"></a>Incorporación de la capa de mapa térmico

1. En Power BI Desktop, seleccione el mapa de Azure que ha creado.
1. En el panel **Formato**, cambie el botón de alternancia **Mapa térmico** a **Activado**.

Ahora puede ajustar toda la configuración de capa de mapa térmico para que se adapte a su informe.

## <a name="heat-map-layer-settings"></a>Configuración de la capa de mapa térmico

La sección **Mapa térmico** del panel **Formato** proporciona flexibilidad para personalizar y diseñar las visualizaciones de mapa térmico para satisfacer sus requisitos específicos. La sección **Mapa térmico** le permite:

- Configurar el radio de cada punto de datos mediante píxeles o metros como unidad de medida.
- Personalizar la opacidad y la intensidad de la capa de mapa térmico.  
- Especificar si el valor del campo de tamaño debe usarse como peso de cada punto de datos.
- Elegir colores diferentes de los selectores de colores.
- Establecer el nivel de zoom mínimo y máximo para que la capa de mapa térmico esté visible.
- Decidir la posición de la capa de mapa térmico entre las distintas capas, por ejemplo, la capa de gráfico de barras 3D y la capa de burbujas.

En la tabla siguiente se muestran los valores principales que están disponibles en la sección **Mapa térmico** del panel **Formato**:

| Configuración              | Descripción      |
|----------------------|------------------|
| Radio | Radio de cada punto de datos en el mapa térmico.<br /><br />Valores válidos cuando Unidad = "píxeles": 1 - 200. Valor predeterminado: **20**<br />Valores válidos cuando Unidad = "metros": 1 - 4 000 000.|
| Unidades  | Unidades de distancia del radio. Los valores posibles son:<br /><br />**píxeles**. Cuando se establece en píxeles, el tamaño de cada punto de datos siempre será el mismo, independientemente del nivel de zoom.<br />**metros**. Cuando se establece en metros, el tamaño de los puntos de datos se escalará en función del nivel de zoom, lo que garantiza que el radio sea espacialmente preciso.<br /><br /> Valor predeterminado: **píxeles**  |
| Opacidad | Permite establecer la opacidad de la capa del mapa térmico. Valor predeterminado: **1**<br/>El valor debe ser un decimal entre 0 y 1. |
| Intensidad | Intensidad de cada punto térmico. La intensidad es un valor decimal entre 0 y 1 que se usa para especificar cómo debe ser "caliente" un único punto de datos. Valor predeterminado: **0,5** |
| Uso de tamaño como peso | Valor booleano que determina si el valor del campo de tamaño debe usarse como peso de cada punto de datos. Si se ha activado, esto hace que la capa se represente como un mapa térmico ponderado. Valor predeterminado: **Desactivado** |
| Degradado |Selección de color para que los usuarios elijan 3 colores para los colores de degradado bajo (0 %), centro (50 %) y alto (100 %). |
| Zoom mínimo |Nivel de zoom mínimo con el que se puede ver la capa. Los valores válidos van de 1 a 22. Valor predeterminado: **0** |
|Zoom máximo |Nivel de zoom máximo con el que se puede ver la capa.  Los valores válidos van de 1 a 22. Valor predeterminado: **22**|
|Layer position (Posición de capa) |Permite especificar la posición de la capa en relación con otras capas del mapa. Los valores válidos incluyen **Etiquetas anteriores**, **Etiquetas inferiores** y **Carreteras inferiores**. |

## <a name="next-steps"></a>Pasos siguientes

Cambie el modo en que los datos se muestran en el mapa:

> [!div class="nextstepaction"]
> [Adición de una capa de gráfico de barras](power-bi-visual-add-bar-chart-layer.md)

Agregue más contexto al mapa:

> [!div class="nextstepaction"]
> [Adición de una capa de referencia](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de icono](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar el tráfico en tiempo real](power-bi-visual-show-real-time-traffic.md)

Personalización del objeto visual:

> [!div class="nextstepaction"]
> [Sugerencias y trucos para el formato de color en Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalización de los títulos, los fondos y las leyendas de las visualizaciones](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)

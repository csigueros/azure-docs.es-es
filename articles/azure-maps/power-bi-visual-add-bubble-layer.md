---
title: Incorporación de una capa de burbujas al objeto visual de Azure Maps en Power BI | Microsoft Azure Maps
description: En este artículo, aprenderá a usar la capa de burbujas en el objeto visual de Microsoft Azure Maps para Power BI.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bc463a69dc0823e14b08e9fd056f3876e1ba9984
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430457"
---
# <a name="add-a-bubble-layer"></a>Adición de una capa de burbuja

La **capa de burbujas** representa los datos de ubicación como círculos a escala en el mapa.

> [!div class="mx-imgBorder"]
> ![Mapa que muestra datos de punto mediante la capa de burbujas](media/power-bi-visual/bubble-layer-with-legend-color.png)

Inicialmente, todas las burbujas tienen el mismo color de relleno. Si se pasa un campo al cubo **Leyenda** del panel **Campos**, las burbujas se colorearán en función de su categorización. El contorno de las burbujas es blanco de forma predeterminada, pero se puede cambiar a un nuevo color o habilitar la opción de contorno de alto contraste. La opción **High-contrast outline** (Contorno de alto contraste) asigna de forma dinámica un color al contorno que es una variante de alto contraste del color del relleno. Esto ayuda a garantizar que, independientemente del estilo del mapa, las burbujas se puedan ver claramente. A continuación se muestran los valores principales del panel **Formato** que están disponibles en la sección **Bubble layer** (Capa de burbujas).

| Configuración               | Descripción    |
|-----------------------|----------------|
| Size                  | Tamaño de cada burbuja. Esta opción está oculta cuando se pasa un campo al cubo **Tamaño** del panel **Campos**. Las opciones adicionales aparecerán como se describen en el tema [Escalado de tamaño de las burbujas](#bubble-size-scaling) más adelante en este artículo. |
| Fill color (Color de relleno)            | Color de cada burbuja. Esta opción está oculta cuando se pasa un campo al cubo **Leyenda** del panel **Campos** y se muestra una sección independiente de **Colores de datos** en el panel **Formato**. |
| Fill transparency (Transparencia de relleno)     | Transparencia de cada burbuja. |
| High-contrast outline (Contorno de alto contraste) | Hace que el color del contorno contraste con el del relleno para así mejorar la accesibilidad mediante el uso de una variante de alto contraste del color de relleno. |
| Outline color (Color del contorno)         | Color del contorno de la burbuja. Esta opción está oculta cuando está habilitada la opción **High-contrast outline** (Contorno de alto contraste). |
| Outline transparency (Transparencia del contorno)  | Transparencia del contorno. |
| Outline width (Ancho del contorno)         | Ancho del contorno en píxeles |
| Blur (Desenfoque)                  | Cantidad de desenfoque aplicada al contorno. Un valor 1 desenfoca las burbujas de modo que solo el punto central no tenga transparencia. Un valor 0 aplica cualquier efecto de desenfoque. |
| Pitch alignment (Alineación de inclinación)       | Especifica el aspecto de las burbujas cuando se inclina el mapa. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Ventanilla: las burbujas aparecen en su borde en el mapa relativo a la ventanilla. (predeterminado).<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Mapa: las burbujas se representan sin relieve en la superficie del mapa. |
| Zoom scale (Escala del zoom)            | Cuánto deben escalar las burbujas en relación con el nivel de zoom. Una escala de zoom de uno significa que no hay escalado. Los valores grandes harán que las burbujas sean más pequeñas al alejar y más grandes al acercar. Esto ayuda a reducir la aglomeración en el mapa cuando este se reduce, y garantiza que los puntos se destacarán más cuando se hace un zoom. Un valor 1 no aplica ningún escalado. |
| Min zoom (Zoom mínimo)              | Nivel de zoom mínimo en el que están disponibles los mosaicos. |
| Zoom máximo              | Nivel de zoom máximo en el que están disponibles los mosaicos. |
| Layer position (Posición de capa)        | Permite especificar la posición de la capa en relación con otras capas del mapa. |

## <a name="bubble-size-scaling"></a>Escalado de tamaño de las burbujas

Si un campo se pasa al cubo **Tamaño** del panel **Campos**, las burbujas se escalarán de forma relativa al valor de medida de cada punto de datos. La opción **Tamaño** de la sección **Bubble layer** (Capa de burbujas) del panel **Formato** desaparecerá cuando se pase un campo al cubo **Tamaño**, ya que los radios de las burbujas se escalarán entre un valor mínimo y un valor máximo. Las siguientes opciones aparecerán en la sección **Bubble layer** (Capa de burbujas) del panel **Formato** cuando un cubo **Tamaño** tiene un campo especificado.

| Configuración             | Descripción  |
|---------------------|--------------|
| Min size (Tamaño mínimo)            | Tamaño mínimo de la burbuja al escalar los datos.|
| Tamaño máximo            | Tamaño máximo de la burbuja al escalar los datos.|
| Size scaling method (Método de escalado de tamaño) | Algoritmo de escalado usado para determinar el tamaño relativo de la burbuja.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Lineal: se asigna un intervalo de datos de entrada linealmente a los tamaños mínimo y máximo. (predeterminado).<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Log: se asigna un intervalo de datos de entrada de forma logarítmica a los tamaños mínimo y máximo.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Bézier cúbica: especifica los valores X1, Y1, X2, Y2 de una curva Bézier cúbica para crear un método de escalado personalizado. |

Cuando **Size scaling method** (Método de escalado de tamaño) se establece en **Log**, las siguientes opciones estarán disponibles.

| Configuración   | Descripción      |
|-----------|------------------|
| Log scale (Escala logarítmica) | Escala logarítmica que se va a aplicar al calcular el tamaño de las burbujas. |

Cuando el método de escalado de tamaño **Size scaling method** (Método de escalado de tamaño) se establece en **Cubic-Bezier** (Bézier cúbica), las siguientes opciones estarán disponibles para personalizar la curva de escalado.

| Configuración | Descripción                           |
|---------|---------------------------------------|
| X1      | Parámetro X1 de una curva Bézier cúbica. |
| Y1      | Parámetro X2 de una curva Bézier cúbica. |
| X2      | Parámetro Y1 de una curva Bézier cúbica. |
| Y2      | Parámetro Y2 de una curva Bézier cúbica. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) tiene una herramienta útil para crear los parámetros para curvas Bézier cúbicas.

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
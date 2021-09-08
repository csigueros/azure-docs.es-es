---
title: Información sobre las capas en el objeto visual de Azure Maps en Power BI | Microsoft Azure Maps
description: En este artículo, obtendrá información sobre las diferentes capas disponibles en el objeto visual de Microsoft Azure Maps para Power BI.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6014bbf5ed5f3600bc79d5083f5fcc85d323acd5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428909"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Información sobre las capas en el objeto visual de Azure Maps para Power BI

Hay dos tipos de capas disponibles en el objeto visual de Azure Maps. El primer tipo se centra en la representación de los datos que se pasan al panel **Campos** del objeto visual, que consiste en las siguientes capas, a las que vamos a llamar capas de representación de datos.

:::row:::
    :::column span="":::
        **Capa de burbujas**

        Representan puntos como círculos a escala en el mapa.

        ![Capa de burbujas en el mapa](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Capa de gráfico de barras**

        Representa puntos como barras 3D en el mapa.
        
        ![Capa de gráfico de barras en el mapa](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

El segundo tipo de capa conecta orígenes de datos externos adicionales al mapa para proporcionar más contexto, y consta de las siguientes capas.

:::row:::
    :::column span="":::
        **Capa de referencia**

        Superposición de un archivo GeoJSON cargado sobre el mapa.

        ![Capa de referencia en el mapa](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Capa de mosaico**

        Superposición de una capa de mosaico personalizada sobre el mapa.
        
        ![Capa de mosaico en el mapa](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Capa de tráfico**

        Superposición de información de tráfico en tiempo real sobre el mapa.
        
        ![Capa de tráfico en el mapa](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Todas las capas de representación de datos, así como la **capa de mosaico**, tienen opciones para los niveles de zoom mínimo y máximo que se usan para especificar un rango de nivel de zoom para mostrar estas capas. Esto permite el uso de un tipo de capa de representación en un nivel de zoom, y una transición a otra capa de representación con otro nivel de zoom.

Estas capas también tienen una opción para colocarse en relación con otras capas del mapa. Cuando se utilizan varias capas de representación de datos, el orden en el que se agregan al mapa determina su orden de capas relativo cuando tienen el mismo valor de **Layer position** (Posición de capa).

## <a name="general-layer-settings"></a>Valores de capa general

La sección de capa general del panel **Formato** está compuesta por valores comunes que se aplican a las capas que están conectadas al conjunto de datos de Power BI en el panel **Campos** (capa de burbujas, gráfico de barras).

| Configuración     | Descripción   |
|-------------|---------------|
| Unselected transparency (Transparencia no seleccionada) | Transparencia de las formas que no están seleccionadas, cuando hay una o varias formas seleccionadas.  |
| Show zeros (Mostrar ceros)              | Permite especificar si los puntos que tienen un valor de tamaño de cero se deben mostrar en el mapa con el radio mínimo. |
| Show negatives (Mostrar negativos)          | Permite especificar si se debe trazar el valor absoluto de los valores de tamaño negativo.   |
| Min data value (Valor mínimo de los datos)          | El valor mínimo de los datos de entrada con los que se va a realizar el escalado. Sirve para recortar valores atípicos.  |
| Max data value (Valor máximo de los datos)          | El valor máximo de los datos de entrada con los que se va a realizar el escalado. Sirve para recortar valores atípicos.  |

## <a name="next-steps"></a>Pasos siguientes

Cambie el modo en que los datos se muestran en el mapa:

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de gráfico de barras](power-bi-visual-add-bar-chart-layer.md)

Agregue más contexto al mapa:

> [!div class="nextstepaction"]
> [Adición de una capa de referencia](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de icono](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar el tráfico en tiempo real](power-bi-visual-show-real-time-traffic.md)

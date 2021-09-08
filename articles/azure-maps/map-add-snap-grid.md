---
title: Adición de una cuadrícula de ajuste al mapa | Microsoft Azure Maps
description: Procedimiento para agregar una cuadricula de ajuste a un mapa mediante el SDK web de Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 07/20/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 0236128925362bf2736489f2dbf97ab936183c93
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114670062"
---
# <a name="add-a-snap-grid-to-the-map"></a>Incorporación de una cuadrícula de ajuste al mapa

Las cuadrículas de ajuste facilitan el dibujo de formas con bordes y nodos compartidos y líneas más rectas. El ajuste de formas a una cuadrícula es útil al dibujar contornos de creación o rutas de acceso de red en el mapa.

La resolución de la cuadrícula de ajuste está en píxeles. La cuadrícula es cuadrada y relativa al nivel de zoom entero más cercano. La cuadrícula se escala por un factor de dos en relación con el área física del mundo real con cada nivel de zoom.

## <a name="use-a-snap-grid"></a>Uso de una cuadrícula de ajuste

Cree una cuadrícula de ajuste mediante la clase `atlas.drawing.SnapGridManager` y pase una referencia al mapa al que desea conectar el administrador. Establezca la opción `showGrid` en `true` si desea que se vea la cuadrícula. Para ajustar una forma a la cuadrícula, pásela a la función `snapShape` de administradores de cuadrícula de ajuste. Si desea ajustar una matriz de posiciones, pásela a la función `snapPositions`.

En el ejemplo siguiente se ajusta un marcador HTML a una cuadrícula cuando se arrastra. Las herramientas de dibujo se usan para ajustar las formas dibujadas a la cuadrícula cuando se desencadena el evento `drawingcomplete`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Usar una cuadrícula de ajuste" src="https://codepen.io/azuremaps/embed/rNmzvXO?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Ver el Lápiz <a href="https://codepen.io/azuremaps/pen/rNmzvXO"> Use a snapping grid</a> (Usar una cuadrícula de ajuste) por Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) en <a href="https://codepen.io">CodePen</a>.
</iframe>


## <a name="snap-grid-options"></a>Opciones de cuadrícula de ajuste

En el siguiente ejemplo se muestran las distintas opciones de personalización disponibles para el administrador de la cuadrícula de ajuste. Los estilos de las línea de la cuadrícula se pueden personalizar mediante la recuperación de la capa de la línea subyacente mediante la función `getGridLayer` de administradores de la cuadrícula de ajuste.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opciones de cuadrícula de ajuste" src="https://codepen.io/azuremaps/embed/RwVZJry?default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el lápiz <a href="https://codepen.io/azuremaps/pen/RwVZJry">Snap grid options</a> (Opciones de cuadrícula de ajuste) de Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) en <a href="https://codepen.io">CodePen</a>.
</iframe>


## <a name="next-steps"></a>Pasos siguientes

Aprenda a usar otras características del módulo de herramientas de dibujo:

> [!div class="nextstepaction"]
> [Obtención de datos de forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reacción a eventos de dibujo](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interacción y métodos abreviados de teclado](drawing-tools-interactions-keyboard-shortcuts.md)
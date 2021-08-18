---
title: Obtención de datos a partir de formas en un mapa | Microsoft Azure Maps
description: En este artículo, obtendrá información sobre cómo obtener datos de formas dibujados en un mapa mediante el SDK web de Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 2ef479a4e1bd4a10a2a62c82de384338b1f016a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751411"
---
# <a name="get-shape-data"></a>Obtención de datos de forma

En este artículo se muestra cómo obtener datos de formas que se dibujan en el mapa. Usamos la función **drawingManager.getSource()** dentro del [administrador de dibujos](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--). Hay varios escenarios en los que quiere extraer datos GeoJSON de una forma dibujada y utilizarlos en otro lugar.  


## <a name="get-data-from-drawn-shape"></a>Obtenga datos de la forma dibujada

La función siguiente obtiene los datos de origen de la forma dibujada y los muestra en la pantalla. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

A continuación se muestra el ejemplo de código de ejecución completo, donde puede dibujar una forma para probar la funcionalidad:

<br/>

<iframe height="686" title="Obtención de datos de forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Obtención de datos de forma</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar características adicionales del módulo de herramientas de dibujo:

> [!div class="nextstepaction"]
> [Reacción a eventos de dibujo](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interacción y métodos abreviados de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Más información sobre las clases y los métodos utilizados en este artículo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Administrador de dibujo](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de herramientas de dibujo](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
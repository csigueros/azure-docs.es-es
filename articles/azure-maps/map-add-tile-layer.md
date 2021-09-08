---
title: Adición de una capa de mosaico a un mapa | Microsoft Azure Maps
description: Aprenda a superponer imágenes en mapas. Vea un ejemplo en el que se usa el SDK web de Azure Maps para agregar una capa de mosaico que contenga una superposición de radar meteorológico a un mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9b645ccc5347e55f46666563dc9bab565b8ba631
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439603"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adición de una capa de mosaico a un mapa

En este artículo se explica cómo puede superponer una capa de mosaico en el mapa. Las capas de mosaico permiten superponer imágenes encima de los mosaicos de mapa base de Azure Maps. Para más información sobre el sistema de mosaicos de Azure Maps, consulte [Niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md).

Una capa de mosaico carga los mosaicos desde un servidor. Estas imágenes se pueden representar previamente o se pueden representar de forma dinámica. Las imágenes representadas previamente se almacenan como cualquier otra imagen en un servidor mediante una convención de nomenclatura que entienda la capa de mosaico. Las imágenes representadas de forma dinámica usan un servicio para cargar las imágenes casi en tiempo real. Hay tres convenciones diferentes de nomenclatura de servicio de mosaico compatibles con la clase [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) de Azure Maps:

* X, Y, notación Zoom: X es la columna, Y es la posición de fila del mosaico en la cuadrícula de mosaico y la notación Zoom es un valor basado en el nivel de zoom.
* Notación Quadkey: combina la información de zoom, x e y en un valor de cadena único. Este valor de cadena se convierte en un identificador único para un solo mosaico.
* Cuadro de límite: especifique una imagen en el formato de coordenadas del cuadro de límite: `{west},{south},{east},{north}`. Este formato se usa normalmente en [servicios de mapas web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Un elemento [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) es una excelente manera de visualizar grandes conjuntos de datos en el mapa. No solo puede generarse una capa de mosaico a partir de una imagen, sino que también se pueden representar datos de vector como una capa de mosaico. Con la representación de datos de vectores como una capa de mosaico, el control de mapa solo necesita cargar los mosaicos que tienen un tamaño de archivo bastante más reducido que los datos de vector que representan. Esta técnica se usa normalmente para representar millones de filas de datos en el mapa.

La dirección URL del mosaico pasada a una capa de mosaico debe ser una dirección URL HTTP/HTTPS que apunte a un recurso TileJSON o a una plantilla de URL de mosaico que use los siguientes parámetros:

* `{x}`: posición del mosaico en X. También necesita `{y}` y `{z}`.
* `{y}`: posición del mosaico en Y. También necesita `{x}` y `{z}`.
* `{z}`: nivel de zoom del mosaico. También necesita `{x}` y `{y}`.
* `{quadkey}`: identificador quadkey de mosaico basado en la convención de nomenclatura del sistema de mosaico de Bing Maps.
* `{bbox-epsg-3857}`: una cadena de un cuadro delimitador con el formato `{west},{south},{east},{north}` en el sistema de referencia espacial EPSG 3857.
* `{subdomain}`: si se especifica `subdomain`, se agregará un marcador de posición para los valores de subdominio.
* `{azMapsDomain}`: un marcador de posición para alinear el dominio y la autenticación de las solicitudes de mosaico con los mismos valores utilizados por el mapa.

## <a name="add-a-tile-layer"></a>Adición de una capa de icono

 En este ejemplo se muestra cómo crear una capa de mosaico que señale a un conjunto de mosaicos. En este ejemplo se usa el sistema de mosaicos de zoom, x e y. El origen de esta capa de mosaico es el [proyecto OpenSeaMap](https://openseamap.org/index.php), que contiene cartas náuticas financiadas por la comunidad. Al examinar los datos de radar, idealmente los usuarios verán claramente las etiquetas de las ciudades mientras se desplazan por el mapa. Este comportamiento se puede implementar mediante la inserción de una capa de mosaico debajo de la capa `labels`.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

A continuación se muestra el código de ejemplo de ejecución completo de la funcionalidad anterior.

<br/>

<iframe height='500' scrolling='no' title='Capa de mosaico que usa X, Y y Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>capa de mosaico que usa X, Y y Z</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Adición de un servicio de mapa web (WMS) de OGC

Un servicio de mapa web (WMTS) es un estándar de Open Geospatial Consortium (OGC) para suministrar imágenes de datos de mapa. Hay muchos conjuntos de datos abiertos disponibles en este formato que puede usar con Azure Maps. Este tipo de servicio se puede usar con una capa de mosaico si el servicio admite el sistema de referencia de coordenadas (CRS) `EPSG:3857`. Al usar un servicio WMS, establezca los parámetros de ancho y alto en el mismo valor que admita el servicio y asegúrese de establecer este mismo valor en la opción `tileSize`. En la dirección URL con formato, establezca el parámetro `BBOX` del servicio con el marcador de posición `{bbox-epsg-3857}`.

En la captura de pantalla siguiente se muestra el código anterior que superpone un servicio de mapa web de datos geológicos de [U.S. Geological Survey (USGS)](https://mrdata.usgs.gov/) encima de un mapa, debajo de las etiquetas.

<br/>

<iframe height="500" scrolling="no" title="Capa de mosaico de WMS" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el pen <a href='https://codepen.io/azuremaps/pen/BapjZqr'>Capa de mosaico de WMS</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Adición de un servicio de mosaico de mapa web de OGC (WMTS)

Un servicio de mosaico de mapa web (WMTS) es un estándar de Open Geospatial Consortium (OGC) para suministrar superposiciones basadas en mosaicos para los mapas. Hay muchos conjuntos de datos abiertos disponibles en este formato que puede usar con Azure Maps. Este tipo de servicio se puede usar con una capa de mosaico si el servicio admite el sistema de referencia de coordenadas (CRS) `EPSG:3857` o `GoogleMapsCompatible`. Al usar un servicio WMS, establezca los parámetros de ancho y alto en el mismo valor que admita el servicio y asegúrese de establecer este mismo valor en la opción `tileSize`. En la dirección URL con formato, reemplace los siguientes marcadores de posición según corresponda:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

En la captura de pantalla siguiente se muestra el código anterior que superpone un servicio de mosaico de mapa web de imágenes de [U.S. Geological Survey (USGS)](https://viewer.nationalmap.gov/services/) encima de un mapa, debajo de las carreteras y etiquetas.

<br/>

<iframe height="500" scrolling="no" title="Capa de mosaico de WMTS" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el pen <a href='https://codepen.io/azuremaps/pen/BapjZVY'>Capa de mosaico de WMTS</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizar una capa de mosaico

La clase de la capa de mosaico tiene muchas opciones de estilo. Esta es una herramienta para probarlas.

<br/>

<iframe height='700' scrolling='no' title='Opciones de capa de mosaico' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>opciones de capa de mosaico</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las clases y los métodos utilizados en este artículo:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Para obtener más ejemplos de código para agregar a los mapas:

> [!div class="nextstepaction"]
> [Adición de una capa de imagen](./map-add-image-layer.md)

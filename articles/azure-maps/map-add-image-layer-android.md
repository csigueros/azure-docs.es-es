---
title: Adición de una capa de imagen a un mapa de Android | Microsoft Azure Maps
description: Aprenda a agregar imágenes a un mapa. Vea cómo usar Android SDK de Azure Maps para personalizar las capas de imagen y superponer imágenes en conjuntos fijos de coordenadas.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 4c11a1275b94eebba33819ae6c9ffeb313a3ce0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463110"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Adición de una capa de imagen a un mapa (Android SDK)

En este artículo se explica cómo superponer una imagen a un conjunto fijo de coordenadas. Estos son algunos ejemplos de diferentes tipos de imágenes que se pueden superponer en los mapas:

* Imágenes capturadas por drones
* Planos de edificios
* Imágenes de mapa históricas u otras imágenes de mapa especializadas
* Planos técnicos de sitios de trabajo

> [!TIP]
> Una capa de imagen es un modo sencillo de superponer una imagen en un mapa. Tenga en cuenta que las imágenes de gran tamaño pueden consumir mucha memoria y podrían causar problemas de rendimiento. En ese caso, considere la posibilidad de dividir la imagen en mosaicos y cargarlos en el mapa como una capa de mosaico.

## <a name="add-an-image-layer"></a>Adición de una capa de imagen

En el código siguiente se superpone una imagen de un mapa de Newark, Nueva Jersey, de 1922 en el mapa. Esta imagen se agrega a la carpeta `drawable` del proyecto. Una capa de imagen se crea estableciendo la imagen y las coordenadas de las cuatro esquinas con el formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`. A menudo, es conveniente agregar las capas de imagen por debajo de la capa `label`.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Como alternativa, se puede especificar una dirección URL para una imagen hospedada en línea. Sin embargo, si el escenario lo permite, agregue la imagen a la carpeta de proyectos `drawable`, que se cargará más rápido, ya que la imagen estará disponible de forma local y no tendrá que descargarse.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

En la captura de pantalla siguiente se muestra un mapa de Newark, New Jersey, de 1922 superpuesto mediante una capa de imagen.

![Mapa de Newark, Nueva Jersey, de 1922 superpuesto mediante una capa de imagen](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importación de un archivo KML como una superposición de suelo

En este ejemplo se muestra cómo agregar la información de superposición de suelo KML como una capa de imagen en el mapa. Las superposiciones de suelo KML proporcionan las coordenadas norte, sur, este y oeste, y una rotación en el sentido contrario al reloj. Pero la capa de imagen espera coordenadas para cada esquina de la imagen. La superposición de suelo KML de este ejemplo se corresponde con la catedral de Chartres, cuya fuente es [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

En el código se usa el método estático `getCoordinatesFromEdges` de la clase `ImageLayer`. Este método calcula las cuatro esquinas de la imagen a partir de la información de norte, sur, este, oeste y la rotación de la superposición de suelo KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

En la captura de pantalla siguiente se muestra un mapa con una superposición de suelo KML superpuesta mediante una capa de imagen.

![Mapa con una superposición de suelo KML superpuesta mediante una capa de imagen](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Use los métodos `getPixels` y `getPositions` de la clase de capa de imagen para la conversión entre las coordenadas geográficas de la capa de imagen posicionada y las coordenadas de píxeles de la imagen local.

## <a name="next-steps"></a>Pasos siguientes

Consulte el siguiente artículo para obtener más información sobre las formas de superponer imágenes en un mapa.

> [!div class="nextstepaction"]
> [Adición de una capa de icono](how-to-add-tile-layer-android-map.md)

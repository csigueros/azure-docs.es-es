---
title: Incorporación de controles a un mapa de Android | Microsoft Azure Maps
description: Cómo agregar control de zoom, control de inclinación, control de giro y un selector de estilos a un mapa en el Android SDK de Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b84150196467606800df737ba9c9d50c69346e30
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093596"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Incorporación de controles a un mapa (Android SDK)

En este artículo se muestra cómo agregar controles de interfaz de usuario a un mapa.

## <a name="add-zoom-control"></a>Agregar un control de zoom

Un control de zoom agrega botones para acercar y alejar el mapa. El siguiente código de ejemplo crea una instancia de la clase `ZoomControl` y la agrega en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

La captura de pantalla siguiente es de un control de zoom cargado en un mapa.

![Control de zoom agregado en el mapa](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Agregar un control de inclinación

Un control de inclinación agrega botones para inclinar el mapa con respecto al horizonte. El siguiente código de ejemplo crea una instancia de la clase `PitchControl` y la agrega en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

La captura de pantalla siguiente es de un control de inclinación cargado en un mapa.

![Control de inclinación agregado al mapa](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Agregar un control de brújula

Un control de brújula agrega un botón para girar el mapa. El siguiente código de ejemplo crea una instancia de la clase `CompassControl` y la agrega en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

La captura de pantalla siguiente es de un control de brújula cargado en un mapa.

![Control de brújula agregado al mapa](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Incorporación de un control de tráfico

Un control de tráfico agrega un botón para alternar la visibilidad de los datos de tráfico en el mapa. El siguiente código de ejemplo crea una instancia de la clase `TrafficControl` y la agrega en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

La captura de pantalla siguiente es de un control de tráfico cargado en un mapa.

![Control de tráfico agregado al mapa](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Mapa con todos los controles

Se pueden colocar varios controles en una matriz, agregarlos al mapa a la vez y colocarlos en la misma área del mapa para simplificar el desarrollo. El código siguiente agrega los controles de navegación estándar al mapa usando este método.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

La captura de pantalla siguiente muestra todos los controles cargados en un mapa. El orden en el que se agreguen al mapa es el orden en el que aparecerán.

![Todos los controles agregados en el mapa](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de código para agregar a los mapas:

> [!div class="nextstepaction"]
> [Adición de una capa de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adición de una capa de línea](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de polígono](how-to-add-shapes-to-android-map.md)

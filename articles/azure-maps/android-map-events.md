---
title: Control de eventos de mapa en los mapas de Android | Microsoft Azure Maps
description: Obtenga información sobre los eventos que se desencadenan cuando los usuarios interactúan con mapas. Vea una lista de todos los eventos de mapa admitidos. Vea cómo usar Android SDK de Azure Maps para controlar eventos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 49a06fdd2c8025515fd42124aa7d00f27d8c5978
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425723"
---
# <a name="interact-with-the-map-android-sdk"></a>Interactuación con el mapa (Android SDK)

En este artículo se muestra cómo usar el administrador de eventos de mapa.

## <a name="interact-with-the-map"></a>Interacción con el mapa

El mapa administra todos los eventos por medio de su propiedad `events`. En la siguiente tabla se muestran todos los eventos de mapa admitidos.

| Evento                  | Formato del controlador de eventos | Descripción |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Se genera después del último fotograma representado antes de que el mapa entre en un estado "inactivo":<ul><li>No hay transiciones de cámara en curso.</li><li>Todos los elementos solicitados actualmente se han cargado.</li><li>Todas las animaciones de fundido y transición se han completado.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Se genera repetidamente durante una transición animada desde una vista a otra, como resultado de la interacción del usuario o los métodos. |
| `OnCameraMoveCanceled` | `()`                 | Se desencadena cuando se cancela una solicitud de movimiento a la cámara. |
| `OnCameraMoveStarted`  | `(int reason)`       | Se genera justo antes de que el mapa inicie una transición desde una vista a otra, como resultado de la interacción del usuario o los métodos. El argumento `reason` del cliente de escucha de eventos devuelve un valor entero que proporciona detalles sobre cómo se inició el movimiento de la cámara. A continuación, se muestra la lista de posibles motivos:<ul><li>1: Gesto</li><li>2: Animación de desarrollador</li><li>3: Animación de API</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | Se genera cuando el mapa se presiona y se libera en el mismo punto del mapa. Este controlador de eventos devuelve un valor booleano que indica si el evento se debe consumir o se debe seguir pasando a otros agentes de escucha de eventos. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | Se genera cuando el mapa se presiona y se libera en el mismo punto de una característica. Este controlador de eventos devuelve un valor booleano que indica si el evento se debe consumir o se debe seguir pasando a otros agentes de escucha de eventos. |
| `OnLayerAdded` | `(Layer layer)` | Se genera cuando se agrega una capa al mapa. |
| `OnLayerRemoved` | `(Layer layer)` | Se genera cuando se quita una capa del mapa. |
| `OnLoaded` | `()` | Se genera inmediatamente después de que se hayan descargado todos los recursos necesarios y de que se haya producido la primera representación visualmente completa del mapa. |
| `OnLongClick`          | `(double lat, double lon): boolean` | Se genera cuando el mapa se presiona, se mantiene durante un momento y, a continuación, se libera en el mismo punto del mapa. Este controlador de eventos devuelve un valor booleano que indica si el evento se debe consumir o se debe seguir pasando a otros agentes de escucha de eventos. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | Se genera cuando el mapa se presiona, se mantiene durante un momento y, a continuación, se libera en el mismo punto de una característica. Este controlador de eventos devuelve un valor booleano que indica si el evento se debe consumir o se debe seguir pasando a otros agentes de escucha de eventos. |
| `OnReady`              | `(AzureMap map)`     | Se desencadena cuando el mapa se carga inicialmente o cuando la orientación de la aplicación cambia, los recursos de mapa mínimos necesarios se cargan y el mapa está listo para que se interactúe con él mediante programación. |
| `OnSourceAdded` | `(Source source)` | Se genera cuando se agrega `DataSource` o `VectorTileSource` al mapa. |
| `OnSourceRemoved` | `(Source source)` | Se genera cuando se quita `DataSource` o `VectorTileSource` del mapa. |
| `OnStyleChange` | `()` | Se genera cuando se carga o cambia el estilo del mapa. |

En el siguiente código se muestra cómo agregar los eventos `OnClick`, `OnFeatureClick` y `OnCameraMove` al mapa.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Para obtener más información, consulte en [Navegación por el mapa](how-to-use-android-map-control-library.md#navigating-the-map) cómo interactuar con el mapa y desencadenar eventos.

## <a name="scope-feature-events-to-layer"></a>Delimitación de los eventos de características a una capa

Al agregar los eventos `OnFeatureClick` o `OnLongFeatureClick` al mapa, se puede pasar un id. de capa o una instancia de capa como segundo parámetro. Cuando se pasa una capa, el evento solo se activa si este se produce en esa capa. Los eventos que se limitan a determinadas capas se admiten en las capas de símbolos, burbujas, líneas y polígonos.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para obtener ejemplos de código completo:

> [!div class="nextstepaction"]
> [Navegación por el mapa](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Adición de una capa de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adición de una capa de línea](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de polígono](how-to-add-shapes-to-android-map.md)

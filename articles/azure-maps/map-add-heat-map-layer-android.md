---
title: Adición de una capa de mapa térmico a mapas de Android | Microsoft Azure Maps
description: Aprenda a crear un mapa térmico. Vea cómo se usa Android SDK de Azure Maps para agregar una capa de mapa térmico a un mapa. Descubra cómo personalizar las capas de mapa térmico.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5e48be82a2486291d2b7fdecf42d759e2c13eb08
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425201"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Adición de una capa de mapa térmico (Android SDK)

Los mapas térmicos, también conocidos como mapas de densidad de puntos, son un tipo de visualización de datos. Se usan para representar la densidad de datos con un rango de colores y muestran las "zonas activas" de los datos en un mapa. Los mapas térmicos son una excelente manera de representar conjuntos de datos con una gran cantidad de puntos.

La representación de decenas de miles de puntos como símbolos puede abarcar la mayor parte del área del mapa. Este caso podría generar la superposición de muchos símbolos. Esto dificulta entender mejor los datos. Sin embargo, la visualización de este mismo conjunto de datos como mapa térmico facilita la visualización de la densidad y la densidad relativa de cada punto de datos.

Puede usar mapas térmicos en muchos escenarios diferentes, entre los que se incluyen:

- **Datos de temperatura**: proporciona aproximaciones en las que la temperatura está entre dos puntos de datos.
- **Datos de sensores de ruido**: no solo muestra la intensidad del ruido en el lugar donde se encuentra el sensor, sino que también puede proporcionar información sobre cómo se va disipando el ruido con la distancia. El nivel de ruido de un sitio podría no ser muy alto. Si las zonas de cobertura de diferentes sensores de ruido se solapan, podría ocurrir que los niveles de ruido fueran mayores en esta área solapada. De ese modo, el área superpuesta sería visible en el mapa térmico.
- **Seguimiento mediante GPS**: incluye la velocidad como un mapa de altura ponderada en el que la intensidad de cada punto de datos depende de la velocidad. Por ejemplo, esta funcionalidad permitiría ver dónde aceleró un vehículo.

> [!TIP]
> De forma predeterminada, las capas de los mapas térmicos representan las coordenadas de todos los objetos geométricos de un origen de datos. Para limitar la capa de forma que solo represente las características geométricas Point, establezca la opción `filter` de la capa en `eq(geometryType(), "Point")`. Si quiera incluir también características MultiPoint, establezca la opción `filter` de la capa en `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de completar los pasos descritos en el documento [Inicio rápido: Creación de una aplicación de Android](quick-android-map.md). Los bloques de código de este artículo se pueden insertar en el controlador de eventos `onReady` de mapas.

## <a name="add-a-heat-map-layer"></a>Adición de una capa de mapa térmico

Para representar un origen de datos de puntos como un mapa térmico, solo tiene que pasar el origen de datos a una instancia de la clase `HeatMapLayer` y agregarlo al mapa.

En el siguiente ejemplo de código se carga una fuente GeoJSON de terremotos de la semana pasada que se representan como un mapa térmico. Cada punto de datos se representa con un radio de 10 píxeles en todos los niveles de zoom. Para garantizar una mejor experiencia del usuario, el mapa térmico está por debajo de la capa de etiquetas para que las etiquetas permanezcan claramente visibles. Los datos de este ejemplo proceden de [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). En este ejemplo se cargan datos GeoJSON desde la Web mediante el bloque de código de la utilidad de importación de datos que se proporciona en [Creación de un origen de datos](create-data-source-android-sdk.md).

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")
```

::: zone-end

En la siguiente captura de pantalla se muestra un mapa que carga un mapa térmico mediante el código anterior.

![Mapa con la capa de mapa térmico de terremotos recientes](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Personalización de la capa de mapa térmico

En el ejemplo anterior se personalizó el mapa térmico con la configuración de las opciones de radio y opacidad. La capa de mapa térmico ofrece varias opciones de personalización:

- `heatmapRadius`: define un radio de píxel en el que representar cada punto de datos. Puede definir el radio como un número fijo o como una expresión. Si utiliza una expresión, es posible escalar el radio en función del nivel de zoom, que parece representar un área espacial coherente del mapa (por ejemplo, un radio de 5 millas).
- `heatmapColor`: especifica cómo se colorea el mapa térmico. Un degradado de color es una característica común de los mapas térmicos. Puede lograr el efecto con una expresión `interpolate`. También se pude usar una expresión `step` para colorear el mapa térmico y separar gráficamente la densidad en intervalos para que se asemeje más a un mapa de radar o con contornos. Las paletas de colores definen los colores desde valores de intensidad mínimos hasta máximos.

  En los mapas térmicos, los valores de los colores se especifican como una expresión del valor `heatmapDensity`. El color del área donde no hay ningún dato se define en el índice 0 de la expresión "Interpolación" o el color predeterminado de una expresión "Escalonada". Puede utilizar este valor para definir un color de fondo. Normalmente, este valor se establece como transparente o en un color negro semitransparente.

  Estos son algunos ejemplos de expresiones de color:

  | Expresión de color de interpolación | Expresión de color escalonado |
  |--------------------------------|--------------------------|
  | interpolate(<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear(), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0, color(Color.TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(Color.MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(parseColor("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(parseColor("#00c3ff")))<br/>)` | step(<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;color(Color.TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.25, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(Color.RED))<br/>) |

- `heatmapOpacity`: especifica el grado de opacidad o transparencia de la capa de mapa térmico.
- `heatmapIntensity`: aplica un multiplicador al peso de cada punto de datos para aumentar la intensidad general del mapa térmico. Esto provoca una diferencia en el peso de los puntos de datos, lo que facilita la visualización.
- `heatmapWeight`: de forma predeterminada, todos los puntos de datos tienen un peso de 1 y, por tanto, todos los puntos tienen la misma ponderación. La opción de ponderación actúa como un multiplicador y puede establecerse como un número o una expresión. Si se establece un número como el peso, es la equivalencia de colocar dos veces cada punto de datos en el mapa. Por ejemplo, si el peso es `2`, la densidad se duplica. Establecer la opción de peso en un número representa el mapa térmico de forma similar a la opción de intensidad.

  Sin embargo, si se usa una expresión, la ponderación de cada punto de datos puede basarse en las propiedades de dicho punto de datos. Por ejemplo, supongamos que cada punto de datos representa un terremoto. El valor de magnitud ha sido una métrica importante para cada punto de datos de un terremoto. Los terremotos se producen a menudo, pero la mayoría tienen una magnitud baja y no se observan. Use el valor de magnitud en una expresión para asignar el peso a cada punto de datos. Al usar el valor de magnitud para asignar el peso, conseguirá una mejor representación de la importancia de los terremotos en el mapa térmico.
- `minZoom` y `maxZoom`: rango de nivel de zoom en el que se debe mostrar la capa.
- `filter`: expresión de filtro que se usa para limitar lo que se recupera del origen y se representa en la capa.
- `sourceLayer`: si el origen de datos conectado a la capa es un origen de mosaico vectorial, debe especificarse una capa de origen dentro de los mosaicos vectoriales.
- `visible`: oculta o muestra la capa.

A continuación se ofrece un ejemplo de un mapa térmico en el que se usa una expresión de interpolación lineal para crear un degradado suave de color. La propiedad `mag` definida en los datos se utiliza con una interpolación exponencial para establecer el peso o la relevancia de cada punto de datos.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

En la siguiente captura de pantalla se muestra la capa de mapa térmico personalizada anterior con los mismos datos del ejemplo de mapa térmico anterior.

![Mapa con la capa de mapa térmico personalizada de terremotos recientes](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Mapa térmico con aplicación de zoom coherente

De forma predeterminada, los radios de los puntos de datos representados en la capa del mapa térmico tienen un radio de píxel fijo para todos los niveles de zoom. A medida que se amplía el mapa, los datos se agregan juntos y la capa de mapa térmico parece diferente. En el siguiente vídeo se muestra el comportamiento predeterminado del mapa térmico en el que se mantiene un radio de píxel cuando se acerca el mapa.

![Animación que muestra el zoom de un mapa con una capa de mapa térmico que presenta un tamaño de píxel coherente](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Utilice una expresión `zoom` para ampliar el radio de cada nivel de zoom de forma que cada punto de datos cubra la misma superficie física del mapa. Esta expresión hará que la capa de mapa térmico parezca más estática y coherente. Cada nivel de zoom del mapa tiene dos veces tantos píxeles vertical y horizontalmente que nivel de zoom anterior.

Si el radio se amplía duplicándose con cada nivel de zoom, se creará un mapa térmico que parecerá coherente en todos los niveles de zoom. Para aplicar este escalado, use `zoom` con una expresión de base 2 `exponential interpolation`, con el radio de píxel establecido para el nivel mínimo de zoom y un radio escalado para el nivel máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)`, tal como se muestra a continuación. Amplía el mapa para ver cómo se escala el mapa térmico con el nivel de zoom.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

En el siguiente vídeo se muestra un mapa que ejecuta el código anterior, que escala el radio mientras se amplía el mapa para crear una representación de mapa térmico coherente entre los niveles de zoom.

![Animación que muestra el zoom de un mapa con una capa de mapa térmico que presenta un tamaño geoespacial coherente](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

La expresión `zoom` solo se puede usar con expresiones `step` y `interpolate`. La expresión siguiente se puede usar para aproximar un radio en metros. Esta expresión usa el marcador de posición `radiusMeters`, que se debe reemplazar por el radio deseado. Esta expresión calcula el radio aproximado de píxeles de un nivel de zoom en el ecuador para los niveles de zoom 0 y 24, y usa una expresión `exponential interpolation` para escalar entre estos valores de la misma manera que funciona el sistema de mosaicos en el mapa.

::: zone pivot="programming-language-java-android"

```java
interpolate(
    exponential(2),
    zoom(),
    stop(1, product(radiusMeters, 0.000012776039596366526)),
    stop(24, product(radiusMeters, 214.34637593279402))
)
```

> [!TIP]
> Si habilita la agrupación en clústeres en el origen de datos, los puntos que estén cerca unos de otros aparecerán como un punto agrupado. Puede usar el recuento de puntos de cada clúster como la expresión de peso para el mapa térmico. Esto puede reducir considerablemente el número de puntos que se van a representar. El número de puntos de un clúster se almacena en la propiedad `point_count` de la característica de puntos:
>
> ```java
> HeatMapLayer layer = new HeatMapLayer(dataSource,
>    heatmapWeight(get("point_count"))
> );
> ```
>
> Si el radio de agrupación en clústeres es solo de unos pocos píxeles, habría una pequeña diferencia visual en la representación. Cuanto mayores sean los grupos de radios, mayor será el número de puntos de cada clúster, lo que mejorará el rendimiento del mapa térmico.

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
interpolate(
    exponential(2),
    zoom(),
    stop(1, product(radiusMeters, 0.000012776039596366526)),
    stop(24, product(radiusMeters, 214.34637593279402))
)
```

> [!TIP]
> Si habilita la agrupación en clústeres en el origen de datos, los puntos que estén cerca unos de otros aparecerán como un punto agrupado. Puede usar el recuento de puntos de cada clúster como la expresión de peso para el mapa térmico. Esto puede reducir considerablemente el número de puntos que se van a representar. El número de puntos de un clúster se almacena en la propiedad `point_count` de la característica de puntos:
>
> ```kotlin
> var layer = new HeatMapLayer(dataSource,
>    heatmapWeight(get("point_count"))
> )
> ```
>
> Si el radio de agrupación en clústeres es solo de unos pocos píxeles, habría una pequeña diferencia visual en la representación. Cuanto mayores sean los grupos de radios, mayor será el número de puntos de cada clúster, lo que mejorará el rendimiento del mapa térmico.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Para más ejemplos de código para agregar a los mapas, consulte los siguientes artículos:

> [!div class="nextstepaction"]
> [Creación de un origen de datos](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Uso de expresiones de estilo controladas por datos](data-driven-style-expressions-android-sdk.md)

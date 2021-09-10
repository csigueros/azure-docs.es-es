---
title: Adición de una capa de símbolo a mapas de Android | Microsoft Azure Maps
description: Obtenga información sobre cómo agregar un marcador a un mapa. Vea un ejemplo en el que se usa el SDK para Android de Azure Maps para agregar una capa de símbolos que contiene datos basados en puntos de un origen de datos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5d0b2e3f04f56b8a379ddbf36c2d9d20dad09d83
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429773"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Adición de una capa de símbolo (Android SDK)

En este artículo se explica cómo representar datos de punto de un origen de datos como una capa de símbolos en un mapa mediante el Android SDK de Azure Maps. Las capas de símbolo representan puntos como una imagen y texto en el mapa.

> [!TIP]
> De forma predeterminada, las capas de símbolo representarán las coordenadas de todos los objetos geométricos en un origen de datos. Para limitar la capa de forma que solo represente las características geométricas del punto, establezca la opción `filter` de la capa en `eq(geometryType(), "Point")`. Si quiera incluir también características MultiPoint, establezca la opción `filter` de la capa en `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`.

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de completar los pasos descritos en el documento [Inicio rápido: Creación de una aplicación de Android](quick-android-map.md). Los bloques de código de este artículo se pueden insertar en el controlador de eventos `onReady` del mapa.

## <a name="add-a-symbol-layer"></a>Adición de una capa de símbolo

Para poder agregar una capa de símbolos al mapa, debe realizar primero algunas operaciones. En primer lugar, cree un origen de datos y agréguelo al mapa. Cree una capa de símbolos. Pase el origen de datos a la capa de símbolos para recuperar los datos de este origen. Por último, agregue datos al origen de datos para que haya algo que representar.

En el código siguiente se muestra lo que debe agregarse al mapa una vez que se haya cargado. En este ejemplo se representa un único punto en el mapa mediante una capa de símbolos.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Hay tres tipos diferentes de datos de puntos que se pueden agregar al mapa:

- Geometría Point de GeoJSON: Este objeto solo contiene una coordenada de un punto y nada más. El método estático `Point.fromLngLat` se puede usar para crear fácilmente estos objetos.
- Geometría MultiPoint de GeoJSON: este objeto solamente contiene las coordenadas de varios puntos; no contiene nada más. Pase una matriz de puntos a la clase `MultiPoint` para crear estos objetos.
- GeoJSON Feature: Este objeto se compone de cualquier geometría GeoJSON y un conjunto de propiedades que contienen metadatos asociados a la geometría.

Para obtener más información, vea el documento [Creación de un origen de datos](create-data-source-android-sdk.md) sobre cómo crear y agregar datos al mapa.

En el ejemplo de código siguiente se crea una geometría Point de GeoJSON, se pasa a la característica de GeoJSON y se le agrega un valor `title` a sus propiedades. La propiedad `title` se muestra como texto encima del icono de símbolo en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

En la captura de pantalla siguiente se muestra el código anterior que representa una característica de punto mediante un icono y una etiqueta de texto con una capa de símbolo.

![Mapa con punto representado mediante una capa de símbolo que muestra un icono y una etiqueta de texto para una característica de punto](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> De forma predeterminada, las capas de símbolos optimizan la representación de los símbolos, ya que ocultan los símbolos que se superponen. A medida que se acerca el zoom, los símbolos ocultos se hacen visibles. Para deshabilitar esta característica y representar todos los símbolos en todo momento, establezca las opciones `iconAllowOverlap` y `textAllowOverlap` en `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer&quot;></a>Adición de un icono personalizado a una capa de símbolo

Las capas de símbolo se representan mediante WebGL. Por tanto, todos los recursos, como las imágenes de icono, se deben cargar en el contexto de WebGL. En este ejemplo, se muestra cómo se agrega un icono personalizado a los recursos del mapa. Este icono se usa después para representar los datos de punto en el mapa con un símbolo personalizado. La propiedad `textField` de la capa de símbolo requiere que se especifique una expresión. En este caso, queremos representar la propiedad &quot;temperature&quot;. Como la temperatura es un número, debe convertirse en una cadena. Además, queremos anexarle &quot;°F&quot;. Para ello, podemos utilizar la expresión `concat(Expression.toString(get(&quot;temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

En este ejemplo, la imagen siguiente se ha cargado en la carpeta Drawable de la aplicación.

| ![Imagen del icono meteorológico de tormentas](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

En la captura de pantalla siguiente se muestra el código anterior que representa una característica de punto mediante un icono personalizado y una etiqueta de texto con formato con una capa de símbolo.

![Mapa con punto representado mediante una capa de símbolo que muestra un icono personalizado y una etiqueta de texto con formato para una característica de punto](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Si solamente desea representar texto con una capa de símbolos, puede ocultar el icono estableciendo la propiedad `iconImage` de las opciones del icono en `"none&quot;`.

## <a name=&quot;modify-symbol-colors&quot;></a>Modificación de los colores de símbolos

La instancia de Android SDK de Azure Maps incluye un conjunto de variaciones de color predefinidas del icono de marcador predeterminado. Por ejemplo, `marker-red` se puede pasar a la opción `iconImage` de una capa de símbolos para representar una versión roja del icono de marcador en esa capa.

::: zone pivot=&quot;programming-language-java-android&quot;

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage(&quot;marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

En la tabla siguiente se enumeran todos los nombres de imagen de icono integrados que hay disponibles. Todos estos marcadores extraen sus colores de los recursos de color que se pueden invalidar. Además, puede invalidar el color de relleno principal de este marcador. Sin embargo, la invalidación del color de uno de estos marcadores se aplicará a todas las capas que usen esa imagen de icono.

| Nombre de la imagen de icono | Nombre del recurso de color |
|-----------------|---------------------|
| `marker-default` | `azure_maps_marker_default` |
| `marker-black` | `azure_maps_marker_black` |
| `marker-blue` | `azure_maps_marker_blue` |
| `marker-darkblue` | `azure_maps_marker_darkblue` |
| `marker-red` | `azure_maps_marker_red` |
| `marker-yellow` | `azure_maps_marker_yellow` |

También puede invalidar el color del borde de todos los marcadores mediante el nombre del recurso de color `azure_maps_marker_border`. Los colores de estos marcadores se pueden invalidar agregando un color con el mismo nombre en el archivo `colors.xml` de la aplicación. Por ejemplo, el siguiente archivo `colors.xml` hace que el color del marcador predeterminado sea verde claro.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="azure_maps_marker_default">#00FF00</color>
</resources>
```

El código siguiente es una versión modificada del XML de vector del marcador predeterminado que puede modificar para crear versiones personalizadas del marcador predeterminado. Asimismo, la versión modificada puede agregarse a la carpeta `drawable` de la aplicación y agregarse a la imagen de mapas de sprite mediante `map.images.add` y, a continuación, usarse con una capa de símbolos.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/azure_maps_marker_default"
        android:strokeColor="@color/azure_maps_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de código para agregar a los mapas:

> [!div class="nextstepaction"]
> [Creación de un origen de datos](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Datos de punto de clúster](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Uso de expresiones de estilo controladas por datos](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Presentación de la información de características](display-feature-information-android.md)

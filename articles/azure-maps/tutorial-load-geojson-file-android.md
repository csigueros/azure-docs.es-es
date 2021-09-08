---
title: 'Tutorial: Carga de datos GeoJSON en Android SDK de Azure Maps | Microsoft Azure Maps'
description: Tutorial sobre la carga de archivos de datos GeoJSON en Android SDK de Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b36c82a12b984aa956cc7d9d41cbc13ffb3d083f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425219"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Tutorial: Carga de datos GeoJSON en Android SDK de Azure Maps

Este tutorial le guiará por el proceso de importación de un archivo GeoJSON de datos de ubicación en Android SDK de Azure Maps. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Incorporar Azure Maps a una aplicación para Android.
> * Crear un origen de datos y cargarlo en un archivo GeoJSON desde un archivo local o desde la web.
> * Mostrar los datos en el mapa.
> * Interactuar con los datos de los mapas para ver sus detalles.

## <a name="prerequisites"></a>Prerrequisitos

1. Complete la guía [Inicio rápido: Creación de una aplicación para Android](quick-android-map.md). En este tutorial se ampliará el código de este inicio rápido.
2. Descargue los [puntos de interés de ejemplo](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) del archivo GeoJSON.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importación de los datos GeoJSON de la web o de la carpeta de recursos

La mayoría de los archivos GeoJSON encapsulan todos los datos en `FeatureCollection`. Teniendo en cuenta este escenario, si los archivos GeoJSON se cargan en la aplicación en forma de cadena, se pueden pasar al método `fromJson` estático de la colección de características, que deserializará la cadena en un objeto `FeatureCollection` GeoJSON que se puede agregar al mapa.

En los pasos siguientes se muestra cómo importar un archivo GeoJSON en la aplicación y deserializarlo como objeto `FeatureCollection` de GeoJSON.

1. Complete la guía [Inicio rápido: Creación de una aplicación para Android](quick-android-map.md), ya que los pasos siguientes se basan en el uso de esta aplicación.
2. En el panel del proyecto de Android Studio, haga clic con el botón derecho en la carpeta **app** y vaya a `New > Folder > Assets Folder`.
3. Arrastre y coloque los [puntos de interés de ejemplo](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) del archivo GeoJSON en la carpeta de recursos.

::: zone pivot="programming-language-java-android"

4. Vaya al archivo **MainActivity.java** y agregue el siguiente código en la devolución de llamada para el evento `mapControl.onReady`, dentro del método `onCreate`. Este código carga el archivo **SamplePoiDataSet.json** de la carpeta assets en un origen de datos mediante el método `importDataFromUrl` y, después, lo agrega al mapa.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Vaya al archivo **MainActivity.kt** y agregue el siguiente código en la devolución de llamada para el evento `mapControl.onReady`, dentro del método `onCreate`. Este código carga el archivo **SamplePoiDataSet.json** de la carpeta assets en un origen de datos mediante el método `importDataFromUrl` y, después, lo agrega al mapa.

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

5. Mediante el código para cargar los datos GeoJSON como un origen de datos, ahora debemos especificar cómo se deben mostrar los datos en el mapa. Hay varias capas de representación diferentes para los datos de punto; la [capa de burbujas](map-add-bubble-layer-android.md), la [capa de símbolos](how-to-add-symbol-to-android-map.md) y la [capa de mapas térmicos](map-add-heat-map-layer-android.md) son los niveles más frecuentes. Agregue el código siguiente para representar los datos de una capa de burbujas en la devolución de llamada para el evento `mapControl.onReady` después del código para importar los datos.

::: zone pivot="programming-language-java-android"

```java
//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a layer and add it to the map.
val layer = new BubbleLayer(source)
map.layers.add(layer)
```

::: zone-end

6. En el panel de proyectos de Android Studio, haga clic con el botón derecho en la carpeta **layout** en la ruta de acceso `app > res > layout` y vaya a `New > File`. Cree un archivo denominado **popup_text.xml**.
7. Abra el archivo **popup_text.xml**. Si el archivo se abre en una vista del diseñador, haga clic con el botón derecho en la pantalla y seleccione "Ir a XML". Copie y pegue el siguiente XML en este archivo. Este XML crea un diseño simple que se puede usar con un elemento emergente y contiene una vista de texto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

::: zone pivot="programming-language-java-android"

8. Vuelva al archivo **MainActivity.java** y, después del código de la capa de burbujas, agregue el código siguiente para crear un elemento emergente reutilizable.

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

//Close it initially.
popup.close();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

8. Vuelva al archivo **MainActivity.java** y, después del código de la capa de burbujas, agregue el código siguiente para crear un elemento emergente reutilizable.

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)
    
//Close it initially.
popup.close()
```

::: zone-end

9. Agregue el código siguiente para asociar un evento de clic a la capa de burbujas. Cuando se pulse cualquiera de las burbujas de la capa de burbujas, el evento se activará y recuperará algunos detalles de las propiedades de la característica seleccionada, creará una vista mediante el archivo de diseño **popup_text.xml**, lo pasará como contenido al elemento emergente y, después, se mostrará el elemento emergente en la posición de las características.

::: zone pivot="programming-language-java-android"

```java
//Add a click event to the layer.
map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Display the name and entity type information of the feature into the text view of the popup layout.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText("%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    );

    //Get the position of the clicked feature.
    Position pos = MapMath.getPosition((Point)f.geometry());

    //Set the options on the popup.
    popup.setOptions(
            //Set the popups position.
            position(pos),

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),

            //Set the content of the popup.
            content(customView)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    return false;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a click event to the layer.
map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Display the name and entity type information of the feature into the text view of the popup layout.
    val tv = customView.findViewById<TextView>(R.id.message)
    tv.text = String.format(
        "%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    )

    //Get the position of the clicked feature.
    val pos = MapMath.getPosition(f.geometry() as Point?)

    //Set the options on the popup.
    popup.setOptions( //Set the popups position.
        position(pos),  //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  //Set the content of the popup.
        content(customView)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    false
} as OnFeatureClick, layer)
```

::: zone-end

10. Ejecute la aplicación. Se mostrará un mapa con burbujas superpuestas para cada ubicación en el archivo GeoJSON. Al pulsar en cualquiera de ellas se mostrará un elemento emergente con el nombre y tipo de entidad de la característica tocada.

    ![Mapa de datos de un archivo GeoJSON en el que se muestra que se ha abierto un elemento emergente cuando se pulsa la ubicación](media/tutorial-load-geojson-file-android/android-import-geojson.gif)

## <a name="clean-up-resources"></a>Limpieza de recursos

Siga estos pasos para limpiar los recursos de este tutorial:

1. Cierre Android Studio y elimine la aplicación que ha creado.
2. Si ha probado la aplicación en un dispositivo externo, desinstálela en ese dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Para ver más ejemplos de código y una experiencia interactiva de codificación:

> [!div class="nextstepaction"]
> [Uso de expresiones de estilo controladas por datos](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Presentación de la información de características](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Adición de una capa de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de una capa de línea](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de polígono](how-to-add-shapes-to-android-map.md)

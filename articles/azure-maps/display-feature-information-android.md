---
title: Visualización de información de características en los mapas de Android | Microsoft Azure Maps
description: Aprenda a mostrar información cuando los usuarios interactúan con las características de mapas. Use Android SDK de Azure Maps para mostrar las notificaciones del sistema y otros tipos de mensajes.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a8bd232ccba2adb3666ee7fd54983be2ed1f7296
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426443"
---
# <a name="display-feature-information"></a>Mostrar información de características

Los datos espaciales a menudo se representan mediante puntos, líneas y polígonos. Estos datos con frecuencia tienen información de metadatos asociada. Por ejemplo, un punto puede representar la ubicación de un restaurante y los metadatos de ese restaurante pueden ser su nombre, dirección y el tipo de comida que sirve. Estos metadatos se pueden agregar como propiedades de un elemento `Feature` GeoJSON. El siguiente código crea una característica de punto simple con una propiedad `title` cuyo valor es "Hello world!"

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Consulte [Creación de un origen de datos](create-data-source-android-sdk.md) para obtener información sobre cómo crear y agregar datos al mapa.

Cuando un usuario interactúa con cualquier característica del mapa, se pueden usar eventos para reaccionar ante esas acciones. Un escenario común es mostrar un mensaje creado con las propiedades de los metadatos de una característica con la que el usuario ha interactuado. `OnFeatureClick` es el evento principal que se usa para detectar cuándo el usuario ha pulsado una característica en el mapa. También hay un evento `OnLongFeatureClick`. Al agregar el evento `OnFeatureClick` al mapa, se puede limitar a una sola capa. Para ello, solo hay que pasar el identificador de una capa. Si no se pasa ningún identificador de capa, al pulsar cualquiera de las características del mapa se activaría este evento, independientemente de la capa en la que se encuentre. El código siguiente crea una capa de símbolos que representa datos de puntos en el mapa y, después, agrega un evento `OnFeatureClick` y lo limita a esta capa de símbolos.

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Presentación de mensajes de notificación del sistema

Un mensaje de notificación del sistema es una de las formas más sencillas de mostrar información al usuario y está disponible en todas las versiones de Android. No admite ningún tipo de entrada del usuario y solo se muestra durante un período breve. Si desea que el usuario sepa algo acerca del elemento que ha pulsado, un mensaje de notificación del sistema puede ser una buena opción. En el código siguiente se muestra cómo se puede utilizar un mensaje de notificación del sistema con el evento `OnFeatureClick`.

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Animación de una característica que se pulsa y se muestra un mensaje de notificación del sistema](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Además de los mensajes de notificación del sistema, hay muchas otras maneras de presentar las propiedades de los metadatos de una característica, como:

- [Widget Snackbar](https://developer.android.com/training/snackbar/showing.html): los `Snackbars` proporcionan pequeños comentarios acerca de una operación. Muestran un mensaje corto en la parte inferior de la pantalla del dispositivo móvil y en la parte inferior izquierda en dispositivos mayores. Los `Snackbars` aparecen encima de los demás elementos de la pantalla y no pueden mostrarse varios a la vez.
- [Cuadros de diálogo](https://developer.android.com/guide/topics/ui/dialogs): un cuadro de diálogo es una ventana pequeña que solicita al usuario que tome una decisión o escriba más información. Un cuadro de diálogo no llena la pantalla y normalmente se usa para eventos modales que requieren que los usuarios realicen una acción para poder continuar.
- Agregue un [fragmento](https://developer.android.com/guide/components/fragments) a la actividad actual.
- Vaya a otra actividad o vista.

## <a name="display-a-popup"></a>Mostrar una ventana emergente

El Android SDK de Azure Maps proporciona una clase `Popup` que facilita la creación de elementos de anotación de interfaz de usuario que estén anclados en una posición del mapa. En el caso de los elementos emergentes, debe pasar una vista con un diseño relativo en la opción `content` del elemento emergente. Este es un ejemplo de diseño sencillo que muestra texto oscuro encima de un fondo blanco.

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

Suponiendo que el diseño anterior está almacenado en un archivo denominado `popup_text.xml` en la carpeta `res -> layout` de una aplicación, el siguiente código crea un elemento emergente y lo agrega al mapa. Cuando se hace clic en una característica, la propiedad `title` se muestra mediante el diseño `popup_text.xml`, con el centro inferior del diseño anclado a la posición especificada en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the position of the clicked feature.
    Position pos = MapMath.getPosition((Point)cluster.geometry());

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
            
        //Optionally offset the popup by a specified number of pixels.
        //pixelOffset(new Pixel(10, 10))
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the position of the clicked feature.
    val pos = MapMath.getPosition(f.geometry() as Point?);

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
            
        //Optionally offset the popup by a specified number of pixels.
        //pixelOffset(Pixel(10, 10))
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    false
})
```

::: zone-end

En la siguiente captura de pantalla se muestran los elementos emergentes que aparecen cuando se hace clic en las características, y permanecen anclados a su ubicación especificada en el mapa a medida que este se mueve.

![Animación de un elemento emergente que se muestra y el mapa que se mueve con el elemento emergente anclado a una posición en el mapa.](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Pasos siguientes

Para agregar más datos al mapa:

> [!div class="nextstepaction"]
> [Reacción a eventos de mapa](android-map-events.md)

> [!div class="nextstepaction"]
> [Creación de un origen de datos](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adición de una capa de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adición de una capa de línea](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de polígono](how-to-add-shapes-to-android-map.md)

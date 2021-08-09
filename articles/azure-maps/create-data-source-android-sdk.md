---
title: Creación de un origen de datos para mapas de Android | Microsoft Azure Maps
description: 'Obtenga información sobre cómo crear un origen de datos para un mapa. Conozca los orígenes de datos que usa Android SDK para Azure Maps: Orígenes GeoJSON e iconos vectoriales.'
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 7ba00e1f3bd28b3fa24b14bc31080655a8d4c98b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110792089"
---
# <a name="create-a-data-source-android-sdk"></a>Creación de un origen de datos (Android SDK)

Android SDK para Azure Maps almacena los datos en orígenes de datos. El uso de orígenes de datos optimiza las operaciones de datos en las consultas y representaciones. Actualmente hay dos tipos de orígenes de datos:

- **Origen GeoJSON**: administra localmente los datos de ubicación sin procesar en formato GeoJSON. Adecuado para conjuntos de datos de tamaño pequeño a medio (con cientos de miles de formas).
- **Origen de mosaico vectorial**: carga los datos con formato de mosaico vectorial para la vista del mapa actual, en función del sistema de mosaico de mapas. Ideal para conjuntos de datos grandes o masivos (millones o miles de millones de formas).

## <a name="geojson-data-source"></a>Origen de datos de GeoJSON

Azure Maps usa GeoJSON como uno de sus modelos de datos principales. GeoJSON es un estándar geoespacial abierto para representar datos geoespaciales en formato JSON. Las clases de GeoJSON disponibles en Android SDK para Azure Maps permiten crear y serializar datos GeoJSON de manera sencilla. Cargue y almacene datos de GeoJSON en la clase `DataSource` y represéntelos mediante capas. En el siguiente código se muestra cómo crear objetos GeoJSON en Azure Maps.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

Como alternativa, las propiedades se pueden cargar en un JsonObject en primer lugar y luego pasarlos a la característica al crearla, tal y como se muestra a continuación.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

Una vez que se ha creado una característica de GeoJSON, se puede agregar un origen de datos al mapa mediante la propiedad `sources` del mapa. En el código siguiente se muestra cómo crear una clase `DataSource`, agregarla al mapa y agregar una característica al origen de datos.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

En el código siguiente se muestran varias maneras de crear una característica de GeoJSON, la clase FeatureCollection y geometrías.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>Serialización y deserialización de GeoJSON

Todas las clases de colección de características, de característica y de geometría tienen métodos estáticos `fromJson()` y `toJson()`, lo que ayuda con la serialización. La cadena JSON válida con formato que se pasa mediante el método `fromJson()` creará el objeto de geometría. Este método `fromJson()` también implica que puede usar Gson u otras estrategias de serialización y deserialización. En el código siguiente se muestra cómo tomar una característica de GeoJSON con formato de cadena y deserializarla en la clase Feature y luego serializarla de nuevo en una cadena de GeoJSON.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importación de datos de GeoJSON de la web o de la carpeta de recursos

La mayoría de los archivos GeoJSON contienen una clase FeatureCollection. Lea archivos GeoJSON como cadenas y use el método `FeatureCollection.fromJson` para deserializarlos.

El código siguiente es una clase reutilizable para importar datos de la carpeta de recursos local o la web como cadena y devolverlos al subproceso de interfaz de usuario mediante una función de devolución de llamada.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

En el código siguiente se muestra cómo usar esta utilidad para importar datos de GeoJSON como cadena y devolverlos al subproceso de la interfaz de usuario mediante una devolución de llamada. En la devolución de llamada, los datos de cadena se pueden serializar en una colección de características GeoJSON y agregarse al origen de datos. Opcionalmente, actualice la cámara de Maps para centrarse en los datos.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>Origen de mosaico vectorial

Un origen de mosaico vectorial describe cómo acceder a una capa de mosaico vectorial. Use la clase `VectorTileSource` para crear una instancia de un origen de mosaico vectorial. Las capas de mosaico vectorial son similares a las de mosaico, pero no son iguales. Una capa de mosaico es una imagen de trama. Las capas de mosaico vectorial son un archivo comprimido, en formato **PBF**. Este archivo comprimido contiene datos de mapas vectoriales y una o varias capas. El archivo se puede representar en el cliente, y aplicarle un estilo, en función del estilo de cada capa. Los datos de un mosaico vectorial contienen características geográficas en forma de puntos, líneas y polígonos. El uso de capas de mosaico vectorial en lugar de capas de mosaico de trama presenta varias ventajas:

- Un tamaño de archivo de un mosaico vectorial suele ser mucho menor que el de un mosaico de trama equivalente. Como tal, se usa menos ancho de banda. Esto significa una menor latencia, un mapa más rápido y una mejor experiencia de usuario.
- Como los mosaicos vectoriales se representan en el cliente, pueden adaptarse a la resolución del dispositivo en el que se muestran. Como resultado, los mapas representados aparecen mejor definidos, con etiquetas nítidas.
- Para cambiar el estilo de los datos en los mapas vectoriales no es necesario descargar los datos de nuevo, ya que el nuevo estilo se puede aplicar en el cliente. Al contrario, para cambiar el estilo de una capa de mosaico de trama es necesario normalmente cargar los mosaicos desde el servidor y luego aplicar el nuevo estilo.
- Dado que los datos se entregan en forma de vector, se requiere menos procesamiento del lado servidor para preparar los datos. Como resultado, los datos más recientes pueden estar disponibles más rápido.

Azure Maps se adhiere a la [especificación de mosaicos vectoriales de Mapbox](https://github.com/mapbox/vector-tile-spec), un estándar abierto. Azure Maps proporciona los siguientes servicios de mosaicos vectoriales como parte de la plataforma:

- [Documentación](/rest/api/maps/renderv2/getmaptilepreview) | [Detalles del formato de datos](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) de mosaicos de carreteras
- [Documentación](/rest/api/maps/traffic/gettrafficincidenttile) | [Detalles del formato de datos](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) de incidentes de tráfico
- [Documentación](/rest/api/maps/traffic/gettrafficflowtile) | [Detalles del formato de datos](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) de flujo de tráfico
- Creator de Azure Maps también permite crear mosaicos vectoriales personalizados y acceder a ellos a través de [Render V2-Get Map Tile API](/rest/api/maps/renderv2/getmaptilepreview).

> [!TIP]
> Al usar mosaicos de imagen vectoriales o de trama del servicio de representación de Azure Maps con el SDK web, puede reemplazar `atlas.microsoft.com` por el marcador de posición `azmapsdomain.invalid`. Este marcador de posición se reemplazará por el mismo dominio usado por el mapa y también anexará automáticamente los mismos detalles de autenticación. Esto simplifica considerablemente la autenticación con el servicio de representación al usar la autenticación de Azure Active Directory.

Para mostrar los datos de un origen de mosaico vectorial en el mapa, conecte el origen a una de las capas de representación de datos. Todas las capas que usan un origen vectorial deben especificar un valor `sourceLayer` en las opciones. El código siguiente carga el servicio de mosaico vectorial de flujo de tráfico de Azure Maps como un origen de mosaico vectorial y, a continuación, lo muestra en un mapa mediante una capa de línea. Este origen de mosaico vectorial tiene un único conjunto de datos en la capa de origen denominado "Traffic flow". Los datos de línea de este conjunto de datos tienen una propiedad denominada `traffic_level` que se usa en este código para seleccionar el color y escalar el tamaño de las líneas.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![Mapa con líneas de carreteras codificadas por colores que muestran los niveles de flujo de tráfico](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Conexión de un origen de datos a una capa

Los datos se representan en el mapa mediante las capas de representación. Una o varias capas de representación pueden hacer referencia a un único origen de datos. Las siguientes capas de representación requieren un origen de datos:

- [Capa de burbuja](map-add-bubble-layer-android.md): representa los datos de puntos como círculos a escala en el mapa.
- [Capa de símbolo](how-to-add-symbol-to-android-map.md): representa los datos de punto como iconos o texto.
- [Capa de mapa térmico](map-add-heat-map-layer-android.md): representa datos de puntos como un mapa térmico de densidad.
- [Capa de línea](android-map-add-line-layer.md): representa una línea o el contorno de los polígonos.
- [Capa de polígono](how-to-add-shapes-to-android-map.md): rellena el área de un polígono con un patrón de imagen o un color sólido.

En el código siguiente se muestra cómo crear un origen de datos, agregarlo al mapa y conectarlo a una capa de burbuja. Luego, importe los datos de punto de GeoJSON desde una ubicación remota hasta el origen de datos.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
        //Optionally, update the maps camera to focus in on the data.
        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc)
    
        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20)
        )
    }
```

::: zone-end

Hay capas de representación adicionales que no se conectan a estos orígenes de datos, sino que cargan los datos directamente para representarlos.

- [Capa de mosaico](how-to-add-tile-layer-android-map.md): superpone una capa de mosaico de trama en la parte superior del mapa.

## <a name="one-data-source-with-multiple-layers"></a>Un origen de datos con varias capas

Varias capas pueden estar conectadas a un solo origen de datos. Hay muchos escenarios diferentes en los que esta opción es útil. Por ejemplo, considere aquel en el que un usuario dibuja un polígono. Debemos representar y rellenar el área del polígono a medida que el usuario agrega puntos al mapa. Agregar una línea con estilo para delinear el polígono permite ver más fácilmente sus bordes, a medida que el usuario dibuja. Para editar cómodamente una posición individual del polígono, podemos agregar un identificador, como una chincheta o una marca, encima de cada posición.

![Mapa que muestra varias capas que representan datos de un único origen de datos](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

En la mayoría de las plataformas de mapas, necesitaría un objeto de polígono, un objeto de línea y una chincheta para cada posición del polígono. A medida que le polígono se modifica, tendría que actualizar manualmente las líneas y las chinchetas, que pueden volverse complejas rápidamente.

Con Azure Maps, lo único que necesita es un solo polígono en un origen de datos, como se muestra en el siguiente código.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> Al agregar capas al mapa mediante el método `map.layers.add`, el identificador o la instancia de una capa existente se pueden pasar como segundo parámetro. Esto indicaría a ese mapa que debe insertar la nueva capa que se va a agregar debajo de la capa existente. Además de pasar un identificador de capa, este método también admite los valores siguientes.
>
> - `"labels"`: inserta la nueva capa debajo de las capas de etiqueta del mapa.
> - `"transit"`: inserta la nueva capa debajo de las capas de carretera y tránsito del mapa.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de código para agregar a los mapas:

> [!div class="nextstepaction"]
> [Uso de expresiones de estilo controladas por datos](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adición de una capa de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de una capa de burbuja](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adición de una capa de línea](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adición de una capa de polígono](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Adición de un mapa térmico](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Ejemplos de código de SDK web](/samples/browse/?products=azure-maps)
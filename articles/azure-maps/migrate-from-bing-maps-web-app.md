---
title: 'Tutorial: Migración de una aplicación web desde Mapas de Bing | Microsoft Azure Maps'
description: Tutorial sobre cómo migrar una aplicación web desde Mapas de Bing a Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: adec0b04e82d0a00b2f95b6b4f7980f628b93a7a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439639"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>Tutorial: Migración de una aplicación web desde Mapas de Bing

Las aplicaciones web que usan Mapas de Bing suelen usar el SDK de JavaScript de Mapas de Bing V8. El SDK web para Azure Maps es el SDK adecuado basado en Azure al que se debe migrar. El SDK web para Azure Maps permite personalizar mapas interactivos con contenido propio e imágenes para su presentación en las aplicaciones web o móviles. Este control usa WebGL, lo que permite representar grandes conjuntos de datos con alto rendimiento. Desarrolle con este SDK mediante JavaScript o TypeScript. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Carga de un mapa
> * Localizar un mapa
> * Agregar marcadores, polilíneas y polígonos.
> * Mostrar información en una ventana emergente o informativa.
> * Cargar y mostrar datos de KML y GeoJSON
> * Marcadores de clúster
> * Superposición de una capa de mosaicos
> * Mostrar datos del tráfico
> * Adición de una superposición de suelo

Si migra una aplicación web existente, compruebe si usa una biblioteca de control de mapa de código abierto, como Cesium, Leaflet y OpenLayers. Si es así y prefiere seguir usando esa biblioteca, puede conectarla a los servicios de mosaico de Azure Maps ([mosaicos de carretera](/rest/api/maps/render/getmaptile) \| [mosaicos de satélite](/rest/api/maps/render/getmapimagerytile)). Los vínculos siguientes proporcionan información detallada sobre el uso de Azure Maps en algunas bibliotecas de control de mapa de código abierto usadas habitualmente.

* [Cesium](https://www.cesium.com/): un control de mapa 3D para la Web. [Ejemplos de código](https://azuremapscodesamples.azurewebsites.net/?search=Cesium) \| [Repositorio de complementos]()
* [Leaflet](https://leafletjs.com/): un control de mapa 2D ligero para la Web. [Ejemplos de código](https://azuremapscodesamples.azurewebsites.net/?search=leaflet) \| [Repositorio de complementos]()
* [OpenLayers](https://openlayers.org/): un control de mapa 2D para la Web que admite proyecciones. [Ejemplos de código](https://azuremapscodesamples.azurewebsites.net/?search=openlayers) \| [Repositorio de complementos]()

Si desarrolla aplicaciones en un entorno JavaScript, puede que uno de los siguientes proyectos de código abierto resulte útil:

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): contenedor de Angular 10 para Azure Maps.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): un componente Blazor de Azure Maps.
* [Componente React de Azure Maps](https://github.com/WiredSolutions/react-azure-maps): un contenedor de React para el control de Azure Maps.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) : un componente de Azure Maps para la aplicación Vue.

## <a name="prerequisites"></a>Requisitos previos

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
2. [Cree una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
3. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción. Para más información sobre la autenticación en Azure Maps, consulte [Administración de la autenticación en Azure Maps](how-to-manage-authentication.md).

## <a name="key-features-support"></a>Compatibilidad de características clave

En la siguiente tabla se indican las principales características de la API del SDK de JavaScript para Mapas de Bing V8 y la compatibilidad de una API similar en el SDK web de Azure Maps.

| Característica de Mapas de Bing        | Compatibilidad del SDK web de Azure Maps                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Marcadores                 | ✓                                                                                      |
| Agrupación en clústeres de marcadores       | ✓                                                                                      |
| Polilíneas y polígonos     | ✓                                                                                      |
| Superposiciones de suelo          | ✓                                                                                      |
| Mapas térmicos                | ✓                                                                                      |
| Capas de mosaicos              | ✓                                                                                      |
| Capa KML                | ✓                                                                                      |
| Capa de contorno            | [Ejemplos](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Capa de discretización de datos       | Incluido en el [módulo de origen de datos en cuadrícula](https://github.com/Azure-Samples/azure-maps-gridded-data-source) de Azure Maps de código abierto       |
| Capa de mosaicos animados      | Se incluye en el [módulo de animación](https://github.com/Azure-Samples/azure-maps-animations) de Azure Maps de código abierto. |
| Diseño de herramientas            | ✓                                                                                      |
| Servicio de codificador geográfico         | ✓                                                                                      |
| Servicio de direcciones       | ✓                                                                                      |
| Servicio de matriz de distancia  | ✓                                                                                      |
| Servicio de datos espaciales     | N/D                                                                                    |
| Imágenes aéreas/por satélite | ✓                                                                                      |
| Imágenes de vista aérea         | N/D                                                                                |
| Imágenes de Streetside       | N/D                                                                                |
| Compatibilidad con GeoJSON          | ✓                                                                                      |
| Compatibilidad con GeoXML           | ✓ [Módulo de E/S espacial](how-to-use-spatial-io-module.md)                                                                                     |
| Compatibilidad con texto conocido  | ✓                                                                                      |
| Estilos de mapa personalizados        | Parcial                                                                                |

Azure Maps también cuenta con muchos [módulos de código abierto para el SDK web](open-source-projects.md#open-web-sdk-modules) adicionales que amplían sus funcionalidades.

## <a name="notable-differences-in-the-web-sdks"></a>Diferencias notables en los SDK web

Las siguientes son algunas de las diferencias principales entre los SDK web de Mapas de Bing y Azure Maps que se deben tener en cuenta:

* Además de proporcionar un punto de conexión hospedado para acceder al SDK web para Azure Maps, también está disponible un paquete NPM para insertar el SDK web en aplicaciones, si lo prefiere. Para más información, consulte esta [documentación](./how-to-use-map-control.md). Este paquete también incluye las definiciones de TypeScript.
* Mapas de Bing proporciona dos ramas hospedadas de su SDK: versión y experimental. La rama experimental puede recibir varias actualizaciones al día cuando se están realizando nuevos desarrollos. Azure Maps solo hospeda una rama de versión; sin embargo, se crean características experimentales como módulos personalizados en el proyecto de ejemplos de Azure Maps de código abierto. Mapas de Bing solía tener también una rama inmovilizada que se actualizaba con menos frecuencia, lo que reducía el riesgo de cambios importantes debidos a una versión. En Azure Maps, puede usar el módulo NPM y apuntar a cualquier versión secundaria anterior.

> [!TIP]
> Azure Maps publica tanto las versiones compactadas como las descompactadas del SDK. Solo tiene que quitar `.min` de los nombres de archivo. La versión descompactada es útil al depurar problemas, pero asegúrese de usar la versión compactada en producción para aprovechar las ventajas del tamaño de archivo más pequeño.

* Después de crear una instancia de la clase Map en Azure Maps, el código debe esperar a que se active el evento `ready` o `load` del mapa antes de interactuar con el mapa. Estos eventos garantizan que todos los recursos del mapa se hayan cargado y estén listos para acceder a ellos.
* Ambas plataformas usan un sistema de mosaicos similar para los mapas base; sin embargo, los mosaicos de Mapas de Bing tienen una dimensión de 256 píxeles, mientras que los de Azure Maps tienen 512 píxeles. Por lo tanto, para obtener la misma vista de mapa en Azure Maps que en Mapas de Bing, al nivel de zoom usado en Mapas de Bing se le debe restar uno en Azure Maps.
* En Mapas de Bing, las coordenadas tienen el formato `latitude, longitude`, mientras que en Azure Maps se usa `longitude, latitude`. Este formato se alinea con el `[x, y]` estándar que siguen la mayoría de las plataformas de GIS.

* Las formas del SDK web para Azure Maps se basan en el esquema GeoJSON. Las clases auxiliares se exponen a través del espacio de nombres [atlas.data](/javascript/api/azure-maps-control/atlas.data). También existe la clase [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) que se puede usar para encapsular objetos GeoJSON y facilitar su actualización y mantenimiento de un modo enlazable a datos.
* Las coordenadas en Azure Maps se definen como objetos Position, que se pueden especificar como una matriz de números simple con el formato `[longitude, latitude]` o `new atlas.data.Position(longitude, latitude)`.

> [!TIP]
> La clase Position tiene una función auxiliar estática para importar coordenadas que se encuentran en formato `latitude, longitude`. La función [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) se puede reemplazar a menudo por la función `new Microsoft.Maps.Location` en el código de Mapas de Bing.

* En lugar de especificar la información de estilo en cada una de las formas que se agregan al mapa, Azure Maps separa los estilos de los datos. Los datos se almacenan en orígenes de datos y se conectan a las capas de representación que el código de Azure Maps usa para representar los datos. Este enfoque proporciona una ventaja de rendimiento mejorado. Además, muchas capas admiten el estilo controlado por datos, donde se puede agregar lógica de negocios a las opciones de estilo de capa que cambiarán la representación de las formas individuales dentro de una capa en función de las propiedades definidas en la forma.
* Azure Maps proporciona un conjunto de funciones matemáticas espaciales útiles en el espacio de nombres `atlas.math`, pero difieren de las del módulo matemático espacial de Mapas de Bing. La principal diferencia es que Azure Maps no proporciona funciones integradas para operaciones binarias, como unión e intersección; sin embargo, como Azure Maps se basa en GeoJSON, un estándar abierto, hay muchas bibliotecas de código abierto disponibles. Una opción conocida que funciona bien con Azure Maps y que proporciona una gran cantidad de funcionalidades matemáticas espaciales es [turf js](http://turfjs.org/).

Consulte también el [Glosario de Azure Maps](./glossary.md) para ver una lista detallada de la terminología asociada a Azure Maps.

## <a name="web-sdk-side-by-side-examples"></a>Ejemplos en paralelo del SDK web

A continuación, se muestra una colección de ejemplos de código para cada plataforma que abarcan los casos de uso comunes para ayudarle a migrar la aplicación web desde el SDK de JavaScript para Mapas de Bing V8 al SDK web de Azure Maps. Los ejemplos de código relacionados con las aplicaciones web se proporcionan en JavaScript; sin embargo, Azure Maps también proporciona definiciones TypeScript como una opción adicional a través de un [módulo NPM](./how-to-use-map-control.md).

**Temas**

* [Carga de un mapa](#load-a-map)
* [Localización del mapa](#localizing-the-map)
* [Establecimiento de la vista del mapa](#setting-the-map-view)
* [Adición de un marcador](#adding-a-pushpin)
* [Adición de un marcador personalizado](#adding-a-custom-pushpin)
* [Adición de una polilínea](#adding-a-polyline)
* [Adición de un polígono](#adding-a-polygon)
* [Mostrar un cuadro de información](#display-an-infobox)
* [Agrupación en clústeres de marcadores](#pushpin-clustering)
* [Adición de un mapa térmico](#add-a-heat-map)
* [Superposición de una capa de mosaicos](#overlay-a-tile-layer)
* [Mostrar datos del tráfico](#show-traffic-data)
* [Adición de una superposición de suelo](#add-a-ground-overlay)
* [Adición de datos KML al mapa](#add-kml-data-to-the-map)
* [Incorporación de herramientas de dibujo](#add-drawing-tools)

### <a name="load-a-map"></a>Carga de un mapa

La carga de un mapa en ambos SDK sigue los mismos pasos:

* Agregue una referencia al SDK de mapa.
* Agregue una etiqueta `div` al cuerpo de la página, que actuará como marcador de posición para el mapa.
* Cree una función de JavaScript a la que se llamará cuando se cargue la página.
* Cree una instancia de la clase de mapa correspondiente.

**Algunas diferencias clave**

* Mapas de Bing requiere que se especifique una clave de cuenta en la referencia de script de la API o como opción de mapa. Las credenciales de autenticación para Azure Maps se especifican como opciones de la clase map. Puede ser una clave de suscripción o información de Azure Active Directory.
* Mapas de Bing toma una función de devolución de llamada en la referencia de script de la API, que se usa para llamar a una función de inicialización para cargar el mapa. Con Azure Maps debe usarse el evento onload de la página.
* Cuando se usa un identificador para hacer referencia al elemento `div` en el que se va a representar el mapa, Mapas de Bing emplea un selector HTML (es decir,`#myMap`), mientras que Azure Maps solo utiliza el valor de identificador (es decir, `myMap`).
* Las coordenadas en Azure Maps se definen como objetos Position, que se pueden especificar como una matriz de números simple con el formato `[longitude, latitude]`.
* El nivel de zoom en Azure Maps es un nivel inferior al del ejemplo de Mapas de Bing debido a la diferencia en los tamaños del sistema de mosaicos de las plataformas.
* De forma predeterminada, Azure Maps no agrega ningún control de navegación al lienzo del mapa, como botones de zoom y botones de estilo de mapa. Sin embargo, hay controles para agregar un selector de estilo de mapa, botones de zoom, control de la brújula o giro y un control de paso.
* Se agrega un controlador de eventos en Azure Maps para supervisar el evento `ready` de la instancia de mapa. Se activará cuando el mapa haya terminado de cargar el contexto WebGL y todos los recursos necesarios. Cualquier código posterior a la carga se puede agregar en este controlador de eventos.

Los ejemplos siguientes muestran cómo cargar un mapa básico, que está centrado en Nueva York en las coordenadas (longitud: -73,985, latitud: 40.747) y está en el nivel de zoom 12 en Mapas de Bing.

**Antes: Mapas de Bing**

El código siguiente es un ejemplo de cómo mostrar un mapa de Bing centrado y que se ha acercado sobre una ubicación.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Al ejecutar este código en un explorador, se mostrará un mapa similar al de la siguiente imagen:

![Mapa de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**Después: Azure Maps**

En el código siguiente se muestra cómo cargar un mapa con la misma vista en Azure Maps junto con un control de estilo de mapa y botones de zoom.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Al ejecutar este código en un explorador, se mostrará un mapa similar al de la siguiente imagen:

![Mapa de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

Puede encontrar documentación detallada sobre cómo configurar y usar el control de mapa de Azure Maps en una aplicación web [aquí](how-to-use-map-control.md).

> [!TIP]
> Azure Maps publica tanto las versiones compactadas como las descompactadas del SDK. Quite `.min` de los nombres de archivo. La versión descompactada es útil al depurar problemas, pero asegúrese de usar la versión compactada en producción para aprovechar las ventajas del tamaño de archivo más pequeño.

**Recursos adicionales**

* Azure Maps también proporciona controles de navegación para girar y desplazar la vista del mapa como se documenta [aquí](map-add-controls.md).

### <a name="localizing-the-map"></a>Localización del mapa

Si el público está distribuido entre varios países o habla distintos idiomas, la localización es importante.

**Antes: Mapas de Bing**

Para localizar Mapas de Bing, se especifican el idioma y la región mediante los parámetros `setLang` y `UR` y se agregan a la referencia de etiqueta `<script>` a la API. Algunas características de Mapas de Bing solo están disponibles en determinados mercados; el mercado del usuario se especifica mediante el parámetro `setMkt`.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Este es un ejemplo de Mapas de Bing con el idioma establecido en "fr-FR".

![Mapa de Mapas de Bing localizado](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**Después: Azure Maps**

Azure Maps solo proporciona opciones para definir el idioma y la vista regional del mapa. No se usa un parámetro de mercado para limitar las características. Hay dos formas diferentes de definir el idioma y la vista regional del mapa. La primera de ellas es agregar esta información al espacio de nombres `atlas` global, lo que provocará que todas las instancias de control de mapa de la aplicación adopten estos valores de forma predeterminada. El código siguiente establece el idioma en francés ("fr-FR") y la vista regional en `"Auto"`:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

La segunda opción consiste en usar esta información a las opciones del mapa al cargar el mapa como:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Con Azure Maps es posible cargar varias instancias del mapa en la misma página con diferentes configuraciones de idioma y región. Además, también es posible actualizar esta configuración en el mapa una vez que se haya cargado. [Aquí](./supported-languages.md) se puede encontrar una lista detallada de idiomas admitidos en Azure Maps.

Este es un ejemplo de Azure Maps con el idioma establecido en "fr" y la región del usuario establecida en "fr-FR".

![Mapa de Azure Maps localizado](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>Establecimiento de la vista del mapa

Los mapas dinámicos en Mapas de Bing y Azure Maps se pueden mover mediante programación a nuevas ubicaciones geográficas con la llamada a las funciones adecuadas en JavaScript. En los siguientes ejemplos se muestra cómo hacer que el mapa muestre las imágenes aéreas de satélite, centre el mapa sobre una ubicación con las coordenadas (longitud: -111,0225, latitud: 35.0272) y cambie el nivel de zoom a 15 en Mapas de Bing.

> [!NOTE]
> En Mapas de Bing se usan mosaicos con una dimensión de 256 píxeles, mientras que en Azure Maps se usa un tamaño de mosaico mayor, 512 píxeles. Como consecuencia, se reduce el número de solicitudes de red que Azure Maps necesita para cargar la misma área del mapa que Mapas de Bing. Sin embargo, debido al modo en que funcionan las pirámides de mosaicos en los controles de mapa, que los mosaicos de Azure Maps sean mayores significa que, para conseguir esa misma área visible como mapa en Mapas de Bing, debe restar uno al nivel de zoom que se usa en Mapas de Bing cuando utilice Azure Maps.

**Antes: Mapas de Bing**

El control de mapa de Mapas de Bing puede moverse mediante programación con la función `setView`, lo que permite especificar el centro del mapa y un nivel de zoom.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Vista de mapa de conjunto de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**Después: Azure Maps**

En Azure Maps, la posición del mapa se puede cambiar mediante programación con la función `setCamera` del mapa y el estilo de mapa se puede cambiar con la función `setStyle`. Tenga en cuenta que las coordenadas en Azure Maps tienen el formato "longitud, latitud" y se resta uno al valor de nivel de zoom.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Vista de mapa de conjunto de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**Recursos adicionales**

-   [Elección de un estilo de mapa](./choose-map-style.md)
-   [Estilos de mapa admitidos](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Adición de un marcador

En Azure Maps, hay varias maneras de representar los datos de puntos en el mapa:

* Marcadores HTML: representan los puntos mediante los elementos DOM tradicionales. Los marcadores HTML admiten el arrastre.
* Capa de símbolos: representa los puntos con un icono o texto dentro del contexto WebGL.
* Capa de burbujas: representa los puntos como círculos en el mapa. Los radios de los círculos se pueden escalar en función de las propiedades de los datos.

Las capas de símbolos y burbujas se representan dentro del contexto WebGL y pueden representar conjuntos de puntos muy grandes en el mapa. Estas capas requieren que los datos se almacenen en un origen de datos. Los orígenes de datos y las capas de representación deben agregarse al mapa una vez activado el evento `ready`. Los marcadores HTML se representan como elementos DOM dentro de la página y no usan un origen de datos. Cuanto más elementos DOM tenga una página, más lenta es. Si se representan más de unos cientos de puntos en un mapa, se recomienda usar en su lugar una de las capas de representación.

En los siguientes ejemplos se agrega un marcador al mapa en (longitud:-0.2, latitud: 51,5) con el número 10 superpuesto como etiqueta.

**Antes: Mapas de Bing**

Con Mapas de Bing, se agregan marcadores al mapa mediante la clase `Microsoft.Maps.Pushpin`*. Los marcadores se agregan al mapa mediante dos funciones.

La primera función es crear una capa, insertar el marcador en ella y, luego, agregar la capa a la propiedad `layers` del mapa.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

La segunda es agregarla mediante la propiedad `entities` del mapa. Esta función está marcada como en desuso en la documentación de Mapas de Bing V8, pero ha permanecido parcialmente funcional en escenarios básicos.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![Adición de marcadores en Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**Después: Azure Maps con marcadores HTML**

En Azure Maps, se pueden usar marcadores HTML para mostrar fácilmente un punto en el mapa y se recomiendan para aplicaciones sencillas que únicamente necesitan mostrar un pequeño número de puntos en el mapa. Para usar un marcador HTML, puede crear una instancia de la clase `atlas.HtmlMarker`, establecer las opciones de texto y posición y agregar el marcador al mapa mediante la función `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Adición de marcadores en Azure Maps](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Después: Azure Maps con una capa de símbolos**

Cuando se usa una capa de símbolos, los datos se deben agregar a un origen de datos y adjuntar el origen de datos a la capa. Además, el origen de datos y la capa deben agregarse al mapa una vez que se ha activado el evento `ready`. Para representar un valor de texto único encima de un símbolo, la información de texto debe almacenarse como una propiedad del punto de datos y hacer referencia a la propiedad en la opción `textField` de la capa. Esto representa un poco más de trabajo que el uso de marcadores HTML, pero ofrece muchas ventajas de rendimiento.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Adición de una capa de símbolos en Azure Maps](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Recursos adicionales**

-   [Creación de un origen de datos](./create-data-source-web-sdk.md)
-   [Adición de una capa de símbolos](./map-add-pin.md)
-   [Adición de una capa de burbujas](./map-add-bubble-layer.md)
-   [Datos de punto de clúster](./clustering-point-data-web-sdk.md)
-   [Adición de marcadores HTML](./map-add-custom-html.md)
-   [Uso de expresiones de estilo controladas por datos](./data-driven-style-expressions-web-sdk.md)
-   [Opciones de icono de capa de símbolos](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opción de texto de capa de símbolos](/javascript/api/azure-maps-control/atlas.textoptions)
-   [Clase de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opciones de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Adición de un marcador personalizado

Se pueden usar imágenes personalizadas para representar puntos en un mapa. En los ejemplos siguientes se usa esta imagen personalizada para mostrar un punto en el mapa en (latitud: 51,5, longitud: -0,2) y desplaza la posición del marcador de modo que el punto del icono de marcador se alinee con la posición correcta en el mapa.

| ![Adición de marcadores en Azure Maps](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**Antes: Mapas de Bing**

En Mapas de Bing, para crear un marcador personalizado se pasa una dirección URL a una imagen en las opciones `icon` de un marcador. La opción `anchor` se usa para alinear el punto de la imagen del marcador con la coordenada del mapa. El valor del delimitador en Mapas de Bing guarda relación con la esquina superior izquierda de la imagen.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![Adición de un marcador personalizado en Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**Después: Azure Maps con marcadores HTML**

Para personalizar un marcador HTML en Azure Maps se puede pasar un elemento HTML `string` o `HTMLElement` a la opción `htmlContent` del marcador. En Azure Maps, se usa una opción `anchor` para especificar la posición relativa del marcador con respecto a la coordenada de posición mediante uno de los nueve puntos de referencia definidos; "center", "top", "bottom", "left", "right", "top-left", "top-right", "bottom-left", "bottom-right". El contenido está delimitado y se establece en "bottom" de forma predeterminada, que es el centro inferior del contenido HTML. Para que sea más fácil migrar el código de Mapas de Bing, establezca el delimitador en "top-left" y, luego, use la opción `offset` con el mismo desplazamiento usado en Mapas de Bing. Los desplazamientos en Azure Maps se mueven en dirección opuesta a la de Mapas de Bing, por lo que se deben multiplicar por menos uno.

> [!TIP]
> Agregue `pointer-events:none` como estilo en el contenido HTML para deshabilitar el comportamiento de arrastre predeterminado de MS Edge, que mostrará un icono no deseado.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Adición de un marcador personalizado de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**Después: Azure Maps con una capa de símbolos**

Además, las capas de símbolos de Azure Maps admiten imágenes personalizadas, pero la imagen debe cargarse primero en los recursos del mapa y debe tener asignado un identificador único. A continuación, la capa de símbolos puede hacer referencia a este identificador. El símbolo se puede desplazar para alinearse con el punto correcto de la imagen mediante la opción `offset` del icono. En Azure Maps, se usa una opción `anchor` para especificar la posición relativa del símbolo con respecto a la coordenada de posición mediante uno de los nueve puntos de referencia definidos; "center", "top", "bottom", "left", "right", "top-left", "top-right", "bottom-left", "bottom-right". El contenido está delimitado y se establece en "bottom" de forma predeterminada, que es el centro inferior del contenido HTML. Para que sea más fácil migrar el código de Mapas de Bing, establezca el delimitador en "top-left" y, luego, use la opción `offset` con el mismo desplazamiento usado en Mapas de Bing. Los desplazamientos en Azure Maps se mueven en dirección opuesta a la de Mapas de Bing, por lo que se deben multiplicar por menos uno.

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Adición de una capa de símbolos personalizada en Mapas de Bing](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> Para crear una representación de puntos personalizada avanzada, use varias capas de representación juntas. Por ejemplo, si desea tener varios marcadores que tengan el mismo icono en diferentes círculos de colores, en lugar de crear muchas imágenes para cada color, puede superponer una capa de símbolos encima de una capa de burbujas y hacer que hagan referencia al mismo origen de datos. Será mucho más eficaz que crear y hacer que el mapa mantenga muchas imágenes diferentes.

**Recursos adicionales**

-   [Creación de un origen de datos](./create-data-source-web-sdk.md)
-   [Adición de una capa de símbolos](./map-add-pin.md)
-   [Adición de marcadores HTML](./map-add-custom-html.md)
-   [Uso de expresiones de estilo controladas por datos](./data-driven-style-expressions-web-sdk.md)
-   [Opciones de icono de capa de símbolos](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opción de texto de capa de símbolos](/javascript/api/azure-maps-control/atlas.textoptions)
-   [Clase de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opciones de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Adición de una polilínea

Las polilíneas se usan para representar una línea o una ruta en el mapa. En los ejemplos siguientes se muestra cómo crear una polilínea discontinua en el mapa.

**Antes: Mapas de Bing**

En Mapas de Bing, la clase Polyline toma una matriz de ubicaciones y un conjunto de opciones.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Polilínea de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**Después: Azure Maps**

En Azure Maps, se hace referencia a las polilíneas con los términos geoespaciales más comunes: objetos `LineString` o `MultiLineString`. Estos objetos se pueden agregar a un origen de datos y se representan mediante una capa de línea. Las opciones de color del trazo, ancho y matriz de guiones son casi idénticas entre las plataformas.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Línea de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**Recursos adicionales**

-   [Adición de líneas al mapa](./map-add-line-layer.md)
-   [Opciones de capa de línea](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Uso de expresiones de estilo controladas por datos](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Adición de un polígono

Los polígonos se usan para representar un área en el mapa. Azure Maps y Mapas de Bing proporcionan una compatibilidad muy similar con los polígonos. En los ejemplos siguientes se muestra cómo crear un polígono que forme un triángulo basado en la coordenada del centro del mapa.

**Antes: Mapas de Bing**

En Mapas de Bing, la clase Polygon toma una matriz de coordenadas o anillos de coordenadas y un conjunto de opciones.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Polígono de Mapas de Bing](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Después: Azure Maps**

En Azure Maps, se pueden agregar los objetos Polygon y MultiPolygon a un origen de datos y se representan en el mapa mediante capas. El área de un polígono se puede representar en una capa de polígono. El contorno de un polígono se puede representar con una capa de línea.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Polígono de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Recursos adicionales**

-   [Adición de un polígono al mapa](./map-add-shape.md#use-a-polygon-layer)
-   [Adición de un círculo al mapa](./map-add-shape.md#add-a-circle-to-the-map)
-   [Opciones de capa de símbolos](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Opciones de capa de línea](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Uso de expresiones de estilo controladas por datos](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Mostrar un cuadro de información

Se puede mostrar información adicional de una entidad en el mapa como una clase `Microsoft.Maps.Infobox` en Mapas de Bing; en Azure Maps esto se puede lograr mediante la clase `atlas.Popup`. En los ejemplos siguientes se agrega un marcador al mapa y, cuando se hace clic en él, se muestra un cuadro de información o una ventana emergente.

**Antes: Mapas de Bing**

Con Mapas de Bing, se crea un cuadro de información con el constructor `Microsoft.Maps.Infobox`.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Cuadro de información de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**Después: Azure Maps**

En Azure Maps se puede usar una ventana emergente para mostrar información adicional de una ubicación. Se puede pasar un objeto HTML `string` o `HTMLElement` a la opción `content` del elemento emergente. Los elementos emergentes se pueden mostrar independientemente de cualquier forma si se desea y, por tanto, es necesario especificar un valor de `position`. Para mostrar una ventana emergente, llame a la función `open` y pase el mapa en el que se va a mostrar la ventana emergente.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Elemento emergente de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> Para hacer lo mismo con una capa de símbolos, burbujas, líneas o polígonos, simplemente pase la capa al código de evento del mapa en lugar de a un marcador.

**Recursos adicionales**

-   [Adición de un elemento emergente](./map-add-popup.md)
-   [Elemento emergente con contenido multimedia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Elementos emergentes en formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Reutilización de un elemento emergente con varias chinchetas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Clase de elemento emergente](/javascript/api/azure-maps-control/atlas.popup)
-   [Opciones de elemento emergente](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Agrupación en clústeres de marcadores

Cuando se visualizan muchos puntos de datos en el mapa, estos se superponen, el mapa presenta un aspecto desordenado y resulta difícil consultar y usar. La agrupación en clústeres de datos de punto puede utilizarse para mejorar esta experiencia de usuario y también el rendimiento. La agrupación en clústeres de datos de punto es el proceso de combinar datos de punto que están cerca unos de otros y representarlos en el mapa como un único punto de datos agrupados en clúster. Cuando el usuario acerca el mapa, los clústeres se separan en sus puntos de datos individuales.

En los siguientes ejemplos se carga una fuente GeoJSON de datos de terremotos de la semana pasada y se agrega al mapa. Los clústeres se representan como círculos con una escala y color que dependen del número de puntos que contengan.

> [!NOTE]
> Hay varios algoritmos diferentes que se usan para la agrupación en clústeres de marcadores. En Mapas de Bing se emplea una sencilla función basada en cuadrícula, mientras que en Azure Maps se usan métodos de agrupación en clústeres basados en puntos más avanzados y visualmente más atractivos.

**Antes: Mapas de Bing**

En Mapas de Bing, los datos de GeoJSON se pueden cargar mediante el módulo GeoJSON. Para agrupar los marcadores, se cargan en el módulo de agrupación en clústeres y se usa la capa de agrupación en clústeres que contiene.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Agrupación en clústeres de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**Después: Azure Maps**

En Azure Maps, un origen de datos agrega y administra los datos. Las capas se conectan a los orígenes de datos y representan los datos en ellas. La clase `DataSource` en Azure Maps proporciona varias opciones de agrupación en clústeres.

* `cluster`: indica al origen de datos que agrupe en clústeres los datos de punto. 
* `clusterRadius`: el radio en píxeles de los puntos de clúster juntos.
* `clusterMaxZoom`: el nivel de zoom máximo en el que se produce la agrupación en clústeres. Si amplía más que esto, todos los puntos se representan como símbolos.
* `clusterProperties`: define las propiedades personalizadas que se calculan mediante expresiones en todos los puntos de cada clúster y se agregan a las propiedades de cada punto de clúster.

Cuando la agrupación en clústeres está habilitada, el origen de datos enviará puntos de datos agrupados y no agrupados en clústeres a capas para su representación. El origen de datos es capaz de agrupar en clústeres cientos de miles de puntos de datos. Un punto de datos en clúster contiene las siguientes propiedades:

| Nombre de propiedad               | Tipo    | Descripción                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Indica si la característica representa un clúster.     |
| `cluster_id`                | string  | Un identificador exclusivo del clúster que se puede usar con las funciones `getClusterExpansionZoom`, `getClusterChildren` y `getClusterLeaves` de las clases `DataSource`. |
| `point_count`               | number  | El número de puntos que contiene el clúster.     |
| `point_count_abbreviated`   | string  | Una cadena que abrevia el valor de `point_count`, si es largo (por ejemplo, 4000 se convierte en 4 K). |

La clase `DataSource` tiene la siguiente función auxiliar para acceder a información adicional sobre un clúster mediante `cluster_id`.

| Función       | Tipo de valor devuelto        | Descripción     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Recupera los elementos secundarios del clúster especificado en el siguiente nivel de zoom. Estos elementos secundarios pueden ser una combinación de formas y subclústeres. Los subclústeres serán características con propiedades que coincidan con las propiedades del clúster. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Calcula un nivel de zoom en el que el clúster empezará a expandirse o separarse.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Recupera todos los puntos de un clúster. Establezca `limit` para que devuelva un subconjunto de los puntos y use `offset` para desplazarse por ellos.    |

Al representar los datos en clúster en el mapa, a menudo es más fácil usar dos o más capas. En el ejemplo siguiente se usan tres capas: una capa de burbujas para dibujar círculos de colores escalados según el tamaño de los clústeres, una capa de símbolos para representar el tamaño del clúster como texto y una segunda capa de símbolos para representar los puntos no agrupados. Hay muchas otras maneras de representar los datos agrupados en clústeres en Azure Maps resaltadas en la documentación sobre los [datos de puntos de clúster](./clustering-point-data-web-sdk.md).

Los datos GeoJSON se pueden importar directamente a Azure Maps mediante la función `importDataFromUrl` de la clase `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Agrupación en clústeres de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**Recursos adicionales**

-   [Adición de una capa de símbolos](./map-add-pin.md)
-   [Adición de una capa de burbujas](./map-add-bubble-layer.md)
-   [Datos de punto de clúster](./clustering-point-data-web-sdk.md)
-   [Uso de expresiones de estilo controladas por datos](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adición de un mapa térmico

Los mapas térmicos, también conocidos como mapas de densidad de puntos, son un tipo de visualización de datos utilizado para representar la densidad de los datos mediante una gama de colores. A menudo se utilizan para mostrar las "zonas activas" de los datos en un mapa y son una forma excelente de representar grandes conjuntos de datos de puntos.

En los siguientes ejemplos se carga una fuente GeoJSON de todos los terremotos del último mes del USGS y se representan en un mapa térmico ponderado.

**Antes: Mapas de Bing**

En Mapas de Bing, para crear un mapa térmico, cárguelo en el módulo de mapa térmico. Del mismo modo, el módulo GeoJSON se carga para agregar compatibilidad con los datos de GeoJSON.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapa térmico de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**Después: Azure Maps**

En Azure Maps, cargue los datos GeoJSON en un origen de datos y conecte el origen de datos a una capa del mapa térmico. Los datos GeoJSON se pueden importar directamente a Azure Maps mediante la función `importDataFromUrl` de la clase `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapa térmico de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**Recursos adicionales**

-   [Adición de una capa de mapa térmico](./map-add-heat-map-layer.md)
-   [Clase de capa de mapa térmico](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Opciones de capa de mapa térmico](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Uso de expresiones de estilo controladas por datos](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Superposición de una capa de mosaicos

Las capas de mosaicos permiten superponer imágenes grandes que se han dividido en imágenes en mosaico más pequeñas y que se alinean con el sistema de mosaicos de los mapas. Esta es una manera común de superponer imágenes grandes o conjuntos de datos muy grandes.

En los ejemplos siguientes se superpone una capa de mosaicos del radar meteorológico de Iowa Environmental Mesonet de la Universidad Estatal de Iowa que usa un esquema de nomenclatura de mosaicos de zoom de X, Y.

**Antes: Mapas de Bing**

En Mapas de Bing, las capas de mosaicos se pueden crear mediante la clase `Microsoft.Maps.TileLayer`.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![Mapa térmico ponderado de Bing Maps](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**Después: Azure Maps**

En Azure Maps, se puede agregar una capa de mosaicos al mapa de la misma manera que cualquier otra. Se usa una dirección URL con formato que tiene marcadores de posición X, Y y zoom (`{x}`, `{y}`, `{z}` respectivamente) para indicar a la capa dónde acceder a los mosaicos. Las capas de mosaicos en Azure Maps también admiten los marcadores de posición `{quadkey}`, `{bbox-epsg-3857}` y `{subdomain}`.

> [!TIP]
> En Azure Maps las capas se pueden representar fácilmente bajo otras capas, incluidas las capas base del mapa. A menudo es conveniente representar las capas de mosaicos debajo de las etiquetas del mapa para que resulten fáciles de leer. La función `map.layers.add` toma un segundo parámetro que es el identificador de una segunda capa para insertar la siguiente capa debajo. Para insertar una capa de mosaicos debajo de las etiquetas del mapa se puede usar el código siguiente:
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Mapa térmico ponderado de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> Las solicitudes de mosaicos se pueden capturar con la opción `transformRequest` del mapa. Esto le permitirá modificar o agregar encabezados a la solicitud si lo desea.

**Recursos adicionales**

-   [Adición de capas de mosaicos](./map-add-tile-layer.md)
-   [Clase de capa de mosaico](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Opciones de capa de mosaico](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Mostrar datos del tráfico

Los datos de tráfico se pueden superponer en mapas de Bing y Azure.

**Antes: Mapas de Bing**

En Mapas de Bing, los datos de tráfico se pueden superponer en el mapa con el módulo de tráfico.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Tráfico de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**Después: Azure Maps**

Azure Maps proporciona varias opciones diferentes para mostrar el tráfico. Los incidentes de tráfico, como los cierres de carreteras y los accidentes, se pueden mostrar como iconos en el mapa. El flujo de tráfico o las carreteras codificadas mediante colores se pueden superponer en el mapa y los colores se pueden modificar para que se basen en el límite de velocidad registrado, el retraso normal esperado o el retraso absoluto. Los datos de incidentes en Azure Maps se actualizan cada minuto y el flujo de datos cada dos minutos.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Tráfico de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Si hace clic en uno de los iconos de tráfico de Azure Maps, se mostrará información adicional en un menú emergente.

![Ventana emergente de tráfico de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**Recursos adicionales**

-   [Visualización del tráfico en el mapa](./map-show-traffic.md)
-   [Opciones de superposición de tráfico](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Control de tráfico](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Adición de una superposición de suelo

Tanto Mapas de Bing como Azure Maps admiten la superposición de imágenes georreferenciadas en el mapa para que se muevan y escalen a medida que se desplaza y se amplía el mapa. En Mapas de Bing, se conocen como superposiciones de suelo mientras que en Azure Maps se conocen como capas de imagen. Son excelentes para la creación de planos de piso, la superposición de mapas antiguos o las imágenes de un dron.

**Antes: Mapas de Bing**

Al crear una superposición de suelo en Mapas de Bing, debe especificar la dirección URL de la imagen que se va a superponer y un rectángulo de selección al que enlazar la imagen en el mapa. En este ejemplo se superpone una imagen de mapa de Newark New Jersey de 1922 en el mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Al ejecutar este código en un explorador, se mostrará un mapa similar al de la siguiente imagen:

![Superposición de suelo de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**Después: Azure Maps**

En Azure Maps, las imágenes georreferenciadas pueden superponerse mediante la clase `atlas.layer.ImageLayer`. Esta clase requiere una dirección URL para una imagen y un conjunto de coordenadas para las cuatro esquinas de la imagen. La imagen debe estar hospedada en el mismo dominio o tener COR habilitados.

> [!TIP]
> Si solo tiene información de norte, sur, este, oeste y giro, y no hay coordenadas para cada esquina de la imagen, puede usar la función estática [atlas.layer.ImageLayer.getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-).

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Superposición de suelo de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**Recursos adicionales**

-   [Superposición de una imagen](./map-add-image-layer.md)
-   [Clase de capa de imagen](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Adición de datos KML al mapa

Tanto Azure Maps como Mapas de Bing pueden importar y representar datos KML, KMZ, GeoRSS, GeoJSON y Well-Known Text (WKT) en el mapa. Azure Maps también admite GPX, GML, archivos CSV espaciales, Web Mapping Services (WMS), Web Mapping Tile Services (WMTS) y Web Feature Services (WFS).

**Antes: Mapas de Bing**

Al ejecutar este código en un explorador, se mostrará un mapa similar al de la siguiente imagen:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![KML de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**Después: Azure Maps**

En Azure Maps, GeoJSON es el formato de datos principal que se usa en el SDK web, los formatos de datos espaciales adicionales se pueden integrar fácilmente mediante el [módulo de E/S espacial](/javascript/api/azure-maps-spatial-io/). Este módulo tiene funciones para la lectura y la escritura de datos espaciales y también incluye una capa de datos simple que puede representar fácilmente los datos de cualquiera de estos formatos de datos espaciales. Para leer los datos de un archivo de datos espaciales, pase una dirección URL, o bien los datos sin procesar como una cadena o un blob, a la función `atlas.io.read`. Esto devolverá todos los datos analizados del archivo que se pueden agregar al mapa. KML es un poco más complejo que el formato de datos espaciales, ya que incluye mucha más información de estilo. La clase `SpatialDataLayer` admite la representación de la mayoría de estos estilos; sin embargo, las imágenes de iconos deben cargarse en el mapa antes de cargar los datos de características y las superposiciones en el suelo deben agregarse como capas al mapa por separado. Al cargar datos mediante una dirección URL, debe estar hospedada en un punto de conexión habilitado para CORS o se debe pasar un servicio proxy como una opción en la función de lectura.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![KML de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**Recursos adicionales**

-   [Función atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Incorporación de herramientas de dibujo

Tanto Mapas de Bing como Azure Maps proporcionan un módulo que incorpora la posibilidad de que el usuario pueda dibujar y editar formas en el mapa mediante el mouse u otro dispositivo de entrada. Ambos admiten el dibujo de marcadores, líneas y polígonos. Azure Maps también proporciona opciones para dibujar círculos y rectángulos.

**Antes: Mapas de Bing**

En Mapas de Bing, el módulo `DrawingTools` se carga mediante la función `Microsoft.Maps.loadModule`. Una vez cargado, se puede crear una instancia de la clase DrawingTools y se llama a la función `showDrawingManager` para agregar una barra de herramientas al mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Herramientas de dibujo de Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**Después: Azure Maps**

En Azure Maps, el módulo de herramientas de dibujo debe cargarse mediante la carga de los archivos JavaScript y CSS a los que se debe hacer referencia en la aplicación. Una vez que se ha cargado el mapa, se puede crear una instancia de la clase `DrawingManager` y asociarse una instancia de `DrawingToolbar`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Herramientas de dibujo de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> En las capas de Azure Maps, las herramientas de dibujo proporcionan a los usuarios varias maneras de dibujar formas. Por ejemplo, al dibujar un polígono, el usuario puede hacer clic para agregar cada punto, o mantener presionado el botón primario del mouse y arrastrar para dibujar un trazado. Esta configuración se puede modificar mediante la opción `interactionType` de `DrawingManager`.

**Recursos adicionales**

-   [Documentación](./set-drawing-options.md)
-   [Ejemplos de código](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>Recursos adicionales

Eche un vistazo a los [módulos del SDK web de Azure Maps de código abierto](open-source-projects.md#open-web-sdk-modules). Estos módulos proporcionan una gran cantidad de funcionalidad adicional y son totalmente personalizables.

Revise los ejemplos de código relacionados con la migración de otras características de Mapas de Bing:

**Visualizaciones de datos**

> [!div class="nextstepaction"]
> [Capa de contorno](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Discretización de datos](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Servicios**

> [!div class="nextstepaction"]
> [Uso del módulo de servicios de Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Búsqueda de puntos de interés](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obtención de información de una coordenada (geocódigo invertido)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Presentación de indicaciones de ruta de A a B](./map-route.md)

> [!div class="nextstepaction"]
> [Buscar sugerencias automáticas con la interfaz de usuario JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Obtenga más información sobre el SDK web de Azure Maps.

> [!div class="nextstepaction"]
> [Uso del control de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Uso del módulo de servicios](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Uso del módulo de herramientas de dibujo](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Ejemplos de código](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Documentación de referencia de la API de servicio del SDK web de Azure Maps](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la migración de Mapas de Bing a Azure Maps.

> [!div class="nextstepaction"]
> [Migración de un servicio web](migrate-from-bing-maps-web-services.md)

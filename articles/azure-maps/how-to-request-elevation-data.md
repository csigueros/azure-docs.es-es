---
title: Solicitud de datos de elevación mediante el servicio Elevation de Azure Maps
description: Descubra cómo solicitar datos de elevación mediante el servicio Elevation de Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
ms.custom: mvc
ms.openlocfilehash: c7c9bcfa405abe21b335156862b0ee6d19e0a9ac
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438451"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>Solicitud de datos de elevación mediante el servicio Elevation de Azure Maps

El [servicio Elevation](/rest/api/maps/elevation) de Azure Maps proporciona unas API para consultar datos de elevación en cualquier parte de la superficie terrestre. Puede solicitar datos de elevación muestreados a lo largo de trazados, dentro de un rectángulo delimitador o en coordenadas específicas. También, puede usar [Get Map Tile API de Render V2](/rest/api/maps/renderv2) para recuperar datos de elevación en formato de mosaico. Los mosaicos se entregan en formato de trama GeoTIFF. En este artículo se describe cómo usar el servicio Elevation de Azure Maps y Get Map Tile API para solicitar datos de elevación. Los datos de elevación se pueden solicitar en los formatos GeoJSON y GeoTiff.

## <a name="prerequisites"></a>Requisitos previos

1. [Cree una cuenta de Azure Maps en el plan de tarifa Gen 1 (S1) o Gen 2](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción.

Para más información sobre la autenticación en Azure Maps, consulte [Administración de la autenticación en Azure Maps](how-to-manage-authentication.md).

En este tutorial se usa la aplicación [Postman](https://www.postman.com/), pero puede usar un entorno de desarrollo de API diferente.

## <a name="request-elevation-data-in-raster-tile-format"></a>Solicitud de datos de elevación en formato de mosaico de trama

Para solicitar datos de elevación en formato de mosaico de trama, use [Get Map Tile API de Render V2](/rest/api/maps/renderv2). Si se puede encontrar el mosaico, la API lo devuelve como formato GeoTIFF. De lo contrario, la API devuelve 0. Todos los mosaicos DEM de trama usan el modo geoide (nivel del mar) de la Tierra. En este ejemplo, solicitaremos datos de elevación del Monte Everest.

>[!TIP]
>Para recuperar un mosaico de un área específica del mapa mundial, deberá encontrar el mosaico correcto con el nivel de zoom adecuado. Tenga en cuenta que WorldDEM abarca toda la masa continental mundial, pero no incluye los océanos.  Para más información, consulte [Niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md).

Para solicitar datos de elevación en formato de mosaico de trama mediante la aplicación Postman:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Crear nuevo**, seleccione **Solicitud HTTP**.

3. Escriba un valor de **Request name** (Nombre de solicitud) para la solicitud.

4. En la pestaña **Generador**, seleccione el método HTTP **GET** y, a continuación, introduzca la URL siguiente para solicitar el mosaico de trama.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

    >[!Important]
    >Para esta solicitud y otras solicitudes mencionadas en este artículo, reemplace `{Azure-Maps-Primary-Subscription-key}` por su clave de suscripción principal.

5. Seleccione el botón **Enviar**.

    Debería recibir el mosaico de trama que contiene los datos de elevación en formato GeoTIFF. Cada píxel dentro de los datos sin procesar del mosaico de trama es de tipo `float`. El valor de cada píxel representa la altura de elevación en metros.

## <a name="request-elevation-data-in-geojson-format"></a>Solicitud de datos de elevación en formato GeoJSON

Para solicitar datos de elevación en formato GeoJSON, use las API del servicio Elevation. En esta sección se describe cada una de estas API:

* [Get Data for Points](/rest/api/maps/elevation/getdataforpoints)
* [Post Data for Points](/rest/api/maps/elevation/postdataforpoints)
* [Get Data for Polyline](/rest/api/maps/elevation/getdataforpolyline)
* [Post Data for Polyline](/rest/api/maps/elevation/postdataforpolyline)
* [Get Data for Bounding Box](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Cuando no se puede devolver ningún dato, todas las API devuelven **0**.

### <a name="request-elevation-data-for-points"></a>Solicitud de datos de elevación de unos puntos

En este ejemplo, se usará [Get Data for Points API](/rest/api/maps/elevation/getdataforpoints) para solicitar datos de elevación de las montañas Everest y Chamlang. Luego, usaremos [Post Data for Points API](/rest/api/maps/elevation/postdataforpoints) para solicitar datos de elevación usando los mismos dos puntos. Se espera que las latitudes y las longitudes de la dirección URL estén en grados decimales de WGS84 (Sistema geodésico mundial).

 >[!IMPORTANT]
 >Dado que el límite de longitud de la URL es de 2048 caracteres, no es posible pasar más de 100 coordenadas como una cadena delimitada por canalización en una solicitud de URL GET. Si tiene previsto hacerlo, use Post Data for Points API.

Para crear la solicitud:

1. En la aplicación Postman, vuelva a seleccionar **Nuevo**.

2. En la ventana **Crear nuevo**, seleccione **Solicitud HTTP**.

3. Escriba un valor de **Request name** (Nombre de solicitud) para la solicitud.

4. En la pestaña **Generador**, seleccione el método HTTP **GET** y, a continuación, introduzca la URL siguiente (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

5. Seleccione el botón **Enviar**.  Recibirá la siguientes respuesta JSON:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

6. Ahora, llamaremos a [Post Data for Points API](/rest/api/maps/elevation/postdataforpoints) para obtener datos de elevación de los mismos dos puntos. En la pestaña **Generador**, seleccione el método HTTP **POST** y, a continuación, escriba la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

7. En el campo **Encabezados** de la solicitud **POST**, establezca `Content-Type` en `application/json`. 

8. En el campo **Cuerpo**, proporcione la información siguiente de los puntos de coordenadas:

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

9. Seleccione **Enviar**.

### <a name="request-elevation-data-samples-along-a-polyline"></a>Solicitud de muestras de datos de elevación a lo largo de una polilínea

En este ejemplo, se usará [Get Data for Polyline API](/rest/api/maps/elevation/getdataforpolyline) para solicitar cinco muestras de datos de elevación equidistantes a lo largo de una línea recta entre las coordenadas de las montañas Everest y Chamlang. Ambas coordenadas deben definirse en formato de longitud y latitud. Si no especifica un valor para el parámetro `samples`, el número de muestras predeterminado es 10. El número máximo de muestras es de 2000.

A continuación, usaremos Get Data for Polyline API para solicitar tres muestras de datos de elevación equidistantes a lo largo de un trazado. Para definir la ubicación exacta de las muestras, se pasarán tres pares de coordenadas de longitud y latitud.

Por último, se usará [Post Data For Polyline API](/rest/api/maps/elevation/postdataforpolyline) para solicitar datos de elevación de las mismas tres muestras equidistantes.

Se espera que las latitudes y las longitudes de la dirección URL estén en grados decimales de WGS84 (Sistema geodésico mundial).

 >[!IMPORTANT]
 >Dado que el límite de longitud de la URL es de 2048 caracteres, no es posible pasar más de 100 coordenadas como una cadena delimitada por canalización en una solicitud de URL GET. Si tiene previsto hacerlo, use Post Data for Points API.

Para crear la solicitud:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Crear nuevo**, seleccione **Solicitud HTTP**.

3. Escriba un **Nombre de solicitud**.

4. En la pestaña **Generador**, seleccione el método HTTP **GET** y, a continuación, introduzca la URL siguiente (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

5. Seleccione el botón **Enviar**.  Recibirá la siguientes respuesta JSON:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

6. Ahora, solicitaremos tres muestras de datos de elevación a lo largo de un trazado entre las coordenadas de las montañas Everest, Chamlang y Jannu. En el campo **Parámetros**, copie la siguiente matriz de coordenadas para el valor de la clave de consulta `lines`.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

7. Cambie el valor de la clave de consulta `samples` a `3`.  En la imagen siguiente se muestran los nuevos valores.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Recupere tres muestras de datos de elevación.":::

8. Seleccione **Enviar**. Recibirá la siguientes respuesta JSON:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

9. Ahora, llamaremos a [Post Data For Polyline API](/rest/api/maps/elevation/postdataforpolyline) para obtener datos de elevación de los mismos tres puntos.  En la pestaña **Generador**, seleccione el método HTTP **POST** y, a continuación, escriba la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

10. En el campo **Encabezados** de la solicitud **POST**, establezca `Content-Type` en `application/json`. 

11. En el campo **Cuerpo**, proporcione la información siguiente de los puntos de coordenadas.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

12. Seleccione **Enviar**.

### <a name="request-elevation-data-by-bounding-box"></a>Solicitud de datos de elevación por rectángulo delimitador

Ahora usaremos [Get Data for Bounding Box](/rest/api/maps/elevation/getdataforboundingbox) para solicitar datos de elevación cerca del monte Rainier en el estado de Washington. Los datos de elevación se devolverán en ubicaciones equidistantes dentro de un rectángulo delimitador. El área de delimitación definida por dos conjuntos de coordenadas de latitud y longitud (latitud sur, longitud oeste | latitud norte, longitud este) se divide en filas y columnas. Los bordes del rectángulo delimitador corresponden a dos de las filas y dos de las columnas. Se devuelven elevaciones para los vértices de cuadrícula creados en las intersecciones de filas y columnas. Se pueden devolver hasta 2000 elevaciones en una sola solicitud.

En este ejemplo, se especifican 3 filas y 6 columnas. La respuesta devuelve 18 valores de elevación. En el diagrama siguiente, los valores de elevación se ordenan empezando por la esquina suroeste y continuando de oeste a este y de sur a norte.  Los puntos de elevación se numeran en el orden en que se devuelven.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Coordenadas del rectángulo delimitador en las esquinas NE y SE.":::

Para crear la solicitud:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Crear nuevo**, seleccione **Solicitud HTTP**.

3. Escriba un **Nombre de solicitud**.

4. En la pestaña **Generador**, seleccione el método HTTP **GET** y, a continuación, introduzca la URL siguiente (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

5. Seleccione **Enviar**.  La respuesta devuelve 18 muestras de datos de elevación, una por cada vértice de la cuadrícula.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>Ejemplos: Uso de las API del servicio Elevation en Control de mapa de Azure

### <a name="get-elevation-data-by-coordinate-position"></a>Obtención de datos de elevación por la posición de las coordenadas

En la página web de ejemplo siguiente se describe cómo usar el control de mapa para mostrar los datos de elevación en un punto de coordenada. Cuando el usuario arrastra el marcador, el mapa muestra los datos de elevación en una ventana emergente.

<br/>

<iframe height="500" scrolling="no" title="Obtención de la elevación en una posición" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el pen <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>Obtención de la elevación en una posición</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Obtención de datos de elevación por rectángulo delimitador

En la página web de ejemplo siguiente se describe cómo usar el control de mapa para mostrar los datos de elevación contenidos en un rectángulo delimitador. Para definir el rectángulo delimitador, el usuario selecciona el icono `square` en la esquina superior izquierda y, a continuación, dibuja el cuadrado en cualquier parte del mapa. A continuación, el control de mapa representa los datos de elevación de acuerdo con los colores especificados en la clave que se encuentra en la esquina superior derecha.

<br/>

<iframe height="500" scrolling="no" title="Elevaciones por rectángulo delimitador" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el pen <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>Elevaciones por rectángulo delimitador</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Obtención de datos de elevación por trazado de Polilínea

En la página web de ejemplo siguiente se describe cómo usar el control de mapa para mostrar los datos de elevación a lo largo de un trazado. Para definir el trazado, el usuario selecciona el icono `Polyline` en la esquina superior izquierda y, a continuación, dibuja la polilínea en el mapa. A continuación, el control de mapa representa los datos de elevación de acuerdo con los colores especificados en la clave que se encuentra en la esquina superior derecha.

<br/>

<iframe height="500" scrolling="no" title="Gradiente del trazado de elevación" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte el pen <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>Gradiente del trazado de elevación</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Pasos siguientes

Para explorar aún más las API Elevation de Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Elevation: obtención de datos para las coordenadas de latitud y longitud](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Elevation: obtención de datos para el rectángulo delimitador](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevation: obtención de datos para la polilínea](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Get Map Tile de Render V2](/rest/api/maps/renderv2)

Para obtener una lista completa de las API REST de Azure Maps, consulte:

> [!div class="nextstepaction"]
> [API REST de Azure Maps](/rest/api/maps/)

---
title: Representación de datos personalizados en un mapa de trama de Microsoft Azure Maps
description: Aprenda a agregar chinchetas, etiquetas y formas geométricas a un mapa de tramas. Vea cómo usar el servicio de imágenes estáticas de Azure Maps para este fin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/02/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e02da9993804ec170ba89b08ee1b46e01a1adac4
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232365"
---
# <a name="render-custom-data-on-a-raster-map"></a>Representación de datos personalizados en un mapa de trama

En este artículo se describe cómo usar el [servicio de imágenes estáticas](/rest/api/maps/render/getmapimage) con la funcionalidad de composición de imágenes. La funcionalidad de composición de imágenes admite la recuperación de mosaicos de trama estáticos que contienen datos personalizados.

Estos son algunos ejemplos de datos personalizados:

- Marcadores personalizados
- Etiquetas
- Superposiciones de geometría

> [!Tip]
> Para mostrar un mapa sencillo en una página web, a menudo es mucho más rentable usar el SDK web de Azure Maps, en lugar de usar el servicio de imágenes estáticas. El SDK web usa mosaicos de mapa y, a menos que el usuario desplace lateralmente el mapa y lo acerque, con frecuencia solo se generará una fracción de una transacción por carga del mapa. El SDK web de Azure Maps tiene opciones para deshabilitar el movimiento panorámico y el zoom. Además, el SDK web de Azure Maps ofrece un conjunto más completo de opciones de visualización de datos que las de un servicio web de mapas estáticos.  

## <a name="prerequisites"></a>Requisitos previos

1. [Cree una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción.

En este artículo se usa la aplicación [Postman](https://www.postman.com/), pero puede usar otro entorno de desarrollo de API.

Se usarán las [API del servicio de datos](/rest/api/maps/data) de Azure Maps para almacenar y representar las superposiciones.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Representación de marcadores con etiquetas e imágenes personalizadas

> [!Note]
> Para el procedimiento descrito en esta sección se necesita una cuenta de Azure Maps en el plan de tarifa Gen 1 o Gen 2.
El nivel S0 estándar Gen 1 de la cuenta de Azure Maps solo admite una única instancia del parámetro `pins`. Permite representar hasta cinco marcadores, especificados en la solicitud de dirección URL, con una imagen personalizada.

### <a name="get-static-image-with-custom-pins-and-labels"></a>Obtención de una imagen estática con marcas y etiquetas personalizadas

Para obtener una imagen estática con marcas y etiquetas personalizadas:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *GET Static Image*.


4. Seleccione el método HTTP **GET**.


5. Escriba la siguiente dirección URL (reemplace `{subscription-key}` por la clave de suscripción principal):

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```

6. Seleccione **Enviar**.

7. El servicio devuelve la siguiente imagen:

    :::image type="content" source="./media/how-to-render-custom-data/render-pins.png" alt-text="Un marcador personalizado con una etiqueta.":::

## <a name="upload-pins-and-path-data"></a>Carga de marcadores y datos de rutas

> [!Note]
> El procedimiento descrito en esta sección requiere una cuenta de Azure Maps en el plan de tarifa Gen 1 (S1) o Gen 2.

En esta sección, se cargarán los datos de rutas y marcadores al almacenamiento de datos de Azure Map.

Para cargar marcadores y datos de rutas:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *POST Path and Pin Data*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL (reemplace `{subscription-key}` por la clave de suscripción principal):

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={subscription-key}&api-version=2.0&dataFormat=geojson
    ```

6. Seleccione la pestaña **Cuerpo**.

7. En las listas desplegables, seleccione **raw** y **JSON**.

8. Copie los siguientes datos JSON como los datos que se van a cargar y, después, péguelos en la ventana **Body** (Cuerpo):
  
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

9. Seleccione **Enviar**.

10. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados).

11. Copie el valor de la clave de **Operation-Location**, que es `status URL`. Se usará `status URL` para comprobar el estado de la solicitud de carga en la sección siguiente. `status URL` tiene el siguiente formato:

   ```HTTP
   https://us.atlas.microsoft.com/mapData/operations/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?api-version=2.0
   ```

>[!TIP]
>Para obtener información propia sobre rutas y la ubicación de los marcadores, use la [API Data Upload](/rest/api/maps/data-v2/upload-preview).

### <a name="check-pins-and-path-data-upload-status"></a>Comprobación del estado de carga de los datos de marcadores y rutas

Para comprobar el estado de la carga de datos y recuperar su identificador único (`udid`):

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *GET Data Upload Status*.

4. Seleccione el método HTTP **GET**.

5. Escriba el valor `status URL` que ha copiado en [Carga de marcadores y datos de rutas](#upload-pins-and-path-data). La solicitud debe ser similar a la siguiente dirección URL (reemplace `{subscription-key}` por la clave de suscripción principal):

   ```HTTP
     https://us.atlas.microsoft.com/mapData/operations/{statusUrl}?api-version=2.0&subscription-key={subscription-key}
   ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados).

8. Copie el valor de la clave **Resource-Location**, que es `resource location URL`. `resource location URL` contiene el identificador único (`udid`) del recurso del paquete de dibujo.

    :::image type="content" source="./media/how-to-render-custom-data/resource-location-url.png" alt-text="Copie la dirección URL de la ubicación del recurso.":::

### <a name="render-uploaded-features-on-the-map"></a>Representación de características cargadas en el mapa

Para representar los datos de marcadores y rutas cargados en el mapa:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *GET Data Upload Status*.

4. Seleccione el método HTTP **GET**.

5. Escriba la siguiente dirección URL en [Render Service](/rest/api/maps/render/get-map-image) (Representar servicio) (reemplace `{subscription-key}` por la clave de suscripción principal y `udid` por el valor `udid` del paquete cargado):

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. El servicio devuelve la siguiente imagen:

    :::image type="content" source="./media/how-to-render-custom-data/uploaded-path.png" alt-text="Representación de los datos cargados en una imagen de mapa estática.":::

## <a name="render-a-polygon-with-color-and-opacity"></a>Representación de un polígono con color y opacidad

> [!Note]
> El procedimiento descrito en esta sección requiere una cuenta de Azure Maps en el plan de tarifa Gen 1 (S1) o Gen 2.

Puede modificar la apariencia de un polígono mediante el uso de modificadores de estilo con el [parámetro path](/rest/api/maps/render/getmapimage#uri-parameters).

Para representar un polígono con color y opacidad:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *GET Polygon*.

4. Seleccione el método HTTP **GET**.

5. Escriba la siguiente dirección URL en [Render Service](/rest/api/maps/render/get-map-image) (Representar servicio) (reemplace `{subscription-key}` por la clave de suscripción principal):
  
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

6. El servicio devuelve la siguiente imagen:

    :::image type="content" source="./media/how-to-render-custom-data/opaque-polygon.png" alt-text="Representación de un polígono opaco.":::

## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Representación de un círculo y marcadores con etiquetas personalizadas

> [!Note]
> El procedimiento descrito en esta sección requiere una cuenta de Azure Maps en el plan de tarifa Gen 1 (S1) o Gen 2.

Puede aplicar modificadores de estilo para modificar la apariencia de los marcadores. Por ejemplo, para hacer que los marcadores y sus etiquetas sean mayores o menores, use el modificador "estilo de escala" `sc`. Este modificador toma un valor que es mayor que cero. Un valor de 1 es la escala estándar. Los valores mayores que 1 aumentarán el tamaño de los marcadores, mientras que los valores menores que 1 reducirán su tamaño. Para más información sobre los modificadores de estilo, consulte los [parámetros path del servicio de imagen estática](/rest/api/maps/render/getmapimage#uri-parameters).

Para representar un círculo y marcadores con etiquetas personalizadas:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Request name** (Nombre de solicitud), escriba un nombre para la solicitud, como *GET Polygon*.

4. Seleccione el método HTTP **GET**.

5. Escriba la siguiente dirección URL en [Render Service](/rest/api/maps/render/get-map-image) (Representar servicio) (reemplace `{subscription-key}` por la clave de suscripción principal):

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

6. Seleccione **Enviar**.

7. El servicio devuelve la siguiente imagen:

    :::image type="content" source="./media/how-to-render-custom-data/circle-custom-pins.png" alt-text="Representación de un círculo con marcadores personalizados.":::

8. Ahora se modificará el modificador de estilo `co` para cambiar el color de los marcadores. Si observa el valor del parámetro `pins` (`pins=default|la15+50|al0.66|lc003C62|co002D62|`), verá que el color actual es `#002D62`. Para cambiar el color a `#41d42a`, se reemplazará `#002D62` por `#41d42a`.  Ahora el parámetro `pins` es `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. La solicitud es similar a la URL siguiente:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

9. Seleccione **Enviar**.

10. El servicio devuelve la siguiente imagen:

    :::image type="content" source="./media/how-to-render-custom-data/circle-updated-pins.png" alt-text="Representación de un círculo con marcadores actualizados.":::

Del mismo modo, puede cambiar, agregar y quitar otros modificadores de estilo.

## <a name="next-steps"></a>Pasos siguientes

* Explore la documentación sobre [Get Map Image API de Azure Maps Get](/rest/api/maps/render/getmapimage).
* Para más información sobre el servicio Data de Azure Maps, consulte la [documentación sobre el servicio](/rest/api/maps/data).

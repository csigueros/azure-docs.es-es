---
title: 'Tutorial: Uso de Creator de Microsoft Azure Maps para crear mapas de interiores'
description: Tutorial sobre cómo usar Creator de Microsoft Azure Maps para crear mapas de interiores
author: anastasia-ms
ms.author: v-stharr
ms.date: 5/19/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 73e7073b67916983ea7bd37cd6adad4ced5633c2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730635"
---
# <a name="tutorial-use-creator-to-create-indoor-maps"></a>Tutorial: Uso de Creator para crear planos interiores

En este tutorial se describe cómo crear mapas de interiores. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Cargar el paquete de dibujo del plano interior.
> * Convertir el paquete de dibujo en datos del plano.
> * Crear un conjunto de datos a partir de los datos del plano.
> * Crear un conjunto de mosaicos a partir de los datos del conjunto de datos.
> * Consultar la Web Feature Service (WFS) API de Azure Maps para obtener información acerca de las características del plano.
> * Crear un conjunto de estados de características mediante las características del plano y los datos del conjunto de datos.
> * Actualizar el conjunto de estados de características.

## <a name="prerequisites"></a>Requisitos previos

1. [Cree una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción.
3. [Cree un recurso de Creator](how-to-manage-creator.md)
4. Descargue el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

En este tutorial se usa la aplicación [Postman](https://www.postman.com/), pero puede usar un entorno de desarrollo de API diferente.

>[!IMPORTANT]
> En este tutorial se usa la dirección URL geográfica `us.atlas.microsoft.com`. Si el servicio Creator no se creó en la Estados Unidos, debe usar una dirección URL geográfica diferente.  Para más información, consulte [Acceso a los servicios de Creator](how-to-manage-creator.md#access-to-creator-services). Para ver las asignaciones de región a una ubicación geográfica, [consulte el ámbito geográfico del servicio Creator](creator-geographic-scope.md).

## <a name="upload-a-drawing-package"></a>Carga de un paquete de dibujo

Use [Data Upload API](/rest/api/maps/data-v2/upload-preview) para cargar un paquete de dibujo a los recursos de Azure Maps.

Data Upload API es una transacción de larga duración que implementa el patrón que se define en [Ámbito geográfico del servicio Creator](creator-long-running-operation-v2.md).

Para cargar el paquete de dibujo:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *POST Data Upload*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL a la [Data Upload API](/rest/api/maps/data-v2/upload-preview). La solicitud debe ser como la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://us.atlas.microsoft.com/mapData?api-version=2.0&dataFormat=dwgzippackage&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione la pestaña **Encabezados**.

7. En el campo **CLAVE**, seleccione `Content-Type`. 

8. En el campo **VALUE**, seleccione `application/octet-stream`.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-header.png"alt-text="Información de la pestaña Encabezado para la carga de datos.":::

9. Seleccione la pestaña **Cuerpo**.

10. En la lista desplegable, seleccione **binary** (binario).

11. Seleccione **Select File** (Seleccionar archivo) y elija un paquete de dibujo.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-body.png" alt-text="Selección de un paquete de dibujo":::

12. Seleccione **Enviar**.

13. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados).

14. Copie el valor de la clave de **Operation-Location**, que es `status URL`. Usaremos `status URL` para comprobar el estado de la carga del paquete de dibujo.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-response-header.png" alt-text="Copie la dirección URL de estado en la clave de ubicación.":::

### <a name="check-the-drawing-package-upload-status"></a>Comprobación del estado de carga del paquete de dibujo

Para comprobar el estado del paquete de dibujo y recuperar su identificador único (`udid`):

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Data Upload Status*.

4. Seleccione el método HTTP **GET**.

5. Escriba la `status URL` que copió en [Upload a Drawing package](#upload-a-drawing-package) (Cargar un paquete de dibujo). La solicitud debe ser como la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://us.atlas.microsoft.com/mapData/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados).

8. Copie el valor de la clave **Resource-Location**, que es `resource location URL`. `resource location URL` contiene el identificador único (`udid`) del recurso del paquete de dibujo.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/resource-location-url.png" alt-text="Copie la dirección URL de la ubicación del recurso.":::

### <a name="optional-retrieve-drawing-package-metadata"></a>(Opcional) Recuperación de metadatos del paquete de dibujo

Puede recuperar los metadatos del recurso de paquete de dibujo. Los metadatos contienen información como la dirección URL de la ubicación del recurso, la fecha de creación, la fecha de actualización, el tamaño y el estado de carga.

Para recuperar los metadatos de contenido:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Data Upload Metadata*.

4. . Seleccione el método HTTP **GET**.

5. Escriba la `resource Location URL` que copió en [Check Drawing package upload status](#check-the-drawing-package-upload-status) (Comprobación del estado de carga del paquete de dibujo). La solicitud debe ser como la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
   https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Cuerpo**. Los metadatos deben ser como el siguiente fragmento JSON:

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Conversión de un paquete de dibujo

Cuando se haya cargado el paquete de dibujos, usaremos el `udid` para el paquete cargado con el fin de convertirlo en datos de mapa. Conversion API usa una transacción de larga duración que implementa el patrón que se define [aquí](creator-long-running-operation-v2.md).

Para convertir un paquete de dibujo:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *POST Convert Drawing Package*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL en el [servicio de conversión](/rest/api/maps/v2/conversion/convert) (reemplace `{Azure-Maps-Primary-Subscription-key}` por su clave de suscripción principal y `udid` por `udid` del paquete cargado):

    ```http
    https://us.atlas.microsoft.com/conversions?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&udid={udid}&inputType=DWG&outputOntology=facility-2.0
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados). 

8. Copie el valor de la clave de **Operation-Location**, que es `status URL`. Usaremos `status URL` para comprobar el estado de la conversión.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-convert-location-url.png" border="true" alt-text="Copie el valor de la clave de ubicación para el paquete de dibujo.":::

### <a name="check-the-drawing-package-conversion-status"></a>Comprobación del estado de conversión del paquete de dibujo

Una vez completada la operación de conversión, devuelve un `conversionId`. Podemos acceder a `conversionId` comprobando el estado del proceso de conversión del paquete de dibujo. A continuación, se puede usar `conversionId` para acceder a los datos convertidos.

Para comprobar el estado del proceso de conversión y recuperar `conversionId`:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Conversion Status*.

4. Seleccione el método HTTP **GET**:

5. Escriba la `status URL` que copió en [Convert a Drawing package](#convert-a-drawing-package) (Convertir un paquete de dibujo). La solicitud debe ser como la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://us.atlas.microsoft.com/conversions/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados). 

8. Copie el valor de la clave **Resource-Location**, que es `resource location URL`. `resource location URL` contiene el identificador único (`conversionId`), que pueden usar otras API para acceder a los datos de mapa convertidos.

      :::image type="content" source="./media/tutorial-creator-indoor-maps/data-conversion-id.png" alt-text="Copia del identificador de conversión.":::

Tenga en cuenta que el paquete de dibujo de ejemplo debe convertirse sin errores ni advertencias. Sin embargo, si recibe errores o advertencias de su propio paquete de dibujo, la respuesta JSON incluye un vínculo al [visualizador de errores de dibujo](drawing-error-visualizer.md). El visualizador de errores de dibujo le permitirá inspeccionar los detalles de los errores y las advertencias. Para recibir recomendaciones sobre cómo resolver errores y advertencias de conversión, consulte [Errores y advertencias en la conversión de dibujos](drawing-conversion-error-codes.md).

El siguiente fragmento JSON muestra una advertencia de conversión de ejemplo:

```json
{
    "operationId": "<operationId>",
    "created": "2021-05-19T18:24:28.7922905+00:00",
    "status": "Succeeded",
     "warning": {
        "code": "dwgConversionProblem",
        "details": [
            {
                "code": "warning",
                "details": [
                    {
                        "code": "manifestWarning",
                        "message": "Ignoring unexpected JSON property: unitProperties[0].nonWheelchairAccessible with value False"
                    }
                ]
            }
        ]
    },
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Crear un conjunto de datos

Un conjunto de datos es una colección de características de plano, como edificios, niveles y salones. Para crear un conjunto de datos, use [Dataset Create API](/rest/api/maps/v2/dataset/create). Dataset Create API toma el valor `conversionId` del paquete de dibujo convertido y devuelve un valor `datasetId` del conjunto de datos creado.

Para crear un conjunto de datos

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *POST Dataset Create*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL para la [Dataset API](/rest/api/maps/v2/dataset). La solicitud debe tener un aspecto similar a la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal y `{conversionId`} por el `conversionId` obtenido en la sección [Comprobación del estado de conversión del paquete de dibujo](#check-the-drawing-package-conversion-status)):

    ```http
    https://us.atlas.microsoft.com/datasets?api-version=2.0&conversionId={conversionId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados). 

8. Copie el valor de la clave de **Operation-Location**, que es `status URL`. Usaremos `status URL` para comprobar el estado del conjunto de datos.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-dataset-location-url.png" border="true" alt-text="Copia del valor de la clave de ubicación para el conjunto de datos":::

### <a name="check-the-dataset-creation-status"></a>Comprobación del estado de creación del conjunto de datos

Para comprobar el estado del proceso de creación del conjunto de datos y recuperar `datasetId`:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Dataset Status*.

4. Seleccione el método HTTP **GET**.

5. Escriba la `status URL` que copió en [Creación de un conjunto de datos](#create-a-dataset). La solicitud debe ser como la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://us.atlas.microsoft.com/datasets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados). El valor de la clave **Resource-Location** es la `resource location URL`. La `resource location URL` contiene el identificador único (`datasetId`) del conjunto de datos. 

8. Copie `datasetId`, ya que lo usará en las secciones siguientes de este tutorial.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/dataset-id.png" alt-text="Copia del identificador del conjunto de datos":::

## <a name="create-a-tileset"></a>Creación de un conjunto de mosaicos

Un conjunto de mosaicos es un conjunto de mosaicos vectoriales que se representan en el plano. Los conjuntos de mosaicos se crean a partir de conjuntos de datos existentes. Sin embargo, un conjunto de mosaicos es independiente del conjunto de datos del que se creó. Si se elimina el conjunto de datos, el conjunto de mosaicos seguirá existiendo.

Para crear un conjunto de mosaicos:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *POST Tileset Create*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL para [Tileset API](/rest/api/maps/v2/tileset). La solicitud debe tener un aspecto similar a la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal) y `{datasetId`} por el `datasetId` obtenido en la sección [Comprobación del estado de creación del conjunto de datos](#check-the-dataset-creation-status):

    ```http
    https://us.atlas.microsoft.com/tilesets?api-version=2.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados). 

8. Copie el valor de la clave de **Operation-Location**, que es `status URL`. Usaremos `status URL` para comprobar el estado del conjunto de mosaicos.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-tileset-location-url.png" border="true" alt-text="Copia del valor de la dirección URL de estado del conjunto de mosaicos":::

### <a name="check-the-tileset-creation-status"></a>Comprobación del estado de creación del conjunto de mosaicos

Para comprobar el estado del proceso de creación del conjunto de datos y recuperar `tilesetId`:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Tileset Status*.

4. Seleccione el método HTTP **GET**.

5. Escriba la `status URL` que copió en [Creación de un conjunto de mosaicos](#create-a-tileset). La solicitud debe ser como la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal):

    ```http
    https://us.atlas.microsoft.com/tilesets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione **Enviar**.

7. En la ventana de respuesta, seleccione la pestaña **Headers** (Encabezados). El valor de la clave **Resource-Location** es la `resource location URL`.  `resource location URL` contiene el identificador único (`tilesetId`) del conjunto de datos.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/tileset-id.png" alt-text="Copia del identificador del conjunto de mosaicos":::

## <a name="query-datasets-with-wfs-api"></a>Consulta de conjuntos de datos con la API de WFS

Los conjuntos de datos se pueden consultar mediante la [API de WFS](/rest/api/maps/v2/wfs). Puede usar la API de WFS para consultar todas las colecciones de características o una recopilación específica. En esta sección del tutorial, haremos ambas cosas. En primer lugar, se consultarán todas las colecciones y, a continuación, se consultará la colección `unit`.

### <a name="query-for-feature-collections"></a>Consulta de colecciones de características

Para consultar todas las colecciones del conjunto de datos:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Dataset Collections*.

4. Seleccione el método HTTP **GET**.

5. Escriba la siguiente dirección URL en la [API de WFS](/rest/api/maps/v2/wfs). La solicitud debe tener un aspecto similar a la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal) y `{datasetId`} por el `datasetId` obtenido en la sección [Comprobación del estado de creación del conjunto de datos](#check-the-dataset-creation-status):

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. Seleccione **Enviar**.

7. El cuerpo de la respuesta se devuelve en formato GeoJSON y contendrá todas las colecciones del conjunto de datos. Para simplificar, en este ejemplo solo se muestra la colección de `unit`. Para ver un ejemplo que contenga todas las colecciones, consulte [Describe Collections API de WFS](/rest/api/maps/v2/wfs/collection-description). Para obtener más información sobre cualquier colección, puede seleccionar cualquiera de las direcciones URL del elemento `link`.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

### <a name="query-for-unit-feature-collection"></a>Consulta de la colección de características unitarias

En esta sección, se consultará la [API de WFS](/rest/api/maps/v2/wfs) para la colección `unit` de características.

Para consultar la colección de unidades en el conjunto de datos:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *GET Unit Collection*.

4. Seleccione el método HTTP **GET**.

5. Escriba la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por su clave de suscripción principal y `{datasetId`} por el `datasetId` obtenido en [Comprobación del estado de creación del conjunto de datos](#check-the-dataset-creation-status)):

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. Seleccione **Enviar**.

7. Una vez que se devuelva la respuesta, copie la característica `id` de una de las características `unit`. En el ejemplo siguiente, el `id` de la característica es "UNIT26". En este tutorial, usaremos "UNIT26" como característica `id` en la sección siguiente.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Creación de un conjunto de estados de características

Los conjuntos de estados de características definen propiedades dinámicas y valores en características específicas que los admiten. En esta sección, crearemos un conjunto de estados que define los valores booleanos y los estilos correspondientes para la propiedad **occupancy**.

Para crear un conjunto de estados:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *POST Create Stateset*.

4. Seleccione el método HTTP **POST**.

5. Escriba la siguiente dirección URL para [Stateset API](/rest/api/maps/v2/feature-state/create-stateset). La solicitud debe tener un aspecto similar a la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal) y `{datasetId`} por el `datasetId` obtenido en la sección [Comprobación del estado de creación del conjunto de datos](#check-the-dataset-creation-status)):

    ```http
    https://us.atlas.microsoft.com/featurestatesets?api-version=2.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione la pestaña **Encabezados**.

7. En el campo **CLAVE**, seleccione `Content-Type`. 

8. En el campo **VALUE**, seleccione `application/json`.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="Información de la pestaña Encabezado para la creación del conjunto de estados.":::

9. Seleccione la pestaña **Cuerpo**.

10. En las listas desplegables, seleccione **raw** y **JSON**.

11. Copie los siguientes estilos JSON y péguelo en la ventana **Cuerpo**:

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          }
       ]
    }
    ```

12. Seleccione **Enviar**.

13. Una vez que la respuesta se devuelva correctamente, copie `statesetId` desde el cuerpo de la respuesta. En la sección siguiente, usaremos `statesetId` para cambiar el estado de propiedad `occupancy` de la unidad con la característica `id` "UNIT26".

    :::image type="content" source="./media/tutorial-creator-indoor-maps/response-stateset-id.png"alt-text="Respuesta del identificador del conjunto de estados":::

### <a name="update-a-feature-state"></a>Actualización del estado de característica

Para actualizar el estado `occupied` de la unidad con la característica `id` "UNIT26":

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *PUT Set Stateset*.

4. Seleccione el método HTTP **PUT**.

5. Escriba la siguiente dirección URL para [Feature Statesets API](/rest/api/maps/v2/feature-state/create-stateset). La solicitud debe tener un aspecto similar a la siguiente dirección URL (reemplace `{Azure-Maps-Primary-Subscription-key}` por la clave de suscripción principal) y `{statesetId`} por el `statesetId` obtenido en la sección [Creación de un conjunto de estados de características](#create-a-feature-stateset)):

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Seleccione la pestaña **Encabezados**.

7. En el campo **CLAVE**, seleccione `Content-Type`. 

8. En el campo **VALUE**, seleccione `application/json`.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="Información de la pestaña Encabezado para la creación del conjunto de estados.":::

9. Seleccione la pestaña **Cuerpo**.

10. En las listas desplegables, seleccione **raw** y **JSON**.

11. Copie el siguiente estilo JSON y péguelo en la ventana **Body**:

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > La actualización solo se guardará si la marca de tiempo registrada es posterior a la marca de tiempo de la solicitud anterior.

12. Seleccione **Enviar**.

13. Cuando se completa la actualización, recibirá un código de estado HTTP `200 OK`. Si ha implementado un [estilo dinámico](indoor-map-dynamic-styling.md) en un mapa de interiores, la actualización se muestra en la marca de tiempo especificada en el mapa representado.

Puede usar [Feature Get States API](/rest/api/maps/v2/feature-state/get-states) para recuperar el estado de una característica mediante el `id` de la característica. También puede usar [Feature State Delete State API](/rest/api/maps/v2/feature-state/delete-stateset) para eliminar el conjunto de estados y sus recursos.

Para obtener más información sobre los diferentes servicios de Azure Maps Creator que se describen en este artículo, consulte [Mapas de interiores de Creator](creator-indoor-maps.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos que requieran limpieza.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar el módulo de mapas de interiores, consulte

> [!div class="nextstepaction"]
> [Uso del módulo de planos interiores](how-to-use-indoor-module.md)

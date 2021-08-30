---
title: Implementación de estilos dinámicos para planos interiores de Azure Maps Creator
description: Aprenda a implementar estilos dinámicos para planos interiores de Creator
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/20/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 05e169667067033428d5fc995af4d866dc46d20b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751432"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Implementación de estilos dinámicos para planos interiores de Creator

Puede usar el [servicio de estado de características](/rest/api/maps/v2/feature-state) de Azure Maps Creator para aplicar estilos basados en las propiedades dinámicas de las características de datos de los mapas de interiores.  Por ejemplo, puede representar las salas de reuniones de la instalación con un color específico para reflejar el estado de ocupación. En este artículo, se describe cómo representar dinámicamente las características de los mapas de interiores con el [servicio de estado de características](/rest/api/maps/v2/feature-state) y el [módulo web de interiores](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Prerrequisitos

1. [Cree una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción.
3. [Cree un recurso de Creator](how-to-manage-creator.md)
4. Descargue el [paquete de dibujos de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Cree un plano interior](tutorial-creator-indoor-maps.md) para obtener un `tilesetId` y un `statesetId`.
6. Cree una aplicación web siguiendo los pasos descritos en [Cómo usar el módulo de planos interiores](how-to-use-indoor-module.md).

En este tutorial se usa la aplicación [Postman](https://www.postman.com/), pero puede elegir un entorno de desarrollo de API diferente.

## <a name="implement-dynamic-styling"></a>Implementación de un estilo dinámico

Cuando haya completado los requisitos previos, tendrá una aplicación web simple configurada con su clave de suscripción, `tilesetId` y `statesetId`.

### <a name="select-features"></a>Selección de características

Para implementar los estilos dinámicos, se debe hacer referencia a una característica, como una sala de reuniones o conferencias, por su `id` de característica. Use el `id` de característica para actualizar la propiedad dinámica o el *estado* de esa característica. Para ver las características definidas en un conjunto de datos, puede usar uno de los métodos siguientes:

* WFS API (Web Feature Service). Puede usar [WFS API](/rest/api/maps/v2/wfs) para consultar conjuntos de datos. WFS sigue las [características de Open Geospatial Consortium API](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). La API de WFS es útil para consultar características dentro de un conjunto de datos. Por ejemplo, puede usar WFS para buscar todas las salas de reuniones de tamaño medio de una instalación y una planta determinadas.

* Implemente código personalizado que un usuario pueda usar para seleccionar características en un mapa mediante la aplicación web. En este artículo, se usa esta opción.  

El script siguiente implementa el evento de clic del mouse. El código recupera el `id` característica según el punto en el que se hizo clic. En la aplicación, puede insertar el código después del bloque de código del administrador de interiores. Ejecute la aplicación y compruebe la consola para obtener el `id` de característica del punto en el que hizo clic.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

El tutorial [Creación de un plano interior](tutorial-creator-indoor-maps.md) configuró el conjunto de estados de características para aceptar las actualizaciones de estado de `occupancy`.

En la siguiente sección, estableceremos el *estado* de ocupación de la oficina `UNIT26` en `true`, y de la oficina `UNIT27` en `false`.

### <a name="set-occupancy-status"></a>Establecimiento del estado de ocupación

 Ahora actualizaremos el estado de las dos oficinas, `UNIT26` y `UNIT27`:

1. En la aplicación Postman, seleccione **New** (Nuevo).

2. En la ventana **Create New** (Crear nuevo), seleccione **HTTP Request** (Solicitud HTTP).

3. En **Nombre de solicitud**, escriba un nombre para la solicitud, como *POST Data Upload*.

4. Escriba la siguiente dirección URL en [Feature Update States API](/rest/api/maps/v2/feature-state/update-states) (reemplace `{Azure-Maps-Primary-Subscription-key}` por su clave de suscripción principal y `statesetId` por `statesetId`):

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Seleccione la pestaña **Encabezados**.

6. En el campo **CLAVE**, seleccione `Content-Type`. En el campo **VALUE**, seleccione `application/json`.

     :::image type="content" source="./media/indoor-map-dynamic-styling/stateset-header.png"alt-text="Información de la pestaña Encabezado para la creación del conjunto de estados.":::

7. Seleccione la pestaña **Cuerpo**.

8. En las listas desplegables, seleccione **raw** y **JSON**.

9. Copie el siguiente estilo JSON y péguelo en la ventana **Body**:

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

    >[!IMPORTANT]
    >La actualización se guardará solo si la marca de tiempo enviada es posterior a la marca de tiempo usada en las solicitudes de actualización de estado de características anteriores para el mismo `ID` de característica.

10. Cambie la dirección URL que usó en el paso 7 reemplazando `UNIT26` por `UNIT27`:

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT27?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

11. Copie el siguiente estilo JSON y péguelo en la ventana **Body**:

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualización de estilos dinámicos en un mapa

La aplicación web que abrió anteriormente en un explorador debería reflejar ahora el estado actualizado de las características del mapa:
- Office `UNIT27`(142) debería aparecer en verde.
- Office `UNIT26`(143) debería aparecer en rojo.

![Sala libre en verde y sala ocupada en rojo](./media/indoor-map-dynamic-styling/room-state.png)

[Ver demo en vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea:

> [!div class="nextstepaction"]
> [Uso de Creator para mapas interiores](creator-indoor-maps.md)

Consulte las referencias de las API mencionadas en este artículo:

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversión de datos](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Conjunto de mosaicos](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de estados de características](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Servicio WFS](creator-indoor-maps.md#web-feature-service-api)

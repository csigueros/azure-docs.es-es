---
title: Adición de una capa de mosaico al objeto visual de Azure Maps en Power BI | Microsoft Azure Maps
description: En este artículo, aprenderá a usar la capa de mosaico en el objeto visual de Microsoft Azure Maps para Power BI.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0edb31901950da22c321af2d6c41966465f916f9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430421"
---
# <a name="add-a-tile-layer"></a>Adición de una capa de icono

La característica de capa de mosaico, como la característica de capa de referencia, permite que los datos adicionales se superpongan en el mapa para proporcionar más contexto. Las capas de mosaico permiten superponer imágenes encima de los mosaicos de mapa base de Azure Maps. Se trata de una excelente manera de superponer conjuntos de datos grandes o complejos, como imágenes de drones, o millones de filas de datos.

> [!div class="mx-imgBorder"]
> ![Mapa que muestra una capa de burbuja sobre una capa de mosaico en la que se muestran los datos climáticos infrarrojos actuales de Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Una capa de mosaico carga los mosaicos desde un servidor. Estas imágenes se pueden representar previamente o se pueden representar de forma dinámica. Las imágenes representadas previamente se almacenan como cualquier otra imagen en un servidor mediante una convención de nomenclatura que entienda la capa de mosaico. Las imágenes representadas de forma dinámica usan un servicio para cargar las imágenes casi en tiempo real. Las capas de mosaico son una excelente manera de visualizar grandes conjuntos de datos en el mapa. No solo puede generarse una capa de mosaico a partir de una imagen, sino que también se pueden representar datos de vector como una capa de mosaico.

El cuadro de límite y el rango de zoom de donde está disponible un servicio de mosaico se pueden pasar como configuración para limitar dónde se solicitan los mosaicos. Se trata de una mejora del rendimiento tanto para el objeto visual como para el servicio de mosaicos. A continuación se muestra información general de todas las opciones disponibles en el panel **Formato** que están disponibles en la sección **Capa de mosaico**.

| Configuración        | Descripción   |
|----------------|---------------|
| Url            | Dirección URL con formato que apunta a un servicio de mosaicos.  |
| Tamaño de mosaico      | Valor entero que especifica las dimensiones de ancho y alto de los mosaicos.   |
| Límite septentrional    | Latitud septentrional del cuadro de límite en el que están disponibles los mosaicos. |
| Límite meridional    | Latitud meridional del cuadro de límite en el que están disponibles los mosaicos. |
| Límite oriental     | Longitud oriental del cuadro de límite en el que están disponibles los mosaicos.  |
| Límite occidental     | Longitud occidental del cuadro de límite en el que están disponibles los mosaicos.   |
| Transparencia   | Transparencia de la capa de mosaico.   |
| Es TMS         | Servicios de mapa de mosaico; especificación que revierte el eje de la coordenada Y de la capa de mosaico. |
| Zoom mínimo       | Nivel de zoom mínimo en el que están disponibles los mosaicos. |
| Zoom máximo       | Nivel de zoom máximo en el que están disponibles los mosaicos.  |
| Layer position (Posición de capa) | Permite especificar la posición de la capa en relación con otras capas de mapa. |

## <a name="tile-url-formatting"></a>Formato de URL de mosaico

El objeto visual de Azure Maps admite tres convenciones de nomenclatura del servicio de mosaico diferentes:

-   **X, Y, notación Zoom**: X es la columna, Y es la posición de fila del mosaico en la cuadrícula de mosaico y la notación Zoom es un valor basado en el nivel de zoom.
-   **Notación Quadkey**: combina la información de zoom, x e y en un valor de cadena único. Este valor de cadena se convierte en un identificador único para un solo mosaico.
-   **Cuadro de límite**: Especifique una imagen en el formato de coordenadas del cuadro de límite: `{west},{south},{east},{north}`. Este formato se usa normalmente en [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

La dirección URL del mosaico es una URL https a una plantilla de dirección URL de un mosaico, que usa los parámetros siguientes:

-   `{x}`: posición del mosaico en X. También necesita `{y}` y `{z}`.
-   `{y}`: posición del mosaico en Y. También necesita `{x}` y `{z}`.
-   `{z}`: nivel de zoom del mosaico. También necesita `{x}` y `{y}`.
-   `{quadkey}`: Identificador `quadkey` del mosaico basado en la convención de nomenclatura del sistema de mosaicos de Bing Maps.
-   `{bbox-epsg-3857}`: Cadena de un cuadro de límite con el formato `{west},{south},{east},{north}` en el sistema de referencia espacial EPSG 3857.

Por ejemplo, a continuación se presenta una dirección URL con formato para el [servicio de mosaicos de radar meteorológico](/rest/api/maps/render-v2/get-map-tile) en Azure Maps. Tenga en cuenta que `[subscription-key]` es un marcador de posición para la clave de suscripción de Azure Maps.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Para más información sobre el sistema de mosaicos de Azure Maps, consulte [Niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Pasos siguientes

Agregue más contexto al mapa:

> [!div class="nextstepaction"]
> [Mostrar el tráfico en tiempo real](power-bi-visual-show-real-time-traffic.md)
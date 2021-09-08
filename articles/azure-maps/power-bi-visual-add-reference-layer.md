---
title: Adición de una capa de referencia al objeto visual de Azure Maps en Power BI | Microsoft Azure Maps
description: En este artículo, aprenderá a usar la capa de referencia en el objeto visual de Microsoft Azure Maps para Power BI.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f1488584f025edc9e8b3e098b283efbb56b449d4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430439"
---
# <a name="add-a-reference-layer"></a>Adición de una capa de referencia

La característica de capa de referencia permite cargar un conjunto de datos espacial secundario en el objeto visual y superponerlo en el mapa para proporcionar contexto adicional. Este conjunto de archivos se hospeda en Power BI y debe ser un [archivo GeoJSON](https://wikipedia.org/wiki/GeoJSON) con una extensión de archivo `.json` o `.geojson`.

Para agregar un archivo **GeoJSON** como capa de referencia, vaya al panel **Formato**, expanda la sección **Capa de referencia** y presione el botón **+ Add local file** (+ Agregar archivo local).

Después de agregar un archivo GeoJSON a la capa de referencia, el nombre del archivo aparecerá en lugar del botón **+ Add local file** (+ Agregar archivo local) con una **X** junto a él. Presione el botón **X** para quitar los datos del objeto visual y eliminar el archivo GeoJSON de Power BI.

En el mapa siguiente se muestran los [distritos censales de Colorado en 2016](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), coloreados por población.

> [!div class="mx-imgBorder"]
> ![Un mapa que muestra los distritos censales de Colorado en 2016, coloreado por población como capa de referencia](media/power-bi-visual/reference-layer-CO-census-tract.png)

A continuación se muestran todas las opciones de configuración del panel **Formato** que están disponibles en la sección **Capa de referencia**.

| Configuración              | Descripción   |
|----------------------|---------------|
| Reference layer data (Datos de la capa de referencia) | Archivo de datos GeoJSON que se va a cargar en el objeto visual como capa adicional dentro de la asignación. El botón **+ Add local file** (+ Agregar archivo local) abre un cuadro de diálogo de archivo que el usuario puede usar para seleccionar un archivo GeoJSON que tenga una extensión de archivo `.json` o `.geojson`. |

> [!NOTE]
> En esta vista previa del objeto visual de Azure Maps, la capa de referencia solo cargará las primeras 5000 características de forma en el mapa. Este límite aumentará en una actualización futura.

## <a name="styling-data-in-a-reference-layer"></a>Aplicar estilos a los datos en una capa de referencia

Se pueden agregar propiedades a cada una de las características del archivo GeoJSON para personalizar su estilo en el mapa. Esta característica utiliza la característica de capa de datos simple en el SDK web de Azure Maps. Para obtener más información, consulte este documento sobre las [propiedades de estilo admitidas](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Como precaución de seguridad, no se admiten las imágenes de icono personalizadas en el objeto visual de Azure Maps.

A continuación se muestra un ejemplo de una característica de punto de GeoJSON que establece el color mostrado en rojo.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Pasos siguientes

Agregue más contexto al mapa:

> [!div class="nextstepaction"]
> [Adición de una capa de icono](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar el tráfico en tiempo real](power-bi-visual-show-real-time-traffic.md)

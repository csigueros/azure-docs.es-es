---
title: Requisitos del paquete de dibujo de Creator de Microsoft Azure Maps
description: Información sobre los requisitos de paquetes de dibujos para convertir los archivos de diseño de las instalaciones a datos de mapa
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/02/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 2e6b380b040697a56179f5fec7f7d10796f035fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746649"
---
# <a name="drawing-package-requirements"></a>Requisitos de paquetes de dibujos

Puede convertir paquetes de dibujo cargados en datos de mapa mediante el [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion). En este artículo se describen los requisitos de los paquetes de dibujos para la API de conversión. Para ver un paquete de ejemplo, puede descargar el [paquete de dibujos](https://github.com/Azure-Samples/am-creator-indoor-data-examples) de ejemplo.

Para una guía sobre cómo preparar el paquete de dibujo, vea [Guía del paquete de dibujo de conversión](drawing-package-guide.md).


## <a name="prerequisites"></a>Requisitos previos

El paquete de dibujos incluye dibujos guardados en formato DWG, que es el formato de archivo nativo del software AutoCAD® de Autodesk.

Puede elegir cualquier software de CAD para generar los dibujos en el paquete de dibujos.  

El [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion) convierte un paquete de dibujos en datos de mapa. El servicio de conversión funciona con el formato de archivo DWG de AutoCAD `AC1032`.


## <a name="glossary-of-terms"></a>Glosario de términos

Para facilitar la referencia, estos son algunos términos y definiciones que son importantes a la hora de leer este artículo.

| Término  | Definición |
|:-------|:------------|
| Nivel | Capa de DWG de AutoCAD del archivo de dibujo.|
| Entidad | Entidad de DWG de AutoCAD del archivo de dibujo. |
| Xref  | Archivo en formato de archivo DWG de AutoCAD adjunto al dibujo principal como referencia externa.  |
| Nivel | Área de un edificio en una elevación determinada. Por ejemplo, la planta de un edificio. |
| Característica | Instancia de un objeto generado a partir del servicio de conversión que combina una geometría con información de metadatos. |
| Clases de características | Proyecto común para las características. Por ejemplo, una *unidad* es una clase de característica y una *oficina* es una característica. |

## <a name="drawing-package-structure"></a>Estructura de un paquete de dibujos

Un paquete de dibujos es un archivo .zip que contiene los archivos siguientes:

- Archivos DWG en formato de archivo DWG de AutoCAD.
- Archivo _manifest.json_ que describe los archivos DWG en el paquete de dibujo.

El paquete de dibujo debe comprimirse en un solo archivo de almacenamiento, con la extensión .zip. Los archivos DWG se pueden organizar de cualquier manera dentro del paquete, pero el archivo de manifiesto debe residir en el directorio raíz del paquete comprimido. En las secciones siguientes se detallan los requisitos para los archivos DWG, el archivo de manifiesto y el contenido de estos archivos. Para ver un paquete de ejemplo, puede descargar el [paquete de dibujos de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="dwg-file-conversion-process"></a>Proceso de conversión de archivos DWG

El [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion) hace lo siguiente en cada archivo DWG:

- Extrae clases de características:
    - Niveles
    - Unidades
    - Zones
    - Aperturas
    - Paredes
    - Penetraciones verticales
- Genera una característica *Facility*.  
- Genera un conjunto mínimo de características Category predeterminadas a las que otras características hacen referencia:
    - room
    - structure
    - wall
    - opening.door
    - zona
    - dispositivo
    
## <a name="dwg-file-requirements"></a>Requisitos del archivo DWG

Se requiere un único archivo DWG para cada nivel de la instalación. Todos los datos de una sola capa deben estar contenidos en un único archivo DWG.  Cualquier referencia externa (_xref_) debe estar enlazada al dibujo principal. Por ejemplo, una instalación con tres capas tendrá tres archivos DWG en el paquete de dibujo.

Cada archivo DWG, debe cumplir los siguientes requisitos:

- El archivo DWG definir las capas _Exterior_ y _Unidad_. Opcionalmente, puede definir las siguientes capas: _Pared_, _Puerta_, _UnitLabel_, _Zona_ y _ZoneLabel_.
- El archivo DWG no puede contener características de varias capas.
- El archivo DWG no puede contener características de varias instalaciones.
- El archivo DWG debe hacer referencia al mismo sistema de medida y a la unidad de medida que otros archivos DWG en el paquete de dibujo.

## <a name="dwg-layer-requirements"></a>Requisitos de las capas de DWG

Cada capa de DWG debe cumplir las reglas siguientes:

- Una capa debe contener exclusivamente características de una sola clase. Por ejemplo, las unidades y las paredes no pueden estar en la misma capa.
- Una única clase de características se puede representar mediante varias capas.
- Se permiten los polígonos que se autointersecan, pero se reparan automáticamente. Cuando se reparan, el [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion) genera una advertencia. Se recomienda inspeccionar manualmente los resultados reparados, ya que es posible que no coincidan con los resultados esperados.
- Cada capa tiene una lista admitida de tipos de entidad. Se omitirán todos los demás tipos de entidad de una capa. Por ejemplo, las entidades de texto no se admiten en la capa de pared.

En la tabla siguiente se describen los tipos de entidad admitidos y las características de mapa convertidas para cada capa. Si una capa contiene tipos de entidad no admitidos, el [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion) omitirá estas entidades.  

| Nivel | Tipos de entidades | Características convertidas |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polígono, polilínea (cerrada), círculo, elipse (cerrada) | Niveles
| [Unidad](#unit-layer) |  Polígono, polilínea (cerrada), círculo, elipse (cerrada) |  Unidades y penetraciones verticales
| [Pared](#wall-layer)  | Polígono, polilínea (cerrada), círculo, elipse (cerrada), estructuras |
| [Puerta](#door-layer) | Polígono, polilínea, línea, arco circular, círculo | Aperturas
| [Zona](#zone-layer) | Polígono, polilínea (cerrada), círculo, elipse (cerrada) | Zones
| [UnitLabel](#unitlabel-layer) | Texto (una línea) | No aplicable. Esta capa solo puede agregar propiedades a las características de las unidades de la capa de unidades. Para más información, consulte la [capa de unitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Texto (una línea) | No aplicable. Esta capa solo puede agregar propiedades a las características de zona de la capa de unidades. Para más información, consulte la [capa de ZoneLabel](#zonelabel-layer).

En las secciones siguientes se describen los requisitos de cada capa.

### <a name="exterior-layer"></a>Capa exterior

El archivo DWG de cada nivel debe contener una capa para definir el perímetro de ese nivel. Esta capa se conoce como capa *exterior*. Por ejemplo, si una instalación contiene dos niveles, debe tener dos archivos DWG, con una capa exterior para cada archivo.

Independientemente de cuántos dibujos de entidad haya en la capa exterior, el [conjunto de la instalación resultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) contendrá solo una característica de nivel para cada archivo DWG. Además:

- Los exteriores deben dibujarse como polígonos, polilíneas (cerradas), círculos o elipses (cerradas).
- Los exteriores pueden superponerse, pero se disuelven en una geometría.
- La característica de nivel resultante debe tener al menos 4 metros cuadrados.
- La característica de nivel resultante no debe tener más de 400 000 metros cuadrados.

Si la capa contiene varias polilíneas superpuestas, estas se disuelven en una única característica de nivel. En su lugar, si la capa contiene varias polilíneas que no se solapan, la característica de nivel resultante tiene una representación multipoligonal.

Puede ver un ejemplo de la capa exterior como capa de contorno en el [paquete de dibujos de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Capa de unidades

El archivo DWG para cada nivel define una capa que contenga unidades. Las unidades son espacios transitables en el edificio, como oficinas, vestíbulos, escaleras y ascensores. Si se define la propiedad `VerticalPenetrationCategory`, las unidades navegables que abarcan varios niveles, como ascensores y escaleras, se convierten en características de penetración vertical. A las características de penetración vertical que se superponen entre sí se les asigna un elemento `setid`.

La capa de unidades debe cumplir los siguientes requisitos:

- Las unidades deben dibujarse como polígonos, polilíneas (cerradas), círculos o elipses (cerradas).
- Las unidades deben estar dentro de los límites del perímetro exterior de la instalación.
- Las unidades no pueden superponerse parcialmente.
- Las unidades no pueden contener ninguna geometría que forme intersección con ella misma.

Asigne un nombre a una unidad creando un objeto de texto en la capa de UnitLabel y, a continuación, coloque el objeto dentro de los límites de la unidad. Para más información, consulte la [capa de unitLabel](#unitlabel-layer).

Puede ver un ejemplo de la capa de unidades en el [paquete de dibujos de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Capa de paredes

El archivo DWG de cada nivel puede contener una capa que defina las extensiones físicas de las paredes, las columnas y otras estructuras del edificio.

- Las paredes deben dibujarse como polígonos, polilíneas (cerradas), círculos o elipses (cerradas).
- La capa de paredes o las paredes solo deben contener geometría que se interprete como estructura del edificio.

Puede ver un ejemplo de la capa de paredes en el [paquete de dibujos de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Capa de puertas

Puede incluir una capa DWG que contenga puertas. Cada puerta debe superponerse con el borde de una unidad de la capa de unidad.

Las aberturas de puertas de un conjunto de datos de Azure Maps se representan como un segmento de una sola línea que se superpone sobre varios límites de unidades. En las imágenes siguientes se muestra cómo convertir la geometría en la capa de puertas a características de aperturas en un conjunto de datos.

![Cuatro gráficos que muestran los pasos para generar aperturas](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Capa de zonas

El archivo DWG de cada nivel puede contener una capa de zonas que defina las extensiones físicas de las zonas. Una zona es un espacio no navegable que se puede denominar y representar. Las zonas pueden abarcar varios niveles y se agrupan mediante la propiedad zoneSetId.

- Las zonas deben dibujarse como polígonos, polilíneas (cerradas) o elipses (cerradas).
- Las zonas pueden superponerse.
- Las zonas pueden estar dentro o fuera del perímetro exterior de la instalación.

Asigne un nombre a una zona creando un objeto de texto en la capa de ZoneLabel y colocando el objeto de texto dentro de los límites de la zona. Para más información, consulte la [capa de ZoneLabel](#zonelabel-layer).

Puede ver un ejemplo de la capa de zona en el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Capa de unitLabel

El archivo DWG de cada nivel puede contener una capa de UnitLabel. La capa de UnitLabel agrega una propiedad de nombre a las unidades extraídas de la capa de unidades. Las unidades con una propiedad de nombre pueden tener más detalles especificados en el archivo de manifiesto.

- Las etiquetas de las unidades deben ser entidades de texto de una sola línea.
- Las etiquetas de las unidades deben estar completamente dentro de los límites de su unidad.
- Las unidades no deben contener varias entidades de texto en la capa de UnitLabel.

Puede ver un ejemplo de la capa de UnitLabel en el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>Capa de ZoneLabel

El archivo DWG de cada nivel puede contener una capa de ZoneLabel. Esta capa agrega una propiedad de nombre a las zonas extraídas de la capa de zonas. Las zonas con una propiedad de nombre pueden tener más detalles especificados en el archivo de manifiesto.

- Las etiquetas de las zonas deben ser entidades de texto de una sola línea.
- Las etiquetas de las zonas deben estar dentro de los límites de su zona.
- Las zonas no deben contener varias entidades de texto en la capa de ZoneLabel.

Puede ver un ejemplo de la capa de ZoneLabel en el [paquete de dibujo de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Requisitos del archivo de manifiesto

La carpeta ZIP debe contener un archivo de manifiesto en el nivel raíz del directorio y el archivo debe tener el nombre **manifest.json**. Describe los archivos DWG para permitir que el [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion) analice su contenido. Solo se ingieren los archivos identificados por el manifiesto. Los archivos que se encuentren en la carpeta zip, pero que no se enumeran correctamente en el manifiesto, se omiten.

Las rutas de acceso en el objeto `buildingLevels` del archivo de manifiesto deben ser relativas a la raíz de la carpeta zip. El nombre del archivo DWG debe coincidir exactamente con el nombre del nivel de la instalación. Por ejemplo, un archivo DWG para el nivel "Sótano" es "Sótano.dwg". Un archivo DWG para el nivel 2 tiene el nombre "nivel_2.dwg". Use un carácter de subrayado si el nombre del nivel tiene un espacio.

Aunque hay requisitos al usar los objetos del manifiesto, no se requieren todos los objetos. En la tabla siguiente se muestran los objetos obligatorios y los opcionales para la versión 1.1 del [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion).

>[!NOTE]
> A menos que se especifique lo contrario, todas las propiedades con un tipo de propiedad de cadena permiten mil caracteres.


| Object | Obligatorio | Descripción |
| :----- | :------- | :------- |
| `version` | true |Versión del esquema de manifiesto. Actualmente, solo se admite la versión 1.1.|
| `directoryInfo` | true | Describe la información geográfica y de contacto de la instalación. También se puede usar para describir información geográfica y de contacto de un ocupante. |
| `buildingLevels` | true | Especifica los niveles de los edificios y los archivos que contienen el diseño de los niveles. |
| `georeference` | true | Contiene información geográfica numérica para el dibujo de la instalación. |
| `dwgLayers` | true | Enumera los nombres de las capas y cada capa enumera los nombres de sus propias características. |
| `unitProperties` | false | Se puede usar para insertar más metadatos para las características de la unidad. |
| `zoneProperties` | false | Se puede usar para insertar más metadatos para las características de la zona. |

En las secciones siguientes se detallan los requisitos de cada objeto.

### `directoryInfo`

| Propiedad  | Tipo | Obligatorio | Descripción |
|-----------|------|----------|-------------|
| `name`      | string | true   |  Nombre del edificio. |
| `streetAddress`|    string |    false    | Dirección del edificio. |
|`unit`     | string    |  false    |  Unidad en el edificio. |
| `locality` |    string |    false |    Nombre de una ciudad, ciudad, área, barrio o región.|
| `adminDivisions` |    Matriz JSON de cadenas |    false     | Matriz que contiene designaciones de direcciones. Por ejemplo: (Código, Estado) use códigos de país ISO 3166 y códigos de estado o territorio ISO 3166-2. |
| `postalCode` |    string    | false    | Código postal. |
| `hoursOfOperation` |    string |     false | Sigue el formato de [horas de apertura de OSM](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). |
| `phone`    | string |    false |    Número de teléfono asociado con el edificio. |
| `website`    | string |    false    | Sitio web asociado con el edificio. |
| `nonPublic` |    bool    | false | Marca que especifica si el edificio está abierto al público. |
| `anchorLatitude` | NUMERIC |    false | Latitud del marcador (chincheta) de la instalación. |
| `anchorLongitude` | NUMERIC |    false | Longitud del marcador (chincheta) de la instalación. |
| `anchorHeightAboveSeaLevel`  | NUMERIC | false | Altura de la planta baja de la instalación sobre el nivel del mar, en metros. |
| `defaultLevelVerticalExtent` | NUMERIC | false | Altura (grosor) predeterminada de un nivel de esta instalación que se usará cuando el valor `verticalExtent` de un nivel no esté definido. |

### `buildingLevels`

El objeto `buildingLevels` contiene una matriz JSON de los niveles del edificio.

| Propiedad  | Tipo | Obligatorio | Descripción |
|-----------|------|----------|-------------|
|`levelName`    |string    |true |    Nombre descriptivo del nivel. Por ejemplo: Piso 1, Vestíbulo, Aparcamiento o Sótano.|
|`ordinal` | integer |    true | Determina el orden vertical de los niveles. Cada instalación debe tener un nivel con el ordinal 0. |
|`heightAboveFacilityAnchor` | NUMERIC | false |    Altura del nivel sobre el marcador en metros. |
| `verticalExtent` | NUMERIC | false | Altura del suelo al techo (grosor) del nivel en metros. |
|`filename` |    string |    true |    Ruta de acceso del sistema de archivos del dibujo de CAD para un nivel del edificio. Debe ser relativa a la raíz del archivo ZIP del edificio. |

### `georeference`

| Propiedad  | Tipo | Obligatorio | Descripción |
|-----------|------|----------|-------------|
|`lat`    | NUMERIC |    true |    Representación decimal de los grados de latitud en el origen del dibujo de la instalación. Las coordenadas de origen deben estar en el formato WGS84 Web Mercator (`EPSG:3857`).|
|`lon`    |NUMERIC|    true|    Representación decimal de los grados de longitud en el origen del dibujo de la instalación. Las coordenadas de origen deben estar en el formato WGS84 Web Mercator (`EPSG:3857`). |
|`angle`|    NUMERIC|    true|   Ángulo en el sentido de las agujas del reloj, en grados, entre el norte verdadero y el eje vertical (Y) del dibujo.   |

### `dwgLayers`

| Propiedad  | Tipo | Obligatorio | Descripción |
|-----------|------|----------|-------------|
|`exterior`    |Matriz de cadenas|    true|    Nombres de las capas que definen el perfil exterior del edificio.|
|`unit`|    Matriz de cadenas|    true|    Nombres de las capas que definen las unidades.|
|`wall`|    Matriz de cadenas    |false|    Nombres de las capas que definen las paredes.|
|`door`    |Matriz de cadenas|    false   | Nombres de las capas que definen las puertas.|
|`unitLabel`    |Matriz de cadenas|    false    |Nombres de las capas que definen los nombres de las unidades.|
|`zone` | Matriz de cadenas    | false    | Nombres de las capas que definen las zonas.|
|`zoneLabel` | Matriz de cadenas |     false |    Nombres de las capas que definen los nombres de las zonas.|

### `unitProperties`

El objeto `unitProperties` contiene una matriz JSON de las propiedades de la unidad.

| Propiedad  | Tipo | Obligatorio | Descripción |
|-----------|------|----------|-------------|
|`unitName`    |string    |true    |Nombre de la unidad que se asociará a este registro `unitProperty`. Este registro solo es válido cuando se encuentra una etiqueta que coincida con `unitName` en las capas de `unitLabel`. |
|`categoryName`|    string|    false    |Finalidad de la aplicación. [Aquí](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json) puede encontrar una lista de valores que pueden usar los estilos de representación proporcionados. |
|`occupants`    |matriz de objetos directoryInfo |false    |Lista de ocupantes de la unidad. |
|`nameAlt`|    string|    false|    Nombre alternativo de la unidad. |
|`nameSubtitle`|    string    |false|    Subtítulo de la unidad. |
|`addressRoomNumber`|    string|    false|    Número de habitación, unidad, apartamento o suite de la unidad.|
|`verticalPenetrationCategory`|    string|    false| Cuando se define esta propiedad, la característica resultante es una penetración vertical (VRT) en lugar de una unidad. Puede usar penetraciones verticales para ir a otras entidades de penetración vertical en las capas superiores o inferiores. La penetración vertical es un nombre de [categoría](https://aka.ms/pa-indoor-spacecategories). Si se define esta propiedad, la propiedad `categoryName` se reemplaza por `verticalPenetrationCategory`. |
|`verticalPenetrationDirection`|    string|    false    |Si se define `verticalPenetrationCategory`, defina opcionalmente la dirección válida de desplazamiento. Los valores admitidos son: `lowToHigh`, `highToLow`, `both` y `closed`. El valor predeterminado es `both`. El valor distingue mayúsculas de minúsculas.|
| `nonPublic` | bool | false | Indica si la unidad está abierta al público. |
| `isRoutable` | bool | false | Cuando esta propiedad se establece en `false`, no se puede ir a la unidad o a través de ella. El valor predeterminado es `true`. |
| `isOpenArea` | bool | false | Permite que el agente de navegación entre en la unidad sin necesidad de una apertura asociada a la unidad. De forma predeterminada, este valor se establece en `true` para las unidades sin aperturas y en `false` para las unidades con aperturas. Si `isOpenArea` se establece manualmente en `false` en una unidad sin aperturas, se genera una advertencia, ya que la unidad resultante no será accesible para un agente de navegación.|

### `zoneProperties`

El objeto `zoneProperties` contiene una matriz JSON de las propiedades de la zona.

| Propiedad  | Tipo | Obligatorio | Descripción |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |Nombre de la zona que se asociará al registro `zoneProperty`. Este registro solo es válido cuando se encuentra una etiqueta que coincida con `zoneName` en la capa de `zoneLabel` de la zona.  |
|categoryName|    string|    false    |Finalidad de la zona. [Aquí](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json) puede encontrar una lista de valores que pueden usar los estilos de representación proporcionados.|
|zoneNameAlt|    string|    false    |Nombre alternativo de la zona.  |
|zoneNameSubtitle|    string |    false    |Subtítulo de la zona. |
|zoneSetId|    string |    false    | Establezca el identificador a fin de fijar una relación entre varias zonas para que se puedan consultar o seleccionar como grupo. Por ejemplo, las zonas que abarcan varios niveles. |

### <a name="sample-drawing-package-manifest"></a>Manifiesto de paquete de dibujo de ejemplo

A continuación se muestra el archivo de manifiesto del paquete de dibujos de ejemplo. Para descargar todo el paquete, consulte [Paquete de dibujos de ejemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Archivo de manifiesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Cuando el paquete de dibujos cumple los requisitos, puede usar el [servicio de conversión de Azure Maps](/rest/api/maps/v2/conversion) para convertir el paquete en un conjunto de datos de mapa. Después, puede utilizar el conjunto de datos para generar un mapa de interiores mediante el módulo de mapas de interiores.

> [!div class="nextstepaction"]
> [Facility Ontology para Creator](creator-facility-ontology.md)

> [!div class="nextstepaction"]
> [Uso de Creator para planos interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Guía de paquetes de dibujos](drawing-package-guide.md)

> [!div class="nextstepaction"]
>[Creator para mapa de interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Tutorial: Creación de un plano interior de Creator](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilo dinámico para mapas de interiores](indoor-map-dynamic-styling.md)
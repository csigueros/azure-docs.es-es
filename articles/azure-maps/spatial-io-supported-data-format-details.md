---
title: Detalles de los formatos de datos admitidos | Microsoft Azure Maps
description: Obtenga información sobre cómo se analizan los datos espaciales delimitados en el módulo de E/S espacial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 278dae8b3569dd0ff92d3ba12197d1049070167a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744916"
---
# <a name="supported-data-format-details"></a>Detalles de formatos de datos admitidos

En este artículo se proporcionan detalles sobre la compatibilidad de lectura y escritura para todas las etiquetas XML y los tipos de geometría de Well-Known Text. También detalla cómo se analizan los datos espaciales delimitados en el módulo de E/S espacial.

## <a name="supported-xml-namespaces"></a>Espacios de nombres XML admitidos

El módulo de E/S espacial admite etiquetas XML de los siguientes espacios de nombres.

| Prefijo de espacio de nombres | URI de espacio de nombres   | Notas                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Compatibilidad con solo lectura en archivos GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Compatibilidad con solo lectura en archivos GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Compatibilidad con solo lectura en archivos GPX. Analiza y usa DisplayColor. Todas las demás propiedades agregadas a los metadatos de la forma. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Se admite en archivos GPX. Usa color de línea. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Solo lectura. GeoRSS escribe con el formato Atom.              |

## <a name="supported-xml-elements"></a>Elementos XML admitidos

El módulo de E/S espacial admite los siguientes elementos XML. Las etiquetas XML que no se admitan se convertirán en un objeto JSON. A continuación, cada etiqueta se agregará como una propiedad en el campo `properties` de la capa o la forma primaria.

### <a name="kml-elements"></a>Elementos KML

El módulo de E/S espacial admite los siguientes elementos KML.

| Nombre del elemento         | Lectura    | Escritura   | Notas                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | sí     | El objeto se analiza, pero no se usa para la forma de posicionamiento.                                                                    |
| `AddressDetails`     | partial | no      | El objeto se analiza, pero no se usa para la forma de posicionamiento.                                                                    |
| `atom:author`        | sí     | Sí     |                                                                                                                            |
| `atom:link`          | Sí     | Sí     |                                                                                                                            |
| `atom:name`          | Sí     | sí     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode` no se admite. Se convierte a una propiedad `PopupTemplate`. Para escribir, agregue una propiedad `popupTemplate` como una propiedad de la característica para la que desee escribirla. |
| `begin`              | sí     | Sí     |                                                                                                                            |
| `color`              | Sí     | sí     | Incluye `#AABBGGRR` y `#BBGGRR`. Analizado en una cadena de color CSS                                                           |
| `colorMode`          | sí     | No      |                                                                                                                            |
| `coordinates`        | sí     | Sí     |                                                                                                                            |
| `Data`               | Sí     | Sí     |                                                                                                                            |
| `description`        | Sí     | Sí     |                                                                                                                            |
| `displayName`        | Sí     | Sí     |                                                                                                                            |
| `Document`           | Sí     | sí     |                                                                                                                            |
| `drawOrder`          | partial | no      | Se lee para las superposiciones de suelo y se usa para ordenarlas. 
| `east`               | sí     | Sí     |                                                                                                                            |
| `end`                | Sí     | Sí     |                                                                                                                            |
| `ExtendedData`       | Sí     | sí     | Admite `Data`, `SimpleData` o `Schema` sin tipo y los reemplazos de entidad del formulario `$[dataName]`.                      |
| `extrude`            | partial | partial | Solo se admite para los polígonos. La multigeometría que tenga polígonos de diferentes altos se dividirá en características individuales. No se admiten los estilos de línea. Los polígonos con una altitud de 0 se representarán como un polígono plano. Al leer, la altitud de la primera coordenada del anillo exterior se agregará como una propiedad de alto del polígono. A continuación, se usará la altitud de la primera coordenada para representar el polígono en el mapa. |
| `fill`               | sí     | Sí     |                                                                                                                            |
| `Folder`             | Sí     | Sí     |                                                                                                                            |
| `GroundOverlay`      | Sí     | sí     | `color` no se admite.                                                                                                   |
| `heading`            | partial | no      | Analizado pero no representado por `SimpleDataLayer`. Solo escribe si los datos se almacenan en la propiedad de la forma.                 |
| `hotSpot`            | sí     | partial | Solo escribe si los datos se almacenan en la propiedad de la forma. Las unidades se configuran solo como "píxeles".                         |
| `href`               | sí     | sí     |                                                                                                                            |
| `Icon`               | partial | partial | Analizado pero no representado por `SimpleDataLayer`. Solo escribe la propiedad icon de la forma si contiene un URI de datos. Solo se admite `href`. |
| `IconStyle`          | partial | partial | Se analizan los valores `icon`, `heading`, `colorMode` y `hotspots`, pero no se representan en `SimpleDataLayer`         |
| `innerBoundaryIs`    | sí     | Sí     |                                                                                                                            |
| `kml`                | Sí     | Sí     |                                                                                                                            |
| `LabelStyle`         | No      | No      |                                                                                                                            |
| `LatLonBox`          | sí     | Sí     |                                                                                                                            |
| `gx:LatLonQuad`      | Sí     | Sí     |                                                                                                                            |
| `LinearRing`         | Sí     | Sí     |                                                                                                                            |
| `LineString`         | Sí     | Sí     |                                                                                                                            |
| `LineStyle`          | Sí     | sí     | `colorMode` no se admite.                                                                                         |
| `Link`               | sí     | no      | Solo se admite la propiedad `href` para los vínculos de red.                                                                   |
| `MultiGeometry`      | partial | partial | Se puede desglosar en características individuales cuando se lee.                                                                     |
| `name`               | sí     | Sí     |                                                                                                                            |
| `NetworkLink`        | Sí     | no      | Los vínculos deben estar en el mismo dominio que el documento.                                                                  |
| `NetworkLinkControl` | no      | No      |                                                                                                                            |
| `north`              | sí     | Sí     |                                                                                                                            |
| `open`               | Sí     | Sí     |                                                                                                                            |
| `outerBoundaryIs`    | Sí     | Sí     |                                                                                                                            |
| `outline`            | Sí     | Sí     |                                                                                                                            |
| `overlayXY`          | No      | no      |                                                                                                                            |
| `Pair`               | partial | no      | Solo se admite el estilo `normal` en un `StyleMap`. `highlight` no se admite.                                   |
| `phoneNumber`        | sí     | Sí     |                                                                                                                            |
| `PhotoOverlay`       | No      | No      |                                                                                                                            |
| `Placemark`          | sí     | Sí     |                                                                                                                            |
| `Point`              | Sí     | Sí     |                                                                                                                            |
| `Polygon`            | Sí     | Sí     |                                                                                                                            |
| `PolyStyle`          | Sí     | sí     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox` se admite en el nivel de documento.                                                                      |
| `rotation`           | no      | No      |                                                                                                                            |
| `rotationXY`         | No      | No      |                                                                                                                            |
| `scale`              | No      | No      |                                                                                                                            |
| `Schema`             | sí     | Sí     |                                                                                                                            |
| `SchemaData`         | Sí     | sí     |                                                                                                                            |
| `schemaUrl`          | partial | sí     | No admite la carga de estilos de documentos externos que no están incluidos en una KMZ.                             |
| `ScreenOverlay`      | no      | No      |                                                                                                                            |
| `screenXY`           | No      | No      |                                                                                                                            |
| `SimpleData`         | sí     | Sí     |                                                                                                                            |
| `SimpleField`        | Sí     | Sí     |                                                                                                                            |
| `size`               | No      | no      |                                                                                                                            |
| `Snippet`            | partial | partial | El atributo `maxLines` se omite.                                                                                  |
| `south`              | sí     | Sí     |                                                                                                                            |
| `Style`              | Sí     | sí     |                                                                                                                            |
| `StyleMap`           | partial | no      | Solo se admite el estilo normal en un `StyleMap`.                                                                        |
| `styleUrl`           | partial | sí     | No se admiten direcciones URL de estilo externo.                                                                         |
| `text`               | sí     | sí     | No se admite la sustitución de `$[geDirections]`                                                                          |
| `textColor`          | sí     | Sí     |                                                                                                                            |
| `TimeSpan`           | Sí     | Sí     |                                                                                                                            |
| `TimeStamp`          | Sí     | Sí     |                                                                                                                            |
| `value`              | Sí     | sí     |                                                                                                                            |
| `viewRefreshMode`    | partial | no      |  Si apunta a un servicio WMS, solo se admite `onStop` para las superposiciones de suelo. Anexará `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` a la dirección URL y se actualizará a medida que se mueva la asignación.  |
| `visibility`         | sí     | Sí     |                                                                                                                            |
| `west`               | Sí     | Sí     |                                                                                                                            |
| `when`               | Sí     | Sí     |                                                                                                                            |
| `width`              | Sí     | sí     |                                                                                                                            |

### <a name="georss-elements"></a>Elementos GeoRSS

El módulo de E/S espacial admite los siguientes elementos GeoRSS.

| Nombre del elemento             | Lectura    | Escritura | Notas                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | sí     | Sí   |                                                                                                |
| `atom:category`          | Sí     | Sí   |                                                                                                |
| `atom:content`           | Sí     | Sí   |                                                                                                |
| `atom:contributor`       | Sí     | Sí   |                                                                                                |
| `atom:email`             | Sí     | Sí   |                                                                                                |
| `atom:entry`             | Sí     | Sí   |                                                                                                |
| `atom:feed`              | Sí     | Sí   |                                                                                                |
| `atom:icon`              | Sí     | Sí   |                                                                                                |
| `atom:id`                | Sí     | Sí   |                                                                                                |
| `atom:link`              | Sí     | Sí   |                                                                                                |
| `atom:logo`              | Sí     | Sí   |                                                                                                |
| `atom:name`              | Sí     | Sí   |                                                                                                |
| `atom:published`         | Sí     | Sí   |                                                                                                |
| `atom:rights`            | Sí     | Sí   |                                                                                                |
| `atom:source`            | Sí     | Sí   |                                                                                                |
| `atom:subtitle`          | Sí     | Sí   |                                                                                                |
| `atom:summary`           | Sí     | Sí   |                                                                                                |
| `atom:title`             | Sí     | Sí   |                                                                                                |
| `atom:updated`           | Sí     | Sí   |                                                                                                |
| `atom:uri`               | Sí     | Sí   |                                                                                                |
| `geo:lat`                | Sí     | no    | Se escribe como un `georss:point`.                                                                   |
| `geo:lon`                | sí     | no    | Se escribe como un `georss:point`.                                                                   |
| `geo:long`               | sí     | no    | Se escribe como un `georss:point`.                                                                   |
| `georss:box`             | sí     | no    | Se lee como un polígono y se le asigna una propiedad `subType` de "Rectangle".                                |
| `georss:circle`          | sí     | Sí   |                                                                                                |
| `georss:elev`            | Sí     | Sí   |                                                                                                |
| `georss:featurename`     | Sí     | Sí   |                                                                                                |
| `georss:featuretypetag`  | Sí     | Sí   |                                                                                                |
| `georss:floor`           | Sí     | Sí   |                                                                                                |
| `georss:line`            | Sí     | Sí   |                                                                                                |
| `georss:point`           | Sí     | Sí   |                                                                                                |
| `georss:polygon`         | Sí     | Sí   |                                                                                                |
| `georss:radius`          | Sí     | Sí   |                                                                                                |
| `georss:relationshiptag` | Sí     | Sí   |                                                                                                |
| `georss:where`           | Sí     | Sí   |                                                                                                |
| `geourl:latitude`        | Sí     | no    | Se escribe como un `georss:point`.                                                                   |
| `geourl:longitude`       | sí     | no    | Se escribe como un `georss:point`.                                                                   |
| `position`               | sí     | no    | Algunas fuentes XML ajustan GML con una etiqueta de posición en lugar de encapsularla con una etiqueta `georss:where`. Leerá esta etiqueta, pero la escribirá con una etiqueta `georss:where`. |
| `rss`                    | sí     | no    | GeoRSS escrito en formato ATOM.                                                                 |
| `rss:author`             | sí     | partial | Se escribe como un `atom:author`.                                                                 |
| `rss:category`           | sí     | partial | Se escribe como un `atom:category`.                                                               |
| `rss:channel`            | sí     | No    |                                                                                                |
| `rss:cloud`              | sí     | No    |                                                                                                |
| `rss:comments`           | sí     | No    |                                                                                                |
| `rss:copyright`          | sí     | partial | Se escribe como un `atom:rights` si la forma no tiene ya una propiedad `properties` `rights`.       |
| `rss:description`        | sí     | partial | Se escribe como un `atom:content` si la forma no tiene ya una propiedad `properties` `content`.      |
| `rss:docs`               | sí     | No    |                                                                                                |
| `rss:enclosure`          | sí     | No    |                                                                                                |
| `rss:generator`          | sí     | No    |                                                                                                |
| `rss:guid`               | sí     | partial | Se escribe como un `atom:id` si la forma no tiene ya una propiedad `properties` `id`.         |
| `rss:image`              | sí     | partial | Se escribe como un `atom:logo` si la forma no tiene ya una propiedad `properties` `logo`.      |
| `rss:item`               | sí     | partial | Se escribe como un `atom:entry`.                                                                  |
| `rss:language`           | sí     | No    |                                                                                                |
| `rss:lastBuildDate`      | sí     | partial | Se escribe como un `atom:updated` si la forma no tiene ya una propiedad `properties` `updated`.     |
| `rss:link`               | sí     | partial | Se escribe como un `atom:link`.                                                                   |
| `rss:managingEditor`     | sí     | partial | Se escribe como un `atom:contributor`.                                                            |
| `rss:pubDate`            | sí     | partial | Se escribe como un `atom:published` si la forma no tiene ya una propiedad `properties` `published`.  |
| `rss:rating`             | sí     | No    |                                                                                                |
| `rss:skipDays`           | sí     | No    |                                                                                                |
| `rss:skipHours`          | sí     | No    |                                                                                                |
| `rss:source`             | sí     | partial | Se escribe como un `atom:source` que contiene un `atom:link`.                                       |
| `rss:textInput`          | sí     | No    |                                                                                                |
| `rss:title`              | sí     | partial | Se escribe como un `atom:title`.                                                                  |
| `rss:ttl`                | sí     | No    |                                                                                                |
| `rss:webMaster`          | sí     | no    |                                                                                                |

### <a name="gml-elements"></a>Elementos de GML

El módulo de E/S espacial admite los siguientes elementos de GML. 

| Nombre del elemento            | Lectura | Escritura | Notas                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sí  | no    | Se escribe como `gml:posList`.                                                              |
| `gml:curveMember`       | sí  | No    |                                                                                        |
| `gml:curveMembers`      | sí  | No    |                                                                                        |
| `gml:Box`               | sí  | no    | Se escribe como `gml:Envelope`.                                                             |
| `gml:description`       | sí  | Sí   |                                                                                        |
| `gml:Envelope`          | Sí  | Sí   |                                                                                        |
| `gml:exterior`          | Sí  | Sí   |                                                                                        |
| `gml:Feature`           | Sí  | no    | Se escribe como una forma.                                                                    |
| `gml:FeatureCollection` | sí  | no    | Se escribe como una colección Geometry.                                                      |
| `gml:featureMember`     | sí  | no    | Se escribe como una colección Geometry.                                                      |
| `gml:geometry`          | sí  | no    | Se escribe como una forma.                                                                    |
| `gml:geometryMember`    | sí  | Sí   |                                                                                        |
| `gml:geometryMembers`   | Sí  | Sí   |                                                                                        |
| `gml:identifier`        | Sí  | Sí   |                                                                                        |
| `gml:innerBoundaryIs`   | Sí  | no    | Se escribe con `gml.interior`.                                                          |
| `gml:interior`          | sí  | Sí   |                                                                                        |
| `gml:LinearRing`        | Sí  | Sí   |                                                                                        |
| `gml:LineString`        | Sí  | Sí   |                                                                                        |
| `gml:lineStringMember`  | Sí  | Sí   |                                                                                        |
| `gml:lineStringMembers` | Sí  | No    |                                                                                        |
| `gml:MultiCurve`        | sí  | no    | Solo lee miembros `gml:LineString`. Se escribe como `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Solo se lee como FeatureCollection.                                              |
| `gml:MultiLineString`   | sí  | Sí   |                                                                                        |
| `gml:MultiPoint`        | Sí  | Sí   |                                                                                        |
| `gml:MultiPolygon`      | Sí  | Sí   |                                                                                        |
| `gml:MultiSurface`      | Sí  | no    | Solo lee miembros `gml:Polygon`. Se escribe como `gml.MultiPolygon`                        |
| `gml:name`              | sí  | Sí   |                                                                                        |
| `gml:outerBoundaryIs`   | Sí  | no    | Se escribe con `gml.exterior`.                                                          |
| `gml:Point`             | sí  | Sí   |                                                                                        |
| `gml:pointMember`       | Sí  | Sí   |                                                                                        |
| `gml:pointMembers`      | Sí  | No    |                                                                                        |
| `gml:Polygon`           | sí  | Sí   |                                                                                        |
| `gml:polygonMember`     | Sí  | Sí   |                                                                                        |
| `gml:polygonMembers`    | Sí  | No    |                                                                                        |
| `gml:pos`               | sí  | Sí   |                                                                                        |
| `gml:posList`           | Sí  | Sí   |                                                                                        |
| `gml:surfaceMember`     | Sí  | sí   |                                                                                        |

#### <a name="additional-notes"></a>Notas adicionales

- Se buscará en los elementos de miembro una geometría que pueda estar oculta en los elementos secundarios. Esta operación de búsqueda es necesaria, ya que muchos formatos XML que se extienden desde GML pueden no colocar una geometría como elemento secundario directo de un elemento de miembro.
- `srsName` es parcialmente compatible con las coordenadas WGS84 y los códigos siguientes: [EPSG: 4326](https://epsg.io/4326) y web Mercator [EPSG: 3857](https://epsg.io/3857), o uno de sus códigos alternativos. Cualquier otro sistema de coordenadas se analizará como WGS84 tal cual.
- A menos que se especifique al leer una fuente XML, el orden del eje se determina en función de las sugerencias de la fuente XML. Se proporciona una preferencia para el orden de los ejes "latitud, longitud".
- A menos que se especifique un espacio de nombres GML personalizado para las propiedades al escribir en un archivo GML, no se agregará información de propiedad adicional.

### <a name="gpx-elements"></a>Elementos GPX

El módulo de E/S espacial admite los siguientes elementos GPX.

| Nombre del elemento             | Lectura    | Escritura   | Notas                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | sí     | Sí     |                                                                                             |
| `gpx:author`             | Sí     | Sí     |                                                                                             |
| `gpx:bounds`             | Sí     | sí     | Se convierte en un LocationRect cuando se lee.                                                    |
| `gpx:cmt`                | sí     | Sí     |                                                                                             |
| `gpx:copyright`          | Sí     | Sí     |                                                                                             |
| `gpx:desc`               | Sí     | sí     | Se copia en una propiedad description cuando la lectura se alinea con otros formatos XML.               |
| `gpx:dgpsid`             | sí     | Sí     |                                                                                             |
| `gpx:ele`                | Sí     | sí     |                                                                                             |
| `gpx:extensions`         | partial | partial | Cuando se lee, se extrae la información de estilo. Todas las demás extensiones se acoplarán en un objeto JSON simple. Solo se escribe la información de estilo de la forma. |
| `gpx:geoidheight`        | sí     | Sí     |                                                                                             |
| `gpx:gpx`                | Sí     | Sí     |                                                                                             |
| `gpx:hdop`               | Sí     | Sí     |                                                                                             |
| `gpx:link`               | Sí     | Sí     |                                                                                             |
| `gpx:magvar`             | Sí     | Sí     |                                                                                             |
| `gpx:metadata`           | Sí     | Sí     |                                                                                             |
| `gpx:name`               | Sí     | Sí     |                                                                                             |
| `gpx:pdop`               | Sí     | Sí     |                                                                                             |
| `gpx:rte`                | Sí     | Sí     |                                                                                             |
| `gpx:rtept`              | Sí     | Sí     |                                                                                             |
| `gpx:sat`                | Sí     | Sí     |                                                                                             |
| `gpx:src`                | Sí     | Sí     |                                                                                             |
| `gpx:sym`                | Sí     | sí     | Se captura el valor, pero no se usa para modificar el icono de alfiler.                               |
| `gpx:text`               | sí     | Sí     |                                                                                             |
| `gpx:time`               | Sí     | Sí     |                                                                                             |
| `gpx:trk`                | Sí     | Sí     |                                                                                             |
| `gpx:trkpt`              | Sí     | Sí     |                                                                                             |
| `gpx:trkseg`             | Sí     | Sí     |                                                                                             |
| `gpx:type`               | Sí     | Sí     |                                                                                             |
| `gpx:vdop`               | Sí     | Sí     |                                                                                             |
| `gpx:wpt`                | Sí     | Sí     |                                                                                             |
| `gpx_style:color`        | Sí     | sí     |                                                                                             |
| `gpx_style:line`         | partial | partial | Se admiten `color`, `opacity`, `width` y `lineCap`.                                           |
| `gpx_style:opacity`      | sí     | Sí     |                                                                                             |
| `gpx_style:width`        | Sí     | Sí     |                                                                                             |
| `gpxx:DisplayColor`      | Sí     | no      | Se utiliza para especificar el color de una forma. Al escribir, en su lugar se utilizará el color `gpx_style:line`.  |
| `gpxx:RouteExtension`    | partial | no      | Todas las propiedades se leen en `properties`. Sólo se utiliza `DisplayColor`.                     |
| `gpxx:TrackExtension`    | partial | no      | Todas las propiedades se leen en `properties`. Sólo se utiliza `DisplayColor`.                     |
| `gpxx:WaypointExtension` | partial | no      | Todas las propiedades se leen en `properties`. Sólo se utiliza `DisplayColor`.                     |
| `gpx:keywords`           | sí     | Sí     |                                                                                             |
| `gpx:fix`                | Sí     | sí     |                                                                                             |

#### <a name="additional-notes"></a>Notas adicionales

Al escribir;

- Los puntos de Multipoint se dividirán en puntos de trayecto individuales.
- Polygons y MultiPolygons se escribirán como pistas. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipos de geometría de texto Well-Known Text

| Tipo Geometry | Lectura | Escritura |
|--------------|:----:|:-----:|
| POINT | x | x |
| POINT Z | x | x | 
| POINT M | x | x<sup>[2]</sup> |
| POINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLYGON | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Solo se captura el parámetro Z y se agrega como tercer valor en el valor de Position.

\[2\] El parámetro M no se captura.

## <a name="delimited-spatial-data-support"></a>Compatibilidad con datos espaciales delimitados

Los datos espaciales delimitados, como los archivos de valores separados por comas (CSV), suelen tener columnas que contienen datos espaciales. Por ejemplo, puede haber columnas que contengan información de latitud y longitud. En el formato Well-Known Text (WKT) puede haber una columna que contenga datos de geometría espacial.

### <a name="spatial-data-column-detection"></a>Detección de columnas de datos espaciales

Al leer un archivo delimitado que contenga datos espaciales, se analizará el encabezado para determinar qué columnas contienen campos de ubicación. Si el encabezado contiene información de tipos, se usará para convertir los valores de celda al tipo adecuado. Si no se especifica ningún encabezado, se analizará la primera fila y se utilizará para generar un encabezado. Al analizar la primera fila, se ejecuta una comprobación para buscar coincidencias de los nombres de columna con los nombres siguientes, sin distinción entre mayúsculas y minúsculas. El orden de los nombres es la prioridad, en caso de que existan dos o más nombres en un archivo.

#### <a name="latitude"></a>Latitud

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitud

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geography

La primera fila de datos se examinará en busca de cadenas con un formato WKT. 

### <a name="delimited-data-column-types"></a>Tipos de columnas de datos delimitados

Al examinar la fila de encabezado, toda la información de tipo que se encuentre en el nombre de columna se extrae y se utiliza para convertir las celdas de esa columna. A continuación se muestra un ejemplo de un nombre de columna que tiene un valor de tipo: "ColumnName (typeName)". Se admiten los siguientes nombres de tipo que no distinguen mayúsculas de minúsculas:

#### <a name="numbers"></a>Números

- edm.int64
- int
- long
- edm.double
- FLOAT
- double
- number

#### <a name="booleans"></a>Valores booleanos

- edm.boolean
- bool
- boolean

#### <a name="dates"></a>Fechas

- edm.datetime
- date
- datetime

#### <a name="geography"></a>Geography

- edm.geography
- geography

#### <a name="strings"></a>Cadenas

- edm.string
- varchar
- text
- string

Si no se puede extraer información de tipo del encabezado y la opción de escritura dinámica está habilitada al leer, cada celda se analizará individualmente para determinar qué tipo de datos es el más adecuado para convertirse.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de código para agregar a los mapas:

[Lectura y escritura de datos espaciales](spatial-io-read-write-spatial-data.md)

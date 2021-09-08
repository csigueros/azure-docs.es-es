---
title: Glosario de Azure Maps | Microsoft Docs
description: Glosario de términos comunes relacionados con Azure Maps, Location Based Services y GIS.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e1bd7da46d235102ec56feee9bdd2e95917f079b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432835"
---
# <a name="glossary"></a>Glosario

En la lista siguiente se describen las palabras comunes que se usan con los servicios de Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a> **Validación de direcciones**: proceso de comprobar la existencia de una dirección.

<a name="advanced-routing"></a> **Enrutamiento avanzado**: colección de servicios que realizan operaciones avanzadas mediante datos de enrutamiento, como calcular intervalos accesibles (isócronos), matrices de distancia y solicitudes de enrutamiento por lotes.

<a name="aerial-imagery"></a> **Imágenes aéreas**: vea [Imágenes de satélite](#satellite-imagery). 

<a name="along-a-route-search"></a> **Búsqueda en ruta delimitada**: consulta espacial que busca los datos que se encuentran dentro de un período o una distancia de desvío especificados desde una ruta de acceso.

<a name="altitude"></a> **Altitud**: altura o elevación vertical de un punto por encima de una superficie de referencia. Las medidas de altitud se basan en datos de referencia concretos; por ejemplo, el nivel medio del mar. Consulte también Elevación.

<a name="ambiguous"></a> **Ambiguo**: estado de incertidumbre de clasificación de datos que se produce cuando se pueden asignar correctamente dos o más valores para un atributo concreto de un objeto. Por ejemplo, al geocodificar "CA", se devuelven dos resultados ambiguos: "Canada" y "California". "CA" es un código de país o región y de estado, para "Canada" y "California", respectivamente. 

<a name="annotation"></a> **Anotación**: texto o gráfico que se muestra en el mapa para proporcionar información al usuario. La anotación puede identificar o describir una entidad de asignación específica, proporcionar información general sobre un área en el mapa o proporcionar información acerca del propio mapa.

<a name="antimeridian"></a> **Antemeridiano**: también se conoce como el meridiano 180 <sup></sup>. Es el punto donde se encuentran los grados de longitud -180 y 180. Es el opuesto al primer meridiano en el globo.

<a name="application-programming-interface-api"></a> **Interfaz de programación de aplicaciones (API)** : especificación que permite a los desarrolladores crear aplicaciones.

<a name="api-key"></a> **Clave de API**: Consulte [Autenticación de clave compartida](#shared-key-authentication).

<a name="area-of-interest-aoi"></a> **Área de interés (AOI)** : extensión que se usa para definir un área de enfoque para la producción de un mapa o una base de datos.

<a name="asset-tracking"></a> **Seguimiento de activos**: proceso de seguimiento de la ubicación de un recurso, como una persona, un vehículo o algún otro objeto.

<a name="asynchronous-request"></a> **Solicitud asincrónica**: solicitud HTTP que abre una conexión y realiza una solicitud al servidor que devuelve un identificador para la solicitud asincrónica. A continuación, cierra la conexión. El servidor continúa procesando la solicitud y el usuario puede comprobar el estado mediante el identificador. Cuando finaliza el procesamiento de la solicitud, el usuario puede descargar la respuesta. Este tipo de solicitud se suele usar para procesos de larga duración.

<a name="autocomplete"></a> **Autocompletar**: una característica de una aplicación que predice el resto de una palabra que un usuario está escribiendo. 

<a name="autosuggest"></a> **Sugerencias automáticas**: una característica de una aplicación que predice las posibilidades lógicas para lo que el usuario está escribiendo.

<a name="azure-location-based-services-lbs"></a> **Azure Location Based Services (LBS)** : nombre anterior de Azure Maps cuando estaba en versión preliminar.

<a name="azure-active-directory"></a> **Azure Active Directory (Azure AD)** : Azure AD es un servicio de administración de acceso y de identidades basado en la nube de Microsoft. La integración de Azure AD y Azure Maps está disponible actualmente en versión preliminar para todas las API de Azure Maps. Azure AD admite el control de acceso basado en roles de Azure (Azure RBAC) para permitir el acceso específico a recursos de Azure Maps. Para obtener más información sobre la integración de Azure AD y Azure Maps, consulte [Azure Maps y Azure AD](azure-maps-authentication.md) y [Administración de la autenticación en Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a> **Clave de Azure Maps**: Consulte [Autenticación de clave compartida](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a> **Mapa base**: parte de una aplicación de mapas que muestra información de referencia en segundo plano, como carreteras, monumentos y fronteras políticas.

<a name="batch-request"></a> **Solicitud por lotes**: proceso de combinar varias solicitudes en una única.

<a name="bearing"></a> **Rumbo**: dirección horizontal de un punto en relación con otro punto. Se expresa como ángulo con respecto al norte, de 0 a 360 grados en sentido horario. 

<a name="boundary"></a> **Límite**: línea o polígono que separa entidades políticas adyacentes, como países o regiones, distritos y propiedades. Un límite es una línea que puede o no seguir características físicas, como paredes, montañas o ríos.

<a name="bounds"></a> **Límites**: vea [Rectángulo de selección](#bounding-box).

<a name="bounding-box"></a> **Rectángulo de selección**: conjunto de coordenadas que se usa para representar un área rectangular en el mapa. 

## <a name="c"></a>C

<a name="cadastre"></a> **Catastro**: registro de tierra y propiedades registradas. Vea también [Parcela](#parcel).

<a name="camera"></a> **Cámara**: en el contexto de un control de mapa interactivo, una cámara define el campo de visión de los mapas. La ventanilla de la cámara se determina a partir de varios parámetros de mapa: centro, nivel de zoom, inclinación, ángulo. 

<a name="centroid"></a> **Centroide**: centro geométrico de una característica. El centroide de una línea sería su punto medio, mientras que el centroide de un polígono sería el centro de su área.

<a name="choropleth-map"></a> **Mapa de coropletas**: mapa temático en el que las áreas están sombreadas en proporción con una medida de una variable estadística. Esta variable estadística se muestra en el mapa. Por ejemplo, se puede colorear el límite de cada estado de EE. UU. en función de su población relativa al resto de estados.

<a name="concave-hull"></a> **Envolvente cóncava**: una forma que representa una posible geometría cóncava que incluye todas las formas del conjunto de datos especificado. La forma generada es similar a envolver los datos con plástico y calentarlo, lo que provoca que los grandes intervalos entre puntos se doblen hacia otros puntos de datos.

<a name="consumption-model"></a> **Modelo de consumo**: información que define la velocidad a la que un vehículo consume combustible o electricidad. Consulte también la [documentación del modelo de consumo](consumption-model.md).

<a name="control"></a> **Control**: componente reutilizable o autocontenido que consta de una interfaz gráfica de usuario que define un conjunto de comportamientos para la interfaz. Por ejemplo, un control de mapa, por lo general, es la parte de la interfaz de usuario que carga un mapa interactivo.

<a name="convex-hull"></a> **Envolvente convexa**: una envolvente convexa es una forma que representa la geometría convexa mínima que incluye todas las formas del conjunto de datos especificado. La forma generada es similar a una banda elástica ajustada alrededor del conjunto de datos.

<a name="coordinate"></a> **Coordenada**: consta de los valores de longitud y latitud utilizados para representar una ubicación en un mapa.

<a name="coordinate-system"></a> **Sistema de coordenadas**: marco de referencia que se utiliza para definir las posiciones de puntos en el espacio en dos o tres dimensiones.

<a name="country-code"></a> **Código de país**: identificador único para un país o región según el estándar ISO. ISO2 es un código de dos caracteres de un país o región (por ejemplo, US), que en ISO3 se representa con un código de tres caracteres (por ejemplo, USA).

<a name="country-subdivision"></a> **Subdivisión de país o región**: subdivisión de primer nivel de un país o región, normalmente conocida como estado o provincia.

<a name="country-secondary-subdivision"></a> **Subdivisión secundaria de país o región**: subdivisión de segundo nivel de país o región, normalmente conocida como condado o municipio.

<a name="country-tertiary-subdivision"></a> **Subdivisión terciaria de país o región**: subdivisión de tercer nivel de país o región. Suele ser un área con un nombre, como un distrito electoral.

<a name="cross-street"></a> **Intersección**: punto de intersección de dos o más calles.

<a name="cylindrical-projection"></a> **Proyección cilíndrica**: proyección que transforma los puntos de un esferoide o esfera en un cilindro secante o tangente. A continuación, se corta de arriba a abajo y se aplana para convertirse en un plano.

## <a name="d"></a>D

<a name="datum"></a> **Datos de referencia**: especificaciones de referencia de un sistema de medición, un sistema de posiciones de coordenadas en una superficie (datos de referencia horizontales) o altura por encima o debajo de una superficie (datos de referencia verticales).

<a name="dbf-file"></a> **Archivo DBF**: formato de archivo de base de datos que se usa en combinación con los archivos de forma (SHP).

<a name="degree-minutes-seconds-dms"></a> **Grados, minutos y segundos (DMS)** : unidad de medida para describir la latitud y longitud. Un grado es 1 de 360 partes de un círculo. Un grado se divide en 60 minutos y un minuto, en 60 segundos.

<a name="delaunay-triangulation"></a> **Triangulación Delaunay**: técnica para crear una malla de triángulos contiguos y no superpuestos a partir de un conjunto de datos de puntos. Cada círculo delimitador de triángulo no contiene ningún punto del conjunto de datos en su interior.

<a name="demographics"></a> **Demografía**: características estadísticas (por ejemplo, edad, tasa de natalidad e ingresos) de una población humana.

<a name="destination"></a> **Destino**: punto de conexión o ubicación a la que se desplaza alguien.

<a name="digital-elevation-model-dem"></a> **Modelo de elevación digital (DEM)** : conjunto de datos de valores de elevación en relación con una superficie, capturados en un área en intervalos periódicos mediante datos de referencia comunes. Normalmente, los DEM se usan para representar un relieve en el terreno.

<a name="dijkstra's-algorithm"></a> **Algoritmo de Dijkstra**: un algoritmo que examina la conectividad de una red para encontrar la ruta más corta entre dos puntos.

<a name="distance-matrix"></a> **Matriz de distancia**: matriz que contiene información de tiempo y distancia entre un conjunto de orígenes y destinos. 

## <a name="e"></a>E

<a name="elevation"></a> **Elevación**: distancia vertical de un punto o un objeto por encima o por debajo de una superficie o de datos de referencia. Por lo general, la superficie de referencia es el valor medio del nivel de mar. Normalmente, la elevación hace referencia a la altura vertical de la tierra.

<a name="envelope"></a> **Sobre**: vea [Rectángulo de selección](#bounding-box).

<a name="extended-postal-code"></a> **Código postal ampliado**: código postal que puede incluir información adicional. Por ejemplo, en EE. UU., los códigos postales tienen cinco dígitos. Pero un código postal extendido, conocido como zip+4, incluye cuatro dígitos adicionales. Estos dígitos adicionales se usan para identificar un segmento geográfico dentro del área de entrega de cinco dígitos, como una manzana en la ciudad, un grupo de apartamentos o un apartado de correos. Conocer el segmento geográfico ayuda a la ordenación y la entrega de correo eficaces.

<a name="extent"></a> **Extensión**: vea [Rectángulo de selección](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Autenticación federada**: método de autenticación que permite usar un mecanismo de autenticación o inicio de sesión único en diferentes aplicaciones web y móviles. 

<a name="feature"></a> **Característica**: objeto que combina una geometría con información adicional sobre metadatos. 

<a name="feature-collection"></a> **Colección de características**: colección de objetos de características.

<a name="find-along-route"></a> **Ruta delimitada de búsqueda**: consulta espacial que busca los datos que se encuentran dentro de un período o distancia de desvío especificados desde una ruta de acceso de enrutamiento.

<a name="find-nearby"></a> **Buscar cerca**: consulta espacial que busca una distancia fija en línea recta desde un punto.

<a name="fleet-management"></a> **Administración de flota**: administración de vehículos comerciales, como coches, camiones, barcos y aviones. La administración de flota puede incluir varias funciones, como la financiación del vehículo, el mantenimiento, la telemática (seguimiento y diagnóstico) así como la administración de conductores, velocidad, combustible, estado y seguridad. La administración de flota es un proceso que usan las empresas que dependen del transporte en su negocio. Estas lo que buscan es minimizar los riesgos y reducir los costos de transporte y personal generales al mismo tiempo que garantizan el cumplimiento de la legislación gubernamental.

<a name="free-flow-speed"></a> **Velocidad de flujo libre**: la velocidad de flujo libre que se espera en condiciones ideales. Normalmente, el límite de velocidad.

<a name="free-form-address"></a> **Dirección de formato libre**: dirección completa que se representa con una sola línea de texto.

<a name="fuzzy-search"></a> **Búsqueda aproximada**: búsqueda que toma una cadena de formato libre de texto que puede ser una dirección o punto de interés. 

## <a name="g"></a>G

<a name="geocode"></a> **Código geográfico**: dirección o ubicación convertida en una coordenada que se puede usar para mostrar esa ubicación en un mapa. 

<a name="geocoding"></a> **Codificación geográfica**: también conocida como codificación geográfica directa, es el proceso de conversión de dirección de datos de ubicación a coordenadas. 

<a name="geodesic-path"></a> **Ruta geodésica**: ruta de acceso más corta entre dos puntos en una superficie curva. Cuando se representa en Azure Maps, esta ruta de acceso aparece como una línea curva debido a la proyección de Mercator.

<a name="geofence"></a> **Geovalla**: región geográfica definida que se puede usar para desencadenar eventos cuando un dispositivo entra en la región o la abandona.

<a name="geojson"></a> **GeoJSON**: formato de archivo basado en JSON común utilizado para almacenar datos vectoriales geográficos, como puntos, líneas y polígonos. **Nota**: Azure Maps usa una versión extendida de GeoJSON como [se documenta aquí](extend-geojson.md).

<a name="geometry"></a> **Geometría**: representa un objeto espacial, como un punto, línea o polígono.

<a name="geometrycollection"></a> **GeometryCollection**: colección de objetos de geometría.

<a name="geopol"></a> **GeoPol**: hace referencia a datos geopolíticos confidenciales, como fronteras y nombres de lugar en conflicto.

<a name="georeference"></a> **Georeferenciar**: proceso de alinear datos geográficos o imágenes con un conocido sistema de coordenadas. Este proceso puede consistir en desplazar, girar, escalar o sesgar los datos.

<a name="georss"></a> **GeoRSS**: extensión XML para agregar datos espaciales a fuentes RSS.

<a name="gis"></a> **GIS**: acrónimo en inglés de "Sistema de información geográfica". Término común que se usa para describir el sector de los mapas.

<a name="gml"></a> **GML**: también conocido como lenguaje de marcado geográfico. Extensión de archivo XML para almacenar datos espaciales.

<a name="gps"></a> **GPS**: también conocido como sistema de posicionamiento global, es un sistema de satélites utilizado para determinar la posición de un dispositivo en la Tierra. Los satélites en órbita transmiten señales que permiten que un receptor GPS en cualquier lugar de la Tierra calcule su propia ubicación mediante la trilateración.

<a name="gpx"></a> **GPX**: también conocido como formato de intercambio GPS, es un formato de archivo XML creado con frecuencia desde dispositivos GPS.  

<a name="great-circle-distance"></a> **Distancia ortodrómica**: distancia más corta entre dos puntos en la superficie de una esfera.

<a name="greenwich-mean-time-gmt"></a> **Hora del meridiano de Greenwich (GMT)** : hora del primer meridiano, que pasa por el Real Observatorio de Greenwich (Inglaterra).

<a name="guid"></a> **GUID**: un identificador único global. Cadena utilizada para identificar de forma única una interfaz, clase, biblioteca de tipos, categoría de componente o registro.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Fórmula del semiverseno**: ecuación común utilizada para calcular la distancia ortodrómica entre dos puntos de una esfera.

<a name="hd-maps"></a> **Mapas HD**: también conocidos como mapas de alta definición, contienen información de redes de carreteras de alta fidelidad, como señales de carreteras angostas, señalización y luces de dirección necesarias para una conducción autónoma.

<a name="heading"></a> **Dirección**: dirección a la que se dirige algo. Vea también [Rumbo](#heading).

<a name="heatmap"></a> **Mapa térmico**: visualización de datos en que una gama de colores representan la densidad de los puntos de un área determinada. Vea también Mapa temático.

<a name="hybrid-imagery"></a> **Imágenes híbridas**: imágenes de satélite o aéreas con datos de carretera y etiquetas superpuestos.

## <a name="i"></a>I

<a name="iana"></a> **IANA**: acrónimo de Internet Assigned Numbers Authority. Grupo sin ánimo de lucro que supervisa la asignación de direcciones IP global.

<a name="isochrone"></a> **Isócrono**: un isócrono define el área en que alguien puede desplazarse durante un tiempo especificado en un medio de transporte en cualquier dirección desde una ubicación determinada. Vea también [Rango alcanzable](#reachable-range).

<a name="isodistance"></a> **Isodistancia**: dada una ubicación, un isócrono define el área en que alguien puede desplazarse en una distancia especificada en un medio de transporte en cualquier dirección. Vea también [Rango alcanzable](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: también conocido como lenguaje de marcado de Keyhole, es un formato de archivo XML común para almacenar datos de vector geográficos, como puntos, líneas y polígonos. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: satélites multiespectrales en órbita con la Tierra desarrollados por la NASA que recopilan imágenes de tierra. Esta imagen se usa en muchos sectores, como la agricultura, la silvicultura y la cartografía.

<a name="latitude"></a> **Latitud**: distancia angular medida en grados desde el Ecuador hacia el norte o el sur.

<a name="level-of-detail"></a> **Nivel de detalle**: Vea Nivel de zoom.

<a name="lidar"></a> **Lidar**: acrónimo para la detección y determinación del alcance de la luz. Técnica de detección remota que usa láseres para medir distancias a superficies reflectantes.

<a name="linear-interpolation"></a> **Interpolación lineal**: estimación de un valor desconocido mediante la distancia lineal entre valores conocidos.

<a name="linestring"></a> **LineString**: geometría usada para representar una línea. También se denomina polilínea. 

<a name="localization"></a> **Localización**: compatibilidad para diferentes idiomas y referencias culturales.

<a name="logistics"></a> **Logística**: proceso de mover personas, vehículos, suministros o activos de manera coordinada.

<a name="longitude"></a> **Longitud**: distancia angular medida en grados desde el primer meridiano hacia el este u oeste.

## <a name="m"></a>M

<a name="map-tile"></a> **Mosaico de mapa**: imagen rectangular que representa una partición de un lienzo de mapa. Para más información, consulte la [documentación de niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marcador**: también conocido como ancla o chincheta, es un icono que representa una ubicación puntual en un mapa.

<a name="mercator-projection"></a> **Proyección de Mercator**: proyección de mapa cilíndrica que se convirtió en la proyección de mapa estándar con fines náuticos debido a su capacidad para representar líneas de curso constante, conocidas como loxodrómicas, como segmentos rectos que conservan los ángulos con los meridianos. Todas las proyecciones de mapa planas distorsionan las formas o el tamaño del mapa cuando se comparan con la disposición real de la superficie terrestre. La proyección de Mercator exagera las áreas lejos del Ecuador, de forma que las áreas más pequeñas parecen de mayor tamaño en el mapa al acercarse a los polos. 

<a name="multilinestring"></a> **MultiLineString**: geometría que representa una colección de objetos LineString. 

<a name="multipoint"></a> **MultiPoint**: geometría que representa una colección de objetos Point.

<a name="multipolygon"></a> **MultiPolygon**: geometría que representa una colección de objetos Polygon. Por ejemplo, para mostrar el límite de Hawai, se crearía un polígono como contorno en cada isla. Por lo tanto, el límite de Hawai sería un elemento MultiPolygon.

<a name="municipality"></a> **Municipio**: ciudad o localidad. 

<a name="municipality-subdivision"></a> **Subdivisión municipal**: subdivisión de un municipio, como un nombre de barrio o área local, como "centro".

## <a name="n"></a>N

<a name="navigation-bar"></a> **Barra de navegación**: conjunto de controles en un mapa que se usa para ajustar el nivel de zoom, rotación alrededor del eje x, rotación y cambio de capa base del mapa.

<a name="nearby-search"></a> **Búsqueda de elementos cercanos**: consulta espacial que busca una distancia fija en línea recta desde un punto.

<a name="neutral-ground-truth"></a> **Verdad terreno**: mapa que representa las etiquetas en el idioma oficial de la región que representa y en scripts locales, si están disponibles.

## <a name="o"></a>O

<a name="origin"></a> **Origen**: punto o ubicación inicial de un usuario.

## <a name="p"></a>P

<a name="panning"></a> **Desplazamiento lateral**: proceso de mover el mapa en cualquier dirección mientras se mantiene un nivel de zoom constante.

<a name="parcel"></a> **Parcela**: trazado de límite de terreno o propiedad.

<a name="pitch"></a> **Buzamiento**: cantidad de inclinación del mapa respecto a la vertical, donde 0 representa directamente mirar hacia abajo el mapa.

<a name="point"></a> **Punto**: geometría que representa una posición única en el mapa. 

<a name="points-of-interest-poi"></a> **Puntos de interés (POI)** : una empresa, punto de referencia o lugar de interés común.

<a name="polygon"></a> **Polígono**: geometría sólida que representa un área en un mapa. 

<a name="polyline"></a> **Polilínea**: geometría usada para representar una línea. También se denomina LineString. 

<a name="position"></a> **Posición**: longitud, latitud y altitud (coordenadas x, y, z) de un punto.

<a name="post-code"></a>**C. P.** : vea [Código postal](#postal-code).

<a name="postal-code"></a> **Código postal**: serie de letras o números, o ambos, en formato específico que usa el servicio postal de un país o una región para dividir las áreas geográficas en zonas con el fin de simplificar la entrega de correo.

<a name="primary-key"></a> **Clave principal**: Primera de las dos claves de suscripción proporcionadas para la autenticación de clave compartida de Azure Maps. Consulte [Autenticación de clave compartida](#shared-key-authentication).

<a name="prime-meridian"></a> **Primer meridiano**: línea de longitud que representa la longitud de 0 grados. Por lo general, los valores de longitud disminuyen al desplazarse hacia el oeste hasta los 180 grados y se reducen al desplazarse hacia el este hasta los -180 grados. 

<a name="prj"></a> **PRJ**: archivo de texto que acompaña a menudo a un archivo de forma que contiene información sobre el sistema de coordenadas proyectado en que se encuentra el conjunto de datos.

<a name="projection"></a> **Proyección**: sistema de coordenadas proyectado basado en una proyección de mapa como Mercator transversal, proyección equiáreas de Albers y Robinson. Proporcionan la capacidad de proyectar mapas de la superficie esférica de la Tierra en un plano de coordenadas cartesianas bidimensional. Los sistemas de coordenadas proyectados a veces se conocen como proyecciones de mapa.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey**: índice de direcciones de base 4 para un corte de un sistema de división en cortes de árbol cuaternario. Para más información, consulte la [documentación de niveles de zoom y cuadrícula de cortes](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a> **Árbol cuaternario**: estructura de datos en que cada nodo tiene exactamente cuatro elementos secundarios. El sistema de división en cortes que se usa en Azure Maps usa una estructura de árbol cuaternario y, cuando un usuario amplía un nivel, cada corte del mapa se divide en cuatro.  Para más información, consulte la [documentación de niveles de zoom y cuadrícula de cortes](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a> **Consultas por segundo (QPS)** : número de consultas o solicitudes que pueden realizarse en una plataforma o servicio en un segundo. 

## <a name="r"></a>R

<a name="radial-search"></a> **Búsqueda radial**: consulta espacial que busca una distancia fija en línea recta desde un punto. 

<a name="raster-data"></a> **Datos de trama**: matriz de celdas (o píxeles) que se organizan en filas y columnas (o una cuadrícula) donde cada celda contiene un valor que representa la información, como la temperatura. Las tramas incluyen fotografías aéreas digitales, imágenes de satélites, fotografías digitales y mapas digitalizados.

<a name="raster-layer"></a> **Capa de trama**: capa de corte que consta de imágenes de trama.

<a name="reachable-range"></a> **Rango de alcance**: un rango de alcance define el área en que alguien puede desplazarse durante un tiempo o en una distancia especificados en un medio de transporte para desplazarse en cualquier dirección desde una ubicación. Vea también [Isócrono](#isochrone) e [Isodistancia](#isodistance).

<a name="remote-sensing"></a> **Teledetección**: proceso de recopilación e interpretación de datos de sensores a distancia.

<a name="rest-service"></a> **Servicio REST**: el acrónimo REST significa transferencia de estado representacional. Un servicio REST es un servicio web basado en URL que se basa en tecnología web básica para la comunicación. Los métodos más comunes son las solicitudes HTTP GET y POST. Estos tipos de servicios tienden a ser mucho más rápidos y más pequeños que los servicios tradicionales basados en SOAP.

<a name="reverse-geocode"></a> **Codificación geográfica inversa**: proceso de tomar una coordenada y determinar la dirección en la que se representa en un mapa.

<a name="reproject"></a> **Reproyectar**: vea [Transformación](#transformation).

<a name="rest-service"></a> **Servicio REST**: acrónimo de transferencia de estado representacional. Una arquitectura para intercambiar información entre equipos del mismo nivel en un entorno distribuido descentralizado. REST permite que los programas de distintos equipos se comuniquen independientemente del sistema operativo o la plataforma. Un servicio puede enviar una solicitud de protocolo de transferencia de hipertexto (HTTP) a un localizador uniforme de recursos (URL) y obtener datos.

<a name="route"></a> **Ruta**: ruta entre dos o más ubicaciones, que también puede incluir información adicional, como instrucciones para puntos de referencia en la ruta.

<a name="requests-per-second-rps"></a> **Solicitudes por segundo (RPS)** : vea [Consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: acrónimo de Really Simple Syndication, resumen de sitio de marco de descripción de recursos (RDF) o fuentes Rich Site Summary, dependiendo del origen. Un formato XML simple y estructurado para compartir contenido entre diferentes sitios web. Los documentos RSS incluyen elementos de metadatos clave, como autor, fecha, título, una breve descripción y un vínculo de hipertexto. Esta información ayuda a un usuario (o servicio del editor RSS) a decidir qué materiales merecen una investigación más minuciosa.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Imágenes de satélite**: imágenes que han capturado aviones y satélites apuntando directamente hacia abajo.

<a name="secondary-key"></a> **Clave secundaria**: Segunda de las dos claves de suscripción proporcionadas para la autenticación de clave compartida de Azure Maps. Consulte [Autenticación de clave compartida](#shared-key-authentication).

<a name="shapefile-shp"></a> **Archivo de forma (SHP)** : también conocido como archivo de forma ESRI, es un formato de almacenamiento de datos de vector para almacenar la ubicación, forma y los atributos de las características geográficas. Un archivo de forma se almacena en un conjunto de archivos relacionados.

<a name="shared-key-authentication"></a> **Autenticación de clave compartida**: La autenticación de clave compartida se basa en pasar las claves generadas por la cuenta de Azure Maps con cada solicitud a Azure Maps. Estas claves se conocen a menudo como claves de suscripción. Es recomendable que las claves se regeneren periódicamente a efectos de seguridad. Se le proporcionan dos claves de acceso para que pueda mantener las conexiones con una mientras se regenera la otra. Cuando regenere las claves, debe actualizar todas las aplicaciones que acceden a esta cuenta para usar las nuevas claves. Para obtener más información sobre la autenticación de Azure Maps, consulte [Azure Maps y Azure AD](azure-maps-authentication.md) y [Administración de la autenticación en Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a> **Kit de desarrollo de software (SDK)** : colección de documentación, código de ejemplo y aplicaciones de ejemplo que ayudan a un desarrollador a utilizar una API para compilar aplicaciones.

<a name="spherical-mercator-projection"></a> **Proyección de Mercator esférica**: vea [Web Mercator](#web-mercator). 

<a name="spatial-query"></a> **Consulta espacial**: solicitud realizada a un servicio que realiza una operación espacial. Por ejemplo, una búsqueda radial o en ruta.

<a name="spatial-reference"></a> **Referencia espacial**: sistema local, regional o global basado en coordenadas usado para localizar con exactitud las entidades geográficas. Define el sistema de coordenadas que se utiliza para relacionar las coordenadas de un mapa con ubicaciones del mundo real. Las referencias espaciales garantizan que los datos espaciales de diferentes capas u orígenes pueden integrarse para consultarlos o analizarlos de forma precisa. Azure Maps usa el sistema de referencia de coordenadas [EPSG:3857](https://epsg.io/3857) y WGS 84 y para los datos de geometría de entrada.

<a name="sql-spatial"></a> **SQL espacial**: hace referencia a la funcionalidad espacial integrada en SQL Azure y SQL Server 2008 y versiones posteriores. Esta funcionalidad espacial también está disponible como una biblioteca de .NET que se puede usar con independencia de SQL Server. Consulte la [documentación de datos espaciales (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server) para obtener más información.

<a name="subscription-key"></a> **Clave de suscripción**: Consulte [Autenticación de clave compartida](#shared-key-authentication).

<a name="synchronous-request"></a>**Solicitud sincrónica**: una solicitud HTTP abre una conexión y espera una respuesta. Los exploradores limitan el número de solicitudes HTTP simultáneas que se pueden realizar desde una página. Si se realizan varias solicitudes sincrónicas de larga duración al mismo tiempo, este límite se puede alcanzar. Las solicitudes se retrasarán hasta que se haya completado una de las otras solicitudes.

## <a name="t"></a>T

<a name="telematics"></a> **Telemática**: enviar, recibir y almacenar información a través de los dispositivos de telecomunicaciones, además de ejercer control sobre objetos remotos. 

<a name="temporal-data"></a> **Datos temporales**: datos que se refieren específicamente a horas o fechas. Los datos temporales pueden hacer referencia a eventos aislados (como rayos), a objetos en movimiento (como trenes) o a observaciones repetidas (como recuentos de sensores de tráfico).

<a name="terrain"></a> **Terreno**: superficie de tierra con una característica concreta, como terreno arenoso o montañoso.

<a name="thematic-maps"></a> **Mapas temáticos**: un mapa temático es un mapa simple creado para reflejar un tema en un área geográfica. Un escenario común para este tipo de mapa es la coloración de regiones administrativas, como países o regiones, en función de las métricas de datos.

<a name="tile-layer"></a> **Capa de cortes**: una capa mostrada ensamblando cortes de mapa (secciones rectangulares) en una capa continua. Los cortes son cortes de vector o de imagen de trama. Las capas de cortes de trama se suelen representar antes de tiempo y se almacenan como imágenes en un servidor. Pueden usar gran espacio de almacenamiento. Las capas de cortes de vector se representan casi en tiempo real en la aplicación cliente. Por lo tanto, la necesidad de almacenamiento del servidor es menor para las capas de cortes de vector.

<a name="time-zone"></a> **Zona horaria**: una región del mundo que observa una hora estándar uniforme con fines legales, comerciales y sociales. Las zonas horarias tienden a seguir los límites de países o regiones, y sus subdivisiones.

<a name="transaction"></a> **Transacción**: Azure Maps usa un modelo de licencias transaccional donde;

- Se crea una transacción para cada 15 cortes de tráfico o mapa solicitados.
- Se crea una transacción para cada llamada API a uno de los servicios de Azure Maps, como la búsqueda y el enrutamiento.

<a name="transformation"></a> **Transformación**: proceso de conversión de datos entre distintos sistemas de coordenadas geográficas. Por ejemplo, podría tener algunos datos capturados en el Reino Unido y basados en el sistema de coordenadas geográficas OSGB 1936. Azure Maps usa la variante del sistema de referencia de coordenadas [EPSG:3857](https://epsg.io/3857) de WGS84. Para que muestren los datos correctamente, sus coordenadas se deben transformar de un sistema a otro.

<a name="traveling-salesmen-problem-tsp"></a> **Problema del vendedor ambulante (TSP)** :  problema de camino hamiltoniano en el que un vendedor debe encontrar la manera más eficaz de hacer una serie de visitas y volver a la ubicación inicial.  

<a name="trilateration"></a> **Trilateración**: proceso de determinar la posición de un punto de la superficie terrestre con respecto a otros dos puntos mediante la medición de las distancias entre los tres puntos.

<a name="turn-by-turn-navigation"></a> **Navegación paso a paso**: aplicación que proporciona instrucciones de ruta para cada paso de una ruta a medida que los usuarios se aproximan a la próxima maniobra.

## <a name="v"></a>V

<a name="vector-data"></a> **Datos vectoriales**: datos basados en coordenadas que se representan como puntos, líneas o polígonos.

<a name="vector-tile"></a> **Corte vectorial**: especificación de datos abiertos para almacenar datos de vectores geoespaciales mediante el mismo sistema de cortes que el control de mapa. Vea también [Capa de cortes](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Problema de enrutamiento de vehículos (VRP)** : clase de problemas en la que se calcula un conjunto de rutas ordenadas para una flota de vehículos teniendo en cuenta una serie de restricciones. Estas restricciones pueden incluir periodos de tiempo de entrega, varias funcionalidades de ruta y restricciones de duración de viaje.

<a name="voronoi-diagram"></a> **Diagrama de Voronoi**: partición de espacio en áreas o celdas que rodean un conjunto de objetos geométricos (normalmente, características de punto). Estas celdas o polígonos deben cumplir los criterios de los triángulos de Delaunay. Todas las ubicaciones de un área están más cerca del objeto que rodea que de cualquier otro objeto del conjunto. A menudo, los diagramas de Voronoi se suelen usar para delinear áreas de influencia en torno a características geográficas. 

## <a name="w"></a>W

<a name="waypoint"></a> **Punto de referencia**: un punto de referencia es una ubicación geográfica especificada definida por la longitud y latitud que se usa para fines de navegación. A menudo se usa para representar un punto a través del que alguien navega por una ruta.

<a name="waypoint-optimization"></a> **Optimización de puntos de referencia**: proceso de reordenación de un conjunto de puntos de referencia para minimizar el tiempo de desplazamiento o la distancia necesaria para atravesar todos los puntos de referencia proporcionados. Según la complejidad de la optimización, esta a menudo se conoce como [Problema del vendedor ambulante](#traveling-salesmen-problem-tsp) o [Problema de enrutamiento de vehículos](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a> **Web Map Service (WMS)** : WMS es un estándar de Open Geographic Consortium (OGC) que define servicios de mapas basados en imagen. Los servicios de WMS proporcionan imágenes de mapa para áreas específicas de un mapa a petición. Las imágenes incluyen una simbología representada previamente y se pueden representar en uno de varios estilos concretos, si así lo define el servicio.

<a name="web-mercator"></a> **Web Mercator**: también se conoce como Proyección de Mercator esférica. Es una ligera variación de la proyección de Mercator que se usa, principalmente, en programas de generación de mapas basados en la web. Usa las mismas fórmulas que la proyección de Mercator estándar que se usa para mapas a pequeña escala. Sin embargo, Web Mercator usa las fórmulas esféricas en todas las escalas, mientras que los mapas de Mercator de gran escala suelen utilizar la forma elipsoidal de la proyección. La discrepancia es imperceptible a escala global, pero hace que los mapas de áreas locales difieran ligeramente de los auténticos mapas de Mercator elipsoidales de la misma escala.

<a name="wgs84"></a> **WGS84**: conjunto de constantes usadas para relacionar las coordenadas espaciales con ubicaciones en la superficie del mapa. Los datos de referencia de WGS84 son el estándar que usan la mayoría de proveedores de mapas y dispositivos GPS. Azure Maps usa la variante del sistema de referencia de coordenadas [EPSG:3857](https://epsg.io/3857) de WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Coordenada Z**: vea [Altitud](#altitude). 

<a name="zip-code"></a> **Zip Code**: vea [Código postal](#postal-code).

<a name="Zoom level"></a> **Nivel de zoom**: especifica el nivel de detalle y qué parte del mapa es visible. Cuando se amplía hasta el nivel 0, se puede ver el mapa del mundo completo. Sin embargo, el mapa mostrará detalles limitados, como los nombres de los países o de las regiones, los bordes y los nombres de los océanos. Si se acerca al nivel 17, el mapa mostrará un área con algunas manzanas de una ciudad con información detallada sobre las calles. En Azure Maps, el nivel de zoom máximo es 22. Para más información, consulte la documentación sobre [niveles de zoom y cuadrícula de cortes](zoom-levels-and-tile-grid.md).
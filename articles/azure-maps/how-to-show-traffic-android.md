---
title: Visualización de los datos de tráfico en mapas de Android | Microsoft Azure Maps
description: En este artículo aprenderá cómo mostrar los datos de tráfico en un mapa mediante el Android SDK de Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 34d5db3b9c7a1f5829850d999cf94fc1a13c167a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437749"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Visualización de datos de tráfico en el mapa (Android SDK)

Los datos de flujo y los datos de incidentes son los dos tipos de datos de tráfico que se pueden mostrar en el mapa. En esta guía se muestra cómo visualizar ambos tipos de datos de tráfico. Los datos de incidentes se componen de datos basados en puntos y líneas para aspectos como construcciones, cierres de carreteras y accidentes. Los datos de flujo proporcionan métricas sobre el flujo de tráfico en la ruta.

## <a name="prerequisites"></a>Prerrequisitos

Asegúrese de completar los pasos descritos en el documento [Inicio rápido: Creación de una aplicación de Android](quick-android-map.md). Los bloques de código de este artículo se pueden insertar en el controlador de eventos `onReady` de mapas.

## <a name="show-traffic-on-the-map"></a>Visualización del tráfico en el mapa

Hay dos tipos de datos de tráfico disponibles en Azure Maps:

- Datos de incidentes, que se componen de datos basados en puntos y líneas para aspectos como construcción, cierres de carreteras y accidentes.
- Datos de flujo, que proporcionan métricas sobre el flujo de tráfico en las carreteras. A menudo, los datos de flujo de tráfico se utilizan para colorear las carreteras. Los colores se basan en la cantidad de tráfico que ralentiza el flujo en relación con el límite de velocidad u otra métrica. Hay cuatro valores que se pueden pasar a la opción `flow` de tráfico del mapa.

    |Enumeración del flujo | Descripción|
    | :-- | :-- |
    | `TrafficFlow.NONE` | No muestra los datos de tráfico en el mapa. |
    | `TrafficFlow.RELATIVE` | Muestra los datos de tráfico relativos a la velocidad de flujo libre de la carretera. |
    | `TrafficFlow.RELATIVE_DELAY` | Muestra las áreas que son más lentas que el retraso promedio esperado. |
    | `TrafficFlow.ABSOLUTE` | Muestra la velocidad absoluta de todos los vehículos en la carretera. |

En el código siguiente se muestra cómo mostrar los datos de tráfico en el mapa.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

En la siguiente captura de pantalla se muestra cómo el código anterior representa información de tráfico en tiempo real en el mapa.

![Mapa que muestra información de tráfico en tiempo real](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Obtención de detalles sobre incidentes de tráfico

Los detalles sobre un incidente de tráfico están disponibles en las propiedades de la característica que se usa para mostrar el incidente en el mapa. Los incidentes de tráfico se agregan al mapa mediante el servicio de mosaico vectorial de incidentes de tráfico de Azure Maps. El formato de los datos de estos mosaicos vectoriales se [documenta aquí](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). El siguiente código agrega un evento Click al mapa, recupera la característica de incidente de tráfico donde se hizo clic y muestra un mensaje de notificación con algunos de los detalles.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

En la siguiente captura de pantalla se muestra cómo el código anterior representa información de tráfico en tiempo real en el mapa con un mensaje de notificación que incluye detalles sobre el incidente.

![Mapa que muestra información de tráfico en tiempo real con un mensaje de notificación que incluye detalles del incidente](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="filter-traffic-incidents"></a>Filtrado de incidentes de tráfico

En un día típico de la mayoría de las ciudades principales, puede haber un número abrumador de incidentes de tráfico; sin embargo, en función de su escenario, puede ser conveniente filtrar y mostrar un subconjunto de estos incidentes. Al establecer las opciones de tráfico, las opciones `incidentCategoryFilter` y `incidentMagnitudeFilter` toman una matriz de categorías de incidentes o enumeradores de magnitud o valores de cadena.

En la tabla siguiente, se muestran todas las categorías de incidentes de tráfico que se pueden usar dentro de la opción `incidentCategoryFilter`.

| Enumeración de la categoría | Valor de cadena | Descripción |
|--------------------|--------------|-------------|
| `IncidentCategory.UNKNOWN` | `"unknown"` | Un incidente que no se ajusta a ninguna de las categorías definidas o que aún no se ha clasificado. |
| `IncidentCategory.ACCIDENT` | `"accident"` | Accidente de tráfico. |
| `IncidentCategory.FOG` | `"fog"` | Niebla que reduce la visibilidad, lo que probablemente reduce el flujo de tráfico y, posiblemente, aumenta el riesgo de un accidente. |
| `IncidentCategory.DANGEROUS_CONDITIONS` | `"dangerousConditions"` | Situación peligrosa en la carretera, como un objeto en la carretera. |
| `IncidentCategory.RAIN` | `"rain"` | Lluvia intensa que puede reducir la visibilidad, dificultar las condiciones de conducción y, posiblemente, aumentar el riesgo de un accidente. |
| `IncidentCategory.ICE` | `"ice"` | Situaciones de carreteras con hielo que pueden hacer que la conducción sea difícil o peligrosa. |
| `IncidentCategory.JAM` | `"jam"` | Atasco de tráfico que da lugar a que el tráfico se mueva más lentamente. |
| `IncidentCategory.LANE_CLOSED` | `"laneClosed"` | Está cerrado un carril de la carretera. |
| `IncidentCategory.ROAD_CLOSED` | `"roadClosed"` | Una carretera está cerrada. |
| `IncidentCategory.ROAD_WORKS` | `"roadWorks"` | Trabajos en la carretera o de construcción en esta área. |
| `IncidentCategory.WIND` | `"wind"` | Fuertes vientos que pueden dificultar la conducción de vehículos con un perfil lateral grande o un centro de gravedad alto. |
| `IncidentCategory.FLOODING` | `"flooding"` | Se han producido inundaciones en la carretera. |
| `IncidentCategory.DETOUR` | `"detour"` | Se dirige el tráfico por un desvío. |
| `IncidentCategory.CLUSTER` | `"cluster"` | Un grupo de incidentes de tráfico de distintas categorías. Al acercar el mapa, el grupo se dividirá en sus incidentes individuales. |
| `IncidentCategory.BROKEN_DOWN_VEHICLE` | `"brokenDownVehicle"` | Vehículo averiado en la carretera o junto a ella. |

En la tabla siguiente, se muestran todas las magnitudes de incidentes de tráfico que se pueden usar dentro de la opción `incidentMagnitudeFilter`.

| Enumeración de la magnitud | Valor de cadena | Descripción |
|--------------------|--------------|-------------|
| `IncidentMagnitude.UNKNOWN` | `"unknown"` | Incidente cuya magnitud aún no se ha clasificado. |
| `IncidentMagnitude.MINOR` | `"minor"` | Problema de tráfico menor que suele ser solo para información y que tiene un impacto mínimo en el flujo de tráfico. |
| `IncidentMagnitude.MODERATE` | `"moderate"` | Problema de tráfico moderado que tiene un cierto impacto en el flujo de tráfico. |
| `IncidentMagnitude.MAJOR` | `"major"` |  Problema importante de tráfico que tiene un cierto significativo en el flujo de tráfico. |

A continuación, se filtran los incidentes de tráfico, de modo que solo se muestran en el mapa los atascos de tráfico moderados y los incidentes con condiciones peligrosas.

::: zone pivot="programming-language-java-android"

``` java
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(new String[] { IncidentMagnitude.MODERATE }),
    incidentCategoryFilter(new String[] { IncidentCategory.DANGEROUS_CONDITIONS, IncidentCategory.JAM })              
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(*arrayOf(IncidentMagnitude.MODERATE)),
    incidentCategoryFilter(
        *arrayOf(
            IncidentCategory.DANGEROUS_CONDITIONS,
            IncidentCategory.JAM
        )
    )
)
```

::: zone-end

En la captura de pantalla siguiente, se muestra un mapa de atascos de tráfico moderados e incidentes con condiciones peligrosas.

![Mapa de atascos de tráfico moderados e incidentes con condiciones peligrosas.](media/how-to-show-traffic-android/android-traffic-incident-filters.jpg)

> [!NOTE]
> Algunos incidentes de tráfico pueden tener varias categorías asignadas. Si un incidente tiene alguna categoría que coincida con cualquier opción pasada a `incidentCategoryFilter`, se mostrará. La categoría principal del incidente puede ser diferente de las categorías especificadas en el filtro y, por tanto, mostrar un icono diferente.

## <a name="next-steps"></a>Pasos siguientes

Consulte las guías siguientes para obtener información sobre cómo agregar más datos al mapa:

> [!div class="nextstepaction"]
> [Adición de una capa de icono](how-to-add-tile-layer-android-map.md)

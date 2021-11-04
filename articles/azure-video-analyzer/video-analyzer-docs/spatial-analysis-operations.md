---
title: 'Operaciones de análisis espacial admitidas: Azure'
description: En este artículo de referencia se detallan las distintas propiedades de las operaciones de análisis espacial compatibles con Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 11062ebbe48e8c7e2451ff448770055073d7e792
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560156"
---
# <a name="supported-spatial-analysis-operations"></a>Operaciones de análisis espacial admitidas

El análisis espacial permite analizar vídeo de streaming en tiempo real para dispositivos de cámara. Para cada dispositivo de cámara que configure, las operaciones generarán un flujo de salida de mensajes JSON enviados a Azure Video Analyzer. 

Video Analyzer implementa las siguientes operaciones de análisis espacial: 

| Identificador de operación| Descripción|
|---------|---------|
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation | Emite un evento _personZoneEnterExitEvent_ cuando una persona entra o sale de la zona y proporciona información direccional con el lado numerado de la zona que se ha cruzado. Emite un evento _personZoneDwellTimeEvent_ cuando la persona sale de la zona y proporciona información direccional, así como el número de milisegundos que la persona pasó dentro de la zona. |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation | Hace un seguimiento de cuando una persona cruza una línea designada en el campo visual de la cámara.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation | Hace un seguimiento de cuando las personas infringen una regla de distancia.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation | Cuenta a las personas que se encuentran en una zona designada del campo visual de la cámara. Una sola cámara debe cubrir completamente la zona para que PersonCount registre un total preciso.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation | Operación genérica que se puede usar para ejecutar todos los escenarios mencionados anteriormente. Esta opción es más útil si quiere ejecutar varios escenarios en la misma cámara o usar los recursos del sistema (por ejemplo, la GPU) más eficazmente. |



## <a name="person-zone-crossing"></a>Cruce de zona de personas

**Identificador de operación**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation`

Vea un ejemplo de la [operación de cruce de zona de persona](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json) en nuestra muestra de GitHub.

#### <a name="parameters"></a>Parámetros:

| Nombre                      | Tipo    | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Lista de zonas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                     | string  | Nombre descriptivo de esta zona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | Cada par de valores representa la x e y de los vértices del polígono. El polígono representa las áreas en las que se realiza el seguimiento o el recuento de personas. Los valores flotantes representan la posición del vértice relativa a la esquina superior izquierda. Para calcular los valores x, y absolutos, debe multiplicar estos valores por el tamaño del fotograma. Los eventos flotantes del umbral se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona. El valor predeterminado es 48 cuando el tipo es zonecrossing, y 16 cuando la hora es DwellTime. Estos son los valores recomendados para lograr la máxima precisión. |
| `eventType`                 | string  | Para cognitiveservices.vision.spatialanalysis-personcrossingpolygon, debe ser `zonecrossing` o `zonedwelltime`.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `trigger`                   | string  | Tipo de desencadenador para enviar un evento. Valores admitidos: "event": se activa cuando alguna persona entra o sale de la zona.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `focus`                     | string  | Ubicación del punto en el rectángulo delimitador de la persona usado para calcular los eventos. El valor del foco puede ser footprint (la superficie de la persona), bottom_center (la parte inferior central del rectángulo delimitador de la persona) o center (el centro del cuadro delimitador de la persona). El valor predeterminado es footprint.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | FLOAT   | Los eventos se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona. El valor predeterminado es 16. Este es el valor recomendado para lograr la máxima precisión.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | true para habilitar la detección de personas que lleven máscaras faciales en la secuencia de vídeo; false para deshabilitarla. De manera predeterminada, esta opción está deshabilitada. La detección de máscaras faciales requiere que el parámetro de ancho de vídeo de entrada sea 1920 "INPUT_VIDEO_WIDTH": 1920. No se devolverá el atributo de máscaras faciales.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | Los parámetros DETECTOR_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | Los parámetros TRACKER_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

### <a name="orientation-parameter-settings"></a>Configuración de los parámetros de orientación

Puede configurar el cálculo de la orientación a través de la configuración del parámetro DETECTOR_NODE_CONFIG
```json
{
    "enable_orientation": true,
}

```
| Nombre                      | Tipo    | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_orientation`                     | bool    | Indica si desea calcular la orientación de las personas detectadas, o no. `enable_orientation` se establece de forma predeterminada en True.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
### <a name="speed-parameter-settings"></a>Configuración de los parámetros de velocidad

Puede configurar el cálculo de la velocidad a través de la configuración del parámetro TRACKER_NODE_CONFIG
```json
{
    "enable_speed": true,
}

```
| Nombre                      | Tipo    | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_speed`                     | bool    | Indica si desea calcular la velocidad de las personas detectadas, o no. `enable_speed` se establece de forma predeterminada en True. Se recomienda encarecidamente habilitar tanto la velocidad como la orientación para tener los mejores valores estimados.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

#### <a name="output"></a>Salida:
```json
{
  "body": {
    "timestamp": 147026846756730,
    "inferences": [
      {
        "type": "entity",
        "inferenceId": "8e8269c1a9584b3a8f16a3cd7a2cd45a",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.9511422
          },
          "box": {
            "l": 0.59845686,
            "t": 0.35958588,
            "w": 0.11951797,
            "h": 0.50172085
          }
        },
        "extensions": {
          "centerGroundPointY": "0.0",
          "footprintY": "inf",
          "centerGroundPointX": "0.0",
          "mappedImageOrientation": "inf",
          "groundOrientationAngle": "inf",
          "footprintX": "inf",
          "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
          "speed": "0.0"
        }
      },
      {
        "type": "entity",
        "inferenceId": "c54c9f92dd0d442b8d1840756715a5c7",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.92762595
          },
          "box": {
            "l": 0.8098704,
            "t": 0.47707137,
            "w": 0.18019487,
            "h": 0.48659682
          }
        },
        "extensions": {
          "footprintY": "inf",
          "groundOrientationAngle": "inf",
          "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
          "mappedImageOrientation": "inf",
          "speed": "0.0",
          "centerGroundPointX": "0.0",
          "centerGroundPointY": "0.0",
          "footprintX": "inf"
        }
      },
      {
        "type": "event",
        "inferenceId": "aad2778756a94afd9055fdbb3a370d62",
        "relatedInferences": [
          "8e8269c1a9584b3a8f16a3cd7a2cd45a"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
            "zone": "retailstore",
            "status": "Enter"
          }
        }
      },
      {
        "type": "event",
        "inferenceId": "e30103d3af28485688d7c77bbe10b5b5",
        "relatedInferences": [
          "c54c9f92dd0d442b8d1840756715a5c7"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
            "status": "Enter",
            "zone": "retailstore"
          }
        }
      }
    ]
  }
```

## <a name="person-line-crossing"></a>Cruce de línea de personas

**Identificador de operación**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation`

Vea un ejemplo de la [operación de cruce de línea de persona](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json) en nuestra muestra de GitHub.

#### <a name="parameters"></a>Parámetros:

| Nombre                      | Tipo    | Descripción                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lines`                     | list    | Lista de líneas.                                                                                                                                                                                                                                                                |
| `name`                      | string  | Nombre descriptivo de esta línea.                                                                                                                                                                                                                                                  |
| `line`                      | string  | Cada par de valores representa el punto inicial y final de la línea. Los valores flotantes representan la posición del vértice relativa a la esquina superior izquierda. Para calcular los valores x, y absolutos, debe multiplicar estos valores por el tamaño del fotograma.                            |
| `start`                      | value pair  | Coordenadas x, y para el punto inicial de la línea. Los valores flotantes representan la posición del vértice relativa a la esquina superior izquierda. Para calcular los valores x, y absolutos, debe multiplicar estos valores por el tamaño del fotograma.                            |
| `end`                      | value pair  | Coordenadas x, y para el punto final de la línea. Los valores flotantes representan la posición del vértice relativa a la esquina superior izquierda. Para calcular los valores x, y absolutos, debe multiplicar estos valores por el tamaño del fotograma.                            |
| `type`                     | string  | Debe ser `linecrossing`. |
| `trigger`                     | string  | Tipo de desencadenador para enviar un evento. Valores admitidos: "event": se activa cuando alguna persona cruza la línea.|
| `outputFrequency`           | int     | Velocidad a la que se generan los eventos. Cuando outputFrequency = X, se genera cada evento X, por ejemplo. outputFrequency = 2 significa que se genera cualquier otro evento. outputFrequency se aplica tanto a eventos como a intervalos.                                                       |
| `focus`                     | string  | Ubicación del punto en el rectángulo delimitador de la persona usado para calcular los eventos. El valor del foco puede ser footprint (la superficie de la persona), bottom_center (la parte inferior central del rectángulo delimitador de la persona) o center (el centro del cuadro delimitador de la persona). El valor predeterminado es footprint. |
| `threshold`                 | FLOAT   | Los eventos se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona. El valor predeterminado es 16. Este es el valor recomendado para lograr la máxima precisión.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | true para habilitar la detección de personas que lleven máscaras faciales en la secuencia de vídeo; false para deshabilitarla. De manera predeterminada, esta opción está deshabilitada. La detección de máscaras faciales requiere que el parámetro de ancho de vídeo de entrada sea 1920 "INPUT_VIDEO_WIDTH": 1920. No se devolverá el atributo de máscaras faciales.          |
| `detectorNodeConfiguration` | string  | Los parámetros DETECTOR_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                      |
| `trackerNodeConfiguration` | string  | Los parámetros TRACKER_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Salida:

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="person-distance"></a>Distancia de la persona

**Identificador de operación**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation`

Vea un ejemplo de la [operación de distancia de persona](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json) en nuestra muestra de GitHub.

#### <a name="parameters"></a>Parámetros:

| Nombre                      | Tipo    | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Lista de zonas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | string  | Nombre descriptivo de esta zona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | Cada par de valores representa la x e y de los vértices del polígono. El polígono representa las áreas en las que se realiza el seguimiento o el recuento de personas. Los valores flotantes representan la posición del vértice relativa a la esquina superior izquierda. Para calcular los valores x, y absolutos, debe multiplicar estos valores por el tamaño del fotograma. Los eventos flotantes del umbral se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona. El valor predeterminado es 48 cuando el tipo es zonecrossing, y 16 cuando la hora es DwellTime. Estos son los valores recomendados para lograr la máxima precisión. |
| `trigger`           | string     | Tipo de desencadenador para enviar un evento. Los valores admitidos son event para enviar eventos cuando el recuento cambia o interval para enviar eventos de manera periódica, independientemente de si el recuento ha cambiado o no.                                                                                                                                                                                                                                                                                                                                                     |
| `focus`                     | string  | Ubicación del punto en el rectángulo delimitador de la persona usado para calcular los eventos. El valor del foco puede ser footprint (la superficie de la persona), bottom_center (la parte inferior central del rectángulo delimitador de la persona) o center (el centro del cuadro delimitador de la persona). El valor predeterminado es footprint.                                                                                                                                                                                                                                                                                               |
| `threshold`                | FLOAT   | Los eventos se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `outputFrequency`           | int     | Velocidad a la que se generan los eventos. Cuando outputFrequency = X, se genera cada evento X, por ejemplo. outputFrequency = 2 significa que se genera cualquier otro evento. outputFrequency se aplica tanto a eventos como a intervalos.                                                                                                                                                                                                                                                                                                                                                     |
| `minimumDistanceThreshold`  | FLOAT   | Distancia en pies que desencadenará un evento "TooClose" cuando las personas estén más cerca de esa distancia entre sí.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `maximumDistanceThreshold`  | FLOAT   | Distancia en pies que desencadenará un evento "TooFar" cuando las personas estén más lejos de esa distancia entre sí.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `aggregationMethod`         | string  | Método para agregar el resultado de persondistance. AggregationMethod es aplicable tanto al modo como al promedio.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `enableFaceMaskClassifier`  | boolean | true para habilitar la detección de personas que lleven máscaras faciales en la secuencia de vídeo; false para deshabilitarla. De manera predeterminada, esta opción está deshabilitada. La detección de máscaras faciales requiere que el parámetro de ancho de vídeo de entrada sea 1920 "INPUT_VIDEO_WIDTH": 1920. No se devolverá el atributo de máscaras faciales.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | Los parámetros DETECTOR_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | Los parámetros TRACKER_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Salida:

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

## <a name="person-count"></a>Recuento de personas

**Identificador de operación**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation`

Vea un ejemplo de la [operación de recuento de personas](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json) en nuestra muestra de GitHub.

#### <a name="parameters"></a>Parámetros:

| Nombre                      | Tipo    | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Lista de zonas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | string  | Nombre descriptivo de esta zona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | Cada par de valores representa la x e y de los vértices del polígono. El polígono representa las áreas en las que se realiza el seguimiento o el recuento de personas. Los valores flotantes representan la posición del vértice relativa a la esquina superior izquierda. Para calcular los valores x, y absolutos, debe multiplicar estos valores por el tamaño del fotograma. Los eventos flotantes del umbral se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona. El valor predeterminado es 48 cuando el tipo es zonecrossing, y 16 cuando la hora es DwellTime. Estos son los valores recomendados para lograr la máxima precisión. |
| `outputFrequency`           | int     | Velocidad a la que se generan los eventos. Cuando outputFrequency = X, se genera cada evento X, por ejemplo. outputFrequency = 2 significa que se genera cualquier otro evento. outputFrequency se aplica tanto a eventos como a intervalos.                                                                                                                                                                                                                                                                                                                                                     |
| `trigger`                   | string  | Tipo de desencadenador para enviar un evento. Los valores admitidos son event para enviar eventos cuando el recuento cambia o interval para enviar eventos de manera periódica, independientemente de si el recuento ha cambiado o no.                                                                                                                                                                                                                                                                                                                                                           |
| `focus`                     | string  | Ubicación del punto en el rectángulo delimitador de la persona usado para calcular los eventos. El valor del foco puede ser footprint (la superficie de la persona), bottom_center (la parte inferior central del rectángulo delimitador de la persona) o center (el centro del cuadro delimitador de la persona). El valor predeterminado es footprint.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | FLOAT   | Los eventos se generan cuando la persona tiene un tamaño mayor que este número de píxeles dentro de la zona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `enableFaceMaskClassifier`  | boolean | true para habilitar la detección de personas que lleven máscaras faciales en la secuencia de vídeo; false para deshabilitarla. De manera predeterminada, esta opción está deshabilitada. La detección de máscaras faciales requiere que el parámetro de ancho de vídeo de entrada sea 1920 "INPUT_VIDEO_WIDTH": 1920. No se devolverá el atributo de máscaras faciales.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | Los parámetros DETECTOR_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | Los parámetros TRACKER_NODE_CONFIG para todas las operaciones de análisis espacial.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Salida:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

## <a name="custom-operation"></a>Operación personalizada

**Identificador de operación**: `Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation`

Vea un ejemplo de la [operación de personalizada](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json) en nuestra muestra de GitHub.

#### <a name="parameters"></a>Parámetros:

| Nombre                   | Tipo   | Descripción                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | string | Representación JSON de la operación. |

#### <a name="output"></a>Salida:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

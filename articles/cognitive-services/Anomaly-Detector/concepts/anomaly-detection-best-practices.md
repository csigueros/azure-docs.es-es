---
title: Procedimientos recomendados para usar la API univariante de Anomaly Detector
titleSuffix: Azure Cognitive Services
description: Conozca los procedimientos recomendados al detectar anomalías con Anomaly Detector API.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: mbullwin
ms.openlocfilehash: de1662fd39030082dfbfef4d38d1803b3d6f82e5
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297305"
---
# <a name="best-practices-for-using-the-anomaly-detector-univariate-api"></a>Procedimientos recomendados para usar la API univariante de Anomaly Detector

Anomaly Detector API es un servicio de detección de anomalías sin estado. La precisión y el rendimiento de sus resultados pueden verse afectados por:

* Cómo se preparan los datos de serie temporal
* Los parámetros de Anomaly Detector API que se usaron
* El número de puntos de datos de la solicitud de API 

Use este artículo para conocer los procedimientos recomendados para usar la API para obtener los mejores resultados para sus datos. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Cuándo usar la detección de anomalías por lotes (completa) o del punto más reciente (última)

El punto de conexión de detección por lotes de Anomaly Detector API le permite detectar anomalías en todos los datos de serie temporal. En este modo de detección, se crea un único modelo estadístico y se aplica a cada punto del conjunto de datos. Si la serie temporal tiene las características siguientes, se recomienda usar la detección por lotes para obtener una vista previa de los datos en una llamada API.

* Serie temporal estacional, con anomalías ocasionales
* Serie temporal de tendencia plana, con subidas y bajadas ocasionales 

No se recomienda usar la detección de anomalías por lotes para la supervisión de datos en tiempo real, ni usarla en datos de serie temporal que no presentan las características anteriores. 

* La detección por lotes crea y aplica un único modelo, la detección de cada punto se realiza en el contexto de la serie completa. Si las tendencias de los datos de serie temporal suben y bajan sin estacionalidad, el modelo puede omitir algunos puntos de cambio (subidas y bajadas en los datos). Igualmente, algunos puntos de cambio que son menos importantes que los más recientes del conjunto de datos no se considerarán como lo suficientemente importantes como para incorporarlos al modelo.

* La detección por lotes es más lenta que la detección del estado de anomalía del punto más reciente cuando se realiza la supervisión de datos en tiempo real, debido al número de puntos que se analizan.

Para la supervisión de datos en tiempo real, se recomienda la detección del estado de anomalía solo del último punto de datos. Mediante la aplicación continua de la detección del último punto, la supervisión de los datos de transmisión se puede realizar de forma más eficaz y precisa.

En el ejemplo siguiente se describe el efecto que pueden tener estos modos de detección sobre el rendimiento. La primera imagen muestra el resultado de la detección de forma continua del estado de anomalía del último punto a lo largo de 28 puntos de datos vistos con anterioridad. Los puntos rojos son anomalías.

![Imagen que muestra la detección de anomalías con el último punto](../media/last.png)

Este es el mismo conjunto de datos pero con la detección de anomalías por lotes. El modelo creado para la operación ha omitido varias anomalías, marcadas con rectángulos.

![Imagen que muestra la detección de anomalías con el método por lotes](../media/entire.png)

## <a name="data-preparation"></a>Preparación de los datos

Anomaly Detector API acepta datos de serie temporal con formato de un objeto de solicitud JSON. Una serie temporal puede ser cualquier dato numérico registrado con el tiempo en orden secuencial. Puede enviar ventanas de los datos de serie temporal al punto de conexión de Anomaly Detector API para mejorar el rendimiento de las API. El número mínimo de puntos de datos que puede enviar es 12, y el máximo es 8640 puntos. [Granularidad](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) se define como la velocidad a la que se muestrean los datos. 

Los puntos de datos enviados a la API de Anomaly Detector deben tener una marca de tiempo válida en hora universal coordinada (UTC) y un valor numérico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Si los datos se muestrean en un intervalo de tiempo no estándar, puede especificarlo agregando el atributo `customInterval` en la solicitud. Por ejemplo, si la serie se muestrea cada 5 minutos, puede agregar lo siguiente a su solicitud JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Puntos de datos que faltan

Que falten puntos de datos es algo común en los conjuntos de datos de serie temporal distribuidos uniformemente, especialmente aquellos con una granularidad fina (un intervalo de muestreo pequeño. Por ejemplo, los datos muestreados cada pocos minutos). La falta de menos del 10 % del número esperado de puntos en los datos no debería afectar negativamente a los resultados de detección. Considere la posibilidad de rellenar los huecos en los datos según sus características, por ejemplo, sustituir puntos de datos de un período anterior, interpolación lineal o una media acumulada.

### <a name="aggregate-distributed-data"></a>Datos distribuidos agregados

Anomaly Detector API funciona mejor en una serie temporal distribuida uniformemente. Si los datos se distribuyen aleatoriamente, debe agregarlos como una unidad de tiempo, por ejemplo, por minuto, hora o día.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detección de anomalías en datos con patrones estacionales

Si sabe que los datos de serie temporal tienen un patrón estacional (uno que se produce a intervalos regulares), puede mejorar la precisión y el tiempo de respuesta de la API. 

Especificar un valor de `period` al construir la solicitud JSON puede reducir la latencia en la detección de anomalías en hasta un 50 %. El valor de `period` es un entero que especifica aproximadamente cuántos puntos de datos toma la serie temporal para repetir un patrón. Por ejemplo, una serie temporal con un punto de datos al día tendría un valor de `period` de `7`, y una serie temporal con un punto por hora (con el mismo patrón semanal) tendría un valor de `period` de `7*24`. Si no está seguro de los patrones de sus datos, no tiene que especificar este parámetro.

Para obtener los mejores resultados, proporcione cuatro veces el valor de `period` del punto de datos, más uno adicional. Por ejemplo, los datos por hora con un patrón semanal como se ha descrito anteriormente deben proporcionar 673 puntos de datos en el cuerpo de la solicitud (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Datos de muestreo para la supervisión en tiempo real

Si se muestrean datos de transmisión en un intervalo corto (por ejemplo, segundos o minutos), al enviar el número recomendado de puntos de datos se puede superar el número máximo permitido de Anomaly Detector API (8640 puntos de datos). Si los datos muestran un patrón estacional estable, considere la posibilidad de enviar una muestra de los datos de serie temporal en un intervalo de tiempo mayor, como horas. El hecho de muestrear los datos de esta manera puede mejorar notablemente también el tiempo de respuesta de la API. 

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Anomaly Detector API?](../overview.md)
* [Inicio rápido: Uso de la biblioteca cliente de Anomaly Detector](../quickstarts/client-libraries.md)

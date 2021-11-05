---
title: Introducción a las funciones de ventana de Azure Stream Analytics
description: En este artículo se describen cuatro funciones de ventana (saltos de tamaño constante, salto, deslizante y sesión) que se usan en los trabajos de Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 83c574ca578247389f8bc8c53c07847e1f01fb8b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003645"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introducción a las funciones de ventana de Stream Analytics

En los escenarios de streaming en tiempo real, realizar operaciones en los datos contenidos en ventanas temporales es un patrón común. Stream Analytics ofrece compatibilidad nativa para las funciones de ventana, lo que permite a los desarrolladores crear trabajos de procesamiento de flujo complejos con un mínimo esfuerzo.

Hay cinco tipos de ventanas temporales para elegir: ventanas de [**saltos de tamaño constante**](/stream-analytics-query/tumbling-window-azure-stream-analytics), de [**salto**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**deslizantes**](/stream-analytics-query/sliding-window-azure-stream-analytics), de [**sesión**](/stream-analytics-query/session-window-azure-stream-analytics) y de [**instantánea**](/stream-analytics-query/snapshot-window-azure-stream-analytics).  Utilice las funciones de ventana en la cláusula [**GROUP BY**](/stream-analytics-query/group-by-azure-stream-analytics) de la sintaxis de consulta en los trabajos de Stream Analytics. También puede agregar eventos a través de varias ventanas mediante la función [**Windows()**](/stream-analytics-query/windows-azure-stream-analytics).

Todas las operaciones de [ventana](/stream-analytics-query/windowing-azure-stream-analytics) generan resultados al **final** de la ventana. Tenga en cuenta que al iniciar un trabajo de Stream Analytics puede especificar la *Hora de inicio de salida del trabajo* para que el sistema capture automáticamente los eventos anteriores de los flujos entrantes para generar la primera ventana a la hora especificada; por ejemplo, al empezar con la opción *Ahora*, comienza a emitir datos inmediatamente. La salida de la ventana será un solo evento basado en la función agregada que se usa. El evento de salida tendrá la marca de tiempo del final de la ventana y todas las funciones de ventana están definidas con una longitud fija. 

![Conceptos de las funciones de ventana de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Ventana de saltos de tamaño constante

Las funciones de [**ventana de saltos de tamaño constante**](/stream-analytics-query/tumbling-window-azure-stream-analytics) se usan para segmentar una transmisión de datos en segmentos de tiempo distintos y realizar una función con ellos, como en el ejemplo siguiente. Los diferenciadores clave de una ventana de saltos de tamaño constante son que se repiten, no se superponen y un evento no puede pertenecer a más de una ventana de saltos de tamaño constante.

![Ventana de saltos de tamaño constante de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

Con los siguientes datos de entrada (que se ilustran anteriormente):

|Marca|CreatedAt|TimeZone|
|-|-|-|
|1|2021-10-26T10:15:01|PST|
|5|2021-10-26T10:15:03|PST|
|4|2021-10-26T10:15:06|PST|
|...|...|...|

La consulta siguiente:

```SQL
SELECT System.Timestamp() as WindowEndTime, TimeZone, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TimeZone, TumblingWindow(second,10)
```

Devolverá:

|WindowEndTime|TimeZone|Count|
|-|-|-|
|2021-10-26T10:15:10|PST|5|
|2021-10-26T10:15:20|PST|2|
|2021-10-26T10:15:30|PST|4|


## <a name="hopping-window"></a>Ventana de salto

Las funciones de [**ventana de salto**](/stream-analytics-query/hopping-window-azure-stream-analytics) saltan hacia adelante en el tiempo un período fijo. Puede imaginarlas fácilmente como ventanas de saltos de tamaño constante que pueden superponerse y emitirse con más frecuencia que el tamaño de ventana. Los eventos pueden pertenecer a más de un conjunto de resultados de ventana de salto. Para hacer que una ventana de salto sea igual que una ventana de saltos de tamaño constante, especifique el tamaño de salto de forma que coincida con el tamaño de la ventana. 

![Ventana de salto de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

Con los siguientes datos de entrada (que se ilustran anteriormente):

|Marca|CreatedAt|Tema|
|-|-|-|
|1|2021-10-26T10:15:01|Streaming|
|5|2021-10-26T10:15:03|Streaming|
|4|2021-10-26T10:15:06|Streaming|
|...|...|...|

La consulta siguiente:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, HoppingWindow(second,10,5)
```

Devolverá:

|WindowEndTime|Tema|Count|
|-|-|-|
|2021-10-26T10:15:10|Streaming|5|
|2021-10-26T10:15:15|Streaming|3|
|2021-10-26T10:15:20|Streaming|2|
|2021-10-26T10:15:25|Streaming|4|
|2021-10-26T10:15:30|Streaming|4|


## <a name="sliding-window"></a>Ventana deslizante

Las [**ventanas deslizantes**](/stream-analytics-query/sliding-window-azure-stream-analytics), a diferencia de las ventanas de salto o de salto de tamaño constante, solo generan eventos para puntos en el tiempo cuando el contenido de la ventana cambia realmente. En otras palabras, cuando un evento entra o sale de la ventana. Por tanto, cada ventana tiene al menos un evento. De forma similar a lo que sucede en las ventanas de salto, los eventos pueden pertenecer a más de una ventana deslizante.

![Ventana deslizante de 10 segundos de Stream Analytics](media/stream-analytics-window-functions/sliding-window-updated.png)

Con los siguientes datos de entrada (que se ilustran anteriormente):

|Marca|CreatedAt|Tema|
|-|-|-|
|1|2021-10-26T10:15:10|Streaming|
|5|2021-10-26T10:15:12|Streaming|
|9|2021-10-26T10:15:15|Streaming|
|7|2021-10-26T10:15:15|Streaming|
|8|2021-10-26T10:15:27|Streaming|

La consulta siguiente:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SlidingWindow(second,10)
HAVING COUNT(*) >=3
```

Devolverá:

|WindowEndTime|Tema|Count|
|-|-|-|
|2021-10-26T10:15:15|Streaming|4|
|2021-10-26T10:15:20|Streaming|3|

## <a name="session-window"></a>Ventana de sesión

Las funciones de [**ventana de sesión**](/stream-analytics-query/session-window-azure-stream-analytics) agrupan eventos que llegan a la misma hora, filtrando los periodos en los que no hay ningún dato. Tiene tres parámetros principales: tiempo de espera, duración máxima y clave de partición (opcional).

![Ventana de sesión de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Una ventana de sesión se inicia cuando se produce el primer evento. Si se produce otro evento en el tiempo de espera especificado desde el último evento ingerido, la ventana se amplía para incluir el nuevo evento. En caso contrario, si no se produce ningún evento en el tiempo de espera, se cierra la ventana en el tiempo de espera.

Si se siguen produciendo eventos en el tiempo de espera especificado, la ventana de sesión se sigue ampliando hasta que se alcanza la duración máxima. Los intervalos de comprobación de la duración máxima se establecen para que tengan el mismo tamaño que la duración máxima especificada. Por ejemplo, si la duración máxima es 10, las comprobaciones, si la ventana supera la duración máxima, se producirán en t = 0, 10, 20, 30, etc.

Cuando se proporciona una clave de partición, los eventos se agrupan por clave, y la ventana de sesión se aplica independientemente a cada grupo. Esta creación de particiones es útil para los casos donde son necesarias distintas ventanas de sesión para distintos usuarios o dispositivos.

Con los siguientes datos de entrada (que se ilustran anteriormente):

|Marca|CreatedAt|Tema|
|-|-|-|
|1|2021-10-26T10:15:01|Streaming|
|2|2021-10-26T10:15:04|Streaming|
|3|2021-10-26T10:15:13|Streaming|
|...|...|...|

La consulta siguiente:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SessionWindow(second,5,10)
```

Devolverá:

|WindowEndTime|Tema|Count|
|-|-|-|
|2021-10-26T10:15:09|Streaming|2|
|2021-10-26T10:15:24|Streaming|4|
|2021-10-26T10:15:31|Streaming|2|
|2021-10-26T10:15:39|Streaming|1|

## <a name="snapshot-window"></a>Ventana de instantánea

Las [**ventanas de instantánea**](/stream-analytics-query/snapshot-window-azure-stream-analytics) agrupan los eventos que tienen la misma marca de tiempo. A diferencia de otros tipos de ventanas, que requieren una función de ventana específica (como [SessionWindow()](/stream-analytics-query/session-window-azure-stream-analytics), puede aplicar una ventana de instantánea si agrega System.Timestamp() a la cláusula GROUP BY.

![Ventana de instantánea de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-snapshot-intro.png)

Con los siguientes datos de entrada (que se ilustran anteriormente):

|Marca|CreatedAt|Tema|
|-|-|-|
|1|2021-10-26T10:15:04|Streaming|
|2|2021-10-26T10:15:04|Streaming|
|3|2021-10-26T10:15:04|Streaming|
|...|...|...|

La consulta siguiente:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, System.Timestamp()
```

Devolverá:

|WindowEndTime|Tema|Count|
|-|-|-|
|2021-10-26T10:15:04|Streaming|4|
|2021-10-26T10:15:10|Streaming|2|
|2021-10-26T10:15:13|Streaming|1|
|2021-10-26T10:15:22|Streaming|2|

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referencia de API de REST de administración de Azure Stream Analytics](/rest/api/streamanalytics/)

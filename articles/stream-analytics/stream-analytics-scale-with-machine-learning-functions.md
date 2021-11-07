---
title: Escalado de funciones de Machine Learning Studio (clásico) en Azure Stream Analytics
description: En este artículo se describe cómo escalar los trabajos de Stream Analytics que usan funciones de Machine Learning Studio (clásico) mediante la configuración de creación de particiones y unidades de streaming.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 8d7c498f0052a3e0da024e8b4902579ab8a98fd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083296"
---
# <a name="scale-your-stream-analytics-job-with-machine-learning-studio-classic-functions"></a>Escalado del trabajo de Stream Analytics con funciones de Machine Learning Studio (clásico)

> [!TIP]
> Es muy recomendable usar las [UDF de Azure Machine Learning](machine-learning-udf.md) en lugar de las de Machine Learning Studio (clásico) para mejorar el rendimiento y la confiabilidad.

En este artículo se describe cómo escalar de manera eficaz trabajos de Azure Stream Analytics que usan funciones de Machine Learning Studio (clásico). Para más información sobre cómo escalar trabajos de Stream Analytics en general, consulte el artículo [Escalado de trabajos de Azure Stream Analytics para incrementar el rendimiento de procesamiento de flujo de datos](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>¿Qué es una función de Studio (clásico) en Stream Analytics?

Una función de Machine Learning Studio (clásico) en Stream Analytics puede utilizarse como una llamada de función normal en el lenguaje de consulta de Stream Analytics. No obstante, en segundo plano, estas llamadas de función son en realidad solicitudes de servicio web de Studio (clásico).

Puede mejorar el rendimiento de las solicitudes de servicio web de Studio (clásico) al agrupar en "lotes" varias filas en la misma llamada API de servicio web. Esta agrupación se denomina minilote. Para más información, consulte [Servicios web de Machine Learning Studio (clásico)](../machine-learning/classic/consume-web-services.md). Compatibilidad de Studio (clásico) en Stream Analytics.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Configuración de un trabajo de Stream Analytics con funciones de Studio (clásico)

Hay dos parámetros para configurar la función de Studio (clásico) usada por el trabajo de Stream Analytics:

* El tamaño de lote de las llamadas de función de Studio (clásico).
* El número de unidades de streaming (SU) aprovisionadas para el trabajo de Stream Analytics.

Para determinar los valores adecuados para las SU, decida si desea optimizar la latencia de cada trabajo de Stream Analytics o el rendimiento de cada SU. Siempre se pueden agregar SU a un trabajo para aumentar el rendimiento de una consulta de Stream Analytics bien particionada. Las SU adicionales aumentarán el costo de ejecución del trabajo.

Determine la *tolerancia* de la latencia para el trabajo de Stream Analytics. Aumentar el tamaño del lote aumentará la latencia de las solicitudes de Studio (clásico) y la latencia del trabajo de Stream Analytics.

Aumentar el tamaño del lote permite que el trabajo de Stream Analytics procese **más eventos** con el **mismo número** de solicitudes de servicio web de Studio (clásico). El aumento de la latencia del servicio web de Studio (clásico) suele ser infralineal al aumento del tamaño del lote. 

Es importante tener en cuenta el tamaño de lote más rentable para un servicio web de Studio (clásico) en cualquier situación dada. El tamaño del lote predeterminado para las solicitudes de servicio web es de 1000. Puede cambiar este tamaño predeterminado con la [API REST de Stream Analytics](/previous-versions/azure/mt653706(v=azure.100) "API de REST de Stream Analytics") o el [cliente de PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Una vez que se haya decidido por un tamaño de lote, puede establecer el número de unidades de streaming (SU) según el número de eventos que la función tenga que procesar por segundo. Para más información sobre el ajuste de las unidades de streaming, consulte [Escalación de trabajos de Stream Analytics](stream-analytics-scale-jobs.md).

Cada 6 SU, se obtienen 20 conexiones simultáneas al servicio web de Studio (clásico). Sin embargo, 1 trabajo de SU y 3 trabajos de SU obtienen 20 conexiones simultáneas.  

Si la aplicación genera 200 000 eventos por segundo y el tamaño del lote es de 1000, la latencia del servicio web resultante es de 200 ms. Esta tasa significa que cada conexión puede hacer cinco solicitudes al servicio web de Studio (clásico) en un segundo. Con 20 conexiones, el trabajo de Stream Analytics puede procesar 20 000 eventos en 200 ms y 100 000 eventos en un segundo.

Para procesar 200 000 eventos por segundo, el trabajo de Stream Analytics necesita 40 conexiones simultáneas, lo que viene a ser 12 SU. En el diagrama siguiente se muestran las solicitudes desde el trabajo de Stream Analytics hasta el punto de conexión del servicio web de Studio (clásico): cada 6 SU, se obtienen 20 conexiones simultáneas al servicio web de Studio (clásico) como máximo.

![Escalado de Stream Analytics con funciones de Studio (clásico): dos ejemplos de trabajo](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Escalado de Stream Analytics con funciones de Studio (clásico): dos ejemplos de trabajo")

En general, si ***B** _ es el tamaño de lote y _*_L_*_ es la latencia del servicio web con el tamaño de lote B en milisegundos, el rendimiento de un trabajo de Stream Analytics con _ *_N_* SU es:

![Fórmula de escalado de Stream Analytics con funciones de Studio (clásico)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula de escalado de Stream Analytics con funciones de Studio (clásico)")

También puede configurar las "llamadas simultáneas máximas" en el servicio web de Studio (clásico). Se recomienda establecer este parámetro en el valor máximo (actualmente 200).

Para obtener más información sobre esta configuración, consulte el [artículo de Escalado para servicio web de Machine Learning Studio (clásico)](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Ejemplo: Análisis de opiniones
En el ejemplo siguiente se incluye un trabajo de Stream Analytics con la función de análisis de opiniones de Studio (clásico), como se describe en el [tutorial de integración de Stream Analytics y Machine Learning Studio (clásico)](stream-analytics-machine-learning-integration-tutorial.md).

La consulta es una consulta sencilla completamente particionada seguida de la función **sentiment**, tal como se muestra en el ejemplo siguiente:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos a examinar la configuración necesaria para crear un trabajo de Stream Analytics que se encarga del análisis de opiniones de tweets con una frecuencia de 10 000 tweets por segundo.

Usando 1 SU, ¿podría este trabajo de Stream Analytics administrar el tráfico? El trabajo debería poder mantenerse al nivel de la entrada con el tamaño de lote predeterminado de 1000. La latencia predeterminada del servicio web de análisis de opiniones de Studio (clásico) (con un tamaño de lote predeterminado de 1000) genera una latencia de no más de un segundo.

La latencia **general** o total del trabajo de Stream Analytics sería normalmente de unos segundos. Examine con más detalle este trabajo de Stream Analytics, *especialmente* las llamadas de función de Studio (clásico). Con un tamaño de lote de 1000, un rendimiento de 10 000 eventos supondrá unas 10 solicitudes al servicio web. Incluso con 1 SU, hay suficientes conexiones simultáneas para dar cabida a este tráfico de entrada.

Si la tasa de eventos de entrada aumenta en 100 x, el trabajo de Stream Analytics necesita procesar 1 000 000 de tweets por segundo. Hay dos opciones para lograr una escala mayor:

1. Aumente el tamaño de lote.
2. Particione el flujo de entrada para procesar los eventos en paralelo.

Con la primera opción, la **latencia** del trabajo aumenta.

Con la segunda opción, será necesario aprovisionar más SU para tener más solicitudes simultáneas de servicio web de Studio (clásico). Este mayor número de SU aumenta el **costo** del trabajo.

Echemos un vistazo a la escala usando las siguientes medidas de latencia para cada tamaño de lote:

| Latencia | Tamaño de lote |
| --- | --- |
| 200 ms | Lotes de 1000 eventos o menos |
| 250 ms | Lotes de 5000 eventos |
| 300 ms | Lotes de 10 000 eventos |
| 500 ms | Lotes de 25 000 eventos |

1. Uso de la primera opción (**no** aprovisionar más SU). Se podría aumentar el tamaño de lote a **25 000**. Aumentar el tamaño de lote de este modo permitirá que el trabajo procese 1 000 000 eventos con 20 conexiones simultáneas al servicio web de Studio (clásico) (con una latencia de 500 ms por llamada). De modo que, la latencia adicional del trabajo de Stream Analytics debido a las solicitudes de la función de opinión contra las solicitudes del servicio web de Studio (clásico) aumentaría de **200 ms** a **500 ms**. Sin embargo, el tamaño de lote **no** se puede aumentar de manera indefinida, dado que el servicio web de Studio (clásico) necesita que el tamaño de la carga de una solicitud sea de 4 MB o menos, y las solicitudes de servicio web tienen un tiempo de espera de 100 segundos de funcionamiento.
1. Con la segunda opción, el tamaño de lote se deja en 1000; con una latencia del servicio web de 200 ms, con cada 20 conexiones simultáneas al servicio web se podrían procesar 1000 * 20 * 5 eventos = 100 000 por segundo. Por tanto, para procesar 1 000 000 eventos por segundo, el trabajo necesitaría 60 SU. En comparación con la primera opción, el trabajo de Stream Analytics haría más solicitudes de lote de servicio web, lo que a su vez generaría un mayor costo.

A continuación se muestra una tabla del rendimiento del trabajo de Stream Analytics para diferentes SU y tamaños de lote (en número de eventos por segundo).

| Tamaño del lote (latencia de Aprendizaje automático) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 unidad de búsqueda** |2,500 |5\.000 |20.000 |30,000 |50.000 |
| **3 unidades de búsqueda** |2,500 |5\.000 |20.000 |30,000 |50.000 |
| **6 unidades de búsqueda** |2,500 |5\.000 |20.000 |30,000 |50.000 |
| **12 unidades de búsqueda** |5\.000 |10 000 |40.000 |60 000 |100 000 |
| **18 unidades de búsqueda** |7 500 |15,000 |60 000 |90 000 |150 000 |
| **24 unidades de búsqueda** |10 000 |20.000 |80 000 |120 000 |200 000 |
| **…** |… |… |… |… |… |
| **60 unidades de búsqueda** |25 000 |50.000 |200 000 |300 000 |500.000 |

En este momento, ya debe tener un buen conocimiento de las funciones de Studio (clásico) en Stream Analytics. Probablemente también sepa que los trabajos de Stream Analytics "extraen" datos de los orígenes de datos y que cada "extracción" devuelve un lote de eventos que procesa el trabajo de Stream Analytics. ¿Cómo afecta este modelo de extracción a las solicitudes del servicio web de Studio (clásico)?

Normalmente, el tamaño de lote que se establece para las funciones de Studio (clásico) no será divisible exactamente por el número de eventos devueltos por cada "extracción" del trabajo de Stream Analytics. Cuando suceda esto, se llamará al servicio web de Studio (clásico) con lotes "parciales". El uso de lotes parciales evita incurrir en una sobrecarga de latencia adicional del trabajo en los eventos de combinación de una extracción a otra.

## <a name="new-function-related-monitoring-metrics"></a>Nuevas métricas de supervisión relacionadas con la función
En el área de supervisión de un trabajo de Stream Analytics, se han agregado tres métricas adicionales relacionadas con las funciones. Y son: **SOLICITUDES DE FUNCIONES**, **EVENTOS DE FUNCIONES** y **SOLICITUDES DE FUNCIONES CON ERRORES**, como se muestra en el siguiente gráfico.

![Métricas de escalado de Stream Analytics con funciones de Studio (clásico)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Métricas de escalado de Stream Analytics con funciones de Studio (clásico)")

Se definen de la manera siguiente:

**SOLICITUDES DE FUNCIONES**: número de solicitudes de función.

**EVENTOS DE FUNCIONES**: número de eventos en las solicitudes de función.

**SOLICITUDES DE FUNCIONES CON ERRORES**: número de solicitudes de función con errores.

## <a name="key-takeaways"></a>Puntos clave

Para escalar un trabajo de Stream Analytics con funciones de Studio (clásico), tenga en cuenta los siguientes factores:

1. La tasa de eventos de entrada.
2. La latencia permitida para el trabajo de Stream Analytics en ejecución (y, por tanto, el tamaño de lote de las solicitudes de servicio web de Studio [clásico]).
3. Las SU de Stream Analytics aprovisionadas y el número de solicitudes de servicio web de Studio (clásico) (los costos relacionados con funciones adicionales).

Como ejemplo se ha utilizado una consulta de Stream Analytics totalmente particionada. Si se necesita una consulta más compleja, la [Página de preguntas y respuestas de Microsoft sobre Azure Stream Analytics](/answers/topics/azure-stream-analytics.html) es un excelente recurso para obtener ayuda adicional del equipo de Stream Analytics.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Stream Analytics, vea:

* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referencia de API de REST de administración de Azure Stream Analytics](/rest/api/streamanalytics/)

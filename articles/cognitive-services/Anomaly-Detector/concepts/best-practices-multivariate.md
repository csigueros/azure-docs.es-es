---
title: Procedimientos recomendados para usar las API multivariantes de Anomaly Detector
titleSuffix: Azure Cognitive Services
description: Procedimientos recomendados para usar las API multivariantes de Anomaly Detector para aplicar la detección de anomalías a los datos de series temporales.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomaly detection, machine learning, algorithms
ms.openlocfilehash: 4114771276f4fec6dfef0e953ef9f52e165db510
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297339"
---
# <a name="best-practices-for-using-the-anomaly-detector-multivariate-api"></a>Procedimientos recomendados para usar las API multivariantes de Anomaly Detector

En este artículo se proporcionan instrucciones sobre los procedimientos recomendados que deben seguirse al usar las API multivariantes de Anomaly Detector (MVAD). En este tutorial, hará lo siguiente:

> [!div class="checklist"]
> * **Uso de API**: aprenda a usar MVAD sin errores.
> * **Ingeniería de datos**: aprenda a preparar mejor los datos para que MVAD se ejecute con una mayor precisión.
> * **Problemas comunes**: obtenga información sobre cómo evitar problemas comunes que encuentran los clientes.
> * **Preguntas más frecuentes**: obtenga respuestas a las preguntas más frecuentes.

## <a name="api-usage"></a>Uso de la API

Siga las instrucciones de esta sección para evitar errores al usar MVAD. Si sigue teniendo errores, consulte la [lista completa de códigos de error](./troubleshoot.md) para obtener explicaciones y conocer las medidas que debe tomar.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]


## <a name="data-engineering"></a>Ingeniería de datos

Ahora puede ejecutar el código con las API de MVAD sin ningún error. ¿Qué se puede hacer para mejorar la precisión del modelo?

### <a name="data-quality"></a>Calidad de los datos

* A medida que el modelo aprende los patrones normales a partir de los datos históricos, los datos de entrenamiento deben representar el estado **normal general** del sistema. Si hay una gran cantidad de anomalías en los datos de entrenamiento, es difícil que el modelo aprenda estos tipos de patrones. Un umbral empírico de tasa anómala es de **1 %** y por debajo para una buena precisión.
* En general, la **proporción de valores que faltan de los datos de entrenamiento debe ser inferior a un 20 %.** El hecho de que falten demasiados datos puede generar valores rellenados de forma automática (normalmente valores lineales o constantes) que se aprenden como patrones normales. Esto puede dar lugar a que se detecten puntos de datos reales (que no faltan) como anomalías.
    Sin embargo, hay casos en los que una proporción alta es aceptable. Por ejemplo, si tiene dos variables (series temporales) en un grupo que utilizan el modo `Outer` para alinear las marcas de tiempo. Una de ellas tiene una granularidad de un minuto y, la otra, una granularidad por hora. La variable por hora tiene por naturaleza al menos 59 / 60 = 98,33 % de puntos de datos que faltan. En estos casos, es correcto rellenar la variable por hora con el único valor disponible (que no falta) si no suele fluctuar demasiado.

### <a name="data-quantity"></a>Cantidad de datos

* El modelo subyacente de MVAD tiene millones de parámetros. Necesita un número mínimo de puntos de datos para aprender un conjunto óptimo de parámetros. La regla empírica es que debe proporcionar **15 000 o más puntos de datos (marcas de tiempo) por variable** para entrenar el modelo para una buena precisión. En general, cuantos más datos de entrenamiento tenga, mejor será la precisión. Sin embargo, en los casos en los que no pueda acumular tantos datos, le recomendamos que experimente con menos datos y vea si la precisión comprometida sigue siendo aceptable.
* Cada vez que llame a la API de inferencia, debe asegurarse de que el archivo de datos de origen contiene suficientes puntos de datos. Normalmente, `slidingWindow` es mayor el número de puntos de datos que **realmente** necesitan resultados de inferencia. Por ejemplo, en un caso de streaming en el que cada vez que se quiera hacer una inferencia sobre **UNA** nueva marca de tiempo, el archivo de datos podría contener solo el `slidingWindow` inicial más **UN** punto de datos; entonces se podría seguir y crear otro archivo ZIP con el mismo número de puntos de datos (`slidingWindow` + 1) pero moviéndolo UN paso hacia el lado "derecho" y enviarlo para otro trabajo de inferencia. 

    Cualquier cosa más allá de eso o "antes" de la ventana deslizante principal no tendrá ningún impacto en el resultado de la inferencia y solo puede provocar una disminución del rendimiento.Cualquier cosa por debajo de eso puede conducir a un error `NotEnoughInput`.


### <a name="timestamp-round-up"></a>Resumen de marca de tiempo

En un grupo de variables (serie temporal), cada variable se puede recopilar de un origen independiente. Las marcas de tiempo de diferentes variables pueden ser incoherentes entre sí y con las frecuencias conocidas. A continuación se muestra un ejemplo sencillo.

*Variable-1*

| timestamp | value |
| --------- | ----- |
| 12:00:01  | 1.0   |
| 12:00:35  | 1.5   |
| 12:01:02  | 0.9   |
| 12:01:31  | 2.2   |
| 12:02:08  | 1.3   |

*Variable-2*

| timestamp | value |
| --------- | ----- |
| 12:00:03  | 2.2   |
| 12:00:37  | 2.6   |
| 12:01:09  | 1.4   |
| 12:01:34  | 1.7   |
| 12:02:04  | 2.0   |

Tenemos dos variables recopiladas de dos sensores que envían un punto de datos cada 30 segundos. Sin embargo, los sensores no envían puntos de datos con una frecuencia estrictamente uniforme, sino que unas veces se adelantan y otras se retrasan. Dado que el MVAD tendrá en cuenta las correlaciones entre las distintas variables, las marcas de tiempo deben alinearse correctamente para que las métricas puedan reflejar correctamente el estado del sistema. En el ejemplo anterior, las marcas de tiempo de la variable 1 y la variable 2 deben estar correctamente "redondeadas" a su frecuencia antes de la alineación.

Veamos lo que sucede si no se procesan previamente. Si se establece `alignMode` en `Outer` (lo que significa la unión de dos conjuntos), la tabla combinada será

| timestamp | Variable-1 | Variable-2 |
| --------- | -------- | -------- |
| 12:00:01  | 1.0      | `nan`    |
| 12:00:03  | `nan`    | 2.2      |
| 12:00:35  | 1.5      | `nan`    |
| 12:00:37  | `nan`    | 2.6      |
| 12:01:02  | 0.9      | `nan`    |
| 12:01:09  | `nan`    | 1.4      |
| 12:01:31  | 2.2      | `nan`    |
| 12:01:34  | `nan`    | 1.7      |
| 12:02:04  | `nan`    | 2.0      |
| 12:02:08  | 1.3      | `nan`    |

`nan` indica que faltan valores. Obviamente, la tabla combinada no es lo que podría haber esperado. La variable 1 y la variable 2 se intercalan, y el modelo MVAD no puede extraer información sobre las correlaciones entre ellas. Si establecemos `alignMode` en `Inner`, la tabla combinada estará vacía, ya que no hay una marca de tiempo común en la variable 1 y la variable 2.

Por lo tanto, las marcas de tiempo de las variables 1 y 2 deben procesarse previamente (redondearse a las marcas de tiempo de 30 segundos más cercanas) y las nuevas series temporales son

*Variable-1*

| timestamp | value |
| --------- | ----- |
| 12:00:00  | 1.0   |
| 12:00:30  | 1.5   |
| 12:01:00  | 0.9   |
| 12:01:30  | 2.2   |
| 12:02:00  | 1.3   |

*Variable-2*

| timestamp | value |
| --------- | ----- |
| 12:00:00  | 2.2   |
| 12:00:30  | 2.6   |
| 12:01:00  | 1.4   |
| 12:01:30  | 1.7   |
| 12:02:00  | 2.0   |

Ahora la tabla combinada es más razonable.

| timestamp | Variable-1 | Variable-2 |
| --------- | -------- | -------- |
| 12:00:00  | 1.0      | 2.2      |
| 12:00:30  | 1.5      | 2.6      |
| 12:01:00  | 0.9      | 1.4      |
| 12:01:30  | 2.2      | 1.7      |
| 12:02:00  | 1.3      | 2.0      |

Los valores de diferentes variables en marcas de tiempo cercanas están bien alineados y el modelo MVAD ahora puede extraer información de correlación.

## <a name="common-pitfalls"></a>Dificultades habituales

Además de la [tabla de códigos de error](./troubleshoot.md), hemos aprendido de clientes como usted algunos problemas comunes al usar las API de MVAD. Esta tabla le ayudará a evitar estos problemas.

| Problema | Consecuencia |Explicación y solución |
| --------- | ----- | ----- |
| Las marcas de tiempo en los datos de entrenamiento o datos de inferencia no se redondearon para alinearse con la frecuencia de datos respectiva de cada variable. | Las marcas de tiempo de los resultados de la inferencia no son las esperadas: o bien hay muy pocas marcas de tiempo o demasiadas.  | Consulte [Resumen de marca de tiempo](#timestamp-round-up).  |
| Demasiados puntos de datos anómalos en los datos de entrenamiento | La precisión del modelo se ve afectada negativamente porque trata los puntos de datos anómalos como patrones normales durante el entrenamiento. | Empíricamente, mantener la tasa anómala en un **1 %** o menos resultará de ayuda. |
| Demasiados pocos datos de entrenamiento | La precisión del modelo está en peligro. | Empíricamente, el entrenamiento de un modelo MVAD requiere 15 000 o más puntos de datos (marcas de tiempo) por variable para mantener una buena precisión.|
| Toma de todos los puntos de datos con `isAnomaly`=`true` como anomalías | Demasiados falsos positivos. | Debe utilizar tanto `isAnomaly` como `severity` (o `score`) para filtrar las anomalías que no son graves y (opcionalmente) utilizar la agrupación para comprobar la duración de las anomalías para suprimir el ruido aleatorio. Consulte la sección de [preguntas más frecuentes](#faq) a continuación para ver la diferencia entre `severity` y `score`.  |
| Compresión de las subcarpetas en el archivo de datos para el entrenamiento o la inferencia | Los archivos de datos csv dentro de subcarpetas se omiten durante el entrenamiento o la inferencia. | No se permite ninguna subcarpeta en el archivo ZIP. Consulte [Estructura de carpetas](#folder-structure) para obtener más información. |
| Demasiados datos en el archivo de datos de inferencia: por ejemplo, comprimir todos los datos históricos en el archivo ZIP de datos de inferencia | Es posible que no vea ningún error, pero experimentará un rendimiento inferior al intentar cargar el archivo ZIP en el blob de Azure, así como al intentar ejecutar la inferencia. | Consulte [Cantidad de datos](#data-quantity) para obtener más información. |
| Creación de recursos de Anomaly Detector en regiones de Azure que aún no admiten MVAD y llamada a las API de MVAD  | Recibirá un error de "recurso no encontrado" al llamar a las API de MVAD. | Durante la fase de versión preliminar, MVAD solo está disponible en algunas regiones. Agregue [Novedades de Anomaly Detector](../whats-new.md) como marcador para mantenerse al día con los lanzamientos de regiones de MVAD. También puede presentar una incidencia de GitHub o ponerse en contacto con nosotros en AnomalyDetector@microsoft.com para solicitar regiones específicas. |

## <a name="faq"></a>Preguntas más frecuentes

### <a name="how-does-mvad-sliding-window-work"></a>¿Cómo funciona la ventana deslizante de MVAD?

Vamos a usar dos ejemplos para aprender cómo funciona la ventana deslizante de MVAD. Supongamos que ha establecido `slidingWindow` = 1440 y los datos de entrada están en una granularidad de un minuto.

* **Escenario de streaming**: Quiere predecir si el punto de datos UNO en "2021-01-02T00:00:00Z" es anómalo. Sus valores `startTime` y `endTime` serán iguales ("2021-01-02T00:00:00Z"). Sin embargo, el origen de datos de inferencia debe contener al menos 1440 + 1 marca de tiempo. Porque MVAD tomará los datos iniciales anteriores al punto de datos objetivo ("2021-01-02T00:00:00Z") para decidir si el objetivo es una anomalía. La longitud de los datos iniciales necesarios es `slidingWindow`, o 1440 en este caso. 1440 = 60 * 24, por lo que sus datos de entrada deben empezar como máximo en "2021-01-01T00:00:00Z".

* **Escenario de lotes**: Tiene múltiples puntos de datos objetivo para predecir. El valor de `endTime` será mayor que el valor de `startTime`. La inferencia en estos escenarios se realiza de una manera de "ventana móvil". Por ejemplo, MVAD utilizará los datos de `2021-01-01T00:00:00Z` a `2021-01-01T23:59:00Z` (inclusive) para determinar si los datos de `2021-01-02T00:00:00Z` son anómalos. Luego avanza y utiliza los datos de `2021-01-01T00:01:00Z` a `2021-01-02T00:00:00Z` (inclusive) para determinar si los datos de `2021-01-02T00:01:00Z` son anómalos. Se mueve de la misma manera (tomando 1440 puntos de datos para comparar) hasta la última marca de tiempo especificada por `endTime` (o la última marca de tiempo real). Por lo tanto, el origen de datos de inferencia debe contener datos a partir de `startTime` - `slidingWindow` e idealmente contiene en total de tamaño `slidingWindow` + (`endTime` - `startTime`).

### <a name="why-only-accepting-zip-files-for-training-and-inference"></a>¿Por qué aceptar solo archivos ZIP para el entrenamiento y la inferencia?

Usamos archivos ZIP porque, en escenarios de lotes, esperamos que el tamaño de los datos de entrenamiento e inferencia sea muy grande y no se pueda colocar en el cuerpo de la solicitud HTTP. Esto permite a los usuarios realizar inferencias por lotes sobre datos históricos, ya sea para la validación de modelos o para el análisis de datos.

Sin embargo, esto podría ser un poco inconveniente para la inferencia de streaming y para los datos de alta frecuencia. Tenemos un plan para agregar una nueva API diseñada específicamente para la inferencia de streaming para que los usuarios puedan pasar datos en el cuerpo de la solicitud.

### <a name="whats-the-difference-between-severity-and-score"></a>¿Cuál es la diferencia entre `severity` y `score`?

Normalmente, le recomendamos que utilice `severity` como filtro para averiguar las "anomalías" que no son tan importantes para su negocio. Dependiendo de su escenario y patrón de datos, aquellas anomalías que son menos importantes a menudo tienen valores `severity` relativamente bajos o valores `severity` altos independientes (discontinuos) como picos aleatorios.

En los casos en los que haya detectado la necesidad de reglas más sofisticadas que los umbrales de `severity` o la duración de los valores `severity` altos continuos, es posible que desee utilizar `score` para generar filtros más eficaces. Comprender cómo usa MVAD `score` para determinar anomalías puede ayudar:

Consideramos si un punto de datos es anómalo desde la perspectiva global y local. Si `score` en una marca de tiempo es mayor que un umbral determinado, la marca de tiempo se marca como una anomalía. Si `score` es menor que el umbral pero es relativamente mayor en un segmento, también se marca como una anomalía.

## <a name="next-steps"></a>Pasos siguientes

* [Inicios rápidos: Uso de la biblioteca cliente multivariante de Anomaly Detector](../quickstarts/client-libraries-multivariate.md).
* [Obtener información sobre los algoritmos subyacentes que se usan en Anomaly Detector para multivariantes](https://arxiv.org/abs/2009.02040)

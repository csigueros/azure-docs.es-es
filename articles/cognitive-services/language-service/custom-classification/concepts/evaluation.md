---
title: Métricas de evaluación de clasificación personalizada
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre las métricas de evaluación en la extracción de entidades personalizadas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 53765ebe1862b4c46a1b4ad8caf75f64f6203e37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091508"
---
# <a name="evaluation-metrics"></a>Métricas de evaluación

El [conjunto de datos se divide](../how-to/train-model.md#data-splits) en dos partes: un conjunto para el entrenamiento y un conjunto para pruebas. El conjunto de entrenamiento al compilar el modelo y el conjunto de pruebas se usan como un conjunto ciego para evaluar el rendimiento del modelo una vez completado el entrenamiento.

La evaluación del modelo se desencadena después de que el entrenamiento se haya completado correctamente. El proceso de evaluación comienza utilizando el modelo entrenado para predecir las clases definidas por el usuario para los archivos del conjunto de pruebas y las compara con las etiquetas de datos proporcionadas (que establecen una base de referencia de autenticidad). Los resultados se devuelven para que pueda revisar el rendimiento del modelo. Para la evaluación, la clasificación de texto personalizada usa las siguientes métricas:

* **Precisión**: mide lo preciso o exacto que es el modelo. Es la relación entre los positivos identificados correctamente (verdaderos positivos) y todos los positivos identificados. La métrica de precisión revela cuántas de las clases previstas están correctamente etiquetadas. 

    `Precision = #True_Positive / (#True_Positive + #False_Positive)`

* **Coincidencia**: mide la capacidad del modelo para predecir clases positivas reales. Es la relación entre los verdaderos positivos previstos y lo que realmente se ha etiquetado. La métrica de coincidencia revela cuántas de las clases previstas están correctamente etiquetadas.

    `Recall = #True_Positive / (#True_Positive + #False_Negatives)`

* **Puntuación F1**: la puntuación F1 es una función de precisión y coincidencia. Es necesaria cuando se busca un equilibrio entre precisión y coincidencia.

    `F1 Score = 2 * Precision * Recall / (Precision + Recall)` <br> 

>[!NOTE]
> La precisión, la coincidencia y la puntuación F1 se calculan para cada clase por separado (evaluación a *nivel de clase*) y para el modelo en su conjunto (evaluación a *nivel de modelo*).

## <a name="model-level-and-class-level-evaluation-metrics"></a>Métricas de evaluación a nivel de modelo y clase

Las definiciones de precisión, coincidencia y evaluación son las mismas para las evaluaciones a nivel de clase y a nivel de modelo. Sin embargo, el recuento de *verdaderos positivos*, *falsos positivos* y *falsos negativos* difiere, como se muestra en el ejemplo siguiente.

En las secciones siguientes se usa el siguiente conjunto de datos de ejemplo:

| Archivo | Clases reales | Clases previstas |
|--|--|--|
| 1 | acción, comedia | comedia|
| 2 | action | action |
| 3 | historias románticas | historias románticas |
| 4 | historias románticas, comedia | historias románticas |
| 5 | comedia | action |

### <a name="class-level-evaluation-for-the-action-class"></a>Evaluación a nivel de clase para la clase de *acción* 

| Clave | Count | Explicación |
|--|--|--|
| Verdadero positivo | 1 | El archivo 2 se clasificó correctamente como *acción*. |
| Falso positivo | 1 | El archivo 5 se clasificó erróneamente como *acción*. |
| Falso negativo | 1 | El archivo 1 no se clasificó como *acción*, pero debería haberse clasificado así. |

**Precisión** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 1) = 0.5`

**Coincidencia** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 1) = 0.5`

**Puntuación F1** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.5 * 0.5) / (0.5 + 0.5) = 0.5`

### <a name="class-level-evaluation-for-the-comedy-class"></a>Evaluación a nivel de clase para la clase de *comedia* 

| Clave | Count | Explicación |
|--|--|--|
| Verdadero positivo | 1 | El archivo 1 se clasificó correctamente como *comedia*. |
| Falso positivo | 0 | Ningún archivo se clasificó erróneamente como *comedia*. |
| Falso negativo | 2 | Los archivos 5 y 4 no se clasificaron como *comedia*, aunque deberían haberse clasificado así. |

**Precisión** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 0) = 1`

**Coincidencia** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 2) = 0.67`

**Puntuación F1** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 1 * 0.67) / (1 + 0.67) = 0.8`

### <a name="model-level-evaluation-for-the-collective-model"></a>Evaluación a nivel de modelo para el modelo colectivo

| Clave | Count | Explicación |
|--|--|--|
| Verdadero positivo | 4 | A los archivos 1, 2, 3 y 4 se les asignaron clases correctas en la predicción. |
| Falso positivo | 1 | Al archivo 5 se le asignó una clase incorrecta en la predicción. |
| Falso negativo | 2 | A los archivos 1 y 4 no se les asignaron todas las clases correctas en la predicción. |

**Precisión** = `#True_Positive / (#True_Positive + #False_Positive) = 4 / (4 + 1) = 0.8`

**Coincidencia** = `#True_Positive / (#True_Positive + #False_Negatives) = 4 / (4 + 2) = 0.67`

**Puntuación F1** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.8 * 0.67) / (0.8 + 0.67) = 0.12`

> [!NOTE] 
> Para los modelos de clasificación de etiqueta única, el recuento de falsos negativos y falsos positivos siempre es igual. Los modelos de clasificación de etiqueta única personalizados siempre predicen una clase para cada archivo. Si la predicción no es correcta, el recuento de falsos positivos de la clase prevista aumenta en uno y el recuento de falsos negativos de la clase real aumenta en uno, mientras que el recuento total de falsos positivos y falsos negativos para el modelo siempre será igual. Este no es el caso de la clasificación multietiqueta, ya que no predecir una de las clases de un archivo cuenta como un falso negativo. 

## <a name="interpreting-class-level-evaluation-metrics"></a>Interpretación de las métricas de evaluación a nivel de clase

¿Qué significa realmente que una determinada clase tenga alta precisión o alta coincidencia?

| Recuperación | Precision | Interpretación |
|--|--|--|
| Alto | Alto | El modelo controla perfectamente esta clase. |
| Bajo | Alto | El modelo no siempre puede predecir esta clase, pero cuando lo hace es con una confianza alta. Esto puede deberse a que esta clase está infrarrepresentada en el conjunto de datos, por lo que debe considerar la posibilidad de equilibrar la distribución de los datos.|
| Alto | Bajo | El modelo predice bien esta clase, pero con confianza baja. Esto puede deberse a que esta clase está excesivamente representada en el conjunto de datos, por lo que debe considerar la posibilidad de equilibrar la distribución de los datos. |
| Bajo | Bajo | El modelo controla mal esta clase, ya que normalmente no se predice y, cuando sí se predice, no tiene una confianza alta. |

Cabe esperar que los modelos de clasificación personalizada experimenten falsos negativos y falsos positivos. Debe tener en cuenta cómo afectará cada caso a todo el sistema y pensar detenidamente en escenarios en los que el modelo omitirá predicciones correctas y reconocerá predicciones incorrectas. En función de su escenario, la *precisión* o la *coincidencia* podrían ser más adecuadas para la evaluación del rendimiento del modelo.  

Por ejemplo, si su escenario implica el procesamiento de incidencias de soporte técnico, la predicción de la clase incorrecta podría provocar que se dirigieran al departamento o equipo incorrectos. En este ejemplo, debe considerar la posibilidad de hacer que el sistema sea más sensible a los falsos positivos y la precisión sería una métrica más pertinente para la evaluación. 

Otro ejemplo sería el de un escenario que implica categorizar correo electrónico como "*importante*" o "*no deseado*", donde una predicción incorrecta podría provocar que se perdiera un correo electrónico útil si se etiquetara como "*no deseado*". Sin embargo, si un correo electrónico no deseado se etiqueta como *importante*, simplemente puede ignorarlo. En este ejemplo, debe considerar la posibilidad de hacer que el sistema sea más sensible a los falsos negativos y la coincidencia sería una métrica más pertinente para la evaluación. 

Si desea optimizar el sistema para escenarios de uso general o para casos en los que tanto la precisión como la coincidencia sean importantes, puede usar la puntuación F1. Las puntuaciones de evaluación son subjetivas en función del escenario y los criterios de aceptación. No hay ninguna métrica absoluta que funcione para todos los escenarios. 

## <a name="see-also"></a>Consulte también

* [Visualización de la evaluación del modelo](../how-to/view-model-evaluation.md)
* [Entrenamiento de un modelo](../how-to/train-model.md)

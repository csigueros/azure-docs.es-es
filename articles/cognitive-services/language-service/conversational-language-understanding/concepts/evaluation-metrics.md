---
title: Métricas de evaluación de Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre las métricas de evaluación en Conversational Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: b81273e3955ab866648a1d70f01991272ecba787
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091144"
---
# <a name="evaluation-metrics-for-conversational-language-understanding-models"></a>Métricas de evaluación de modelos de Conversational Language Understanding

La evaluación de modelos en el reconocimiento del lenguaje conversacional usa las siguientes métricas:


|Metric |Descripción  |Cálculo  |
|---------|---------|---------|
|Precision     |  Proporción de reconocimientos correctos en relación con todos los reconocimientos intentados. Muestra cuántas veces el reconocimiento de entidades del modelo es realmente un buen reconocimiento.       | `Precision = #True_Positive / (#True_Positive + #False_Positive)`        |
|Recuperación     | Proporción de reconocimientos correctos en relación con el número real de entidades presentes.        | `Recall = #True_Positive / (#True_Positive + #False_Negatives)`        |
|Puntuación F1    |  Combinación de precisión y coincidencia.       |  `F1 Score = 2 * Precision * Recall / (Precision + Recall)`       |

## <a name="model-level-and-entity-level-evaluation-metrics"></a>Métricas de evaluación a nivel de modelo y entidad

La precisión, la coincidencia y la puntuación F1 se calculan para cada entidad por separado (evaluación a nivel de entidad) y para el modelo en su conjunto (evaluación a nivel de modelo).

Las definiciones de precisión, coincidencia y evaluación son las mismas para las evaluaciones a nivel de entidad y a nivel de modelo. Sin embargo, lo recuentos de *verdaderos positivos*, *falsos positivos* y *falsos negativos* pueden variar. Por ejemplo, considere el texto siguiente.

### <a name="example"></a>Ejemplo

*La primera parte de este contrato es John Smith, residente de 5678 Main Rd., Ciudad de Frederick, estado de Nebraska. Y la segunda parte es Forrest Ray, residente de 123-345 Integer Rd., ciudad de Corona, estado de Nuevo México. También está Fannie Thomas residente de 7890 River Road, ciudad de Colorado Springs, estado de Colorado.*

El modelo que extrae entidades de este texto podría tener las siguientes predicciones:

| Entidad | Se predice como | Tipo real |
|--|--|--|
| John Smith | Person | Person |
| Frederick | Person | City |
| Forrest | City | Person |
| Fannie Thomas | Person | Person |
| Colorado Springs | City | City |

### <a name="entity-level-evaluation-for-the-person-entity"></a>Evaluación a nivel de entidad para la entidad *persona* 

El modelo tendría la siguiente evaluación a nivel de entidad para la entidad *persona*:

| Clave | Count | Explicación |
|--|--|--|
| Verdadero positivo | 2 | *John Smith* y *Fannie Thomas* se perdijeron correctamente como *persona*. |
| Falso positivo | 1 | *Frederick* se predijo incorrectamente como *persona*, cuando se debería haber predicho como *ciudad*. |
| Falso negativo | 1 | *Forrest* se predijo incorrectamente como *ciudad*, cuando se debería haber predicho como *persona*. |

* **Precisión**: `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **Coincidencia**: `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **Puntuación F1**: `2 * Precision * Recall / (Precision + Recall)` = `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="entity-level-evaluation-for-the-city-entity"></a>Evaluación a nivel de entidad para la entidad *ciudad*

El modelo tendría la siguiente evaluación a nivel de entidad para la entidad *ciudad*:

| Clave | Count | Explicación |
|--|--|--|
| Verdadero positivo | 1 | *Colorado Springs* se predijo correctamente como *ciudad*. |
| Falso positivo | 1 | *Forrest* se predijo incorrectamente como *ciudad*, cuando se debería haber predicho como *persona*. |
| Falso negativo | 1 | *Frederick* se predijo incorrectamente como *persona*, cuando se debería haber predicho como *ciudad*. |

* **Precisión** = `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **Coincidencia** = `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **Puntuación F1** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="model-level-evaluation-for-the-collective-model"></a>Evaluación a nivel de modelo del modelo colectivo

El modelo tendría la siguiente evaluación para el modelo en su totalidad:

| Clave | Count | Explicación |
|--|--|--|
| Verdadero positivo | 3 | *John Smith* y *Fannie Thomas* se perdijeron correctamente como *persona*. *Colorado Springs* se predijo correctamente como *ciudad*. Esta es la suma de verdaderos positivos para todas las entidades. |
| Falso positivo | 2 | *Forrest* se predijo incorrectamente como *ciudad*, cuando se debería haber predicho como *persona*. *Frederick* se predijo incorrectamente como *persona*, cuando se debería haber predicho como *ciudad*. Esta es la suma de falsos positivos para todas las entidades. |
| Falso negativo | 2 | *Forrest* se predijo incorrectamente como *ciudad*, cuando se debería haber predicho como *persona*. *Frederick* se predijo incorrectamente como *persona*, cuando se debería haber predicho como *ciudad*. Esta es la suma de falsos negativos para todas las entidades. |

* **Precisión** = `#True_Positive / (#True_Positive + #False_Positive)` = `3 / (3 + 2) = 0.6`
* **Coincidencia** = `#True_Positive / (#True_Positive + #False_Negatives)` = `3 / (3 + 2) = 0.6`
* **Puntuación F1** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.6 * 0.6) / (0.6 + 0.6) = 0.6`

## <a name="interpreting-entity-level-evaluation-metrics"></a>Interpretación de las métricas de evaluación a nivel de entidad

¿Qué significa realmente que una determinada entidad tenga una precisión o una coincidencia altas?

| Recuperación | Precision | Interpretación |
|--|--|--|
| Alto | Alto | El modelo controla bien esta entidad. |
| Bajo | Alto | El modelo no siempre puede extraer esta entidad, pero cuando lo hace es con una confianza alta. |
| Alto | Bajo | El modelo extrae bien esta entidad, pero con confianza baja, ya que a veces se extrae como otro tipo. |
| Bajo | Bajo | El modelo no controla bien este tipo de entidad, porque normalmente no se extrae. Cuando lo hace, no es con una confianza alta. |

## <a name="confusion-matrix"></a>Matriz de confusión

Una matriz de confusión es una matriz N x N que se usa para la evaluación del rendimiento del modelo, donde N es el número de entidades.
La matriz compara las etiquetas reales con las etiquetas predichas por el modelo.
Esto proporciona una vista holística de cómo funciona el modelo y qué tipos de errores está cometiendo.

Puede usar la matriz de confusión para identificar las entidades que están demasiado cerca unas de otras y que a menudo se confunden (ambigüedad). En este caso, considere la posibilidad de combinar estos tipos de entidad. Si no es posible, considere la posibilidad de agregar más ejemplos etiquetados de ambas entidades para ayudar al modelo a diferenciarlas.

La diagonal resaltada en la imagen siguiente son las entidades predichas correctamente, donde la etiqueta predicha es la misma que la etiqueta real.

:::image type="content" source="../media/confusion-matrix-example.png" alt-text="Captura de pantalla de una matriz de confusión de ejemplo" lightbox="../media/confusion-matrix-example.png":::

Puede calcular las métricas de evaluación a nivel de entidad y a nivel de modelo a partir de la matriz de confusión:

* Los valores de la diagonal son los valores *verdaderos positivos* de cada entidad.
* La suma de los valores de las filas de entidad (sin incluir la diagonal) es el *falso positivo* del modelo.
* La suma de los valores de las columnas de entidad (sin incluir la diagonal) es el *falso negativo* del modelo.

De forma similar:

* El *verdadero positivo* del modelo es la suma de *verdaderos positivos* de todas las entidades.
* El *falso positivo* del modelo es la suma de *falsos positivos* de todas las entidades.
* El *falso negativo* del modelo es la suma de *falsos negativos* de todas las entidades.

## <a name="next-steps"></a>Pasos siguientes

[Entrenamiento de un modelo en Language Studio](../how-to/train-model.md)

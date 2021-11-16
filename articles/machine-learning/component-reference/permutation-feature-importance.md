---
title: 'Importancia de la característica de permutación: referencia de componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Importancia de la característica de permutación del diseñador para calcular las puntuaciones de importancia de la característica de permutación de las variables de la característica.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 256425a672b3eb491ca49097601a1755a7944c03
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566122"
---
# <a name="permutation-feature-importance"></a>Importancia de la característica de permutación

En este artículo se explica cómo usar el componente Importancia de la característica de permutación del diseñador de Azure Machine Learning para calcular un conjunto de puntuaciones de importancia de la característica del conjunto de datos. Estas puntuaciones se usan para ayudarle a determinar las mejores características que se deben usar en un modelo.

En este componente, los valores de la característica se ordenan aleatoriamente, una columna cada vez. El rendimiento del modelo se mide antes y después. Puede elegir una de las métricas estándar para medir el rendimiento.

Las puntuaciones que devuelve el componente representan el *cambio* en el rendimiento de un modelo entrenado, después de la permutación. Las características importantes suelen ser más sensibles al proceso orden aleatorio y, por tanto, se obtendrán mayores puntuaciones de importancia. 

En este artículo se proporciona información general sobre la característica de permutación, su base teórica y sus aplicaciones en aprendizaje automático: [Permutation Feature Importance](/archive/blogs/machinelearning/permutation-feature-importance) (Importancia de la característica de permutación).  

## <a name="how-to-use-permutation-feature-importance"></a>Cómo usar la Importancia de la característica de permutación

Para generar un conjunto de puntuaciones de características, es necesario tener un modelo ya entrenado, así como un conjunto de datos de prueba.  

1.  Agregue el componente Importancia de la característica de permutación a la canalización. Puede encontrar este componente en la categoría **Selección de características**. 

2.  Conecte un modelo formado a la entrada izquierda. El modelo tiene que ser un modelo de regresión o un modelo de clasificación.  

3.  En la entrada derecha, conecte un conjunto de datos. Preferiblemente, elija una que sea diferente del conjunto de datos que usó para entrenar el modelo. Este conjunto de datos se usa para puntuar en base al modelo entrenado. También se usa para evaluar el modelo después de que los valores de características hayan cambiado.  

4.  Para **Valor de inicialización aleatorio**, escriba un valor que se usará como valor de inicialización para la selección aleatoria. Si especifica 0 (el valor predeterminado), se genera un número basado en el reloj del sistema.

     Un valor de inicialización es opcional, pero debe proporcionar un valor si desea la reproducibilidad en ejecuciones de la misma canalización.  

5.  Para **Metric for measuring performance** (Métrica para medir el rendimiento), seleccione una sola métrica para usar al calcular la calidad del modelo después de la permutación.  

     El diseñador de Azure Machine Learning admite las siguientes métricas, en función de si está evaluando un modelo de clasificación o regresión:  

    -   **Clasificación**

        Exactitud, precisión, recuperación  

    -   **Regresión**

        Precisión, recuperación, error absoluto medio, error cuadrático medio raíz, error absoluto relativo, error cuadrado relativo, coeficiente de determinación  

     Para una descripción más detallada de estas métricas de evaluación y cómo se calculan, consulte [Evaluar modelo](evaluate-model.md).  

6.  Envíe la canalización.  

7.  El componente genera una lista de columnas de características y las puntuaciones asociadas. La lista está en orden descendente de las puntuaciones.  


##  <a name="technical-notes"></a>Notas técnicas

Permutation Feature Importance (Importancia de la característica de permutación) funciona cambiando aleatoriamente los valores de cada columna de característica, una columna a la vez. A continuación, evalúa el modelo. 

Las clasificaciones que proporciona el componente suelen ser diferentes de las que se obtienen de [Selección de características basada en filtro](filter-based-feature-selection.md). Selección de características basada en filtro calcula las puntuaciones *antes de que* se cree un modelo. 

La razón de la diferencia es que Permutation Feature Importance (Importancia de la característica de permutación) no mide la asociación entre una característica y un valor de destino. En su lugar, captura el grado de influencia que tiene cada característica en las predicciones del modelo.
  
## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.
---
title: 'Regresión logística multiclase: referencia de componentes'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente de regresión logística multiclase en el diseñador de Azure Machine Learning para predecir varios valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a5c823b2fc2464907ef224bc3a34d5150dfe3ba2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566009"
---
# <a name="multiclass-logistic-regression-component"></a>Componente de Regresión logística multiclase

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para crear un modelo de regresión logística que pueda usarse para predecir varios valores.

La clasificación con la regresión logística es un método de aprendizaje supervisado y, por lo tanto, requiere un conjunto de datos con etiquetas. Puede entrenar el modelo proporcionando el modelo y el conjunto de datos etiquetado como entrada para un componente como [Entrenar modelo](./train-model.md). Después, el modelo entrenado puede usarse para predecir valores para los nuevos ejemplos de entrada.

Azure Machine Learning también proporciona un componente [Regresión logística de dos clases](./two-class-logistic-regression.md), que es adecuado para la clasificación de variables binarias o dicotómicas.

## <a name="about-multiclass-logistic-regression"></a>Acerca de la regresión logística multiclase

La regresión logística es un método conocido en estadística que se usa para predecir la probabilidad de un resultado y es popular para las tareas de clasificación. El algoritmo predice la probabilidad de aparición de un evento ajustando los datos a una función logística. 

En la regresión logística multiclase, el clasificador puede usarse para predecir varios resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar una regresión logística multiclase

1. Agregue el componente **Regresión logística multiclase** a la canalización.

2. Especifique cómo quiere que se entrene el modelo, estableciendo la opción **Create trainer mode** (Crear modo entrenador).

    + **Single Parameter** (Parámetro único): Use esta opción si sabe cómo quiere configurar el modelo y proporcione un conjunto específico de valores como argumentos.

    + **Parameter Range** (Intervalo de parámetros): seleccione esta opción si no está seguro de los mejores parámetros y quiere ejecutar un barrido de parámetros. Seleccione un rango de valores que iterar y el módulo [Optimización de hiperparámetros de un modelo](tune-model-hyperparameters.md) itera en todas las combinaciones posibles de los valores proporcionados para determinar los hiperparámetros que generan los resultados óptimos.  

3. **Optimization tolerance** (Tolerancia de optimización), especifique el valor de umbral de convergencia del optimizador. Si la mejora entre las iteraciones es menor que el umbral, el algoritmo se detiene y devuelve el modelo actual.

4. **L1 regularization weight** (Ponderación de regularización L1), **L2 regularization weight** (Ponderación de regularización L2): Escriba un valor que se usará para los parámetros de regularización L1 y L2. Se recomienda un valor distinto de cero para ambos.

    La regularización es un método para evitar el sobreajuste mediante la penalización de modelos con valores de coeficiente extremos. La regularización funciona agregando la penalización asociada a los valores de coeficiente al error de la hipótesis. Un modelo preciso con valores de coeficiente extremos se penalizaría más, y un modelo menos preciso con valores más conservadores se penalizaría menos.

     Las regularizaciones L1 y L2 tienen efectos y usos diferentes. L1 puede aplicarse a modelos dispersos, lo que resulta útil cuando se trabaja con datos muy dimensionales. En cambio, la regularización L2 es preferible para los datos que no están dispersos.  Este algoritmo es compatible con una combinación lineal de los valores de regularización L1 y L2: es decir, si `x = L1` y `y = L2`, `ax + by = c` define el intervalo lineal de los términos de regularización.

     Se han diseñado diferentes combinaciones lineales de los términos de L1 y L2 para los modelos de regresión logística: por ejemplo, [regularización elástica neta](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Número de iniciación aleatorio**: Escriba un valor entero para usar como valor de inicialización para el algoritmo si quiere que los resultados se puedan repetir entre ejecuciones. En caso contrario, como inicialización se usa un valor de reloj del sistema, lo que puede producir resultados ligeramente diferentes cada vez que ejecute la misma canalización.

8. Conecte un conjunto de datos etiquetado y entrene el modelo:

    + Si establece **Crear el modo de entrenador** en **Parámetro único**, conecte un conjunto de datos etiquetado y el componente [Entrenar modelo](train-model.md).  
  
    + Si establece **Create trainer mode** (Crear el modo de entrenador) en **Parameter Range** (Intervalo de parámetros), conecte un conjunto de datos etiquetado y entrene el modelo mediante [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Optimizar los hiperparámetros del modelo).  
  
    > [!NOTE]
    > 
    > Si pasa un intervalo de parámetros a [Train Model](train-model.md) (Entrenar modelo), solo utiliza el valor predeterminado en la lista de parámetros única.  
    > 
    > Si pasa un único conjunto de valores de parámetro al componente [Optimizar los hiperparámetros del modelo](tune-model-hyperparameters.md), cuando espera un intervalo de valores para cada parámetro, omite los valores y usa los valores predeterminados para el aprendiz.  
    > 
    > Si selecciona la opción **Parameter Range** (Intervalo de parámetros) y especifica un valor único para algún parámetro, ese valor único que haya especificado se utilizará en todo el barrido, incluso si otros parámetros cambian en un intervalo de valores.

9. Envíe la canalización.



## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
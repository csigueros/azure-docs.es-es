---
title: 'Regresión de Poisson: referencia de componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Regresión de Poisson del diseñador de Azure Machine Learning para crear un modelo de regresión de Poisson.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 3c77b0ac501d6829429cde79ca9165a1df450215
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566098"
---
# <a name="poisson-regression"></a>Regresión de Poisson

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para crear un modelo de regresión de Poisson en una canalización. La regresión de Poisson está pensada para predecir valores numéricos, normalmente recuentos. Por lo tanto, debe usar este componente para crear el modelo de regresión únicamente si los valores que está intentando predecir cumplen las siguientes condiciones:

- La variable de respuesta tiene una [distribución de Poisson](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Los recuentos no pueden ser negativos. El método fallará por completo si intenta utilizarlo con etiquetas negativas.

- Una distribución de Poisson es una distribución discreta; por lo tanto, no tiene sentido usar este método con números no enteros.

> [!TIP]
> Si el destino no es un recuento, la regresión de Poisson probablemente no sea un método adecuado. Pruebe [otros componentes de regresión del diseñador](./component-reference.md#machine-learning-algorithms). 

Después de haber configurado el método de regresión, debe entrenar el modelo mediante un conjunto de datos que contenga ejemplos del valor que desea predecir. A continuación, el modelo entrenado podrá usarse para realizar predicciones.

## <a name="more-about-poisson-regression"></a>Más información sobre la regresión de Poisson

La regresión de Poisson es un tipo especial de análisis de regresión que se utiliza normalmente para modelar recuentos. Por ejemplo, la regresión de Poisson sería útil para:

- Modelar el número de resfriados asociados con vuelos en avión

- Calcular el número de llamadas al servicio de emergencia durante un evento

- Proyectar el número de consultas de clientes posteriores a una promoción

- Creación de tablas de contingencia

Dado que la variable de respuesta tiene una distribución de Poisson, el modelo hace diferentes suposiciones sobre los datos y su distribución de probabilidad que, por ejemplo, la regresión de mínimos cuadrados. Por lo tanto, los modelos de Poisson deben interpretarse de manera diferente a otros modelos de regresión.

## <a name="how-to-configure-poisson-regression"></a>Configuración de la regresión de Poisson

1. Agregue el componente **Regresión de Poisson** a la canalización del diseñador. Puede encontrar este componente en **Algoritmos de aprendizaje automático**, en la categoría **Regresión**.

2. Agregue un conjunto de datos que contenga datos de entrenamiento del tipo correcto. 

    Se recomienda usar [Normalize Data](normalize-data.md) (Normalizar datos) para normalizar el conjunto de datos de entrada antes de usarlo para entrenar el regresor.

3. En el panel derecho del componente **Regresión de Poisson**, especifique cómo quiere que se entrene el modelo. Para ello, establezca la opción **Crear el modo de entrenador**.  
  
    - **Single Parameter** (Parámetro único): Si sabe cómo quiere configurar el modelo, proporcione un conjunto específico de valores como argumentos.
  
    - **Intervalo de parámetros**: si no está seguro de los mejores parámetros, realice un barrido de parámetros mediante el componente [Optimizar los hiperparámetros del modelo](tune-model-hyperparameters.md). El instructor recorre en iteración varios valores que usted especifica para encontrar la configuración óptima.
  
4. **Tolerancia de optimización**: Escriba un valor que defina el intervalo de tolerancia durante la optimización. Cuanto menor sea el valor, más lento y más preciso será el ajuste.

5. **L1 regularization weight** (Ponderación de regularización L1), **L2 regularization weight** (Ponderación de regularización L2): Escriba valores para la regularización L1 y L2. La *regularización* agrega restricciones al algoritmo sobre aspectos del modelo que son independientes de los datos de entrenamiento. La regularización se utiliza habitualmente para evitar el sobreajuste. 

    - La regularización L1 es útil si el objetivo es tener un modelo que sea tan disperso como sea posible.

        La regularización L1 se realiza restando el peso L1 del vector de peso de la expresión de pérdida que el aprendiz está intentando minimizar. La norma L1 es una buena aproximación a la norma L0, que es el número de coordenadas distintas de cero.

    - La regularización L2 impide que una única coordenada del vector de peso crezca demasiado en magnitud. La regularización L2 es útil si el objetivo es tener un modelo con pesos generales pequeños.

    En este componente puede aplicar una combinación de regularizaciones L1 y L2. Si combina regularizaciones L1 y L2, puede imponer una penalización en la magnitud de los valores de parámetro. El aprendiz intenta minimizar la penalización, buscando el equilibrio con la minimización de la pérdida.

    Para obtener una buena explicación de las regularizaciones L1 y L2, consulte [Regularización L1 y L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Tamaño de memoria para L-BFGS**: Especifique la cantidad de memoria que se reservará para la optimización y el ajuste del modelo.

     L-BFGS es un método específico para la optimización, basado en el algoritmo Broyden-Fletcher-Goldfarb-Shanno (BFGS). El método utiliza una cantidad limitada de memoria (L) para calcular la dirección del siguiente paso.

     Al cambiar este parámetro, puede afectar el número de posiciones y gradientes pasados que se almacenan para el cálculo del siguiente paso.

7. Conecte el conjunto de datos de entrenamiento y el modelo sin entrenar a uno de los componentes de entrenamiento: 

    - Si establece **Crear el modo de entrenador** en **Parámetro único**, use el componente [Entrenar modelo](train-model.md).

    - Si establece **Crear el modo de entrenador** en **Intervalo de parámetros**, use el componente [Optimizar los hiperparámetros del modelo](tune-model-hyperparameters.md).

    > [!WARNING]
    > 
    > - Si pasa un intervalo de parámetros a [Entrenar modelo](train-model.md), solo utiliza el primer valor en la lista del intervalo de parámetros.
    > 
    > - Si pasa un único conjunto de valores de parámetro al componente [Optimizar los hiperparámetros del modelo](tune-model-hyperparameters.md), cuando espera un intervalo de valores para cada parámetro, omite los valores y usa los valores predeterminados para el aprendiz.
    > 
    > - Si selecciona la opción **Parameter Range** (Intervalo de parámetros) y especifica un valor único para algún parámetro, ese valor único que haya especificado se utilizará en todo el barrido, incluso si otros parámetros cambian en un intervalo de valores.

8.  Envíe la canalización.

## <a name="results"></a>Results

Una vez completado el entrenamiento:

+ Para guardar una instantánea del modelo entrenado, seleccione el componente de entrenamiento y cambie a la pestaña **Resultados y registros** del panel derecho. Haga clic en el icono **Registro de un conjunto de datos**.  Puede encontrar el modelo guardado como componente en el árbol de componentes. 

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.
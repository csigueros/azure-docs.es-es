---
title: Uno frente a todos multiclase
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar el componente Uno frente a todos multiclase en el diseñador de Azure Machine Learning para crear un conjunto de modelos de clasificación binaria.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 7d935c2b3b5342b8a3407e607a15fcafd7371ebb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565960"
---
# <a name="one-vs-all-multiclass"></a>Uno frente a todos multiclase

En este artículo se describe cómo usar el componente Uno frente a todos multiclase del diseñador de Azure Machine Learning. El objetivo es crear un modelo de clasificación que pueda predecir varias clases mediante el uso de la estrategia *uno frente a todos*.

Este componente es útil para crear modelos que predicen tres o más resultados posibles, cuando el resultado depende de variables de predicción continuas o de categorías. Este método también permite usar métodos de clasificación binaria para problemas que requieren varias clases de salida.

### <a name="more-about-one-versus-all-models"></a>Más información sobre los modelos uno frente a todos

Algunos algoritmos de clasificación permiten el uso de más de dos clases intencionadamente. Otros restringen los posibles resultados a uno de dos valores (un modelo binario, o de dos clases). Pero, incluso los algoritmos de clasificación binaria se pueden adaptar a las tareas de clasificación de varias clases con diversas estrategias. 

Este componente implementa el método uno frente a todos, en el que se crea un modelo binario para cada una de las distintas clases de salida. El componente evalúa cada uno de estos modelos binarios para las clases individuales con respecto a su complemento (todas las demás clases del modelo) como si fuese un problema de clasificación binaria. Además de la eficacia de cálculo (solo se necesitan clasificadores `n_classes` ), una ventaja de este enfoque es su interpretación. Puesto que cada clase solo está representada por un clasificador de uno contra uno, es posible obtener información sobre la clase mediante la inspección de su clasificador correspondiente. Esta es la estrategia que se usa con más frecuencia para la clasificación multiclase y es una opción predeterminada apropiada. El componente realiza entonces la predicción mediante la ejecución de estos clasificadores binarios, y la elección de la predicción con la puntuación de confianza más alta. 

En esencia, el componente crea un conjunto de modelos individuales, y luego combina los resultados para crear un único modelo que predice todas las clases. Cualquier clasificador binario se puede utilizar como base para un modelo uno frente a todos.  

Por ejemplo, supongamos que configura un modelo de [Máquina de vectores de soporte de dos clases](two-class-support-vector-machine.md) y lo proporciona como entrada al componente Uno frente a todos multiclase. El componente creará modelos de máquina de vectores de soporte de dos clases para todos los miembros de la clase de salida. A continuación, aplicará el método uno frente a todos para combinar los resultados de todas las clases.  

El componente usa OneVsRestClassifier de sklearn. Puede consultar información más detallada [aquí](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html).

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Cómo configurar el clasificador One-vs-All Multiclass (Uno frente a todos multiclase)  

Este componente crea un conjunto de modelos de clasificación binaria para analizar varias clases. Para usar este componente, tiene que configurar y entrenar primero un modelo de *clasificación binaria*. 

Conecte el modelo binario al componente Uno frente a todos multiclase. A continuación, entren el conjunto de modelos mediante [Train Model](train-model.md) (Entrenar modelo) con un conjunto de datos de entrenamiento etiquetado.

Cuando combina los modelos, One-vs-All Multiclass (Uno frente a todos multiclase) crea varios modelos de clasificación binaria, optimiza el algoritmo para cada clase y, después, combina los modelos. El componente realiza estas tareas aunque el conjunto de datos de entrenamiento pueda tener varios valores de clase.

1. Agregue el componente Uno frente a todos multiclase a la canalización en el diseñador. Puede encontrar este componente en **Machine Learning: Inicializar**, en la categoría **Clasificación**.

   El clasificador One-vs-All Multiclass (Uno frente a todos multiclase) no tiene parámetros configurables propios. Las personalizaciones tienen que realizarse en el modelo de clasificación binaria que se proporciona como entrada.

2. Agregue un modelo de clasificación binaria a la canalización y configúrelo. Por ejemplo, puede usar una [Máquina de vectores de soporte de dos clases](two-class-support-vector-machine.md) o un [Árbol de decisión promovido por dos clases](two-class-boosted-decision-tree.md).

3. Agregue el componente [Entrenar modelo](train-model.md) a la canalización. Conecte el clasificador no entrenado que es la salida de One-vs-All Multiclass (Uno frente a todos multiclase).

4. En la otra entrada de [Train Model](train-model.md) (Entrenar modelo), conecte un conjunto de datos de aprendizaje etiquetado que tenga varios valores de clase.

5. Envíe la canalización.

## <a name="results"></a>Results

Una vez completa la formación, puede usar el modelo para realizar predicciones multiclase.

Como alternativa, puede pasar el clasificador no entrenado al [Modelo de validación cruzada](cross-validate-model.md) para la validación cruzada en un conjunto de datos de validación etiquetado.


## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 

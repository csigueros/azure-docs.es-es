---
title: Uno frente a uno multiclase
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar el componente multiclase Uno frente a uno en Azure Machine Learning para crear un modelo de clasificación multiclase a partir de un conjunto de modelos de clasificación binaria.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 176b605de08e705571aea1b0e391192d2c18532b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566103"
---
# <a name="one-vs-one-multiclass"></a>Uno frente a uno multiclase

En este artículo se describe cómo usar el componente multiclase Uno frente a uno del diseñador de Azure Machine Learning. El objetivo es crear un modelo de clasificación que pueda predecir varias clases mediante el uso de la estrategia *uno frente a uno*.

Este componente es útil para crear modelos que predicen tres o más resultados posibles, cuando el resultado depende de variables de predicción continuas o de categorías. Este método también permite usar métodos de clasificación binaria para problemas que requieren varias clases de salida.

### <a name="more-about-one-versus-one-models"></a>Más información sobre los modelos uno frente a uno

Algunos algoritmos de clasificación permiten el uso de más de dos clases intencionadamente. Otros restringen los posibles resultados a uno de dos valores (un modelo binario, o de dos clases). Pero, incluso los algoritmos de clasificación binaria se pueden adaptar a las tareas de clasificación de varias clases con diversas estrategias. 

Este componente implementa el método uno frente a uno, en el que se crea un modelo binario por par de clases. En el momento de la predicción, se selecciona la clase que recibió el mayor número de votos. Puesto que requiere ajustar clasificadores `n_classes * (n_classes - 1) / 2`, este método suele ser más lento que uno frente a todos, debido a su complejidad O(n_classes^2). Sin embargo, este método puede ser ventajoso para algoritmos como algoritmos de kernel que no se escalan correctamente con `n_samples`. Esto se debe a que cada problema de aprendizaje individual solo implica un pequeño subconjunto de los datos, mientras que, con uno frente a todos, el conjunto de datos completo se usa `n_classes` veces.

En esencia, el componente crea un conjunto de modelos individuales, y luego combina los resultados para crear un único modelo que predice todas las clases. Cualquier clasificador binario se puede utilizar como base para un modelo uno frente a uno.  

Por ejemplo, supongamos que configura un modelo de [Máquina de vectores de soporte de dos clases](two-class-support-vector-machine.md) y lo proporciona como entrada al componente multiclase Uno frente a uno. El componente creará modelos de máquina de vectores de soporte de dos clases para todos los miembros de la clase de salida. A continuación, aplicará el método uno frente a uno para combinar los resultados de todas las clases.  

El componente usa OneVsOneClassifier de sklearn. Puede consultar información más detallada [aquí](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html).

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Configuración del clasificador Uno frente a uno multiclase  

Este componente crea un conjunto de modelos de clasificación binaria para analizar varias clases. Para usar este componente, tiene que configurar y entrenar primero un modelo de *clasificación binaria*. 

Conecte el modelo binario al componente multiclase Uno frente a uno. A continuación, entren el conjunto de modelos mediante [Train Model](train-model.md) (Entrenar modelo) con un conjunto de datos de entrenamiento etiquetado.

Cuando combina los modelos, Uno frente a uno multiclase crea varios modelos de clasificación binaria, optimiza el algoritmo para cada clase y, después, combina los modelos. El componente realiza estas tareas aunque el conjunto de datos de entrenamiento pueda tener varios valores de clase.

1. Agregue el componente multiclase Uno frente a uno a la canalización en el diseñador. Puede encontrar este componente en **Machine Learning: Inicializar**, en la categoría **Clasificación**.

   El clasificador Uno frente a uno multiclase no tiene parámetros configurables propios. Las personalizaciones tienen que realizarse en el modelo de clasificación binaria que se proporciona como entrada.

2. Agregue un modelo de clasificación binaria a la canalización y configúrelo. Por ejemplo, puede usar una [Máquina de vectores de soporte de dos clases](two-class-support-vector-machine.md) o un [Árbol de decisión promovido por dos clases](two-class-boosted-decision-tree.md).

3. Agregue el componente [Entrenar modelo](train-model.md) a la canalización. Conecte el clasificador no entrenado que es la salida de Uno frente a uno multiclase.

4. En la otra entrada de [Train Model](train-model.md) (Entrenar modelo), conecte un conjunto de datos de aprendizaje etiquetado que tenga varios valores de clase.

5. Envíe la canalización.

## <a name="results"></a>Results

Una vez completa la formación, puede usar el modelo para realizar predicciones multiclase.

Como alternativa, puede pasar el clasificador no entrenado al [Modelo de validación cruzada](cross-validate-model.md) para la validación cruzada en un conjunto de datos de validación etiquetado.


## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 

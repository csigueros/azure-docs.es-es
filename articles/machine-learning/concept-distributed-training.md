---
title: ¿Qué es el entrenamiento distribuido?
titleSuffix: Azure Machine Learning
description: Obtenga información sobre qué tipo de entrenamiento distribuido se admite en Azure Machine Learning y conozca las integraciones del marco de código abierto disponibles para el entrenamiento distribuido.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 517eb0a0abfc00349b42e1948192ad53baca9c8b
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122603704"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Entrenamiento distribuido con Azure Machine Learning

En este artículo, obtendrá información sobre el entrenamiento distribuido y sobre cómo lo admite Azure Machine Learning para los modelos de aprendizaje profundo. 

En el entrenamiento distribuido, la carga de trabajo para entrenar un modelo se divide y se comparte entre varios procesadores pequeños, denominados nodos de trabajo. Estos nodos de trabajo trabajan en paralelo para acelerar el entrenamiento del modelo. El entrenamiento distribuido se puede usar para modelos tradicionales de ML, pero es más adecuado para tareas de proceso y que requieren mucho tiempo, como el [aprendizaje profundo](concept-deep-learning-vs-machine-learning.md) para entrenar redes neuronales profundas. 

## <a name="deep-learning-and-distributed-training"></a>Aprendizaje profundo y entrenamiento distribuido 

Hay dos tipos principales de entrenamiento distribuido: el [paralelismo de datos](#data-parallelism) y el [paralelismo de modelos](#model-parallelism). En el caso del entrenamiento distribuido en modelos de aprendizaje profundo, el [SDK de Azure Machine Learning en Python](/python/api/overview/azure/ml/intro) admite integraciones con plataformas populares, como PyTorch y TensorFlow. Estas dos plataformas emplean el paralelismo de datos para el entrenamiento distribuido y pueden aprovechar [Horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) para optimizar la velocidad de proceso.


* [Entrenamiento distribuido con PyTorch](how-to-train-distributed-gpu.md#pytorch)

* [Entrenamiento distribuido con TensorFlow](how-to-train-distributed-gpu.md#tensorflow)

Para los modelos de ML que no requieren entrenamiento distribuido, consulte el artículo [Entrenamiento de modelos con Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) para conocer las distintas formas de entrenar modelos mediante el SDK de Python.

## <a name="data-parallelism"></a>Paralelismo de datos

De entre los dos enfoques de entrenamiento distribuido, el paralelismo de datos es el más sencillo de implementar y es suficiente para la mayoría de los casos de uso.

En este enfoque, los datos se dividen en particiones, donde el número de particiones es igual al número total de nodos disponibles en el clúster de proceso. El modelo se copia en cada uno de estos nodos de trabajo y cada trabajador opera en su propio subconjunto de los datos. Tenga en cuenta que cada nodo debe tener la capacidad de admitir el modelo que se está entrenando; es decir, el modelo debe ajustarse por completo a cada nodo. En el siguiente diagrama se ofrece una demostración visual de este enfoque.

![Diagrama conceptual de paralelismo de datos](./media/concept-distributed-training/distributed-training.svg)

Cada uno de los nodos calcula de forma independiente los errores entre sus predicciones en sus ejemplos de entrenamiento y las salidas etiquetadas. A su vez, cada nodo actualiza su modelo en función de los errores encontrados y debe comunicar todos sus cambios al resto de nodos para que actualicen sus modelos correspondientes. Esto significa que los nodos de trabajo deben sincronizar los parámetros del modelo, o gradientes, al final del cálculo por lotes para asegurarse de que están entrenando un modelo coherente. 

## <a name="model-parallelism"></a>Paralelismo de modelos

En el paralelismo de modelos, también conocido como paralelismo de redes, el modelo se segmenta en diferentes partes que se pueden ejecutar simultáneamente en nodos diferentes, y cada una de ellas se ejecutará con los mismos datos. La escalabilidad de este método depende del grado de paralelización de tareas del algoritmo y es más complejo de implementar que el paralelismo de datos. 

En el paralelismo de modelos, los nodos de trabajo solo tienen que sincronizar los parámetros compartidos (normalmente una vez por cada paso de propagación hacia delante o hacia atrás). Además, los modelos más grandes no son un problema, ya que cada nodo trabaja en una subsección del modelo en los mismos datos de entrenamiento.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [usar destinos de proceso para el entrenamiento de modelos](how-to-set-up-training-targets.md) con el SDK para Python.
* Para ver un ejemplo técnico, consulte el [escenario de arquitectura de referencia](/azure/architecture/reference-architectures/ai/training-deep-learning).
* En la [Guía de entrenamiento de GPU distribuido](how-to-train-distributed-gpu.md) encontrará sugerencias para MPI, TensorFlow y PyTorch. 
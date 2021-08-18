---
title: Integración del aprendizaje automático de código abierto
titleSuffix: Azure Machine Learning
description: Aprenda a usar plataformas de aprendizaje automático de Python de código abierto para entrenar, implementar y administrar soluciones completas de aprendizaje automático en Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 8ec9aaf2222924718ad21cf0654d138be22c2d19
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114293386"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integración de código abierto con proyectos de Azure Machine Learning

Puede entrenar, implementar y administrar el proceso de aprendizaje automático de un extremo a otro en Azure Machine Learning mediante plataformas y bibliotecas de aprendizaje automático de Python de código abierto.  Use herramientas de desarrollo, como cuadernos de Jupyter Notebook y Visual Studio Code, para aprovechar sus modelos y scripts existentes en Azure Machine Learning.  

En este artículo, obtendrá más información sobre estas plataformas y bibliotecas de código abierto.

## <a name="train-open-source-machine-learning-models"></a>Entrenamiento de modelos de Machine Learning de código abierto

El proceso de entrenamiento de aprendizaje automático implica la aplicación de algoritmos a los datos con el fin de lograr una tarea o resolver un problema. En función del problema, puede elegir diferentes algoritmos que se ajusten mejor a la tarea y los datos. Para obtener más información sobre las distintas ramas del aprendizaje automático, consulte el [artículo sobre aprendizaje profundo y aprendizaje automático](./concept-deep-learning-vs-machine-learning.md) y la [hoja de características de los algoritmos de Machine Learning](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Conservación de la privacidad de los datos con privacidad diferencial

Para entrenar un modelo de Machine Learning, necesitará datos. A veces, los datos son confidenciales y es importante asegurarse de que los datos sean seguros y privados. La privacidad diferencial es una técnica para conservar la confidencialidad de la información de un conjunto de datos. Para obtener más información, consulte el artículo sobre [conservación de la privacidad de datos](concept-differential-privacy.md). 

Los kits herramientas de privacidad diferencial de código abierto, como [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python), ayudan a [conservar la privacidad de los datos](how-to-differential-privacy.md) en las soluciones de Azure Machine Learning.

### <a name="classical-machine-learning-scikit-learn"></a>Entrenamiento de aprendizaje automático clásico: scikit-learn

En el caso de tareas de entrenamiento que impliquen tareas de algoritmos de aprendizaje automático clásico, como la clasificación, la agrupación en clústeres y la regresión, podría usar algo como Scikit-learn. Para aprender a entrenar un modelo de clasificación de flores, consulte el [artículo sobre cómo entrenar con Scikit-learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Redes neuronales: PyTorch, TensorFlow, Keras

Los algoritmos de aprendizaje automático de código abierto denominados redes neuronales, un subconjunto de aprendizaje automático, resultan útiles para entrenar modelos de aprendizaje profundo en Azure Machine Learning.

Los marcos de aprendizaje profundo y las guías de procedimientos de código abierto incluyen:

 *  [PyTorch](https://github.com/pytorch/pytorch): [entrenamiento de un modelo de clasificación de imágenes de aprendizaje profundo con aprendizaje por transferencia](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [reconocimiento de dígitos manuscritos con TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [creación de una red neuronal para analizar imágenes con Keras](how-to-train-keras.md)

El entrenamiento de un modelo de aprendizaje profundo desde cero a menudo requiere grandes cantidades de tiempo, datos y recursos de proceso. Puede abreviar el proceso de entrenamiento mediante el aprendizaje por transferencia. El aprendizaje por transferencia es una técnica que aplica los conocimientos que se adquieren al resolver un problema en un problema diferente, pero relacionado. Esto significa que puede tomar un modelo existente y reasignarlo. Consulte el artículo [Aprendizaje profundo frente a aprendizaje automático](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) para más información sobre el aprendizaje por transferencia.

### <a name="reinforcement-learning-ray-rllib"></a>Aprendizaje de refuerzo: Ray RLLib

El aprendizaje de refuerzo es una técnica de inteligencia artificial que entrena modelos mediante acciones, estados y recompensas: Los agentes de aprendizaje de refuerzo aprenden a tomar un conjunto de acciones predefinidas que maximizan las recompensas especificadas en función del estado actual del entorno. 

El proyecto [Ray RLLib](https://github.com/ray-project/ray) tiene un conjunto de características que ofrecen alta escalabilidad a lo largo del proceso de entrenamiento. El proceso iterativo requiere mucho tiempo y recursos, ya que los agentes de aprendizaje por refuerzo intentan aprender la manera óptima de lograr una tarea.  Ray RLLib también admite de forma nativa marcos de aprendizaje profundo como TensorFlow y PyTorch.  

Para aprender usar Ray RLLib con Azure Machine Learning, consulte el artículo sobre [procedimientos para entrenar un modelo de aprendizaje por refuerzo](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Supervisión del rendimiento del modelo: TensorBoard

El entrenamiento de uno o varios modelos requiere la visualización y la inspección de las métricas deseadas para asegurarse de que el modelo funciona según lo previsto. Puede [usar TensorBoard en Azure Machine Learning para hacer un seguimiento de las métricas del experimento y visualizarlas](./how-to-monitor-tensorboard.md).

### <a name="frameworks-for-interpretable-and-fair-models"></a>Marcos para modelos interpretables y equilibrados

Los sistemas de aprendizaje automático se usan en diferentes áreas de la sociedad, como la banca, la educación y la atención sanitaria. Por ello, es importante que esos sistemas sean responsables de las predicciones y recomendaciones que realizan para evitar consecuencias imprevistas.

Marcos de código abierto como [InterpretML](https://github.com/interpretml/interpret/) y Fairlearn (https://github.com/fairlearn/fairlearn) trabajan con Azure Machine Learning para crear modelos de aprendizaje automático más transparentes y equitativos.

Para obtener más información sobre cómo crear modelos interpretables y equilibrados, consulte los siguientes artículos:

- [Interpretación del modelo de Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpretación de los modelos de Machine Learning](how-to-machine-learning-interpretability-aml.md)
- [Explicación de los modelos de AutoML](how-to-machine-learning-interpretability-automl.md)
- [Mitigación de la equidad en los modelos de Machine Learning](concept-fairness-ml.md)
- [Uso de Azure Machine Learning con equidad del modelo de recursos](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Implementación de modelo

Una vez que los modelos están entrenados y listos para producción, tiene que elegir cómo implementarlos. Azure Machine Learning proporciona varios destinos de implementación. Para obtener más información, consulte el [artículo sobre cómo y dónde llevar a cabo la implementación](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Estandarización de formatos de modelo con ONNX

Después del entrenamiento, el contenido del modelo, como los parámetros aprendidos, se serializa y se guarda en un archivo. Cada marco tiene su propio formato de serialización. Cuando se trabaja con diferentes marcos y herramientas, hay que implementar modelos según los requisitos del marco. Para estandarizar este proceso, puede usar el formato Open Neural Network Exchange (ONNX). ONNX es un formato de código abierto para modelos de inteligencia artificial. ONNX admite la interoperabilidad entre marcos. Esto significa que puede entrenar un modelo en uno de los muchos marcos de aprendizaje automático que son más conocidos, como PyTorch, convertirlo a un formato ONNX y consumir el modelo de ONNX en un marco de trabajo diferente, como ML.NET.

Para obtener más información sobre ONNX y cómo utilizar modelos de ONNX, consulte los siguientes artículos:

- [Creación y aceleración de modelos de ML con ONNX](concept-onnx.md)
- [Uso de modelos de ONNX en aplicaciones .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Empaquetado e implementación de contenedores

Las tecnologías de contenedor como Docker son una manera de implementar modelos como servicios web. Los contenedores ofrecen una manera independiente de la plataforma y los recursos para crear y organizar entornos de software reproducibles. Con estas tecnologías básicas, puede usar [entornos preconfigurados](./how-to-use-environments.md), [imágenes de contenedor preconfiguradas](./how-to-deploy-custom-container.md) o las personalizadas para implementar los modelos de Machine Learning en, por ejemplo, [clústeres de Kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python). En el caso de flujos de trabajo intensivos de GPU, puede usar herramientas como NVIDIA Triton Inference Server para [realizar predicciones con GPU](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Implementaciones seguras con cifrado homomórfico

La protección de las implementaciones es una parte importante del proceso de implementación. Para [implementar servicios de inferencia cifrados](how-to-homomorphic-encryption-seal.md), use la biblioteca de Python de código abierto `encrypted-inference`. El paquete `encrypted inferencing` proporciona enlaces basados en [Microsoft SEAL](https://github.com/Microsoft/SEAL), una biblioteca de cifrado homomórfico.

## <a name="machine-learning-operations-mlops"></a>Operaciones de Machine Learning (MLOps)

Operaciones de Machine Learning (MLOps), también conocido como DevOps para aprendizaje automático, permite crear flujos de trabajo de aprendizaje automático más transparentes, resistentes y reproducibles. Consulte el [artículo sobre qué es MLOps](./concept-model-management-and-deployment.md) para obtener más información sobre MLOps. 

Mediante el uso de prácticas de DevOps como la integración continua (CI) y la implementación continua (CD), puede automatizar el ciclo de vida de aprendizaje automático de un extremo a otro y capturar datos de gobernanza en torno a ellos. Puede definir la [canalización de CI/CD de aprendizaje automático con acciones de GitHub](./how-to-github-actions-machine-learning.md) para ejecutar tareas de entrenamiento e implementación de Azure Machine Learning. 

La captura de dependencias de software, métricas, metadatos, datos y versiones de modelos son una parte importante del proceso de MLOps para construir canalizaciones transparentes, reproducibles y auditables. Para esta tarea, puede [usar MLFlow en Azure Machine Learning](how-to-use-mlflow.md) y también decidir cuándo [entrenar modelos de Machine Learning en Azure Databricks](./how-to-use-mlflow-azure-databricks.md). También puede [implementar modelos de MLflow como un servicio web de Azure](how-to-deploy-mlflow-models.md).
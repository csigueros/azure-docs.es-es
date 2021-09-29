---
title: Creación y entrenamiento de modelos
titleSuffix: Azure Machine Learning
description: Aprenda a entrenar modelos con Azure Machine Learning. Explore los distintos métodos de aprendizaje y elija el adecuado para su proyecto.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/23/2021
ms.custom: devx-track-python
ms.openlocfilehash: 6e75d2a7a7bfbe08447df98f170ba470e3503ebe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601394"
---
# <a name="train-models-with-azure-machine-learning"></a>Entrenamiento de modelos con Azure Machine Learning

Azure Machine Learning proporciona varias maneras de entrenar los modelos, desde soluciones en las que se da prioridad al código con el SDK hasta soluciones con poco código, como el aprendizaje automático automatizado y el diseñador visual. Use la lista siguiente para determinar el método de entrenamiento adecuado para usted:

+ [SDK de Azure Machine Learning para Python](#python-sdk): el SDK de Python proporciona varias maneras de entrenar modelos, cada una con distintas funcionalidades.

    | Método de entrenamiento | Descripción |
    | ----- | ----- |
    | [Configuración de ejecución](#run-configuration) | Una **manera típica de entrenar modelos** es usar un script de entrenamiento y una configuración de ejecución. La configuración de ejecución proporciona la información necesaria para configurar el entorno de entrenamiento que se usa para entrenar el modelo. Puede especificar el script de entrenamiento, el destino de proceso y el entorno de aprendizaje automático de Azure en la configuración de ejecución y ejecutar un trabajo de entrenamiento. |
    | [Aprendizaje automático automatizado](#automated-machine-learning) | El aprendizaje automático automatizado el permite **entrenar modelos sin un amplio conocimiento de programación ni de ciencia de datos**. En el caso de las personas con conocimientos de programación y de ciencia de datos, proporciona una manera de ahorrar tiempo y recursos mediante la automatización de la selección de algoritmos y la optimización de los hiperparámetros. No tiene que preocuparse de definir una configuración de ejecución al usar el aprendizaje automático automatizado. |
    | [Canalización de aprendizaje automático](#machine-learning-pipeline) | Las canalizaciones no son un método de entrenamiento distinto, sino una **manera de definir un flujo de trabajo con pasos modulares y reutilizables**, que puede incluir el entrenamiento como parte del flujo de trabajo. Las canalizaciones de aprendizaje automático admiten el uso de aprendizaje automático automatizado y configuración de ejecución para entrenar modelos. Como las canalizaciones no se centran específicamente en el entrenamiento, las razones para usar una canalización son más variadas que los demás métodos de entrenamiento. Por lo general, puede usar una canalización cuando:<br>* Quiere **programar procesos desatendidos**, como trabajos de entrenamiento de larga duración o la preparación de los datos.<br>* Use **varios pasos** coordinados entre ubicaciones de almacenamiento y recursos de proceso heterogéneos.<br>* Use la canalización como una **plantilla reutilizable** para escenarios específicos, como el reentrenamiento o la puntuación por lotes.<br>* **Haga seguimiento y realice versiones de sus orígenes de datos, entradas y salidas** del flujo de trabajo.<br>* El flujo de trabajo **lo implementan distintos equipos que trabajan en pasos específicos de manera independiente**. Luego, los pasos se pueden reunir en una canalización para implementar el flujo de trabajo. |

+ **Diseñador**: el diseñador de Azure Machine Learning proporciona un punto de entrada sencillo al aprendizaje automático para crear una prueba de conceptos o para los usuarios que no tengan mucha experiencia en la codificación. Permite entrenar modelos mediante una interfaz de usuario basada en web de arrastrar y colocar. Puede usar el código de Python como parte del diseño o entrenar modelos sin necesidad de escribir nada de código.

+ **CLI de Azure**: la CLI de Machine Learning proporciona comandos para tareas comunes con Azure Machine Learning y se usa a menudo para las **tareas de script y automatización**. Por ejemplo, una vez que haya creado una canalización o un script de entrenamiento, puede usar la CLI de Azure para iniciar una ejecución de entrenamiento según una programación o cuando se actualicen los archivos de datos usados para el entrenamiento. En el caso de los modelos de entrenamiento, proporciona comandos que envían trabajos de entrenamiento. Puede enviar trabajos mediante configuraciones de ejecución o canalizaciones.

Cada uno de estos métodos de entrenamiento puede usar distintos tipos de recursos de proceso para el entrenamiento. En conjunto, estos recursos se conocen como [__destinos de proceso__](concept-azure-machine-learning-architecture.md#compute-targets). Un destino de proceso puede ser una máquina local o un recurso de nube, como una instancia de proceso de Azure Machine Learning, Azure HDInsight o una máquina virtual remota.

## <a name="python-sdk"></a>SDK de Python

El SDK de Azure Machine Learning para Python permite compilar y ejecutar flujos de trabajo de aprendizaje automático con Azure Machine Learning. Puede interactuar con el servicio desde una sesión de Python interactiva, instancias de Jupyter Notebook, Visual Studio Code u otro IDE.

* [¿Qué es el SDK de Azure Machine Learning para Python?](/python/api/overview/azure/ml/intro)
* [Instalación o actualización del SDK](/python/api/overview/azure/ml/install)
* [Configuración de un entorno de desarrollo para Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuración de ejecución

Se puede definir un trabajo de entrenamiento genérico con Azure Machine Learning mediante [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig). A continuación, se usa la configuración de ejecución del script junto con los scripts de entrenamiento para entrenar un modelo en un destino de proceso.

Puede empezar con una configuración de ejecución para el equipo local y, a continuación, cambiar a una para un destino de proceso basado en la nube según sea necesario. Al cambiar el destino de proceso, solo se cambia la configuración de ejecución que se usa. Una ejecución también registra información sobre el trabajo de entrenamiento, como las entradas, las salidas y los registros.

* [¿En qué consiste una configuración de ejecución?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Entrenamiento del primer modelo de Machine Learning](tutorial-1st-experiment-sdk-train.md)
* [Ejemplos: ejemplos de entrenamiento de modelos de Jupyter Notebook y Phyton](https://github.com/Azure/azureml-examples)
* [Cómo: Configurar una ejecución de entrenamiento](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automated Machine Learning

Defina las iteraciones, la configuración de hiperparámetros, la caracterización y otras opciones. Durante el entrenamiento, Azure Machine Learning intenta diferentes algoritmos y parámetros en paralelo. El entrenamiento se detiene una vez que alcanza los criterios de salida definidos.

> [!TIP]
> Además del SDK de Python, también puede usar el aprendizaje automático automatizado a través de [Azure Machine Learning Studio](https://ml.azure.com).

* [Descripción del aprendizaje automático](concept-automated-ml.md)
* [Tutorial: Creación del primer modelo de clasificación con el aprendizaje automático automatizado](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Uso del aprendizaje automático para crear predecir tarifas de taxi](tutorial-auto-train-models.md)
* [Ejemplos: ejemplos de Jupyter Notebook para el aprendizaje automático automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Cómo: Configuración de experimentos de ML automatizado en Python](how-to-configure-auto-train.md)
* [Cómo: Entrenamiento automático de un modelo de previsión de series temporales](how-to-auto-train-forecast.md)
* [Cómo: Cree, explore e implemente experimentos de aprendizaje automático automatizado con Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Canalización de aprendizaje automático

Las canalizaciones de aprendizaje automático pueden usar los métodos de entrenamiento mencionados anteriormente. Las canalizaciones tienen más que ver con la creación de un flujo de trabajo, por lo que abarcan algo más que solo el entrenamiento de los modelos. En una canalización, puede entrenar un modelo mediante el aprendizaje automático automatizado o las configuraciones de ejecución.

* [¿Qué son las canalizaciones de Machine Learning en Azure Machine Learning?](concept-ml-pipelines.md)
* [Creación y ejecución de canalizaciones de Machine Learning con el SDK de Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
* [Tutorial: Uso de canalizaciones de Azure Machine Learning para la puntuación por lotes](tutorial-pipeline-batch-scoring-classification.md)
* [Ejemplos: ejemplos de Jupyter Notebook para las canalizaciones de aprendizaje automático](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Ejemplos: canalización con aprendizaje automático automatizado](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Descripción de lo que ocurre cuando se envía un trabajo de entrenamiento

El ciclo de vida de entrenamiento de Azure consta de los pasos siguientes:

1. Comprimir los archivos de la carpeta del proyecto y omitir los especificados en _.amlignore_ o _.gitignore_
1. Escalar verticalmente el clúster de proceso 
1. Compilar o descargar el Dockerfile en el nodo de proceso 
    1. El sistema calcula un valor de hash de: 
        - La imagen base 
        - Pasos personalizados de Docker (consulte [Implementación de un modelo con una imagen base de Docker personalizada](./how-to-deploy-custom-container.md))
        - El archivo YAML de definición de Conda (consulte [Creación y uso de entornos de software en Azure Machine Learning](./how-to-use-environments.md))
    1. El sistema utiliza este valor de hash como clave en una búsqueda de la instancia de Azure Container Registry (ACR) del área de trabajo.
    1. Si no la encuentra, busca una coincidencia en la instancia de ACR global.
    1. Si no existe, el sistema genera una imagen (que se almacenará en la memoria caché y se registrará en la instancia de ACR del área de trabajo).
1. Descargar el archivo de proyecto comprimido en el almacenamiento temporal del nodo de proceso.
1. Descomprimir el archivo de proyecto.
1. El nodo de proceso que ejecuta `python <entry script> <arguments>`.
1. Guardar registros, archivos de modelo y otros archivos escritos en `./outputs` en la cuenta de almacenamiento asociada con el área de trabajo.
1. Reducir verticalmente el proceso, incluida la eliminación del almacenamiento temporal. 

Si elige entrenar en la máquina local ("configurar como ejecución local"), no es necesario usar Docker. Si lo prefiere, puede usar Docker localmente (consulte [Configurar de la canalización de ML](./how-to-debug-pipelines.md) para ver un ejemplo).

## <a name="azure-machine-learning-designer"></a>Diseñador de Azure Machine Learning

El diseñador permite entrenar modelos mediante una interfaz de arrastrar y colocar en el explorador web.

+ [¿Qué es el diseñador?](concept-designer.md)
+ [Tutorial: Predicción del precio de un automóvil](tutorial-designer-automobile-price-train-score.md)

## <a name="azure-cli"></a>Azure CLI

La CLI de Machine Learning es una extensión para la CLI de Azure. Proporciona comandos de la CLI multiplataforma para trabajar con Azure Machine Learning. Por lo general, se usa la CLI para automatizar las tareas, como entrenar un modelo de aprendizaje automático.

* [Use la extensión de la CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps en Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>Código de VS

Puede usar la extensión de VS Code para ejecutar y administrar los trabajos de entrenamiento. Visite la [guía paso a paso sobre la administración de recursos de VS Code](how-to-manage-resources-vscode.md#experiments) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de cómo [configurar una ejecución de entrenamiento](how-to-set-up-training-targets.md).
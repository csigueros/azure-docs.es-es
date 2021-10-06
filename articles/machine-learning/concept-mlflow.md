---
title: MLflow y Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Obtenga información sobre MLflow con Azure Machine Learning para registrar métricas y artefactos de modelos de Machine Learning e implementar los modelos de Machine Learning como un servicio web.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 05/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 87c1bd275c2b407d1afe96c9e225c29edb42060a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424623"
---
# <a name="mlflow-and-azure-machine-learning"></a>MLflow y Azure Machine Learning

[MLflow](https://www.mlflow.org) es una biblioteca de código abierto para administrar el ciclo de vida de los experimentos de aprendizaje automático.  El URI de seguimiento y la API de registro de MLflow, que colectivamente se denominan [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), es un componente de MLflow que registra y realiza el seguimiento de las métricas de ejecución de entrenamiento y de los artefactos del modelo, independientemente del entorno del experimento: local en el equipo, en un destino de proceso remoto, en una máquina virtual o en un clúster de Azure Databricks. 

De manera conjunta, MLflow Tracking y Azure Machine Learning permiten realizar el seguimiento de las métricas de ejecución de un experimento y almacenar los artefactos del modelo en el área de trabajo de Azure Machine Learning. Ese experimento se podría haber ejecutado localmente en el equipo, en un destino de proceso remoto, una máquina virtual o un [clúster de Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparación entre los clientes de MLflow y Azure Machine Learning

 En la tabla siguiente se resumen los distintos clientes que pueden usar Azure Machine Learning y sus respectivas funcionalidades.

 El Seguimiento de MLflow ofrece funciones de registro de métricas y almacenamiento de artefactos que de otra manera solo están disponibles a través del [SDK de Python de Azure Machine Learning](/python/api/overview/azure/ml/intro).

| Capacidad | Implementación y seguimiento de MLflow | SDK de Python de Azure Machine Learning |  CLI de Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Administración del área de trabajo |   | ✓ | ✓ | ✓ |
| Uso de almacenes de datos  |   | ✓ | ✓ | |
| Métricas de registro      | ✓ | ✓ |   | |
| Carga de artefactos | ✓ | ✓ |   | |
| Visualización de métricas     | ✓ | ✓ | ✓ | ✓ |
| Administración de procesos   |   | ✓ | ✓ | ✓ |
| Implementación de modelos    | ✓ | ✓ | ✓ | ✓ |
|Supervisión del rendimiento del modelo||✓|  |   |
| Detección del desfase de datos |   | ✓ |   | ✓ |


## <a name="track-experiments"></a>Seguimiento de experimentos

Con MLflow Tracking puede conectar Azure Machine Learning como back-end de los experimentos de MLflow. Al hacerlo, puede realizar las tareas siguientes:

+ Realizar un seguimiento de las métricas y los artefactos de los experimentos, así como registrarlos, en el [área de trabajo de Azure Machine Learning](./concept-azure-machine-learning-architecture.md#workspace). Si ya usa Seguimiento de MLflow para los experimentos, el área de trabajo proporciona una ubicación centralizada, segura y escalable para almacenar los modelos y las métricas de entrenamiento. 

+ Hacer un seguimiento de modelos y administrarlos en MLflow y el registro de modelos de Azure Machine Learning.

+ [Seguimiento de ejecuciones de entrenamiento de Azure Databricks](how-to-use-mlflow-azure-databricks.md).

Más información en [Seguimiento de modelos de Machine Learning con MLflow y Azure Machine Learning](how-to-use-mlflow.md). 

## <a name="train-mlflow-projects"></a>Entrenamiento de proyectos de MLflow

Puede usar el URI de seguimiento y la API de registro de MLflow, que colectivamente se conocen como MLflow Tracking, para enviar trabajos de entrenamiento con [proyectos de MLflow](https://www.mlflow.org/docs/latest/projects.html) y compatibilidad de back-end con Azure Machine Learning (versión preliminar). Puede enviar trabajos localmente con el seguimiento de Azure Machine Learning o migrar las ejecuciones a la nube, por ejemplo, a través de un [Proceso de Azure Machine Learning](./how-to-create-attach-compute-cluster.md).

Más información en [Entrenamiento de modelos de Machine Learning con proyectos de MLflow y Azure Machine Learning](how-to-train-mlflow-projects.md).


## <a name="deploy-mlflow-experiments"></a>Implementación de experimentos de MLflow

Puede [implementar el modelo de MLflow como servicio web de Azure](how-to-deploy-mlflow-models.md) para aprovechar las funcionalidades de detección de desfase de datos y de administración de modelos de Azure Machine Learning y aplicarlas a los modelos de producción.

## <a name="next-steps"></a>Pasos siguientes
* [Seguimiento de modelos de Machine Learning con MLflow y Azure Machine Learning](how-to-use-mlflow.md). 
* [Entrenamiento de modelos de Machine Learning con proyectos de MLflow y Azure Machine Learning](how-to-train-mlflow-projects.md).
* [Seguimiento de las ejecuciones de Azure Databricks con MLflow](how-to-use-mlflow-azure-databricks.md).
* [Implementación de modelos con MLflow](how-to-deploy-mlflow-models.md).



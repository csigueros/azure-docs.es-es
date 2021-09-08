---
title: Entornos mantenidos
titleSuffix: Azure Machine Learning
description: Obtenga información acerca de los entornos mantenidos de Azure Machine Learning, un conjunto de entornos preconfigurados que ayudan a reducir los tiempos de preparación del experimento y la implementación.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 07/08/2021
ms.openlocfilehash: cb78928badb067731f6e4fbed75a01346409b70a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451056"
---
# <a name="azure-machine-learning-curated-environments"></a>Entornos mantenidos de Azure Machine Learning

En este artículo se enumeran los entornos mantenidos disponibles en Azure Machine Learning. Los entornos mantenidos los proporciona Azure Machine Learning y están disponibles en el área de trabajo de forma predeterminada. Están respaldados por imágenes de Docker en caché que usan la versión más reciente del SDK de Azure Machine Learning, lo que reduce el costo de preparación de la ejecución y permite un tiempo de implementación más rápido. Use estos entornos para empezar a trabajar rápidamente con varios marcos de aprendizaje automático.

> [!NOTE]
> Esta lista está actualizada a julio de 2021. Use el [SDK de Python](how-to-use-environments.md), la [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) o [Estudio de Azure Machine Learning](how-to-manage-environments-in-studio.md) para obtener la lista más actualizada de entornos y sus dependencias. Para más información, consulte el [artículo sobre los entornos](how-to-use-environments.md#use-a-curated-environment). Después del lanzamiento de este conjunto nuevo, se ocultarán los entornos mantenidos anteriormente, pero se podrán seguir usando. 

## <a name="pytorch"></a>PyTorch

**Nombre**: AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu.  
**Descripción**: entorno de aprendizaje profundo con PyTorch que contiene el SDK de Python de Azure ML y paquetes adicionales de Python.  
**Configuración de Dockerfile**: el Dockerfile siguiente se puede personalizar para los flujos de trabajo personales:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.7

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
    pytorch=1.7.1 \
    torchvision=0.8.2 \
    torchaudio=0.7.2 \
    cudatoolkit=11.0 \
    nvidia-apex=0.1.0 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_PYTORCH=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="lightgbm"></a>LightGBM

**Nombre**: AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu.  
**Descripción**: entorno de aprendizaje automático con Scikit-learn, LightGBM, XGBoost y Dask que contiene el SDK de Python de Azure ML y paquetes adicionales.  
**Configuración de Dockerfile**: el Dockerfile siguiente se puede personalizar para los flujos de trabajo personales:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="sklearn"></a>Sklearn
**Nombre**: AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu.  
**Descripción**: entorno para tareas como regresión, agrupación en clústeres y clasificación con Scikit-learn. Contiene el SDK de Python de Azure ML y paquetes adicionales de Python.  
**Configuración de Dockerfile**: el Dockerfile siguiente se puede personalizar para los flujos de trabajo personales:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="tensorflow"></a>TensorFlow

**Nombre**: AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu.  
**Descripción**: entorno de aprendizaje profundo con Tensorflow que contiene el SDK de Python de Azure ML y paquetes adicionales de Python.  
**Configuración de Dockerfile**: el Dockerfile siguiente se puede personalizar para los flujos de trabajo personales:

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>Aprendizaje automático automatizado (AutoML)

Los flujos de trabajo de entrenamiento de canalización de Azure ML que usan AutoML seleccionan automáticamente un entorno mantenido en función del tipo de proceso y de si DNN está habilitado. AutoML proporciona los siguientes entornos mantenidos:

| Nombre | Tipo de proceso | DNN habilitado |
| --- | --- | --- |
|AzureML-AutoML | CPU | No |
|AzureML-AutoML-DNN | CPU | Sí |
| AzureML-AutoML-GPU | GPU | No |
| AzureML-AutoML-DNN-GPU | GPU | Sí |

Para obtener más información sobre AutoML y las canalizaciones de Azure ML, consulte [Uso de ML automatizado en una canalización de Azure Machine Learning en Python](how-to-use-automlstep-in-pipelines.md).

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>Entornos mantenidos de solo inferencia e imágenes de Docker precompiladas

Aprenda sobre los entornos mantenidos de solo inferencia y la ruta de acceso MCR de las imágenes Docker precompiladas. Consulte [Imágenes de Docker precompiladas para inferencia](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference).

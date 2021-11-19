---
title: Grupos acelerados por GPU
description: Introducción a las GPU dentro de Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/10/2021
ms.author: midesa
ms.openlocfilehash: d4a4c38b61dcce01ec7fb73dc70605ce795b6412
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495289"
---
# <a name="gpu-accelerated-apache-spark-pools-in-azure-synapse-analytics"></a>Grupos de Apache Spark acelerados por GPU en Azure Synapse Analytics
Azure Synapse Analytics ahora admite grupos de Apache Spark acelerados con unidades de procesamiento gráfico (GPU). 

Mediante el uso de GPU NVIDIA, los científicos de datos y los ingenieros pueden reducir el tiempo necesario para ejecutar canalizaciones de integración de datos, puntuar modelos de Machine Learning, etc. En este artículo se describe cómo se pueden crear y usar grupos acelerados por GPU con Azure Synapse Analytics. En este artículo también se detallan los controladores y bibliotecas de GPU que están preinstalados como parte del entorno de ejecución acelerado por GPU.

> [!NOTE]
> Los grupos de Azure Synapse habilitados para GPU están actualmente en versión preliminar pública.

## <a name="create-a-gpu-accelerated-pool"></a>Creación de un grupo acelerado por GPU
Para simplificar el proceso de creación y administración de grupos, Azure Synapse se encarga de instalar previamente bibliotecas de bajo nivel y de configurar todos los requisitos de red complejos entre nodos de ejecución. Esta integración permite a los usuarios empezar a trabajar con grupos acelerados por GPU en tan solo unos minutos. Para más información sobre cómo crear un grupo acelerado por GPU, puede visitar el inicio rápido sobre cómo [crear un grupo acelerado por GPU](../quickstart-create-apache-gpu-pool-portal.md).

> [!NOTE]
>  - Los grupos acelerados por GPU se pueden crear en áreas de trabajo ubicadas en las regiones Este de EE. UU., Este de Australia y Norte de Europa.
>  - Los grupos acelerados por GPU solo están disponibles con el entorno de ejecución de Apache Spark 3.
>  - Es posible que tenga que solicitar un [aumento del límite](./apache-spark-rapids-gpu.md#quotas-and-resource-constraints-in-azure-synapse-gpu-enabled-pools) para crear clústeres habilitados para GPU. 
 
## <a name="gpu-accelerated-runtime"></a>Entorno de ejecución acelerado por GPU

### <a name="nvidia-gpu-driver-cuda-and-cudnn"></a>Controlador de GPU de NVIDIA, CUDA y cuDNN
Azure Synapse Analytics ofrece ahora grupos de Apache Spark acelerados por GPU, que incluyen varias bibliotecas y configuraciones de NVIDIA. De forma predeterminada, Azure Synapse Analytics instala el controlador NVIDIA y las bibliotecas necesarias para usar GPU en las instancias de trabajo y del controlador de Spark:
 - CUDA 11.2
 - libnccl2=2.8.4
 - libnccl-dev=2.8.4
 - libcudnn8=8.1.1 
 - libcudnn8-dev=8.1.1

> [!NOTE]
> Este software contiene código fuente proporcionado por NVIDIA Corporation. En concreto, para admitir los grupos acelerados por GPU, los grupos de Apache Spark de Azure Synapse incluyen código de [ejemplos de CUDA](https://docs.nvidia.com/cuda/eula/#nvidia-cuda-samples-preface).

### <a name="nvidia-end-user-license-agreement-eula"></a>Contrato de licencia para el usuario final (CLUF) de NVIDIA
Al seleccionar una opción de hardware acelerado por GPU en Synapse Spark, acepta implícitamente los términos y condiciones descritos en el CLUF de NVIDIA con respecto a lo siguiente:
  - CUDA 11.2: [CLUF :: Documentación del kit de herramientas de CUDA (nvidia.com)](https://docs.nvidia.com/cuda/eula/index.html)
  - libnccl2=2.8.4: [nccl/LICENSE.txt en principal · NVIDIA/nccl (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libnccl-dev=2.8.4: [nccl/LICENSE.txt en principal · NVIDIA/nccl (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libcudnn8=8.1.1: [Contrato de licencia de software :: Documentación de NVIDIA Deep Learning cuDNN](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - libcudnn8-dev=8.1.1: [Contrato de licencia de software :: Documentación de NVIDIA Deep Learning cuDNN](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - Las bibliotecas CUDA, NCCL y cuDNN, y el [contrato de licencia para el usuario final de NVIDIA (con el complemento NCCL)](https://docs.nvidia.com/deeplearning/nccl/sla/index.html#overview) para la biblioteca NCCL

## <a name="accelerate-etl-workloads"></a>Aceleración de las cargas de trabajo de ETL
Con la compatibilidad integrada con el [acelerador RAPIDS para Apache Spark](https://nvidia.github.io/spark-rapids/) de NVIDIA, los grupos de Spark acelerados por GPU en Azure Synapse pueden proporcionar mejoras de rendimiento significativas en comparación con los puntos de referencia analíticos estándar sin necesidad de realizar ningún cambio en el código. Basado en NVIDIA CUDA y UCX, NVIDIA RAPIDS permite órdenes aleatorios de Spark, operaciones de DataFrame y SQL acelerados por GPU. Dado que no se requiere ningún cambio en el código para aprovechar estas aceleraciones, los usuarios también pueden acelerar sus canalizaciones de datos que se basan en la indexación Hyperspace de Microsoft o Delta Lake de Linux Foundation. 

Para más información sobre cómo usar el acelerador RAPIDS de NVIDIA con un grupo acelerado por GPU en Azure Synapse Analytics, visite esta guía sobre cómo [mejorar el rendimiento con RAPIDS](apache-spark-rapids-gpu.md).

## <a name="improve-machine-learning-scoring-workloads"></a>Mejora de las cargas de trabajo de puntuación de aprendizaje automático
Muchas organizaciones dependen de la ejecución frecuente de trabajos de puntuación por lotes de gran envergadura en períodos de tiempo limitados. Para mejorar los trabajos de puntuación por lotes, también puede usar grupos de Spark acelerados por GPU con la [biblioteca Hummingbird](https://github.com/Microsoft/hummingbird) de Microsoft. Con Hummingbird, los usuarios pueden tomar sus modelos de ML tradicionales basados en árboles y compilarlos en cálculos de tensores. Hummingbird permite a los usuarios aprovechar fácilmente la aceleración de hardware nativo y marcos de redes neuronales para acelerar la puntuación de modelos de ML sin necesidad de volver a escribir sus modelos.  

## <a name="next-steps"></a>Pasos siguientes
- [Azure Synapse Analytics](../overview-what-is.md)

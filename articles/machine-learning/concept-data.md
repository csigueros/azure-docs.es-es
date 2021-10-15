---
title: Acceso seguro a datos en la nube
titleSuffix: Azure Machine Learning
description: Obtenga información acerca de cómo conectarse de manera segura al almacenamiento de datos en Azure con los conjuntos de datos y almacenes de datos de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 1b439f65f2807d2d076363938169631651b85d20
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430162"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Acceso seguro a datos en Azure Machine Learning

Azure Machine Learning facilita la conexión con los datos en la nube. Proporciona una capa de abstracción en el servicio de almacenamiento subyacente, por lo que puede acceder de forma segura a los datos y trabajar con ellos sin tener que escribir código específico para su tipo de almacenamiento. Azure Machine Learning también ofrece las siguientes funcionalidades de datos:

*    Interoperabilidad con DataFrames de Pandas y Spark
*    Control de versiones y seguimiento del linaje de datos
*    Etiquetado de datos 
*    Supervisión del desfase de datos
    
## <a name="data-workflow"></a>Flujo de trabajo de datos

Cuando esté listo para usar los datos de la solución de almacenamiento basada en la nube, se recomienda el siguiente flujo de trabajo de entrega de datos. En este flujo de trabajo se supone que tiene una [cuenta de Azure Storage ](../storage/common/storage-account-create.md?tabs=azure-portal) y datos en un servicio de almacenamiento basado en la nube en Azure. 

1. Cree un [almacén de datos de Azure Machine Learning](#datastores) para almacenar la información de conexión en Azure Storage.

2. En ese almacén de datos, cree un [conjunto de datos de Azure Machine Learning](#datasets) para que apunte a un archivo o archivos específicos del almacenamiento subyacente. 

3. Para usar ese conjunto de datos en el experimento de aprendizaje automático; puede:
    1. montarlo en el destino de proceso del experimento para el entrenamiento del modelo

        **OR** 

    1. consumirlo directamente en soluciones de Azure Machine Learning como las ejecuciones de experimentos de aprendizaje automático automatizado (ML automatizado), las canalizaciones de aprendizaje automático o el [diseñador de Azure Machine Learning](concept-designer.md).

4. Cree [monitores de conjunto de datos](#drift) para el conjunto de datos de salida del modelo con el fin de detectar desfases de datos. 

5. Si se detecta un desfase de datos, actualice el conjunto de datos de entrada y vuelva a entrenar el modelo como corresponde.

En el siguiente diagrama se ofrece una demostración visual de este flujo de trabajo recomendado.

![En el diagrama se muestra el servicio Azure Storage que fluye a un almacén de datos, que fluye a un conjunto de datos. El conjunto de datos fluye al entrenamiento del modelo, que fluye al desfase de datos, que fluye de vuelta al conjunto de datos.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Conexión al almacenamiento con almacenes de datos

Los almacenes de datos de Azure Machine Learning conservan de forma segura la información de conexión en el almacenamiento de datos de Azure, por lo que no tiene que codificarla en los scripts. [Registre y cree un almacén de datos](how-to-access-data.md) para conectarse fácilmente a la cuenta de almacenamiento y acceder a los datos del servicio de almacenamiento subyacente. 

A continuación se indican los servicios de almacenamiento basados en la nube de Azure que se pueden registrar como almacenes de datos:

+ Azure Blob Container
+ Recurso compartido de archivos de Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Sistema de archivos de Databricks
+ Azure Database for MySQL

>[!TIP]
> La funcionalidad disponible con carácter general para crear almacenes de datos requiere la autenticación basada en credenciales para acceder a los servicios de almacenamiento, como una entidad de servicio o un token de firma de acceso compartido (SAS). Los usuarios que tienen acceso de *Lector* en el área de trabajo pueden acceder a estas credenciales. <br><br>Si esto supone un problema, [cree un almacén de datos que use el acceso a datos basado en identidad para los servicios de almacenamiento (versión preliminar)](how-to-identity-based-data-access.md). Esta funcionalidad es una característica [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) en versión preliminar y puede cambiar en cualquier momento.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>Datos de referencia en el almacenamiento con conjuntos de datos

Los conjuntos de datos de Azure Machine Learning no son copias de los datos. Mediante la creación de un conjunto de datos, puede crear una referencia a los datos en su servicio de almacenamiento, junto con una copia de sus metadatos. 

Dado que los conjuntos de datos se evalúan de forma diferida y los datos permanecen en su ubicación existente, usted:

* No generará ningún costo de almacenamiento adicional.
* No se arriesgará de forma no intencionada cambiando sus orígenes de datos originales.
* Mejorará las velocidades de rendimiento del flujo de trabajo de ML.

Para interactuar con los datos del almacenamiento, [cree un conjunto de datos](how-to-create-register-datasets.md) para empaquetar los datos en un objeto consumible para las tareas de aprendizaje automático. Registre el conjunto de datos en el área de trabajo para compartirlo y reutilizarlo en distintos experimentos sin las complejidades de la ingesta de datos.

Los conjuntos de datos se pueden crear a partir de archivos locales, direcciones URL públicas, instancias de [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) o servicios de almacenamiento de Azure mediante almacenes de datos. 

Hay 2 tipos de conjuntos de datos: 

+ [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) hace referencia a uno o varios archivos de los almacenes de datos o direcciones URL públicas. Si los datos ya están limpios y listos para su uso en experimentos de entrenamiento, puede [descargar o montar archivos](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) a los que hacen referencia los objetos FileDataset en el destino de proceso.

+ [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) representa los datos en formato tabular mediante el análisis del archivo o la lista de archivos proporcionados. Puede cargar un objeto TabularDataset en un dataframe de Pandas o Spark para manipularlo o limpiarlo mejor. Para obtener una lista completa de formatos de datos a partir de los cuales se pueden crear objetos TabularDataset, consulte la [clase TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

En la siguiente documentación se pueden encontrar funcionalidades adicionales de los conjuntos de datos:

+ [Versión y seguimiento](how-to-version-track-datasets.md) del linaje del conjunto de datos.
+ [Supervisión del conjunto de datos](how-to-monitor-datasets.md) para ayudar con la detección de desfases de datos.    

## <a name="work-with-your-data"></a>Trabajo con los datos

Con los conjuntos de datos puede realizar una serie de tareas de aprendizaje automático, gracias a la perfecta integración con las características de Azure Machine Learning. 

+ Crear un [proyecto de etiquetado de datos](#label).
+ Entrenar modelos de Machine Learning:
     + [experimentos de ML automatizado](how-to-use-automated-ml-for-ml-models.md)
     + el [diseñador](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebooks](how-to-train-with-datasets.md)
     + [Canalizaciones de Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
+ Acceder a los conjuntos de datos para la puntuación con la [inferencia por lotes](./tutorial-pipeline-batch-scoring-classification.md) en [canalizaciones de aprendizaje automático](./how-to-create-machine-learning-pipelines.md).
+ Configurar un monitor de conjunto de datos para la detección de [desfase de datos](#drift).

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Etiquetado de datos con proyectos de etiquetado de datos

El etiquetado de grandes cantidades de datos a menudo ha resultado un dolor de cabeza en los proyectos de aprendizaje automático. Los que incluyen un componente de Computer Vision, como la clasificación de imágenes o la detección de objetos, normalmente requieren miles de imágenes y sus etiquetas correspondientes.

Azure Machine Learning le proporciona una ubicación central para crear, administrar y supervisar proyectos de etiquetado. Los proyectos de etiquetado ayudan a coordinar los datos, las etiquetas y los miembros del equipo, lo que le permite administrar de forma más eficaz las tareas de etiquetado. Actualmente, las tareas admitidas son la clasificación de imágenes (de varias etiquetas y de varias clases) y la identificación de objetos mediante cuadros de límite.

Cree un [proyecto de etiquetado de imágenes](how-to-create-image-labeling-projects.md) o un [proyecto de etiquetado de texto](how-to-create-text-labeling-projects.md) y genere un conjunto de datos que puede usar en experimentos de aprendizaje automático.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Supervisión del rendimiento del modelo con desfase de datos

En el contexto de aprendizaje automático, el desfase de datos es el cambio en los datos de entrada del modelo que conduce a la degradación del rendimiento del modelo. Es uno de los principales motivos por los que la precisión del modelo se degrada con el tiempo. Por lo tanto, la supervisión del desfase de datos ayuda a detectar problemas de rendimiento del modelo.

Consulte el artículo sobre [creación de monitores de conjunto de datos](how-to-monitor-datasets.md) para obtener información sobre cómo detectar y alertar sobre el desfase de datos en los nuevos datos de un conjunto de datos.

## <a name="next-steps"></a>Pasos siguientes 

+ Cree un conjunto de datos en Azure Machine Learning Studio o con el SDK de Python [mediante estos pasos](how-to-create-register-datasets.md).
+ Pruebe los ejemplos de entrenamiento del conjunto de datos con nuestros [cuadernos de ejemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
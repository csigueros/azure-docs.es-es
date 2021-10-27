---
title: Preparación de datos para tareas de Computer Vision
titleSuffix: Azure Machine Learning
description: Preparación de datos de imagen de aprendizaje automático automatizado de Azure Machine Learning para entrenar modelos de Computer Vision en la clasificación, la detección de objetos y la segmentación
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: template-how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7f2f3cf3c0af623c6c33e46fe9735a2af03c14c6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007086"
---
# <a name="prepare-data-for-computer-vision-tasks-with-automated-machine-learning-preview"></a>Preparación de datos para tareas de Computer Vision con aprendizaje automático automatizado (versión preliminar)

> [!IMPORTANT]
> La compatibilidad con el entrenamiento de modelos de aprendizaje automático automatizado de Computer Vision en Azure Machine Learning es una característica experimental en versión preliminar pública. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este artículo, aprenderá a preparar los datos de imagen para entrenar modelos de Computer Vision con [aprendizaje automático automatizado en Azure Machine Learning](concept-automated-ml.md). 

Para generar modelos para tareas de Computer Vision con aprendizaje automático automatizado, debe traer datos de imagen etiquetados como entrada para el entrenamiento del modelo en forma de [TabularDataset de Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset). 

Para asegurarse de que TabularDataset contiene el esquema aceptado para su consumo en aprendizaje automático automatizado, puede usar la herramienta de etiquetado de datos de Azure Machine Learning o usar un script de conversión. 

## <a name="prerequisites"></a>Requisitos previos

* Familiarícese con los [esquemas de archivos JSONL aceptados de los experimentos de Computer Vision de AutoML](reference-automl-images-schema.md).

* Los datos etiquetados que quiere usar para entrenar modelos de Computer Vision con aprendizaje automático automatizado.

## <a name="azure-machine-learning-data-labeling"></a>Etiquetado de datos de Azure Machine Learning

Si no tiene datos etiquetados, puede usar la [herramienta de etiquetado de datos](how-to-create-image-labeling-projects.md) de Azure Machine Learning para etiquetar manualmente las imágenes. Esta herramienta genera automáticamente los datos necesarios para el entrenamiento en el formato aceptado.

Ayuda a crear, administrar y supervisar tareas de etiquetado de datos para: 

+ Clasificación de imágenes (varias clases y varias etiquetas)
+ Detección de objetos (rectángulo de selección)
+ Segmentación de instancias (polígono)

Si ya tiene un proyecto de etiquetado de datos y quiere usar esos datos, puede [exportar los datos etiquetados como un objeto TabularDataset de Azure Machine Learning](how-to-create-image-labeling-projects.md#export-the-labels), que se puede usar directamente con el aprendizaje automático automatizado para entrenar modelos de Computer Vision.

## <a name="use-conversion-scripts"></a>Uso de scripts de conversión

Si ha etiquetado datos en formatos de datos conocidos de Computer Vision, como VOC o COCO, se proporcionan scripts para generar archivos JSONL para los datos de entrenamiento y validación. Para instrucciones y scripts detallados, consulte nuestros [cuadernos](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml).

Si los datos no siguen ninguno de los formatos mencionados anteriormente, puede usar su propio script para generar archivos de líneas JSON basados en esquemas definidos en [Esquema para archivos JSONL para experimentos de imagen de AutoML](reference-automl-images-schema.md).

Después de convertir los archivos de datos al formato JSONL aceptado, puede cargarlos en su cuenta de almacenamiento en Azure. 

## <a name="upload-the-jsonl-file-and-images-to-storage"></a>Carga del archivo JSONL y las imágenes en el almacenamiento

Para usar los datos en el entrenamiento de aprendizaje automático automatizado, cargue los datos en el [área de trabajo de Azure Machine Learning](concept-workspace.md) mediante un [almacén de datos](how-to-access-data.md). El almacén de datos proporciona un mecanismo para cargar o descargar datos en el almacenamiento en Azure e interactuar con ellos desde los destinos de proceso remotos.

Cargue el directorio primario completo que consta de imágenes y archivos JSONL en el almacén de datos predeterminado que se crea automáticamente tras la creación del área de trabajo.  Este almacén de datos se conecta al contenedor predeterminado de Azure Blob Storage que se creó como parte de la creación del área de trabajo.

```python
# Retrieve default datastore that's automatically created when we setup a workspace
ds = ws.get_default_datastore()
ds.upload(src_dir='./fridgeObjects', target_path='fridgeObjects')
```
Una vez realizada la carga de datos, puede crear un objeto [TabularDataset de Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset) y registrarlo en el área de trabajo para usarlo en el futuro como entrada para los experimentos de aprendizaje automático automatizado en los modelos de Computer Vision.

```python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'fridgeObjectsTrainingDataset'
# create training dataset
training_dataset = Dataset.Tabular.from_json_lines_files(path=ds.path("fridgeObjects/train_annotations.jsonl"),
                                                         set_column_types={"image_url": DataType.to_stream(ds.workspace)}
                                                        )
training_dataset = training_dataset.register( workspace=ws,name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

## <a name="next-steps"></a>Pasos siguientes

* [Entrenamiento de modelos de Computer Vision con aprendizaje automático automatizado](how-to-auto-train-image-models.md).
* [Entrenamiento de un modelo de detección de objetos pequeño con aprendizaje automático automatizado](how-to-use-automl-small-object-detect.md). 
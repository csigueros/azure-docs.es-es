---
title: Configuración de AutoML para Computer Vision
titleSuffix: Azure Machine Learning
description: Configure la funcionalidad ML automatizado de Azure Machine Learning para entrenar modelos de Computer Vision con el SDK de Python de Azure Machine Learning (versión preliminar).
services: machine-learning
author: swatig007
ms.author: swatig
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: automl
ms.date: 10/06/2021
ms.openlocfilehash: 944bab251d8e98da94a8d2e9d13418478e2dd46c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007091"
---
# <a name="set-up-automl-to-train-computer-vision-models-with-python-preview"></a>Configuración de AutoML para entrenar modelos de Computer Vision con Python (versión preliminar)

> [!IMPORTANT]
> Esta característica actualmente está en su versión preliminar pública. Esta versión preliminar se proporciona sin un contrato de nivel de servicio. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este artículo, aprenderá a entrenar modelos de Computer Vision en datos de imagen con ML automatizado en el [SDK de Python de Azure Machine Learning](/python/api/overview/azure/ml/).

ML automatizado admite el entrenamiento de modelos para tareas de Computer Vision, como la clasificación de imágenes, la detección de objetos y la segmentación de instancias. La creación de modelos de AutoML para tareas de Computer Vision se admite actualmente a través del SDK de Python de Azure Machine Learning. Se puede acceder a las ejecuciones, modelos y salidas de experimentación resultantes desde IU de Estudio de Azure Machine Learning. [Obtenga más información sobre ML automatizado para tareas de Computer Vision en datos de imagen.](concept-automated-ml.md)

> [!NOTE]
> ML automatizado para tareas de Computer Vision solo está disponible a través del SDK de Python de Azure Machine Learning. 

## <a name="prerequisites"></a>Requisitos previos

* Un área de trabajo de Azure Machine Learning. Para crear el área de trabajo, consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

* El SDK de Azure Machine Learning para Python instalado.
    Para instalar el SDK, puede: 
    * Crear una instancia de proceso, que instala automáticamente el SDK y está preconfigurada para flujos de trabajo de aprendizaje automático. Para más información, consulte [Creación y administración de una instancia de proceso de Azure Machine Learning](how-to-create-manage-compute-instance.md).

    * [Instale el paquete `automl`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), que incluye la [instalación predeterminada](/python/api/overview/azure/ml/install#default-install) del SDK.
    
    > [!NOTE]
    > Solo Python 3.6 y 3.7 son compatibles con el soporte de ML automatizado para tareas de Computer Vision. 

## <a name="select-your-task-type"></a>Selección de su tipo de tarea
ML automatizado para imágenes admite los siguientes tipos de tareas:


Tipo de tarea | sintaxis de configuración de AutoMLImage
---|---
 clasificación de imágenes | `ImageTask.IMAGE_CLASSIFICATION`
clasificación de imágenes con varias etiquetas | `ImageTask.IMAGE_CLASSIFICATION_MULTILABEL`
detección de objetos de imagen | `ImageTask.IMAGE_OBJECT_DETECTION`
segmentación de instancias de imagen| `ImageTask.IMAGE_INSTANCE_SEGMENTATION`

Este tipo de tarea es un parámetro obligatorio y se pasa mediante el parámetro `task` en `AutoMLImageConfig`. Por ejemplo:

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.automl.core.shared.constants import ImageTask
automl_image_config = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION)
```

## <a name="training-and-validation-data"></a>Datos de entrenamiento y validación

Para generar modelos para Computer Vision, debe traer datos de imagen etiquetados como entrada para el entrenamiento del modelo en forma de [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) de Azure Machine Learning. Puede usar un objeto `TabularDataset` que haya [exportado desde un proyecto de etiquetado de datos](how-to-create-labeling-projects.md#export-the-labels) o crear un nuevo `TabularDataset` con los datos de entrenamiento etiquetados. 

Si los datos de entrenamiento están en un formato diferente (por ejemplo, Pascal VOC o COCO), puede aplicar los scripts auxiliares incluidos con los cuadernos de ejemplo para convertir los datos a JSONL. Obtenga más información sobre la [preparación de datos para tareas de Computer Vision con ML automatizado](how-to-prepare-datasets-for-automl-images.md). 

> [!Warning]
> La creación de TabularDatasets solo se admite mediante el SDK para crear conjuntos de datos a partir de datos en formato JSONL para esta funcionalidad. En este momento no se admite la creación del conjunto de datos a través de la interfaz de usuario.


### <a name="jsonl-schema-samples"></a>Ejemplos de esquemas JSONL

La estructura del TabularDataset depende de la tarea que se vaya a realizar. Para los tipos de tareas de Computer Vision, consta de los siguientes campos:

Campo| Descripción
---|---
`image_url`| Contiene filepath como un objeto StreamInfo.
`image_details`|La información de metadatos de imagen consta de alto, ancho y formato. Este campo es opcional y, por tanto, puede existir o no.
`label`| Representación json de la etiqueta de imagen, basada en el tipo de tarea.

A continuación se muestra un archivo JSONL de ejemplo para la clasificación de imágenes:

```python
{
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label": "cat"
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.jpeg",
      "image_details":
      {
          "format": "jpeg",
          "width": "3456px",
          "height": "3467px"
      },
      "label": "dog"
  }
  ```

  El código siguiente es un archivo JSONL de ejemplo para la detección de objetos:

  ```python
  {
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label":
      {
          "label": "cat",
          "topX": "1",
          "topY": "0",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "true",
      }
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.png",
      "image_details":
      {
          "format": "jpeg",
          "width": "1230px",
          "height": "2356px"
      },
      "label":
      {
          "label": "dog",
          "topX": "0",
          "topY": "1",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "false",
      }
  }
  ```


### <a name="consume-data"></a>Consumo de datos

Una vez que los datos están en formato JSONL, puede crear un objeto TabularDataset con el código siguiente:

```python
from azureml.core import Dataset

training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/odFridgeObjects.jsonl'),
        set_column_types={'image_url': DataType.to_stream(ds.workspace)})
training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)
```

ML automatizado no impone ninguna restricción en el tamaño de los datos de entrenamiento o validación para las tareas de Computer Vision. El tamaño máximo del conjunto de datos solo está limitado por la capa de almacenamiento detrás del conjunto de datos (es decir, el almacén de blobs). No hay ningún número mínimo de imágenes o etiquetas. Sin embargo, se recomienda empezar con un mínimo de 10-15 ejemplos por etiqueta para asegurarse de que el modelo de salida está suficientemente entrenado. Cuanto mayor sea el número total de etiquetas o clases, más ejemplos necesitará por etiqueta.



Los datos de entrenamiento son obligatorios y se pasan mediante el parámetro `training_data`. Opcionalmente, puede especificar otro TabularDataset como conjunto de datos de validación que se usará para el modelo con el parámetro `validation_data` de AutoMLImageConfig. Si no se especifica ningún conjunto de datos de validación, el 20 % de los datos de entrenamiento se usará para la validación de forma predeterminada, a menos que pase el argumento `split_ratio` con un valor diferente.

Por ejemplo:

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(training_data=training_dataset)
```

## <a name="compute-to-run-experiment"></a>Proceso para ejecutar el experimento

Proporcione un [destino de proceso](concept-azure-machine-learning-architecture.md#compute-targets) para que ML automatizado realice el entrenamiento del modelo. Los modelos de ML automatizado para tareas de Computer Vision requieren SKU de GPU y son compatibles con las familias NC y ND. Se recomienda la serie NCsv3 (con GPU v100) para un entrenamiento más rápido. Un destino de proceso con una SKU de máquina virtual con varias GPU aprovecha varias GPU para acelerar también el entrenamiento. Además, al configurar un destino de proceso con varios nodos, puede realizar un entrenamiento del modelo más rápido a través del paralelismo al ajustar hiperparámetros para el modelo.

El destino de proceso es un parámetro obligatorio y se pasa mediante el parámetro `compute_target` de `AutoMLImageConfig`. Por ejemplo:

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(compute_target=compute_target)
```

## <a name="configure-model-algorithms-and-hyperparameters"></a>Configuración de algoritmos de modelo e hiperparámetros

Con la compatibilidad con tareas de Computer Vision, puede controlar el algoritmo del modelo y los hiperparámetros de barrido. Estos algoritmos de modelo y los hiperparámetros se pasan como espacio de parámetros para el barrido.

El algoritmo del modelo es necesario y se pasa a través del parámetro `model_name`. Puede especificar un único `model_name` o elegir entre varios. Además de controlar el algoritmo de modelo, también puede ajustar los hiperparámetros usados para el entrenamiento del modelo. Aunque muchos de los hiperparámetros expuestos son independientes del modelo, hay instancias en las que los hiperparámetros son específicos de la tarea o el modelo.

### <a name="supported-model-algorithms"></a>Algoritmos de modelo admitidos

En la tabla siguiente se resumen los modelos admitidos para cada tarea de Computer Vision. 

Tarea |  Algoritmos de modelo | Sintaxis de literal de cadena<br> ***`default_model`\**** indicado con \*
---|----------|----------
Clasificación de imágenes<br> (varias clases y varias etiquetas)| **MobileNet:** modelos ligeros para aplicaciones móviles <br> **ResNet**: redes residuales<br> **ResNeSt**: redes de atención dividida<br> **SE-ResNeXt50**: redes de compresión y excitación<br> **ViT**: redes de transformadores de visión| `mobilenetv2`   <br>`resnet18` <br>`resnet34` <br> `resnet50`  <br> `resnet101` <br> `resnet152`    <br> `resnest50` <br> `resnest101`  <br> `seresnext`  <br> `vits16r224` (pequeña) <br> **_`vitb16r224`\_** * (base) <br>`vitl16r224` (grande)|
Detección de objetos | **YOLOv5**: modelo de detección de objetos de una fase   <br>  **Faster RCNN ResNet FPN**: modelos de detección de objetos de dos fases  <br> **RetinaNet ResNet FPN**: desequilibrio de la clase de dirección con pérdida focal <br> <br>*Nota: Consulte el [hiperparámetro `model_size`](#model-specific-hyperparameters) para ver los tamaños de modelo YOLOv5.*| ***`yolov5`\**** <br> `fasterrcnn_resnet18_fpn` <br> `fasterrcnn_resnet34_fpn` <br> `fasterrcnn_resnet50_fpn` <br> `fasterrcnn_resnet101_fpn` <br> `fasterrcnn_resnet152_fpn` <br> `retinanet_resnet50_fpn` 
Segmentación de instancias | **MaskRCNN ResNet FPN**| `maskrcnn_resnet18_fpn` <br> `maskrcnn_resnet34_fpn` <br> **_`maskrcnn_resnet50_fpn`\_***  <br> `maskrcnn_resnet101_fpn` <br> `maskrcnn_resnet152_fpn` <br>`maskrcnn_resnet50_fpn`


### <a name="model-agnostic-hyperparameters"></a>Hiperparámetros independientes del modelo

En la tabla siguiente se describen los hiperparámetros que son independientes del modelo.

| Nombre de parámetro | Descripción | Valor predeterminado|
| ------------ | ------------- | ------------ |
| `number_of_epochs` | Número de épocas de entrenamiento. <br>Debe ser un entero positivo. |  15 <br> (excepto `yolov5`: 30) |
| `training_batch_size` | Tamaño del lote de entrenamiento.<br> Debe ser un entero positivo.  | Multiclase/multietiqueta: 78 <br>(excepto *vit-variants*: <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10)<br><br>Detección de objetos: 2 <br>(excepto `yolov5`: 16) <br><br> Segmentación de instancias: 2  <br> <br> *Nota: Los valores predeterminados son el tamaño de lote más grande que se puede usar en la memoria de GPU de 12 GiB*.|
| `validation_batch_size` | Tamaño del lote de validación.<br> Debe ser un entero positivo. | Multiclase/multietiqueta: 78 <br>(excepto *vit-variants*: <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`: 10)<br><br>Detección de objetos: 1 <br>(excepto `yolov5`: 16) <br><br> Segmentación de instancias: 1  <br> <br> *Nota: Los valores predeterminados son el tamaño de lote más grande que se puede usar en la memoria de GPU de 12 GiB*.|
| `grad_accumulation_step` | La acumulación de gradiente implica la ejecución de un número configurado de `grad_accumulation_step` sin actualizar los pesos del modelo mientras se acumulan los gradientes de esos pasos y, a continuación, el uso de gradientes acumulados para calcular las actualizaciones de peso. <br> Debe ser un entero positivo. | 1 |
| `early_stopping` | Habilite la lógica de detención temprana durante el entrenamiento. <br> Debe ser 0 o 1.| 1 |
| `early_stopping_patience` | Número mínimo de épocas o evaluaciones de validación sin<br>ninguna mejora de métrica principal antes de que se detenga la ejecución.<br> Debe ser un entero positivo. | 5 |
| `early_stopping_delay` | Número mínimo de épocas o evaluaciones de validación que se esperarán<br>antes de que se realice un seguimiento de la mejora de la métrica principal para la detención temprana.<br> Debe ser un entero positivo. | 5 |
| `learning_rate` | Velocidad de aprendizaje inicial. <br>Debe ser un valor float en el intervalo [0, 1]. | Varias clases: 0,01 <br>(excepto *vit-variants*: <br> `vits16r224`: 0,0125<br>`vitb16r224`: 0,0125<br>`vitl16r224`: 0,001) <br><br> Varias etiquetas: 0,035 <br>(excepto *vit-variants*:<br>`vits16r224`: 0,025<br>`vitb16r224`: 0,025 <br>`vitl16r224`: 0,002) <br><br> Detección de objetos: 0,005 <br>(excepto `yolov5`: 0,01) <br><br> Segmentación de instancias: 0,005  |
| `lr_scheduler` | Tipo de programador de velocidad de aprendizaje. <br> Debe ser `warmup_cosine` o `step`. | `warmup_cosine` |
| `step_lr_gamma` | Valor de gamma cuando el programador de velocidad de aprendizaje es `step`.<br> Debe ser un valor float en el intervalo [0, 1]. | 0.5 |
| `step_lr_step_size` | Valor del tamaño de paso cuando el programador de velocidad de aprendizaje es `step`.<br> Debe ser un entero positivo. | 5 |
| `warmup_cosine_lr_cycles` | Valor del ciclo de coseno cuando el programador de velocidad de aprendizaje es `warmup_cosine`. <br> Debe ser un valor float en el intervalo [0, 1]. | 0.45 |
| `warmup_cosine_lr_warmup_epochs` | Valor de las épocas de warmup cuando el programador de velocidad de aprendizaje es `warmup_cosine`. <br> Debe ser un entero positivo. | 2 |
| `optimizer` | Tipo de optimizador. <br> Debe ser `sgd`, `adam`, `adamw`.  | `sgd` |
| `momentum` | Valor de momentum cuando el optimizador es `sgd`. <br> Debe ser un valor float en el intervalo [0, 1]. | 0.9 |
| `weight_decay` | Valor de weight decay cuando el optimizador es `sgd`, `adam` o `adamw`. <br> Debe ser un valor float en el intervalo [0, 1]. | 1e-4 |
|`nesterov`| Habilite `nesterov` cuando el optimizador sea `sgd`. <br> Debe ser 0 o 1.| 1 |
|`beta1` | Valor de `beta1` cuando el optimizador es `adam` o `adamw`. <br> Debe ser un valor float en el intervalo [0, 1]. | 0.9 |
|`beta2` | Valor de `beta2` cuando el optimizador es `adam` o `adamw`.<br> Debe ser un valor float en el intervalo [0, 1]. | 0,999 |
|`amsgrad` | Habilite `amsgrad` cuando el optimizador sea `adam` o `adamw`.<br> Debe ser 0 o 1. | 0 |
|`evaluation_frequency`| Frecuencia para evaluar el conjunto de datos de validación para obtener puntuaciones de métricas. <br> Debe ser un entero positivo. | 1 |
|`split_ratio`| Si no se definen los datos de validación, especifica la proporción de división para dividir los datos del entrenamiento en subconjuntos aleatorios de entrenamiento y validación. <br> Debe ser un valor float en el intervalo [0, 1].| 0,2 |
|`checkpoint_frequency`| Frecuencia para almacenar puntos de control del modelo. <br> Debe ser un entero positivo. | Punto de control en época con la mejor métrica principal en la validación.|
|`layers_to_freeze`| Número de capas que se inmovilizarán para el modelo. Por ejemplo, pasar 2 como valor para `seresnext` significa inmovilizar layer0 y layer1 haciendo referencia a la siguiente información de capa de modelo admitida. <br> Debe ser un entero positivo. <br><br>`'resnet': [('conv1.', 'bn1.'), 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'mobilenetv2': ['features.0.', 'features.1.', 'features.2.', 'features.3.', 'features.4.', 'features.5.', 'features.6.', 'features.7.', 'features.8.', 'features.9.', 'features.10.', 'features.11.', 'features.12.', 'features.13.', 'features.14.', 'features.15.', 'features.16.', 'features.17.', 'features.18.'],`<br>`'seresnext': ['layer0.', 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'vit': ['patch_embed', 'blocks.0.', 'blocks.1.', 'blocks.2.', 'blocks.3.', 'blocks.4.', 'blocks.5.', 'blocks.6.','blocks.7.', 'blocks.8.', 'blocks.9.', 'blocks.10.', 'blocks.11.'],`<br>`'yolov5_backbone': ['model.0.', 'model.1.', 'model.2.', 'model.3.', 'model.4.','model.5.', 'model.6.', 'model.7.', 'model.8.', 'model.9.'],`<br>`'resnet_backbone': ['backbone.body.conv1.', 'backbone.body.layer1.', 'backbone.body.layer2.','backbone.body.layer3.', 'backbone.body.layer4.']` | sin valor predeterminado  |


### <a name="task-specific-hyperparameters"></a>Hiperparámetros específicos de la tarea

En la tabla siguiente se resumen los hiperparámetros para las tareas de clasificación de imágenes (de varias clases y de varias etiquetas).


| Nombre de parámetro       | Descripción           | Valor predeterminado  |
| ------------- |-------------|-----|
| `weighted_loss` | 0 para ninguna pérdida ponderada.<br>1 para pérdida ponderada con sqrt. (class_weights) <br> 2 para pérdida ponderada con class_weights. <br> Debe ser 0, 1 o 2. | 0 |
| `valid_resize_size` | Tamaño de imagen al que se va a cambiar el tamaño antes de recortar para el conjunto de datos de validación. <br> Debe ser un entero positivo. <br> <br> *Notas: <li> `seresnext` no toma un tamaño arbitrario. <li> La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño es demasiado grande*.  | 256  |
| `valid_crop_size` | Tamaño de recorte de imagen que se introduce en la red neuronal para el conjunto de datos de validación.  <br> Debe ser un entero positivo. <br> <br> *Notas: <li> `seresnext` no toma un tamaño arbitrario. <li> *ViT-variants* debe tener el mismo valor de `valid_crop_size` y `train_crop_size`. <li> La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño es demasiado grande*. | 224 |
| `train_crop_size` | Tamaño de recorte de imagen que se introduce en la red neuronal para el conjunto de datos de entrenamiento.  <br> Debe ser un entero positivo. <br> <br> *Notas: <li> `seresnext` no toma un tamaño arbitrario. <li> *ViT-variants* debe tener el mismo valor de `valid_crop_size` y `train_crop_size`. <li> La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño es demasiado grande*. | 224 |


Los siguientes hiperparámetros son para tareas de detección de objetos y segmentación de instancias.

> [!Warning]
> Estos parámetros no se admiten con el algoritmo `yolov5`.

| Nombre de parámetro       | Descripción           | Valor predeterminado  |
| ------------- |-------------|-----|
| `validation_metric_type` | Método de cálculo de métricas que se usará para las métricas de validación.  <br> Debe ser `none`, `coco`, `voc`, o `coco_voc`. | `voc` |
| `min_size` | Tamaño mínimo de la imagen que se va a volver a escalar antes de proporcionársela a la red troncal. <br> Debe ser un entero positivo. <br> <br> *Nota: La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño es demasiado grande*.| 600 |
| `max_size` | Tamaño máximo de la imagen que se va a volver a escalar antes de proporcionársela a la red troncal. <br> Debe ser un entero positivo.<br> <br> *Nota: La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño es demasiado grande*. | 1333 |
| `box_score_thresh` | Durante la inferencia, solo se devuelven propuestas con una puntuación de clasificación mayor que `box_score_thresh`. <br> Debe ser un valor float en el intervalo [0, 1].| 0,3 |
| `box_nms_thresh` | Umbral de supresión no máxima (NMS) para el encabezado de predicción. Se usa durante la inferencia.  <br>Debe ser un valor float en el intervalo [0, 1]. | 0.5 |
| `box_detections_per_img` | Número máximo de detecciones por imagen, para todas las clases. <br> Debe ser un entero positivo.| 100 |
| `tile_grid_size` | Tamaño de la cuadrícula que se usará para la colocación en mosaico de cada imagen. <br>*Nota: tile_grid_size no debe ser None para habilitar la lógica de [detección de objetos pequeños](how-to-use-automl-small-object-detect.md)*<br> Tupla de dos enteros pasados como una cadena. Ejemplo: --tile_grid_size "(3, 2)" | Sin valor predeterminado |
| `tile_overlap_ratio` | Proporción de superposición entre mosaicos adyacentes en cada dimensión. <br> Debe ser float en el intervalo de [0, 1) | 0,25 |
| `tile_predictions_nms_thresh` | Umbral IOU que se usará para realizar NMS al combinar predicciones de mosaicos e imágenes. Se usa en la validación o inferencia. <br> Debe ser float en el intervalo de [0, 1] | 0,25 |

### <a name="model-specific-hyperparameters"></a>Hiperparámetros específicos del modelo

En esta tabla se resumen los hiperparámetros específicos del algoritmo `yolov5`.

| Nombre de parámetro       | Descripción           | Valor predeterminado  |
| ------------- |-------------|----|
| `validation_metric_type` | Método de cálculo de métricas que se usará para las métricas de validación.  <br> Debe ser `none`, `coco`, `voc`, o `coco_voc`. | `voc` |
| `img_size` | Tamaño de la imagen para entrenamiento y validación. <br> Debe ser un entero positivo. <br> <br> *Nota: La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño es demasiado grande*. | 640 |
| `model_size` | Tamaño del modelo. <br> Debe ser `small`, `medium`, `large` o `xlarge`. <br><br> *Nota: La ejecución del entrenamiento puede llegar a CUDA OOM si el tamaño del modelo es demasiado grande*.  | `medium` |
| `multi_scale` | Habilitación de la imagen a escala múltiple variando el tamaño de la imagen en +/- 50 %. <br> Debe ser 0 o 1. <br> <br> *Nota: La ejecución del entrenamiento puede llegar a CUDA OOM si no hay suficiente memoria de GPU*. | 0 |
| `box_score_thresh` | Durante la inferencia, solo se devuelven propuestas con una puntuación mayor que `box_score_thresh`. La puntuación es la multiplicación de la puntuación de objetividad y la probabilidad de clasificación. <br> Debe ser un valor float en el intervalo [0, 1]. | 0,1 |
| `box_iou_thresh` | Umbral de IoU usado durante la inferencia en el procesamiento posterior de la supresión no máxima. <br> Debe ser un valor float en el intervalo [0, 1]. | 0.5 |


### <a name="data-augmentation"></a>Aumento de datos 

En general, el rendimiento del modelo de aprendizaje profundo a menudo puede mejorar con más datos. El aumento de datos es una técnica práctica para ampliar el tamaño y la variabilidad de los datos de un conjunto de datos que ayuda a evitar el sobreajuste y a mejorar la capacidad de generalización del modelo en datos no vistos. ML automatizado aplica diferentes técnicas de aumento de datos en función de la tarea de Computer Vision, antes de proporcionar las imágenes de entrada al modelo. Actualmente, no hay ningún hiperparámetro expuesto para controlar los aumentos de datos. 

|Tarea | Conjunto de datos afectado | Técnicas de aumento de datos aplicadas |
|-------|----------|---------|
|Clasificación de imágenes (varias clases y varias etiquetas) | Cursos <br><br><br> Validación y prueba| Cambio de tamaño y recorte aleatorios, volteo horizontal, vibración de color (brillo, contraste, saturación y matiz), normalización mediante la media y la desviación estándar de ImageNet por canal <br><br><br>Cambio de tamaño, recorte central, normalización |
|Detección de objetos, segmentación de instancias| Cursos <br><br> Validación y prueba |Recorte aleatorio alrededor de rectángulos delimitadores, expansión, volteo horizontal, normalización, cambio de tamaño <br><br><br>Normalización, cambio de tamaño
|Detección de objetos mediante yolov5| Cursos <br><br> Validación y prueba  |Mosaico, afín aleatorio (rotación, traslación, escala, distorsión), volteo horizontal <br><br><br> Cambio de tamaño de formato letterbox|

## <a name="configure-your-experiment-settings"></a>Establecer la configuración de experimento

Antes de realizar un barrido grande para buscar los modelos óptimos y los hiperparámetros, se recomienda probar los valores predeterminados para obtener una primera línea base. A continuación, puede explorar varios hiperparámetros para el mismo modelo antes de examinar varios modelos y sus parámetros. De este modo, puede emplear un enfoque más iterativo, ya que con varios modelos y varios hiperparámetros para cada uno, el espacio de búsqueda crece exponencialmente y necesita más iteraciones para encontrar configuraciones óptimas.

Si desea usar los valores de hiperparámetro predeterminados para un algoritmo determinado (por ejemplo, yolov5), puede especificar la configuración de la imagen de AutoML que se ejecuta de la siguiente manera:

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.train.hyperdrive import GridParameterSampling, choice
from azureml.automl.core.shared.constants import ImageTask

automl_image_config_yolov5 = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION,
                                               compute_target=compute_target,
                                               training_data=training_dataset,
                                               validation_data=validation_dataset,
                                               hyperparameter_sampling=GridParameterSampling({'model_name': choice('yolov5')}),
                                               iterations=1)
```

Una vez que haya creado un modelo de línea base, es posible que desee optimizar el rendimiento del modelo con el fin de realizar un barrido sobre el algoritmo del modelo y el espacio de hiperparámetros. Puede usar la siguiente configuración de ejemplo para realizar un barrido de los hiperparámetros de cada algoritmo, eligiendo entre un intervalo de valores para learning_rate, optimizer, lr_scheduler, etc., para generar un modelo con la métrica principal óptima. Si no se especifican los valores de hiperparámetro, se usan los valores predeterminados para el algoritmo especificado.

### <a name="primary-metric"></a>Métrica principal

La métrica principal usada para la optimización del modelo y el ajuste de hiperparámetros depende del tipo de tarea. Actualmente no se admite el uso de otros valores de métricas principales. 

* `accuracy` para IMAGE_CLASSIFICATION
* `iou` para IMAGE_CLASSIFICATION_MULTILABEL
* `mean_average_precision` para IMAGE_OBJECT_DETECTION
* `mean_average_precision` para IMAGE_INSTANCE_SEGMENTATION
    
### <a name="experiment-budget"></a>Presupuesto del experimento

Opcionalmente, puede especificar el presupuesto de tiempo máximo para el experimento de AutoML Vision mediante `experiment_timeout_hours`, la cantidad de tiempo en horas antes de que finalice el experimento. Si no se especifica ninguno, el tiempo de espera predeterminado del experimento es de siete días (máximo de 60 días).

## <a name="sweeping-hyperparameters-for-your-model"></a>Barrido de los hiperparámetros de un modelo

Al entrenar modelos de Computer Vision, el rendimiento del modelo depende en gran medida de los valores de hiperparámetro seleccionados. A menudo, es posible que desee optimizar los hiperparámetros para obtener un rendimiento óptimo.
Gracias a la compatibilidad con tareas de Computer Vision en ML automatizado, puede barrer hiperparámetros para encontrar la configuración óptima para el modelo. Esta característica aplica las funcionalidades de ajuste de hiperparámetros en Azure Machine Learning. [Aprenda a ajustar los hiperparámetros](how-to-tune-hyperparameters.md).

### <a name="define-the-parameter-search-space"></a>Definir el espacio de búsqueda de parámetros

Puede definir los algoritmos de modelo y los hiperparámetros para barrido en el espacio de parámetros. Consulte [Configuración de algoritmos de modelo e hiperparámetros](#configure-model-algorithms-and-hyperparameters) para obtener la lista de algoritmos de modelo e hiperparámetros admitidos para cada tipo de tarea. Consulte los [detalles sobre las distribuciones admitidas para hiperparámetros discretos y continuos](how-to-tune-hyperparameters.md#define-the-search-space).


### <a name="sampling-methods-for-the-sweep"></a>Métodos de muestreo para el barrido

Al realizar el barrido de hiperparámetros, debe especificar el método de muestreo que se usará para el barrido en el espacio de parámetros definido. Actualmente, se admiten los siguientes métodos de muestreo con el parámetro `hyperparameter_sampling`:

* [Muestreo aleatorio](how-to-tune-hyperparameters.md#random-sampling)
* [Muestreo de cuadrícula](how-to-tune-hyperparameters.md#grid-sampling) 
* [Muestreo bayesiano](how-to-tune-hyperparameters.md#bayesian-sampling) 
    
Debe tenerse en cuenta que actualmente solo el muestreo aleatorio admite espacios condicionales de hiperparámetros.


### <a name="early-termination-policies"></a>Directivas de terminación anticipada

Puede terminar de forma automática las series con un bajo rendimiento con la ayuda de una directiva de terminación anticipada. La terminación anticipada mejora la eficacia computacional, lo que ahorra recursos de proceso que, de lo contrario, se habrían invertido en configuraciones menos prometedoras. ML automatizado para imágenes admite las siguientes directivas de terminación anticipada mediante el parámetro `early_termination_policy`. Si no se especifica ninguna directiva de terminación, todas las configuraciones se ejecutan hasta su finalización.

* [Directiva de bandidos](how-to-tune-hyperparameters.md#bandit-policy)
* [Directiva de mediana de detención](how-to-tune-hyperparameters.md#median-stopping-policy)
* [Directiva de selección de truncamiento](how-to-tune-hyperparameters.md#truncation-selection-policy)

Obtenga más información [sobre cómo configurar la directiva de terminación anticipada para el barrido de hiperparámetros](how-to-tune-hyperparameters.md#early-termination).

### <a name="resources-for-the-sweep"></a>Recursos para el barrido

Puede controlar los recursos invertidos en el barrido de hiperparámetros especificando `iterations` y `max_concurrent_iterations` para el barrido.

Parámetro | Detalle
-----|----
`iterations` |  Parámetro necesario para el número máximo de configuraciones que se deben barrer. Debe ser un entero entre 1 y 1000. Al explorar solo los hiperparámetros predeterminados para un algoritmo de modelo determinado, establezca este parámetro en 1.
`max_concurrent_iterations`| Número máximo de series que se pueden ejecutar simultáneamente. Si no se especifica, todas las series se inician en paralelo. Si se especifica, el tiempo de espera debe ser un entero comprendido entre 1 y 100.  <br><br> **NOTA:** El número de ejecuciones simultáneas viene determinado por los recursos disponibles en el destino de proceso especificado. Asegúrese de que el destino de proceso tenga los recursos disponibles para la simultaneidad deseada.


> [!NOTE]
> Para obtener un ejemplo completo de configuración de barrido, consulte este [tutorial](tutorial-auto-train-image-models.md#hyperparameter-sweeping-for-image-tasks).

### <a name="arguments"></a>Argumentos

Puede pasar valores fijos o parámetros que no cambien durante el barrido de espacio de parámetros como argumentos. Los argumentos se pasan en pares nombre-valor y el nombre debe ir precedido por un guion doble. 

```python
from azureml.train.automl import AutoMLImageConfig
arguments = ["--early_stopping", 1, "--evaluation_frequency", 2]
automl_image_config = AutoMLImageConfig(arguments=arguments)
```

## <a name="submit-the-run"></a>Envío de la ejecución

Cuando tenga el objeto `AutoMLImageConfig` listo, puede enviar el experimento.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-image-object-detection")
automl_image_run = experiment.submit(automl_image_config)
```
## <a name="outputs-and-evaluation-metrics"></a>Salidas y métricas de evaluación

Las ejecuciones de entrenamiento de ML automatizado generan archivos de modelo de salida, métricas de evaluación, registros y artefactos de implementación, como el archivo de puntuación y el archivo de entorno que se pueden ver desde la pestaña salidas, registros y métricas de las ejecuciones secundarias.

> [!TIP]
> Compruebe cómo navegar a los resultados de la ejecución en la sección [Visualización de los resultados de las ejecuciones](how-to-understand-automated-ml.md#view-run-results).

Para obtener definiciones y ejemplos de los gráficos de rendimiento y las métricas proporcionadas en cada ejecución, vea [Evaluación de los resultados del experimento de aprendizaje automático automatizado](how-to-understand-automated-ml.md#metrics-for-image-models-preview).

## <a name="register-and-deploy-model"></a>Registrar e implementar el modelo

Una vez completada la ejecución, puede registrar el modelo que se creó a partir de la mejor ejecución (configuración que dio lugar a la mejor métrica principal).

```Python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

Después de registrar el modelo que desea usar, puede implementarlo como un servicio web en [Azure Container Instances (ACI)](how-to-deploy-azure-container-instance.md) o [Azure Kubernetes Service (AKS)](how-to-deploy-azure-kubernetes-service.md). ACI es la opción perfecta para las implementaciones de prueba, mientras que AKS es más adecuado para el uso de producción a gran escala.

En este ejemplo se implementa el modelo como un servicio web en AKS. Para realizar la implementación en AKS, cree primero un clúster de proceso de AKS o use un clúster de AKS existente. Puede usar las SKU de máquina virtual de CPU o GPU para el clúster de implementación. 

```python

from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "cluster-aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6", 
                                                        location="eastus2")
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws, 
                                      name=aks_name, 
                                      provisioning_configuration=prov_config)
    aks_target.wait_for_completion(show_output=True)
```

A continuación, puede definir la configuración de inferencia, que describe cómo configurar el servicio web que contiene el modelo. Puede usar el script de puntuación y el entorno de la ejecución de entrenamiento en la configuración de inferencia.

```python
from azureml.core.model import InferenceConfig

best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
environment = best_child_run.get_environment()
inference_config = InferenceConfig(entry_script='score.py', environment=environment)
```

A continuación, puede implementar el modelo como un servicio web de AKS.

```python
# Deploy the model from the best run as an AKS web service
from azureml.core.webservice import AksWebservice
from azureml.core.webservice import Webservice
from azureml.core.model import Model
from azureml.core.environment import Environment

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,                                                    
                                                cpu_cores=1,
                                                memory_gb=50,
                                                enable_app_insights=True)

aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name='automl-image-test',
                           overwrite=True)
aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

También puede implementar el modelo desde la [interfaz de usuario de Estudio de Azure Machine Learning](https://ml.azure.com/). Vaya al modelo que desea implementar en la pestaña **Modelos** de la ejecución de ML automatizado y seleccione **Implementar**.  

![Selección del modelo de las ejecuciones de automl en la interfaz de usuario de Estudio  ](./media/how-to-auto-train-image-models/select-model.png)

Puede configurar el nombre del punto de conexión de implementación del modelo y el clúster de inferencia que se usará para la implementación del modelo en el panel **Implementar un modelo**.

![Configuración de la implementación](./media/how-to-auto-train-image-models/deploy-image-model.png)

### <a name="update-inference-configuration"></a>Actualización de la configuración de inferencia

En el paso anterior, se descargó el archivo de puntuación `outputs/scoring_file_v_1_0_0.py` del mejor modelo en un archivo `score.py` local y se usó para crear un objeto `InferenceConfig`. Este script se puede modificar para cambiar la configuración de inferencia específica del modelo si es necesario una vez descargado y antes de crear `InferenceConfig`. Por ejemplo, esta es la sección de código que inicializa el modelo en el archivo de puntuación:
    
```
...
def init():
    ...
    try:
        logger.info("Loading model from path: {}.".format(model_path))
        model_settings = {...}
        model = load_model(TASK_TYPE, model_path, **model_settings)
        logger.info("Loading successful.")
    except Exception as e:
        logging_utilities.log_traceback(e, logger)
        raise
...
```

Cada una de las tareas (y algunos modelos) tienen un conjunto de parámetros en el diccionario `model_settings`. De forma predeterminada, usamos los mismos valores para los parámetros que se usaron durante el entrenamiento y la validación. Dependiendo del comportamiento que necesitemos al usar el modelo para la inferencia, podemos cambiar estos parámetros. A continuación puede encontrar una lista de parámetros para cada tipo de tarea y modelo.  

| Tarea | Nombre de parámetro | Valor predeterminado  |
|--------- |------------- | --------- |
|Clasificación de imágenes (varias clases y varias etiquetas) | `valid_resize_size`<br>`valid_crop_size` | 256<br>224 |
|Detección de objetos, segmentación de instancias| `min_size`<br>`max_size`<br>`box_score_thresh`<br>`box_nms_thresh`<br>`box_detections_per_img` | 600<br>1333<br>0,3<br>0.5<br>100 |
|Detección de objetos con `yolov5`| `img_size`<br>`model_size`<br>`box_score_thresh`<br>`box_iou_thresh` | 640<br>medio<br>0,1<br>0.5 |

Para obtener una descripción detallada sobre estos parámetros, consulte la sección anterior sobre [hiperparámetros específicos de la tarea](#task-specific-hyperparameters).
    
Si desea usar la colocación en mosaico y desea controlar su comportamiento, están disponibles los parámetros `tile_grid_size`, `tile_overlap_ratio` y `tile_predictions_nms_thresh`. Para más información sobre estos parámetros, consulte [Entrenamiento de un modelo de detección de objetos pequeños mediante AutoML](how-to-use-automl-small-object-detect.md).

## <a name="example-notebooks"></a>Cuadernos de ejemplo
Revise los ejemplos de código y los casos de uso detallados en el repositorio del [cuaderno de GitHub para obtener muestras de aprendizaje automático automatizado](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml). Compruebe las carpetas con el prefijo "image-" para obtener ejemplos específicos de la creación de modelos de Computer Vision.


## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Entrenamiento de un modelo de detección de objetos (versión preliminar) con AutoML y Python](tutorial-auto-train-image-models.md)
* [Solución de problemas de experimentos de aprendizaje automático automatizado](how-to-troubleshoot-auto-ml.md). 

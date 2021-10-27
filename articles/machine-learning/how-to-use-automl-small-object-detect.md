---
title: Uso de AutoML para detectar objetos pequeños en imágenes
description: Configure el aprendizaje automático automatizado de Azure Machine Learning para entrenar modelos de detección de objetos pequeños.
author: PhaniShekhar
ms.author: phmantri
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/13/2021
ms.openlocfilehash: 2fb5dfc77a15e1f9fcd5b90a058e2cc03f81b910
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007150"
---
# <a name="train-a-small-object-detection-model-with-automl-preview"></a>Entrenamiento de un modelo de detección de objetos pequeños con AutoML (versión preliminar)

> [!IMPORTANT]
> Esta característica actualmente está en su versión preliminar pública. Esta versión preliminar se proporciona sin un contrato de nivel de servicio. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este artículo, aprenderá a entrenar un modelo de detección de objetos para detectar objetos pequeños en imágenes de alta resolución con [aprendizaje automático automatizado](concept-automated-ml.md) en Azure Machine Learning.

Normalmente, los modelos de Computer Vision de detección de objetos funcionan bien con conjuntos de datos que tienen objetos relativamente grandes. Sin embargo, debido a las restricciones de memoria y cálculo, estos modelos tienden a tener un rendimiento inferior cuando se les asigna la tarea de detectar objetos pequeños en imágenes de alta resolución. Dado que las imágenes de alta resolución suelen ser grandes, se les cambia el tamaño antes de introducirlas en el modelo, lo que limita su capacidad para detectar objetos más pequeños, en relación con el tamaño inicial de la imagen.

Para ayudar a solucionar este problema, el aprendizaje automático automatizado admite la colocación en mosaico como parte de las funcionalidades de Computer Vision en versión preliminar pública. La funcionalidad de colocación en mosaico del aprendizaje automático automatizado se basa en los conceptos del documento sobre [el poder de la colocación en mosaico para la detección de objetos pequeños](https://openaccess.thecvf.com/content_CVPRW_2019/papers/UAVision/Unel_The_Power_of_Tiling_for_Small_Object_Detection_CVPRW_2019_paper.pdf).

En la colocación en mosaico, cada imagen se divide en una cuadrícula de mosaicos. Los mosaicos adyacentes se superponen entre sí en dimensiones de ancho y alto. Los mosaicos se recortan del original, como se muestra en la siguiente imagen. 

![Generación de mosaicos](./media/how-to-use-automl-small-object-detect/tiles-generation.png)

## <a name="prerequisites"></a>Requisitos previos

* Un área de trabajo de Azure Machine Learning. Para crear el área de trabajo, consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

* En este artículo se supone que tiene algún conocimiento sobre cómo configurar un [experimento de aprendizaje automático automatizado para tareas de Computer Vision](how-to-auto-train-image-models.md). 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="supported-models"></a>Modelos admitidos

Actualmente se admite la detección de objetos pequeños mediante la colocación en mosaico en los siguientes modelos:

* fasterrcnn_resnet18_fpn
* fasterrcnn_resnet50_fpn
* fasterrcnn_resnet34_fpn
* fasterrcnn_resnet101_fpn
* fasterrcnn_resnet152_fpn
* retinanet_resnet50_fpn

## <a name="enable-tiling-during-training"></a>Habilitación de la colocación en mosaico durante el entrenamiento

Para habilitar la colocación en mosaico, puede establecer el parámetro `tile_grid_size` en un valor como (3, 2), donde 3 es el número de mosaicos a lo largo de la dimensión de ancho y 2 es el número de mosaicos a lo largo de la dimensión de alto. Cuando este parámetro se establece en (3, 2), cada imagen se divide en una cuadrícula de 3 x 2 mosaicos. Cada mosaico se superpone con los mosaicos adyacentes, de modo que todos los objetos que se encuentran en el borde del mosaico se incluyen completamente en uno de los mosaicos. Esta superposición se puede controlar mediante el parámetro `tile_overlap_ratio`, que tiene como valor predeterminado el 25 %.

Cuando se habilita la colocación en mosaico, la imagen completa y los mosaicos generados a partir de ella se pasan a través del modelo. Estas imágenes y mosaicos se cambian de tamaño según los parámetros `min_size` y `max_size` antes de introducirse en el modelo. El tiempo de cálculo aumenta proporcionalmente debido al procesamiento de estos datos adicionales. 

Por ejemplo, cuando el parámetro `tile_grid_size` es (3, 2), el tiempo de cálculo será aproximadamente siete veces en comparación con cuando no se usa la colocación en mosaico.

Puede especificar el valor de `tile_grid_size` en el espacio de hiperparámetros como una cadena.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(3, 2)'),
    ...
}
```

El valor del parámetro `tile_grid_size` depende de las dimensiones de la imagen y del tamaño de los objetos que contiene. Por ejemplo, un mayor número de mosaicos sería útil cuando haya objetos más pequeños en las imágenes.

Para elegir el valor óptimo de este parámetro para el conjunto de datos, puede usar la búsqueda de hiperparámetros. Para ello, puede especificar los valores para este parámetro en el espacio de hiperparámetros.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(2, 1)', '(3, 2)', '(5, 3)'),
    ...
}
```
## <a name="tiling-during-inference"></a>Colocación en mosaico durante la inferencia

Cuando se implementa un modelo entrenado con la colocación en mosaico, esta funcionalidad también se aplica durante la inferencia. El aprendizaje automático automatizado usa el valor `tile_grid_size` del entrenamiento para generar los mosaicos durante la inferencia. La imagen completa y los mosaicos correspondientes se pasan por el modelo, y las propuestas de objetos de estos se combinan para obtener las predicciones finales, como en la siguiente imagen.

![Combinación de las propuestas de objetos](./media/how-to-use-automl-small-object-detect/tiles-merge.png)

> [!NOTE] 
> Es posible que el mismo objeto se detecte desde varios mosaicos. A tal efecto, se realiza la detección de duplicación para eliminar dichos duplicados.
>
> Para realizar la detección de duplicados, se ejecuta NMS en las propuestas de los mosaicos y la imagen. Cuando se superponen varias propuestas, se elige la que tiene la puntuación más alta y las otras se descartan como duplicados. Se considera que dos propuestas se superponen cuando la intersección sobre la unión (iou) entre ellas es mayor que el parámetro `tile_predictions_nms_thresh`.

También tiene la opción de habilitar la colocación en mosaico solo durante la inferencia sin hacerlo en el entrenamiento. Para ello, establezca el parámetro `tile_grid_size` solo durante la inferencia, no para el entrenamiento. 

Si lo hace, puede mejorar el rendimiento de algunos conjuntos de datos y no incurrirá en el costo adicional que supone la colocación en mosaico en tiempo de entrenamiento. 

## <a name="tiling-hyperparameters"></a>Hiperparámetros de la colocación en mosaico 

Estos son los parámetros que puede usar para controlar la característica de colocación en mosaico.

| Nombre de parámetro    | Descripción   | Valor predeterminado |
| --------------- |-------------| -------|
| `tile_grid_size` |  Tamaño de la cuadrícula que se usará para la colocación en mosaico de cada imagen. Disponible para su uso durante el entrenamiento, la validación y la inferencia.<br><br>Tupla de dos enteros pasados como una cadena, por ejemplo, `'(3, 2)'`.<br><br> *Nota: Al establecer este parámetro aumenta el tiempo de cálculo proporcionalmente, ya que el modelo procesa todos los mosaicos e imágenes.*| sin valor predeterminado |
| `tile_overlap_ratio` | Controla la proporción de superposición entre los mosaicos adyacentes de cada dimensión. Cuando los objetos que se encuentran en el límite del mosaico sean demasiado grandes para caber completamente en uno de los mosaicos, aumente el valor de este parámetro.<br> <br>  Debe ser un valor flotante en [0, 1).| 0,25 |
| `tile_predictions_nms_thresh` | La intersección sobre el umbral de unión que se usará para realizar la supresión no máxima (nms) al combinar predicciones de mosaicos e imágenes. Disponible durante la validación y la inferencia. Cambie este parámetro si se detectan varios cuadros por objeto en las predicciones finales.  <br><br> Debe ser un valor flotante en [0, 1]. | 0,25 |


## <a name="example-notebooks"></a>Cuadernos de ejemplo

Consulte el [cuaderno de ejemplo de detección de objetos](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/auto-ml-image-object-detection.ipynb) para obtener ejemplos de código detallados sobre cómo configurar y entrenar un modelo de detección de objetos.

>[!NOTE]
> Todas las imágenes de este artículo están disponibles de acuerdo con la sección de uso permitido del [contrato de licencia MIT](https://choosealicense.com/licenses/mit/).  
> Copyright © 2020 Roboflow, Inc.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre [cómo y dónde implementar un modelo](how-to-deploy-and-where.md).
* Para obtener definiciones y ejemplos de los gráficos de rendimiento y las métricas proporcionadas en cada ejecución, vea [Evaluación de los resultados del experimento de aprendizaje automático automatizado](how-to-understand-automated-ml.md). 

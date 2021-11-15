---
title: ResNet
titleSuffix: Azure Machine Learning
description: Aprenda a crear un modelo de clasificación de imágenes en el diseñador de Azure Machine Learning con el algoritmo ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: c6be11cc4dafb5423d38bc869fc8b915b48814ce
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565832"
---
# <a name="resnet"></a>ResNet

En este artículo se describe cómo usar el componente **ResNet** en el diseñador de Azure Machine Learning para crear un modelo de clasificación de imágenes con el algoritmo ResNet.  

Este algoritmo de clasificación es un método de aprendizaje supervisado y requiere un conjunto de datos etiquetado. 
> [!NOTE]
> Este componente no admite el conjunto de datos con etiqueta generado a partir del *etiquetado de datos* en el estudio, sino que solo admite el directorio de imágenes con etiqueta generado a partir del componente [Convert to Image Directory](convert-to-image-directory.md) (Convertir en directorio de imágenes). 

Puede entrenar el modelo proporcionando un modelo y un directorio de imagen con etiquetas como entradas para [Entrenamiento del modelo de PyTorch](train-pytorch-model.md). Después, el modelo entrenado puede utilizarse para predecir valores para los nuevos ejemplos de entrada mediante [Puntuación del modelo de imagen](score-image-model.md).

### <a name="more-about-resnet"></a>Más información sobre ResNet

Consulte [este documento](https://pytorch.org/vision/stable/models.html#torchvision.models.resnext101_32x8d) para obtener más información sobre ResNet.

## <a name="how-to-configure-resnet"></a>Configuración de ResNet

1.  Agregue el componente **ResNet** a la canalización en el diseñador.  

2.  En **Nombre del modelo**, especifique el nombre de una determinada estructura ResNet y puede seleccionar entre ResNet compatibles: "resnet18", "resnet34", "resnet50", "resnet101", "resnet152", "resnet152", "resnext50\_32x4d", "resnext101\_32x8d", "wide_resnet50\_2", "wide_resnet101\_2".

3.  En **Pretrained** (Entrenado previamente), especifique si desea usar un modelo entrenado previamente en ImageNet. Si se selecciona esta opción, puede ajustar el modelo en función de un modelo previamente entrenado seleccionado. Si se anula la selección, puede entrenarlo desde cero.

4.  En **Zero init residual** (valor residual de inicialización cero), especifique si quiere inicializar en cero la última capa de normalización por lotes en cada rama residual. Si se selecciona, la rama residual comienza con ceros y cada bloque residual se comporta como una identidad. Esto puede ayudar a la convergencia en lotes de gran tamaño de acuerdo con https://arxiv.org/abs/1706.02677.

5.  Conecte la salida del componente **ResNet** y del componente de conjunto de datos de imágenes de entrenamiento y validación a [Train PyTorch Model](train-pytorch-model.md) (Entrenar modelo de PyTorch). 

6.  Envíe la canalización.

## <a name="results"></a>Results

Una vez completada la ejecución de la canalización, para usar el modelo para la puntuación, conecte [Entrenamiento del modelo de PyTorch](train-pytorch-model.md) a [Puntuación del modelo de imagen](score-image-model.md), para predecir valores para los nuevos ejemplos de entrada.

## <a name="technical-notes"></a>Notas técnicas  

###  <a name="component-parameters"></a>Parámetros del componente  

| Nombre       | Intervalo | Tipo    | Valor predeterminado           | Descripción                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Nombre del modelo | Any   | Mode    | resnext101\_32x8d | Nombre de una determinada estructura ResNet       |
| Entrenado previamente | Any   | Boolean | True              | Si desea usar un modelo entrenado previamente en ImageNet |
| Valor residual de inicialización cero | Any | Boolean | False | Si se debe inicializar en cero la última capa de normalización por lotes en cada rama residual |
|            |       |         |                   |                                          |

###  <a name="output"></a>Output  

| Nombre            | Tipo                    | Descripción                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo no entrenado | UntrainedModelDirectory | Un modelo ResNet no entrenado que se puede conectar a Entrenamiento del modelo de PyTorch. |

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
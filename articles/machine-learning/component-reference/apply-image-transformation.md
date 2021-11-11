---
title: Aplicación de transformación de imagen
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Aplicación de transformación de imagen para aplicar una transformación de imagen a un directorio de imagen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 45929ecdcc4327a12e2b2867a0cb0c95ca78a3fd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566126"
---
# <a name="apply-image-transformation"></a>Aplicación de transformación de imagen 

En este artículo se explica cómo usar el componente Aplicación de transformación de imagen del diseñador de Azure Machine Learning para modificar un directorio de imagen de entrada en función de una transformación de imagen especificada previamente.  

Debe conectar un componente [Transformación de imagen init](init-image-transformation.md) para especificar la transformación; luego puede aplicar dicha transformación al directorio de imagen de entrada del componente Aplicación de transformación de imagen.

## <a name="how-to-use-apply-image-transformation"></a>Uso de la aplicación de transformación de imagen  

1. Agregue el componente **Aplicación de transformación de imagen** a la canalización. Puede encontrar este componente en la categoría *Computer Vision/Image Data Transformation*. 

2. Conecte la salida de **Transformación de imagen init** a la entrada de la izquierda de **Aplicación de transformación de imagen**.

     > [!NOTE]
     > En este componente solo se acepta la transformación de imagen generada por el componente [Transformación de imagen init](init-image-transformation.md). Para otro tipo de transformación, conéctelo a [Aplicación de transformación](apply-transformation.md); de lo contrario, se producirá el error "InvalidTransformationDirectoryError".


3. Conecte el directorio de imagen que quiera transformar.

4. En **Modo**, especifique para qué propósito usa la transformación de entrada: "Para entrenamiento" o "Para inferencia". 

   Si selecciona **For training** (Para entrenamiento), se aplicará toda la transformación que especifique en la transformación de imagen init.

   Si selecciona **For inference** (Para inferencia), se excluirá alguna transformación, como la creación de nuevos ejemplos de forma aleatoria, antes de aplicarla. Esto se debe a que las operaciones de transformación para crear nuevos ejemplos de forma aleatoria, como "Random horizontal Flip", (Volteo horizontal aleatorio), se usan para el aumento de datos en el entrenamiento y se deben quitar en inferencia porque los ejemplos de inferencia se deben corregir para una predicción y evaluación precisas.

   > [!NOTE]
   > Las transformaciones que se excluirán en modo **For inference** (Para inferencia) son: Recorte de cambio de tamaño aleatorio, recorte aleatorio, volteo horizontal aleatorio, volteo vertical aleatorio, rotación aleatoria, afín aleatoria, escala de grises aleatoria, perspectiva aleatoria, borrado aleatorio.

5. Para aplicar una transformación de imagen a un nuevo directorio de imagen, envíe la canalización.  

### <a name="component-parameters"></a>Parámetros del componente

| Nombre | Intervalo | Tipo | Valor predeterminado                   | Descripción                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | Any   | Mode | (Requerir al usuario que lo especifique) | Para qué propósito usa la transformación de entrada. Debe excluir las operaciones de transformación "aleatoria" en la inferencia, pero mantenerlas en el entrenamiento. |

### <a name="expected-inputs"></a>Entradas esperadas  

| Nombre                       | Tipo                    | Descripción                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Transformación de imagen de entrada | TransformationDirectory | Transformación de imagen de entrada        |
| Directorio de imagen de entrada      | ImageDirectory          | Directorio de imagen que se va a transformar |

### <a name="outputs"></a>Salidas  

| Nombre                   | Tipo           | Descripción            |
| ---------------------- | -------------- | ---------------------- |
| Directorio de imagen de salida | ImageDirectory | Directorio de imagen de salida |

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 

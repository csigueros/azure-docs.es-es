---
title: 'Entrenamiento del modelo de clasificación personalizado: Azure Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo entrenar el modelo de clasificación de texto personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 466ddd800c70c27787a0a8ebcd8c14a4392e6c2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061946"
---
# <a name="how-to-train-a-text-classification-model"></a>Entrenamiento de un modelo de clasificación de texto


El entrenamiento es el proceso en el que el modelo aprende de los [datos etiquetados](tag-data.md). Una vez completado el entrenamiento, podrá usar las [métricas de evaluación del modelo](../how-to/view-model-evaluation.md) para determinar si necesita [mejorar el modelo](../how-to/improve-model.md).

## <a name="prerequisites"></a>Prerrequisitos

Antes de entrenar el modelo, necesita:

* [Un proyecto creado correctamente](create-project.md) con una cuenta de Azure Blob Storage configurada 
* Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md)

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#project-development-lifecycle) para más información.

## <a name="data-splits"></a>Divisiones de datos

Antes de iniciar el proceso de entrenamiento, los archivos del conjunto de datos se dividen en tres grupos de forma aleatoria:

* El **conjunto de entrenamiento** contiene el 80 % de los archivos del conjunto de datos. Es el conjunto principal que se usa para entrenar el modelo.

* El **conjunto de entrenamiento** contiene el 20 % de los archivos del conjunto de datos. Este conjunto se usa para proporcionar una [evaluación](../how-to/view-model-evaluation.md) sin sesgar del modelo. Este conjunto no se introduce en el modelo durante el entrenamiento. Los detalles de las predicciones correctas e incorrectas de este conjunto no se muestran para que no tenga que reajustar los datos de entrenamiento ni se modifiquen los resultados.

## <a name="train-model-in-language-studio"></a>Entrenamiento de un modelo en Language Studio

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/LanguageStudio).

2. En el menú de la izquierda, seleccione **Train** (Entrenar).

3. Para entrenar un nuevo modelo, seleccione **Train a new model** (Entrenar un nuevo modelo) y escriba el nombre del modelo en el cuadro de texto siguiente. Para **sobrescribir un modelo**, seleccione esta opción y elija el modelo que desee en la lista desplegable siguiente.

    :::image type="content" source="../media/train-model.png" alt-text="Creación de un modelo" lightbox="../media/train-model.png":::

4. Seleccione el botón **Train** (Entrenar) en la parte inferior de la página.

El tiempo para entrenar un modelo varía en función del conjunto de datos; puede tardar hasta varias horas. Solo puede entrenar un modelo cada vez, y no puede crear ni entrenar otros modelos si ya se está entrenando uno en el mismo proyecto. 


Una vez completado correctamente el entrenamiento, tenga en cuenta lo siguiente:

* [Visualización de los detalles de evaluación del modelo](../how-to/view-model-evaluation.md) Después del entrenamiento del modelo, la evaluación del modelo se realiza en el [conjunto de pruebas](../how-to/train-model.md#data-splits), que no se introdujo en el modelo durante el entrenamiento. Al ver la evaluación, puede obtener una idea de cómo funciona el modelo en escenarios reales.

* [Examen de la distribución de datos](../how-to/improve-model.md#examine-data-distribution-from-language-studio) Asegúrese de que todas las clases están bien representadas y de que tiene una distribución de datos equilibrada para asegurarse de que todas las clases están representadas adecuadamente. Si una clase determinada se etiqueta de una forma mucho menos frecuente que las demás, es probable que esta clase tenga poca representación y el modelo probablemente no reconozca correctamente la mayoría de las repeticiones en el tiempo de ejecución. En este caso, plantéese agregar más archivos que pertenezcan a esta clase al conjunto de datos.

* [Mejora del rendimiento (opcional)](../how-to/improve-model.md) Además de revisar los [datos etiquetados](tag-data.md) en función del análisis de errores, recomendamos aumentar el número de etiquetas para los tipos de entidad de bajo rendimiento o mejorar la diversidad de los datos etiquetados. Esto ayudará al modelo a aprender a proporcionar predicciones correctas sobre posibles fenómenos lingüísticos que provocan errores.

<!-- * Define your own test set: If you are using a random split option and the resulting test set was not comprehensive enough, consider defining your own test to include a variety of data layouts and balanced tagged classes.
 -->


## <a name="next-steps"></a>Pasos siguientes

Una vez completado el entrenamiento, podrá usar las [métricas de evaluación del modelo](../how-to/view-model-evaluation.md) para [mejorar el modelo](../how-to/improve-model.md) si lo desea. Una vez que esté satisfecho con el modelo, puede implementarlo, para que pueda usarse para [clasificar texto](call-api.md).

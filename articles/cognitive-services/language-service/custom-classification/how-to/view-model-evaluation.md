---
title: 'Visualización de una evaluación de modelo de clasificación personalizada: Azure Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo ver las puntuaciones de evaluación de un modelo de clasificación personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 89cb476141544c136ac38abf996cf16d3ac4d002
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093364"
---
# <a name="view-the-model-evaluation"></a>Visualización de la evaluación del modelo

Revisar la evaluación de modelos es un paso importante para desarrollar un modelo de clasificación personalizado. Gracias a ello, podrá obtener información sobre el rendimiento del modelo y podrá hacerse una idea de cómo funcionará cuando lo use en producción. 


## <a name="prerequisites"></a>Prerrequisitos

Antes de entrenar el modelo, necesita lo siguiente:
* [Un proyecto de clasificación personalizado](create-project.md) con una cuenta de Azure Blob Storage configurada. 
* Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md)
* Un [modelo entrenado](train-model.md) correctamente.

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.

## <a name="model-evaluation"></a>Evaluación del modelo

El proceso de evaluación usa el modelo entrenado para predecir las clases definidas por el usuario para los archivos del conjunto de pruebas y las compara con las etiquetas de datos proporcionadas. El conjunto de pruebas consta de datos que no se introdujeron en el modelo durante el proceso de entrenamiento. 

## <a name="view-the-model-details-using-language-studio"></a>Visualización de los detalles del modelo mediante Language Studio

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/languageStudio).
    1. Busque la sección de Language Studio con la etiqueta **Clasificar texto**.
    2. Seleccione **Clasificación de texto personalizada**. 

2. Seleccione **Ver detalles del modelo** en el menú situado en el lado izquierdo.

3. Consulte el estado de entrenamiento del modelo en la columna **Status** (Estado) y la puntuación F1 del modelo en la columna **F1 score** (Puntuación F1).

    :::image type="content" source="../media/model-details-1.png" alt-text="Botón para ver los detalles de los modelos" lightbox="../media/model-details-1.png":::

1. Haga clic en el nombre del modelo para obtener más detalles.

2. Puede encontrar las métricas de evaluación de **nivel de modelo** en la sección **Información general** y las métricas de evaluación de **nivel de clase** en la sección **Métricas de rendimiento de clase**. Para obtener más información, consulte las [métricas de evaluación](../concepts/evaluation.md#model-level-and-class-level-evaluation-metrics).

    :::image type="content" source="../media/model-details-2.png" alt-text="Métricas de rendimiento de modelos" lightbox="../media/model-details-2.png":::

> [!NOTE]
> Si no encuentra todas las clases que se muestran aquí, es porque no hay ningún archivo etiquetado de esta clase en el conjunto de pruebas.

La matriz de confusión del modelo se encuentra en **Matriz de confusión del conjunto de pruebas**.

**Clasificación de etiqueta única**

:::image type="content" source="../media/conf-matrix-single.png" alt-text="Matriz de confusión para la clasificación de clase única" lightbox="../media/conf-matrix-single.png":::

**Clasificación de varias etiquetas**

:::image type="content" source="../media/conf-matrix-multi.png" alt-text="Matriz de confusión para la clasificación de varias clases" lightbox="../media/conf-matrix-multi.png":::

## <a name="next-steps"></a>Pasos siguientes

A medida que revise el rendimiento del modelo, puede obtener información sobre las [métricas de evaluación](../concepts/evaluation.md) que se usan. Una vez que sepa si el rendimiento del modelo debe mejorar, puede empezar a [mejorarlo](improve-model.md).

---
title: Visualización de la evaluación de un modelo de Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo ver las puntuaciones de evaluación de un modelo de Reconocimiento de entidades con nombre (NER) personalizado
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 62724137bd02f8c3ff58665ad92a4346eddc0b53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093514"
---
# <a name="view-the-models-evaluation-and-details"></a>Visualización de la evaluación y los detalles del modelo

Una vez que el modelo haya finalizado el entrenamiento, puede ver los detalles del modelo y su rendimiento con respecto al conjunto de pruebas, que contiene el 10 % de los datos elegidos de forma aleatoria, y se crea durante el [entrenamiento](train-model.md#data-split). El conjunto de pruebas consta de datos que no se introdujeron en el modelo durante el proceso de entrenamiento. Para que el proceso de evaluación se complete, debe haber al menos 10 archivos en el conjunto de datos. También debe tener un [proyecto de NER personalizado](../quickstart.md) con un [modelo entrenado](train-model.md).

## <a name="prerequisites"></a>Prerrequisitos

* Un [proyecto creado](create-project.md) correctamente con una cuenta de Azure Blob Storage configurada
    * Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md)
* Un [modelo entrenado correctamente](train-model.md)

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.

## <a name="view-the-models-evaluation-details"></a>Visualización de los detalles de evaluación del modelo

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/languageStudio).
    1. Busque la sección de Language Studio con la etiqueta **Extract information** (Extraer información).
    2. Seleccione **Custom named entity extraction** (Extracción personalizada de entidades con nombre).

2. Seleccione **View model details** (Ver detalles del modelo) en el menú situado en el lado izquierdo de la pantalla.

3. Consulte el estado de entrenamiento del modelo en la columna **Status** (Estado) y la puntuación F1 del modelo en la columna **F1 score** (Puntuación F1). Puede hacer clic en el nombre del modelo para más detalles.

4. Puede encontrar las métricas de evaluación de **nivel de modelo** en **Overview** (Información general) y las métricas de evaluación de **nivel de entidad** en **Entity performance metrics** (Métricas de rendimiento de entidades). La matriz de confusión del modelo se encuentra en **Test set confusion matrix** (Matriz de confusión del conjunto de pruebas).
    
    > [!NOTE]
    > Si no aparecen aquí todas las entidades, se debe a que no estaban en ninguno de los archivos del conjunto de pruebas.

    :::image type="content" source="../media/model-details.png" alt-text="Captura de pantalla de las métricas de rendimiento del modelo en Language Studio" lightbox="../media/model-details.png":::

## <a name="next-steps"></a>Pasos siguientes

* Después de revisar la evaluación del modelo, puede empezar a [mejorar el modelo](improve-model.md).
* Obtenga información sobre las [métricas utilizadas en la evaluación](../concepts/evaluation-metrics.md). 

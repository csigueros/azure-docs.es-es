---
title: Entrenamiento del modelo de Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Descubra cómo entrenar el modelo para el Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 6fdf5ad5ed2eb8291b9a43e6004d8b18c5879b93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093516"
---
# <a name="train-your-custom-named-entity-recognition-ner-model"></a>Entrenamiento del modelo de Reconocimiento de entidades con nombre (NER) personalizado

El entrenamiento es el proceso en el que el modelo aprende de los [datos etiquetados](tag-data.md). Una vez completado el entrenamiento, podrá usar las [métricas de evaluación del modelo](../how-to/view-model-evaluation.md) para determinar si necesita [mejorar el modelo](../how-to/improve-model.md).

El tiempo para entrenar un modelo varía en función del conjunto de datos; puede tardar hasta varias horas. Solo puede entrenar un modelo cada vez, y no puede crear ni entrenar otros modelos si ya se está entrenando uno en el mismo proyecto. 

## <a name="prerequisites"></a>Prerrequisitos

* Un [proyecto creado](create-project.md) correctamente con una cuenta de Azure Blob Storage configurada
    * Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md)

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.

## <a name="data-split"></a>División de datos

Antes de iniciar el proceso de entrenamiento, los archivos del conjunto de datos se dividen en dos grupos de forma aleatoria:

* El **conjunto de entrenamiento** contiene el 80 % de los archivos del conjunto de datos. Es el conjunto principal que se usa para entrenar el modelo.

* El **conjunto de entrenamiento** contiene el 20 % de los archivos del conjunto de datos. Este conjunto se usa para proporcionar una [evaluación](../how-to/view-model-evaluation.md) sin sesgar del modelo. Este conjunto no se introduce en el modelo durante el entrenamiento.

## <a name="train-model-in-language-studio"></a>Entrenamiento de un modelo en Language Studio

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/LanguageStudio).

2. En el menú de la izquierda, seleccione **Train** (Entrenar).

3. Para entrenar un nuevo modelo, seleccione **Train a new model** (Entrenar un nuevo modelo) y escriba el nombre del modelo en el cuadro de texto siguiente. Para **sobrescribir un modelo**, seleccione esta opción y elija el modelo que desee en la lista desplegable siguiente.

    :::image type="content" source="../media/train-model.png" alt-text="Creación de un modelo" lightbox="../media/train-model.png":::

4. Seleccione el botón **Train** (Entrenar) en la parte inferior de la página.

## <a name="next-steps"></a>Pasos siguientes

Una vez completado el entrenamiento, podrá usar las [métricas de evaluación del modelo](view-model-evaluation.md) para [mejorar el modelo](improve-model.md), si así lo desea. Cuando esté satisfecho con el modelo, puede implementarlo y permitir que esté disponible para [extraer entidades](call-api.md) del texto.

---
title: Cómo mejorar el rendimiento del modelo de Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Información sobre cómo mejorar un modelo de Reconocimiento de entidades con nombre (NER) personalizado
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 5ae65097fbd4e7bdc9f4e602a64bc392e45ef75e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091257"
---
# <a name="improve-the-performance-of-custom-named-entity-recognition-ner-models"></a>Mejora del rendimiento de los modelos de Reconocimiento de entidades con nombre (NER) personalizados 

Después de entrenar el modelo y revisar los detalles de evaluación, puede empezar a mejorar el rendimiento del modelo. En este artículo, revisará las incoherencias entre las clases previstas y las clases etiquetadas por el modelo, y examinará la distribución de datos.

## <a name="prerequisites"></a>Prerrequisitos

* Un [proyecto creado](create-project.md) correctamente con una cuenta de Azure Blob Storage configurada
    * Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md)
* Un [modelo entrenado correctamente](train-model.md)
* Se han revisado los [detalles de evaluación del modelo](view-model-evaluation.md) para determinar el rendimiento del modelo.
    * Se ha familiarizado con las [métricas de evaluación](../concepts/evaluation-metrics.md) que se usan para la evaluación.

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.


## <a name="improve-model"></a>Mejora del modelo

Una vez que haya revisado la [evaluación del modelo](view-model-evaluation.md), habrá formado una idea sobre lo que está mal con la predicción del modelo. 

> [!NOTE]
> Esta guía se centra en los datos del [conjunto de validación](train-model.md#data-split) que se creó durante el entrenamiento.

### <a name="review-validation-set"></a>Revisión del conjunto de validación

Con Language Studio, puede revisar el rendimiento del modelo y cómo esperaba que se comportara. Puede revisar las clases previstas y etiquetadas de cada modelo que haya entrenado.

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/languageStudio).
    1. Busque la sección de Language Studio con la etiqueta **Clasificar texto**.
    2. Seleccione **Clasificación de texto personalizada**. 

2. Seleccione **Improve model** (Mejorar modelo) en el menú de la izquierda.

3. Seleccione **Review validation set** (Revisar conjunto de validación).

4. Elija el modelo entrenado en el menú desplegable **Modelo**.

5. Para facilitar el análisis, puede activar **Show incorrect predictions only** (Mostrar solo predicciones incorrectas) para ver solo los errores.

Use la siguiente información como guía para mejorar el modelo. 

* Si la entidad `X` se identifica constantemente como la entidad `Y`, significa que hay ambigüedad entre estos tipos de entidad y debe replantearse el esquema.

* Si una entidad compleja no se extrae de forma reiterada, considere la posibilidad de dividirla en entidades más sencillas para facilitar la extracción.

* Si se predice una entidad cuando no estaba etiquetada en los datos, significa que debe revisar las etiquetas. Asegúrese de que todas las instancias de una entidad están etiquetadas correctamente en todos los archivos.

### <a name="examine-data-distribution"></a>Examen de la distribución de datos

Al examinar la distribución de datos en los archivos, puede decidir si alguna entidad está subrepresentada. El desequilibrio de datos se produce cuando las etiquetas no se distribuyen por igual entre las entidades y es un riesgo para el rendimiento del modelo. Por ejemplo, si la *entidad 1* tiene 50 etiquetas y la *entidad 2* solo tiene 10, este es un ejemplo de desequilibrio de datos donde la *entidad 1* está representada en exceso y la *entidad 2*, subrepresentada. El modelo está sesgado hacia la extracción de la *entidad 1* y puede que esté pasando por alto a la *entidad 2*. Si el esquema es ambiguo, pueden surgir problemas más complejos derivados del desequilibrio de datos. Si las dos entidades son más o menos similares y la *entidad 2* está subrepresentada, lo más probable es que el modelo la esté extrayendo como *entidad 1*.

En la [evaluación del modelo](view-model-evaluation.md), las entidades que están representadas en exceso tienden a tener una recuperación mayor que otras entidades, mientras que las entidades subrepresentadas tienen una recuperación menor.

Para examinar la distribución de datos en el conjunto de datos:

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/languageStudio).
    1. Busque la sección de Language Studio con la etiqueta **Extract information** (Extraer información).
    2. Seleccione **Custom named entity extraction** (Extracción personalizada de entidades con nombre). 
2. Seleccione **Improve model** (Mejorar modelo) en el menú de la izquierda.

3. Seleccione **Examine data distribution** (Examinar distribución de datos).

## <a name="next-steps"></a>Pasos siguientes

Cuando esté satisfecho con el rendimiento del modelo, puede empezar a [enviar solicitudes de extracción de entidades](call-api.md) mediante la API en tiempo de ejecución.

---
title: Procedimiento para mejorar el rendimiento del modelo de clasificación de texto personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo mejorar un modelo para la clasificación de texto personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 73513e90f37a3d003351604d119612306ba66277
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062745"
---
# <a name="improve-model-performance"></a>Mejorar el rendimiento del modelo

Después de entrenar el modelo y revisar sus detalles de evaluación, puede decidir si tiene que mejorar el rendimiento del modelo. En este artículo, revisará las incoherencias entre las clases previstas y las clases etiquetadas por el modelo, y examinará la distribución de datos.

## <a name="prerequisites"></a>Prerrequisitos

Para mejorar opcionalmente un modelo, necesitará lo siguiente:

* [Un proyecto de clasificación personalizado](create-project.md) con una cuenta de Azure Blob Storage configurada. 
* Los datos del texto que se [han cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md) para [entrenar un modelo](train-model.md) correctamente.
* Se han revisado los [detalles de evaluación del modelo](view-model-evaluation.md) para determinar su rendimiento.
* Se ha familiarizado con las [métricas de evaluación](../concepts/evaluation.md) que se usan para la evaluación.

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#project-development-lifecycle) para más información.

## <a name="review-test-set-predictions"></a>Revisión de las predicciones del conjunto de pruebas

Con Language Studio, puede revisar el rendimiento del modelo y cómo esperaba que se comportara. Puede revisar las clases previstas y etiquetadas en paralelo para cada modelo que haya entrenado.

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/languageStudio).
    1. Busque la sección de Language Studio con la etiqueta **Classify text** (Clasificar texto).
    2. Seleccione **Clasificación de texto personalizado**. 

2. Seleccione **Improve model** (Mejorar modelo) en el menú de la izquierda.

3. Seleccione **Review test set** (Revisar conjunto de pruebas).

4. Elija el modelo entrenado en el menú desplegable **Modelo**.

5. Para facilitar el análisis, puede activar **Show incorrect predictions only** (Mostrar solo predicciones incorrectas) para ver solo los errores.

    :::image type="content" source="../media/review-validation-set.png" alt-text="Revisión del conjunto de validación" lightbox="../media/review-validation-set.png":::

6. Si un archivo que debe pertenecer a la clase `X`se clasifica constantemente como clase `Y`, significa que hay ambigüedad entre estas clases y se debe replantear el esquema.

## <a name="examine-data-distribution-from-language-studio"></a>Examen de la distribución de datos desde Language Studio

Al examinar la distribución de datos en los archivos, puede decidir si alguna clase está subrepresentada. El desequilibrio de datos se produce cuando los archivos usados para el entrenamiento no se distribuyen de manera equitativa entre las clases e introduce un riesgo para el rendimiento del modelo. Por ejemplo, si la *clase 1* tiene 50 archivos etiquetados mientras que la *clase 2* solo tiene 10, se trata de un desequilibrio de datos donde la *clase 1* está sobrerepresentada y la *clase 2* subrepresentada. 

En este caso, el modelo está sesgado hacia la clasificación del archivo como de *clase 1* y puede pasar por alto la *clase 2*. Si el esquema es ambiguo, puede surgir un problema más complejo del desequilibrio de datos. Si no hay una distinción clara entre las dos clases y la *clase 2* está subrepresentada, lo más probable es que el modelo clasifique el texto como de *clase 1*.

En las [métricas de evaluación](../concepts/evaluation.md), cuando una clase está sobrerepresentada, suele tener una coincidencia mayor que otras clases, mientras que las clases subrepresentadas tienen una coincidencia menor.

Para examinar la distribución de datos en el conjunto de datos:

1. Vaya a la página del proyecto en [Language Studio](https://aka.ms/languageStudio).
    1. Busque la sección de Language Studio con la etiqueta **Classify text** (Clasificar texto).
    2. Seleccione **Clasificación de texto personalizado**. 

2. Seleccione **Improve model** (Mejorar modelo) en el menú de la izquierda.

3. Seleccione **Examine data distribution** (Examinar distribución de datos).

    :::image type="content" source="../media/examine-data-distribution.png" alt-text="Examen de la distribución de datos" lightbox="../media/examine-data-distribution.png":::

4. Vuelva a la página **Tag data** (Etiquetar datos) y realice ajustes una vez que sepa cómo debe etiquetar los datos de otra forma.

## <a name="next-steps"></a>Pasos siguientes

* Cuando esté satisfecho con el rendimiento del modelo, puede empezar a [enviar solicitudes de clasificación de texto](call-api.md) mediante la API en tiempo de ejecución.

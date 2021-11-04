---
title: Entrenamiento y evaluación de modelos en Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Use este artículo para entrenar un modelo y ver sus detalles de evaluación para realizar mejoras.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: a5f4ccb11b759f735618ef991e7dfd85fd071035
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091136"
---
# <a name="train-and-evaluate-models"></a>Entrenamiento y evaluación de modelos

Una vez que haya completado el [etiquetado de las expresiones](./tag-utterances.md), puede entrenar el modelo. El entrenamiento es el acto de convertir el estado actual de los datos de entrenamiento del proyecto para crear un modelo que se pueda usar para las predicciones. Cada vez que entrena, debe dar nombre a la instancia de entrenamiento. 

Puede crear y entrenar varios modelos dentro del mismo proyecto. Sin embargo, si vuelve a entrenar un modelo específico, sobrescribe el último estado.

Los tiempos de entrenamiento pueden estar en cualquier lugar desde unos segundos cuando se trabaja con proyectos de flujo de trabajo de orquestación, hasta un par de horas cuando se alcanza el [límite máximo](../service-limits.md) de expresiones. Antes del entrenamiento, tendrá la opción de habilitar la evaluación, lo que le permite ver el rendimiento del modelo. 

## <a name="train-model"></a>Entrenamiento de un modelo

Escriba un nuevo nombre de modelo o seleccione un modelo existente en la lista desplegable **Nombre del modelo**. Presione la tecla Enter después de agregar un nombre de modelo. Seleccione si desea evaluar el modelo cambiando el botón de alternancia **Ejecutar evaluación con entrenamiento**. Si está habilitada, las expresiones etiquetadas se verán en tres partes; 80 % para entrenamiento, 10 % para validación y 10 % para pruebas. Después, podrá ver los resultados de evaluación del modelo.

:::image type="content" source="../media/train-model.png" alt-text="Una captura de pantalla que muestra la página Entrenar modelo para proyectos de Conversational Language Understanding." lightbox="../media/train-model.png":::

Haga clic en el botón **Entrenar** y espere a que finalice el entrenamiento. Verá el estado de entrenamiento del modelo en la página de detalles del modelo de vista.

## <a name="evaluate-model"></a>Evaluación de modelo

Una vez completado el entrenamiento del modelo, puede ver los detalles del modelo y ver su rendimiento con respecto al conjunto de pruebas si habilitó la evaluación en el paso de entrenamiento. Observar el rendimiento del modelo se denomina evaluación. El conjunto de pruebas se compone del 20 % de las expresiones y esta división se realiza aleatoriamente antes del entrenamiento. El conjunto de pruebas consta de datos que no se introdujeron en el modelo durante el proceso de entrenamiento. Para que el proceso de evaluación se complete, debe haber al menos 10 expresiones en el conjunto de entrenamiento.

En la página de **detalles del modelo de vista**, podrá ver todos los modelos, con su estado de entrenamiento actual y la fecha en que se entrenaron por última vez.

:::image type="content" source="../media/model-page-1.png" alt-text="Una captura de pantalla que muestra la página de detalles del modelo para proyectos de Conversational Language Understanding." lightbox="../media/model-page-1.png":::

* Haga clic en el nombre del modelo para obtener más detalles. Solo se puede hacer clic en el nombre de un modelo si ha habilitado la evaluación de antemano. 
* En la sección **Información general** puede encontrar la precisión de la macro, la coincidencia y la puntuación F1 para las intenciones o entidades colectivas, en función de la opción que seleccione. 
* En las pestañas **Intenciones** y **entidades**, puede encontrar la micro precisión, la coincidencia y la puntuación F1 para cada intención o entidad por separado.

> [!NOTE]
> Si no ve ninguna de las intenciones o entidades que tiene en el modelo que se muestra aquí, es porque no estaban en ninguna de las expresiones que se usaron para el conjunto de pruebas.

Puede ver la [matriz de confusión](../concepts/evaluation-metrics.md#confusion-matrix) para las intenciones y las entidades al hacer clic en la pestaña **Matriz de confusión del conjunto de prueba** en la parte superior de la pantalla. 

## <a name="next-steps"></a>Pasos siguientes
* [Métricas de evaluación de modelos](../concepts/evaluation-metrics.md)
* [Implementación y consulta del modelo](./deploy-query-model.md)

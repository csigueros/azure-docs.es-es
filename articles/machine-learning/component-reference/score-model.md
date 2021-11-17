---
title: 'Score Model: Referencia del componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Score Model (Puntuar modelo) en Azure Machine Learning para generar predicciones mediante un modelo entrenado de clasificación o regresión.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 83e466e7218b327bbd871c6693466a10744c9b79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565833"
---
# <a name="score-model"></a>Puntuación de modelo

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Utilice este componente para generar predicciones con un modelo entrenado de clasificación o regresión.

## <a name="how-to-use"></a>Modo de uso

1. Agregue el módulo **Score Model** (Puntuar modelo) a la canalización.

2. Adjunte un modelo entrenado y un conjunto de datos que contenga los nuevos datos de entrada. 

    Los datos deben estar en un formato compatible con el tipo de modelo entrenado que usa. El esquema del conjunto de datos de entrada también debe coincidir generalmente con el esquema de los datos utilizados para entrenar el modelo.

3. Envíe la canalización.

## <a name="results"></a>Results

Después de haber generado un conjunto de puntuaciones mediante [Puntuar modelo](./score-model.md):

+ Para generar un conjunto de métricas usadas para evaluar la precisión del modelo (rendimiento), puede conectar el conjunto de datos puntuado a [Evaluar modelo](./evaluate-model.md). 
+ Haga clic con el botón derecho en el componente y seleccione **Visualizar** para ver un ejemplo de los resultados.
<!-- + To Save the results to a dataset. -->

La puntuación, o el valor de predicción, puede estar en muchos formatos diferentes, según el modelo y los datos de entrada:

- Para los modelos de clasificación, las salidas de [Puntuar modelo](./score-model.md) dan como resultado un valor de predicción para la clase, así como la probabilidad del valor de predicción.
- Para los modelos de regresión, [Puntuar modelo](./score-model.md) genera simplemente el valor numérico de predicción.


## <a name="publish-scores-as-a-web-service"></a>Publicar puntuaciones como servicio web

Un uso común de la puntuación es devolver la salida como parte de un servicio web predictivo. Para obtener más información, vea [este tutorial](../tutorial-designer-automobile-price-deploy.md) sobre cómo implementar un punto de conexión en tiempo real basado en una canalización en el diseñador de Azure Machine Learning.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.
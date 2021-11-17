---
title: 'Evaluate Recommender: Referencia del componente'
titleSuffix: Azure Machine Learning
description: Obtenga más información sobre cómo usar el componente Evaluate Recommender (Evaluar recomendador) en Azure Machine Learning para evaluar la precisión de las predicciones del modelo del recomendador.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: aa43a0e7fdf7214f52855ad2bc7601188c305474
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565889"
---
# <a name="evaluate-recommender"></a>Evaluación del recomendador

En este artículo se describe cómo usar el componente Evaluate Recommender (Evaluar recomendador) del diseñador de Azure Machine Learning. El objetivo es medir la precisión de las predicciones realizadas por un modelo de recomendación. Con este componente, puede evaluar tipos diferentes de recomendaciones:  
  
-   Clasificaciones previstas para un usuario y un elemento    
-   Elementos recomendados para un usuario  
  
Al crear predicciones con un modelo de recomendación, se devuelven resultados ligeramente diferentes para cada uno de estos tipos de predicción admitidos. El componente Evaluate Recommender (Evaluar recomendador) deduce el tipo de predicción del formato de columna del conjunto de datos puntuado. Por ejemplo, el conjunto de datos puntuado puede contener:

- Tripleta clasificación-usuario-elemento
- Usuarios y sus elementos recomendados

El componente también aplica las métricas de rendimiento adecuadas, en función del tipo de predicción que se realice. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Cómo configurar la evaluación del recomendador

El componente Evaluate Recommender (Evaluar recomendador) compara las predicciones generadas con el uso de un modelo de recomendación con los datos "ciertos" correspondientes. Por ejemplo, el componente [Score SVD Recommender](score-svd-recommender.md) (Puntuar recomendador de SVD) genera conjuntos de datos de puntuación que se pueden analizar con Evaluate Recommender (Evaluar recomendador).

### <a name="requirements"></a>Requisitos

Evaluate Recommender (Evaluar recomendador) requiere como entrada los siguientes conjuntos de datos. 
  
#### <a name="test-dataset"></a>Conjunto de datos de prueba

El conjunto de datos de prueba contiene datos ciertos en forma de tripleta usuario-elemento-clasificación.  

#### <a name="scored-dataset"></a>Conjunto de datos puntuado

El conjunto de datos puntuado contiene las predicciones generadas por el modelo de recomendación.  
  
Las columnas de este segundo conjunto de datos dependen del tipo de predicción que realizó durante la puntuación. Por ejemplo, el conjunto de datos puntuado puede contener cualquier de los siguientes:

- Usuarios, elementos y clasificaciones que el usuario podría proporcionar para el elemento
- Una lista de usuarios y elementos recomendados para ellos 

### <a name="metrics"></a>Métricas

Las métricas de rendimiento para el modelo se generan en función del tipo de entrada. Las secciones siguientes proporcionan información detallada.

## <a name="evaluate-predicted-ratings"></a>Evaluación de las clasificaciones previstas  

Al evaluar las clasificaciones previstas, el conjunto de datos puntuado (la segunda entrada de Evaluate Recommender) tiene que contener la tripleta usuario-elemento-clasificación que cumpla estos requisitos:
  
-   La primera columna del conjunto de datos contiene los identificadores de usuario.    
-   La segunda columna contiene identificadores de elemento.  
-   La tercera columna contiene las clasificaciones correspondientes del usuario-elemento.  
  
> [!IMPORTANT] 
> Para que la evaluación sea correcta, los nombres de columna deben ser `User`, `Item`y `Rating`, respectivamente.  
  
Evaluate Recommender (Evaluar recomendador) compara las clasificaciones del conjunto de datos "ciertos" con las clasificaciones previstas del conjunto de resultados puntuado. A continuación, calcula el error absoluto medio (EAM) y al raíz del error cuadrático medio (RECM).



## <a name="evaluate-item-recommendations"></a>Evaluación de recomendaciones de elemento

Al evaluar la recomendación de elemento, use un conjunto de datos puntuado que incluya los elementos recomendados para cada usuario:
  
-   La primera columna del conjunto de datos debe contener los identificadores de usuario.    
-   Todas las columnas subsiguientes deben contener los identificadores de elemento recomendados correspondientes, ordenados según la importancia de un elemento para el usuario. 

Antes de conectar este conjunto de datos, recomendamos que ordene el conjunto de datos para que los elementos más relevantes aparezcan primero.  

> [!IMPORTANT] 
> Para que Evaluate Recommender (Evaluar recomendador) funcione, los nombres de la columna tienen que ser `User`, `Item 1`, `Item 2`, `Item 3`, etc.  
  
Evaluate Recommender (Evaluar recomendador) calcula el promedio de ganancia acumulativa con descuento normalizado (NDCG) y lo devuelve en el conjunto de datos de salida.  
  
Dado que es imposible conocer los datos "ciertos" actuales de los elementos recomendados, Evaluate Recommender (Evaluar recomendador) usa la clasificación del usuario local en el conjunto de datos de prueba como ganancia en el cálculo de NDCG. Para evaluar, el componente Evaluate Recommender (Evaluar recomendador) solo tiene que generar recomendaciones para los elementos con clasificaciones de datos "ciertos" (en el conjunto de datos de prueba).  
  

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 

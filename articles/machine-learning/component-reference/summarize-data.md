---
title: Resumen de datos
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Resumen de datos de Azure Machine Learning para generar un informe básico de estadísticas descriptivas de las columnas de un conjunto de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 459804fb7c3a962b7d3ee29928984f435f0dea06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565959"
---
# <a name="summarize-data"></a>Resumen de datos

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use el componente Resumen de datos para crear un conjunto de medidas estadísticas estándar que describan cada columna de la tabla de entrada.

Las estadísticas resumidas son útiles cuando se desea conocer las características del conjunto de información completo. Por ejemplo, podría necesitar saber:

- ¿Cuántos valores faltan en cada columna?
- ¿Cuántos valores únicos hay en una columna de características?
- ¿Cuál es la media y la desviación típica de cada columna?

El componente calcula las puntuaciones importantes de cada columna y devuelve una fila de estadísticas de resumen de cada variable (columna de datos) que se proporciona como entrada.

## <a name="how-to-configure-summarize-data"></a>Configuración de Summarize Data (Resumen de datos)  

1. Agregue el componente **Resumen de datos** a la canalización. Puede encontrar este componente en la categoría **Funciones estadísticas** del diseñador.

1. Seleccione el conjunto de datos para el que desea generar un informe.

    Si quiere un informe solo de algunas columnas, use el componente [Seleccionar columnas de conjunto de datos](select-columns-in-dataset.md) para proyectar un subconjunto de columnas con el que trabajar.

1. No hay ningún otro parámetro obligatorio. De manera predeterminada, el componente analiza todas las columnas proporcionadas como entrada y, en función del tipo de valores de las columnas, genera un conjunto relevante de estadísticas, como se explica en la sección [Resultados](#results).

1. Envíe la canalización.

## <a name="results"></a>Results

El informe del componente puede incluir las siguientes estadísticas. 

|Nombre de la columna|Descripción|
|------|------|  
|**Característica**|Nombre de la columna|
|**Recuento**|Recuento de filas|
|**Número de valores únicos**|Recuento de valores únicos en la columna|
|**Missing Value Count** (Número de valores ausentes)|Recuento de valores únicos en la columna|
|**Mín.**|Valor más bajo de la columna|  
|**Máx.**|Valor más alto de la columna|
|**Promedio**|Promedio de todos los valores de la columna|
|**Mean Deviation** (Desviación media)|Desviación media de los valores de la columna|
|**1st Quartile** (1er cuartil)|Valor en el primer cuartil|
|**Valor medio**|Valor de la mediana de la columna|
|**3rd Quartile** (3er cuartil)|Valor en el tercer cuartil|
|**Modo**|Modo de los valores de la columna|
|**Range**|Entero que representa el número de valores entre los valores máximo y mínimo|
|**Sample Variance** (Varianza de la muestra)|Varianza de la columna; consulte la nota|
|**Sample Standard Deviation** (Desviación típica de la muestra)|Desviación típica de la columna; consulte la nota|
|**Sample Skewness** (Sesgo de la muestra)|Sesgo de la columna; consulte la nota|
|**Sample Kurtosis** (Curtosis de la muestra)|Curtosis de la columna; consulte la nota|
|**P0.5**|Percentil 0,5 %|
|**P1**|Percentil 1 %|
|**P5**|Percentil 5 %|
|**P95**|Percentil 95 %|
|**P99.5**|Percentil 99,5 % |

## <a name="technical-notes"></a>Notas técnicas

- En el caso de las columnas no numéricas, solo se calculan los valores de Recuento, Recuento de valores únicos y Recuento de valores que faltan. En el caso de otras estadísticas, se devuelve un valor nulo.

- Las columnas que contienen valores booleanos se procesan mediante las siguientes reglas:

    - Al calcular el Mín., se aplica un operador lógico AND.
    
    - Al calcular el Máx., se aplica un operador lógico OR.
    
    - Al calcular Intervalo, el componente comprueba primero si el número de valores únicos de la columna es igual a 2.
    
    - Cuando se calcula cualquier estadística que requiere cálculos de punto flotante, los valores True se tratan como 1,0 y los valores False se tratan como 0,0.

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning.  

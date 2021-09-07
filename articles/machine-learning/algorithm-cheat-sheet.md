---
title: 'Hoja de características de los algoritmos de Machine Learning: diseñador'
titleSuffix: Azure Machine Learning
description: Una hoja de características de los algoritmos de Machine Learning que pueda imprimirse le ayuda a elegir el algoritmo correcto para el modelo de predicción en el diseñador de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 07/20/2021
adobe-target: true
ms.openlocfilehash: ebe02e264a9f3faea2812253eeced6d56319627d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742167"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Hoja de características de los algoritmos de Machine Learning para el diseñador de Azure Machine Learning

La **hoja de características de los algoritmos de Azure Machine Learning** lo ayuda a elegir el algoritmo correcto para un modelo de análisis predictivo.

Azure Machine Learning tiene una gran biblioteca de algoritmos de las familias ***clasificación** _, _*_sistemas de recomendación_*_, _*_agrupación en clústeres_*_, _*_detección de anomalías_*_, _*_regresión_*_ y _ *_análisis de texto_* *. Cada uno de ellos está diseñado para resolver un tipo de problema de aprendizaje automático diferente.

Para más información, consulte [Selección de algoritmos](how-to-select-algorithms.md).

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Descargar: Hoja de características de los algoritmos de Machine Learning

**Descargue aquí la hoja de referencia rápida: [Hoja de referencia rápida de algoritmos de aprendizaje automático (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Hoja de características de los algoritmos de Machine Learning: Aprenda a elegir un algoritmo de Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.png)

Descargue e imprima la hoja de referencia rápida de algoritmos de Machine Learning en tamaño tabloide para tenerlo a mano y que le sirva de ayuda al elegir un algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Cómo usar la hoja de características de los algoritmos de Machine Learning

Las sugerencias ofrecidas en esta hoja de referencia rápida de algoritmos son reglas generales aproximadas. Algunas se pueden ignorar y otras se pueden infringir de forma fragante. Esta hoja de referencia rápida tiene como finalidad sugerir un punto de partida. No tema ejecutar una competición directa entre varios algoritmos en sus datos. Realmente no hay sustituto para comprender los principios de cada algoritmo y el sistema que generó los datos.

Cada algoritmo de aprendizaje automático tiene su propio estilo o sesgo inductivo. Para un problema específico, varios algoritmos pueden ser apropiados y un algoritmo puede servir mejor que otros. Pero no siempre es posible saber de antemano cuál es la mejor opción. En estos casos, varios algoritmos se muestran juntos en la hoja de referencia rápida. Una estrategia adecuada sería probar un algoritmo y, si los resultados no resultan satisfactorios, probar los demás. 

Para más información sobre los algoritmos del diseñador de Azure Machine Learning, vaya a la [referencia de módulo y algoritmo](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Variantes del aprendizaje automático

Existen tres categorías principales de aprendizaje automático: *aprendizaje supervisado*, *aprendizaje sin supervisar* y *aprendizaje de refuerzo*.

### <a name="supervised-learning"></a>Aprendizaje supervisado

En el aprendizaje supervisado, cada punto de datos se etiqueta o asocia con una categoría o valor de interés. Un ejemplo de una etiqueta de categoría es asignar una imagen como un 'gato' o 'perro'. Un ejemplo de una etiqueta de valor es el precio de venta asociado a un coche usado. El objetivo del aprendizaje supervisado es estudiar muchos ejemplos etiquetados como los anteriores y, luego, poder realizar predicciones sobre los puntos de datos futuros. Por ejemplo, identificar nuevas fotografías con el animal correcto o asignar precios de venta precisos a otros coches usados. Este es un tipo conocido y útil del aprendizaje automático.

### <a name="unsupervised-learning"></a>Aprendizaje sin supervisión

En el aprendizaje sin supervisar, los puntos de datos no tienen etiquetas asociadas a ellos. En su lugar, el objetivo de un algoritmo de aprendizaje sin supervisar es organizar los datos de alguna manera o describir su estructura. El aprendizaje sin supervisar agrupa los datos en clústeres, al igual que K-means, o busca diferentes maneras de examinar datos complejos para que parezcan más simples.

### <a name="reinforcement-learning"></a>Aprendizaje de refuerzo

En el aprendizaje de refuerzo, el algoritmo elige una acción en respuesta a cada punto de datos. Se trata de un enfoque común en robótica, donde el conjunto de lecturas del sensor en un punto en el tiempo es un punto de datos, y el algoritmo debe elegir la siguiente acción del robot. También resulta perfecto para las aplicaciones de Internet de las cosas. El algoritmo de aprendizaje también recibe una señal de recompensa un poco más adelante, que indica cómo de buena fue la decisión. Según esta señal, el algoritmo modifica su estrategia para lograr la mayor recompensa. 

## <a name="next-steps"></a>Pasos siguientes

* Consulte más información en [Selección de algoritmos](how-to-select-algorithms.md).

* [Obtenga información sobre Studio en Azure Machine Learning y Azure Portal](overview-what-is-azure-machine-learning.md).

* [Tutorial: Compilar un modelo de predicción en el diseñador de Azure Machine Learning ](tutorial-designer-automobile-price-train-score.md).

* [Más información sobre el aprendizaje profundo y el aprendizaje automático](concept-deep-learning-vs-machine-learning.md).

---
title: 'Entrenamiento de un modelo de detección de anomalías: referencia de componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Entrenamiento de un modelo de detección de anomalías para crear un modelo de detección de anomalías entrenado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 8114492c9740cf706e9d2ba6fbd99f22029ba769
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289838"
---
# <a name="train-anomaly-detection-model-component"></a>Componente Entrenamiento de un modelo de detección de anomalías

En este artículo se explica cómo usar el componente Entrenamiento de un modelo de detección de anomalías del diseñador de Azure Machine Learning para crear un modelo de detección de anomalías entrenado.

El componente toma como entrada un conjunto de parámetros de un modelo de detección de anomalías y un conjunto de datos sin etiquetar. Devuelve un modelo de detección de anomalías entrenado junto con un conjunto de etiquetas para los datos de entrenamiento.  

Para más información sobre los algoritmos de detección de anomalías que se proporcionan en el diseñador, consulte [Detección de anomalías basada en PCA](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Configuración del entrenamiento de un modelo de detección de anomalías 

1.  Agregue el componente **Entrenamiento de un modelo de detección de anomalías** a la canalización del diseñador. Puede encontrar este componente en la categoría **Detección de anomalías**.

2. Conecte uno de los componentes diseñados para la detección de anomalías, como [Detección de anomalías basada en PCA](pca-based-anomaly-detection.md).

    No se admiten otros tipos de modelos. Al ejecutar la canalización, obtendrá el error "All models must have the same learner type" (Todos los modelos tienen que tener el mismo tipo de aprendiz).  

3.  Para configurar el componente de detección de anomalías, seleccione la columna de etiqueta y establezca otros parámetros específicos del algoritmo.  

4.  Asocie un conjunto de datos de entrenamiento a la entrada de la derecha de **Train Anomaly Detection Mode** (Entrenamiento de un modelo de detección de anomalías).  

5.  Envíe la canalización.  

## <a name="results"></a>Results

Una vez completado el entrenamiento:

+ Para ver los parámetros del modelo, haga clic con el botón derecho en el componente y seleccione **Visualizar**. 

+ Para crear predicciones, use el componente [Puntuación de modelo](score-model.md) con datos de entrada nuevos.

+ Para guardar una instantánea del modelo entrenado, seleccione el componente. A continuación, seleccione el icono de **Registro de un conjunto de datos** en la pestaña **Outputs+logs** (Salidas+registros) del panel derecho.   

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 

Vea [Excepciones y códigos de error del diseñador](designer-error-codes.md) para examinar una lista de errores específicos de los componentes del diseñador.

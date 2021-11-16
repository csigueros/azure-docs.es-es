---
title: 'Entrenar modelo de agrupación en clústeres: referencia del componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el módulo Entrenar modelo de agrupación en clústeres en Azure Machine Learning para entrenar modelos de agrupación en clústeres.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 16d20e71dba5c83093ec572aad4910ec9a89ba06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565997"
---
# <a name="train-clustering-model"></a>Entrenamiento del modelo de agrupación en clústeres

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para entrenar un modelo de agrupación en clústeres.

El componente toma un modelo de agrupación en clústeres no entrenado que ya ha configurado mediante el componente [Agrupación en clústeres k-means](k-means-clustering.md) y entrena el componente utilizando un conjunto de datos sin etiqueta o con etiqueta. El componente crea un modelo entrenado que puede usar para la predicción y un conjunto de asignaciones de clúster para cada caso de los datos de entrenamiento.

> [!NOTE]
> No se puede entrenar un modelo de agrupación en clústeres con el componente [Entrenar modelo](train-model.md), que es el componente genérico para el entrenamiento de modelos de Machine Learning. Es debido a que [Entrenar modelo](train-model.md) solo funciona con algoritmos de aprendizaje supervisado. K-means y otros algoritmos de agrupación en clústeres permiten el aprendizaje no supervisado, lo que significa que el algoritmo puede aprender de los datos sin etiqueta.  
  
## <a name="how-to-use-train-clustering-model"></a>Procedimiento para usar el modelo de entrenamiento de almacenamiento en clúster  

1.  Agregue el componente **Entrenar modelo de agrupación en clústeres** a la canalización en el diseñador. Puede encontrar el componente en **Componentes de Machine Learning** de la categoría **Entrenar**.  
  
2. Agregue el componente [Agrupación en clústeres k-means](k-means-clustering.md) u otro componente personalizado que cree un modelo de agrupación en clústeres compatible y establezca los parámetros del modelo de agrupación en clústeres.  
    
3.  Adjunte un conjunto de datos de entrenamiento a la entrada de la derecha de **Entrenamiento del modelo de agrupación en clústeres**.
  
5.  En **Conjunto de columnas**, seleccione las columnas del conjunto de datos que desea utilizar para compilar clústeres. Asegúrese de seleccionar columnas que sean buenas características: por ejemplo, evite utilizar id. u otras columnas que tengan valores únicos o columnas que tengan todos los mismos valores.

    Si hay una etiqueta, puede usarla como una característica u omitirla.  
  
6. Seleccione la opción **Check for Append or Uncheck for Result Only** (Marcar para anexar o no marcar para resultados solo), si desea producir los datos de entrenamiento junto con la nueva etiqueta de clúster.

    Si anula la selección de esta opción, la salida solo contiene las asignaciones de clúster. 

7. Envíe la canalización o haga clic en el componente **Entrenar modelo de agrupación en clústeres** y seleccione **Ejecutar seleccionados**.  
  
### <a name="results"></a>Results

Una vez completado el entrenamiento:

+ Para guardar una instantánea del modelo entrenado, seleccione la pestaña **Salidas** en el panel derecho del componente **Entrenar modelo**. Seleccione el icono **Registrar conjunto de datos** para guardar el modelo como componente reutilizable.

+ Para generar puntuaciones a partir del modelo, utilice [Asignación de datos a clústeres](assign-data-to-clusters.md).

> [!NOTE]
> Si necesita implementar el modelo entrenado en el diseñador, asegúrese de que esté conectado el módulo [Asignación de datos a clústeres](assign-data-to-clusters.md) en lugar de **Puntuación del modelo** a la entrada del [componente de salida del servicio web](web-service-input-output.md) en la canalización de inferencia.

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
---
title: 'Aplicar transformación: Referencia de componentes'
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar el componente Aplicar transformación en Azure Machine Learning para modificar un conjunto de datos de entrada basado en una transformación calculada previamente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: 3091c4d85594f5bcb4236f04f8d58e21dfe125de
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566070"
---
# <a name="apply-transformation-component"></a>Componente Aplicar transformación

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Utilice este componente para modificar un conjunto de datos de entrada basado en una transformación calculada previamente. Este componente es necesario si tiene que actualizar transformaciones en canalizaciones de inferencia.

Por ejemplo, si usa puntuaciones z para normalizar los datos de aprendizaje utilizando el componente **Normalizar datos**, tendrá que usar el valor de puntuación z que se calculó para el aprendizaje durante la fase de puntuación. En Azure Machine Learning, puede guardar el método de normalización como una transformación y después usar **Aplicar transformación** para aplicar la puntuación z a los datos de entrada antes de la puntuación.

## <a name="how-to-save-transformations"></a>Procedimiento para guardar transformaciones

El diseñador le permite guardar transformaciones de datos como **conjuntos de datos** para poder usarlos en otras canalizaciones.

1. Seleccione un componente de transformación de datos que se haya ejecutado correctamente.

1. Seleccione la pestaña **Outputs + logs** (Resultados y registros).

1. Busque la salida de la transformación y seleccione **Registrar conjunto de datos** para guardarlo como un componente en la categoría **Conjuntos de datos** de la paleta de componentes.

## <a name="how-to-use-apply-transformation"></a>Cómo se usa Aplicar transformación  
  
1. Agregue el componente **Aplicación de la transformación** a la canalización. Puede encontrar este componente en la sección **Puntuación y evaluación de modelos** de la paleta de componentes. 
  
1. Busque la transformación guardada que quiera usar en el **Conjunto de datos** en la paleta de componentes.

1. Conecte la salida de la transformación guardada al puerto de entrada izquierdo del componente **Aplicar transformación**.

    El conjunto de datos debe tener exactamente el mismo esquema (número de columnas, nombres de columna, tipos de datos) que el conjunto de datos para el que la transformación se ha diseñó inicialmente.  
  
1. Conecte la salida del conjunto de datos del componente deseado al puerto de entrada derecho del componente **Aplicar transformación**.
  
1. Para aplicar una transformación al nuevo conjunto de datos, envíe la canalización.  

> [!IMPORTANT]
> Para asegurarse de que la transformación actualizada en las canalizaciones de entrenamiento también es factible en las canalizaciones de inferencia, debe seguir los pasos que se indican a continuación cada vez que haya una transformación actualizada en la canalización de entrenamiento:
> 1. En la canalización de entrenamiento, registre la salida de [Select Columns Transform](select-columns-transform.md) (Seleccionar transformación de columnas) como un conjunto de registros.
> ![Registro del conjunto de datos de salida del componente](media/module/select-columns-transform-register-dataset.png)
> 1. En la canalización de inferencia, quite el componente **TD-** y reemplácelo por el conjunto de datos registrado en el paso anterior.
> ![Reemplazar el componente TD](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
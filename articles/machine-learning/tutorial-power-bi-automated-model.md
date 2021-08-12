---
title: 'Tutorial: Creación del modelo predictivo mediante el aprendizaje automático automatizado (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a crear e implementar modelos de aprendizaje automático automatizado, de modo que pueda usar el mejor de ellos para predecir los resultados en Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 4d760f5017c20ac88d3329bb79937c971d4fba75
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459431"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Tutorial: Integración de Power BI: creación del modelo predictivo mediante el aprendizaje automático automatizado (parte 1 de 2)

En la parte 1 de este tutorial, va a entrenar e implementar un modelo de aprendizaje automático predictivo. Puede usar el aprendizaje automático automatizado (ML) de Azure Machine Learning Studio.  En la parte 2, usará el modelo con el mejor rendimiento para predecir los resultados en Microsoft Power BI.

En este tutorial ha:

> [!div class="checklist"]
> * Creará un clúster de proceso de Azure Machine Learning.
> * Crear un conjunto de datos.
> * Creará una ejecución de aprendizaje automático automatizado.
> * Implementará el mejor modelo en un punto de conexión de puntuación en tiempo real.


Existen tres formas diferentes de crear e implementar el modelo que se va a usar en Power BI.  En este artículo se describe la "Opción C: Entrenamiento e implementación de modelos mediante el aprendizaje automático automatizado en Studio".  Esta opción es una experiencia de creación sin código. Con ella, se automatiza totalmente la preparación de los datos y el entrenamiento del modelo. 

Sin embargo, podría usar una de las otras opciones:

* [Opción A: Entrenamiento e implementación de modelos con cuadernos de Jupyter Notebook](tutorial-power-bi-custom-model.md). En esta experiencia de creación donde el código es lo primero se usan cuadernos de Jupyter Notebook que se hospedan en Azure Machine Learning Studio.
* [Opción B: Entrenamiento e implementación de modelos con el diseñador de Azure Machine Learning](tutorial-power-bi-designer-model.md). En esta experiencia de creación con poco código se usa una interfaz de usuario basada en el método de arrastrar y colocar.

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. Si aún no tiene una suscripción, puede usar una [evaluación gratuita](https://azure.microsoft.com/free/). 
- Un área de trabajo de Azure Machine Learning. Si aún no tiene un área de trabajo, consulte [Creación y administración de áreas de trabajo de Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Creación de un clúster de proceso

En el aprendizaje automático automatizado se entrenan muchos modelos de aprendizaje automático hasta encontrar el algoritmo y los parámetros "mejores". Azure Machine Learning realiza en paralelo la ejecución del entrenamiento del modelo a través de un clúster de proceso.

Para empezar, en [Azure Machine Learning Studio](https://ml.azure.com), en el menú de la izquierda, seleccione **Proceso**. Abra la pestaña **Clústeres de proceso**. A continuación, seleccione **Nuevo**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Captura de pantalla que muestra cómo crear un clúster de proceso.":::

En la página **Create compute cluster** (Crear clúster de proceso):

1. Seleccione un tamaño de máquina virtual. En este tutorial, una máquina **Standard_D11_v2** está bien.
1. Seleccione **Next** (Siguiente).
1. Proporcione un nombre de proceso válido.
1. Mantenga **Número mínimo de nodos** en `0`.
1. Cambie **Número máximo de nodos** a `4`.
1. Seleccione **Crear**.

El estado del clúster cambia a **Creando**.

>[!NOTE]
> El nuevo clúster tiene 0 nodos, por lo que no se generan costos de proceso. Solo se generan costos cuando se ejecuta el trabajo de aprendizaje automático automatizado. El clúster se escala de nuevo a 0 automáticamente al cabo de 120 segundos de tiempo de inactividad.


## <a name="create-a-dataset"></a>Crear un conjunto de datos

En este tutorial, usará el [conjunto de datos Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Este conjunto de datos está disponible en [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Para crear el conjunto de datos, en el menú de la izquierda, seleccione **Conjuntos de datos**. A continuación, seleccione **Crear conjunto de datos**. Tiene las siguientes opciones:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Captura de pantalla que muestra cómo crear un conjunto de datos.":::

Seleccione **From Open Datasets** (Desde Open Datasets). A continuación, en la página **Create dataset from Open Datasets** (Crear conjunto de datos desde Open Datasets):

1. Use la barra de búsqueda para encontrar *diabetes*.
1. Seleccione **Muestra: Diabetes**.
1. Seleccione **Next** (Siguiente).
1. Asigne un nombre al conjunto de datos *diabetes*.
1. Seleccione **Crear**.

Para explorar los datos, seleccione el conjunto de datos y, a continuación, elija **Explorar**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Captura de pantalla que muestra cómo explorar un conjunto de datos.":::

Los datos contienen 10 variables de entrada de base de referencia, como la edad, el sexo, el índice de masa corporal, la presión arterial media y seis mediciones del suero sanguíneo. También contienen una variable de destino, denominada **Y**. Esta variable de destino es una medida cuantitativa de la progresión de la diabetes un año después de la base de referencia.

## <a name="create-an-automated-machine-learning-run"></a>Creación de una ejecución de aprendizaje automático automatizado

En [Azure Machine Learning Studio](https://ml.azure.com), en el menú de la izquierda, seleccione **ML automatizado**. Luego, seleccione **New automated ML run** (Nueva ejecución de ML automatizado):

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Captura de pantalla que muestra cómo crear una ejecución de aprendizaje automático automatizado.":::

A continuación, seleccione el conjunto de datos **diabetes** que creó anteriormente. Después, seleccione **Siguiente**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Captura de pantalla que muestra cómo seleccionar un conjunto de datos.":::
 
En la página **Configure run** (Configurar ejecución):

1. En **Nombre del experimento**, seleccione **Crear nuevo**.
1. Asigne un nombre al experimento.
1. En el campo **Columna de destino**, seleccione **Y**.
1. En el campo **Seleccionar clúster de proceso**, seleccione el clúster de proceso que creó anteriormente. 

El formulario completado se parecerá a este:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Captura de pantalla que muestra cómo configurar el aprendizaje automático automatizado.":::

Por último, seleccione una tarea de aprendizaje automático. En este caso, la tarea es **Regresión**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Captura de pantalla que muestra cómo configurar una tarea.":::

Seleccione **Finalizar**.

> [!IMPORTANT]
> Con el aprendizaje automático automatizado se tardan unos 30 minutos en completar el entrenamiento de los 100 modelos.

## <a name="deploy-the-best-model"></a>Implementación del mejor modelo

Cuando finalice el aprendizaje automático automatizado, puede seleccionar la pestaña **Modelos** para ver todos los modelos de aprendizaje automático que se han probado. Los modelos se ordenan por rendimiento, estando en primer lugar el modelo con el mejor rendimiento. Después de seleccionar el mejor modelo, se habilita el botón **Implementar**:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Captura de pantalla que muestra la lista de modelos.":::

Seleccione **Implementar** para abrir una ventana **Implementar un modelo**:

1. Asigne al servicio de modelo el nombre *diabetes-modelo*.
1. Seleccione **Azure Container Service**.
1. Seleccione **Implementar**.

Verá un mensaje que indica que el modelo se ha implementado correctamente.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha visto cómo entrenar e implementar un modelo de aprendizaje automático mediante el aprendizaje automático automatizado. En el siguiente tutorial, aprenderá a consumir (puntuar) este modelo en Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumo de un modelo en Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

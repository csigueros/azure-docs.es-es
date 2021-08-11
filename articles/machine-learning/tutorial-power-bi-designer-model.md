---
title: 'Tutorial: Arrastrar y colocar para crear el modelo predictivo (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a crear e implementar un modelo predictivo de aprendizaje automático mediante el diseñador. Después, este modelo se puede usar para predecir los resultados en Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 9390fc53f4d10ad770313f1f1459f02fc7e7a998
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459449"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Tutorial: Integración de Power BI: Arrastrar y colocar para crear el modelo predictivo (parte 1 de 2)

En la parte 1 de este tutorial, va a entrenar e implementar un modelo de aprendizaje automático predictivo mediante el diseñador de Azure Machine Learning. El diseñador es una interfaz de usuario del tipo arrastrar y colocar, con poco código. En la parte 2, usará el modelo para predecir los resultados en Microsoft Power BI.

En este tutorial ha:

> [!div class="checklist"]
> * Creará una instancia de proceso de Azure Machine Learning.
> * Creará un clúster de inferencia de Azure Machine Learning.
> * Crear un conjunto de datos.
> * Entrenará un modelo de regresión.
> * Implementará el modelo en un punto de conexión de puntuación en tiempo real.


Existen tres formas diferentes de crear e implementar el modelo que se va a usar en Power BI.  En este artículo se describe la "Opción B: Entrenamiento e implementación de modelos mediante el diseñador".  Esta opción es una experiencia de creación con poco código que usa la interfaz del diseñador.  

Sin embargo, podría usar una de las otras opciones:

* [Opción A: Entrenamiento e implementación de modelos con cuadernos de Jupyter Notebook](tutorial-power-bi-custom-model.md). En esta experiencia de creación donde el código es lo primero se usan cuadernos de Jupyter Notebook que se hospedan en Azure Machine Learning Studio.
* [Opción C: Entrenamiento e implementación de modelos mediante aprendizaje automático automatizado](tutorial-power-bi-automated-model.md). En esta experiencia de creación sin código se automatiza totalmente la preparación de los datos y el entrenamiento del modelo.

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. Si aún no tiene una suscripción, puede usar una [evaluación gratuita](https://azure.microsoft.com/free/). 
- Un área de trabajo de Azure Machine Learning. Si aún no tiene un área de trabajo, consulte [Creación y administración de áreas de trabajo de Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conocimiento básico de los flujos de trabajo de aprendizaje automático.


## <a name="create-compute-to-train-and-score"></a>Creación de un proceso para entrenar y puntuar

En esta sección se creará una *instancia de proceso*. Las instancias de proceso se usan para entrenar modelos de aprendizaje automático. También podrá crear un *clúster de inferencia* para hospedar el modelo implementado para la puntuación en tiempo real.

Inicie sesión en [Azure Machine Learning Studio](https://ml.azure.com). En el menú de la izquierda, seleccione **Proceso** y, a continuación, **Nuevo**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Captura de pantalla que muestra cómo crear una instancia de proceso.":::

A continuación, en la página **Create compute instance** (Crear instancia de proceso), seleccione un tamaño de máquina virtual. Para este tutorial, seleccione una máquina virtual **Standard_D11_v2**. Luego, seleccione **Siguiente**. 

En la página **Configuración**, asigne un nombre a la instancia de proceso. Seleccione **Crear**. 

>[!TIP]
> También puede usar la instancia de proceso para crear y ejecutar cuadernos.

El **estado** de la instancia de proceso es ahora **Creación en curso**. La máquina tarda unos 4 minutos en aprovisionarse. 

Mientras espera, en la página **Proceso**, seleccione la pestaña **Inference clusters** (Clústeres de inferencia). A continuación, seleccione **Nuevo**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Captura de pantalla que muestra cómo crear un clúster de inferencia.":::

En la página **Creación de un clúster de inferencia**, seleccione una región y un tamaño de máquina virtual. Para este tutorial, seleccione una máquina virtual **Standard_D11_v2**. Luego, seleccione **Siguiente**. 

En la página **Parámetros de configuración**, haga lo siguiente:

1. Proporcione un nombre de proceso válido.
1. Como finalidad del clúster, seleccione **Desarrollo/pruebas**. Esta opción crea un solo nodo para hospedar el modelo implementado.
1. Seleccione **Crear**.

El **estado** del clúster de inferencia es ahora **Creación en curso**. El clúster de un solo nodo tarda aproximadamente 4 minutos en implementarse.

## <a name="create-a-dataset"></a>Crear un conjunto de datos

En este tutorial, usará el [conjunto de datos Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Este conjunto de datos está disponible en [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Para crear el conjunto de datos, en el menú de la izquierda, seleccione **Conjuntos de datos**. A continuación, seleccione **Crear conjunto de datos**. Tiene las siguientes opciones:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Captura de pantalla que muestra cómo crear un conjunto de datos.":::

Seleccione **From Open Datasets** (Desde Open Datasets). En la página **Create dataset from Open Datasets** (Crear conjunto de datos desde Open Datasets):

1. Use la barra de búsqueda para encontrar *diabetes*.
1. Seleccione **Muestra: Diabetes**.
1. Seleccione **Next** (Siguiente).
1. Asigne un nombre al conjunto de datos *diabetes*.
1. Seleccione **Crear**.

Para explorar los datos, seleccione el conjunto de datos y, a continuación, elija **Explorar**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Captura de pantalla que muestra cómo explorar un conjunto de datos.":::

Los datos contienen 10 variables de entrada de base de referencia, como la edad, el sexo, el índice de masa corporal, la presión arterial media y seis mediciones del suero sanguíneo. También contienen una variable de destino, denominada **Y**. Esta variable de destino es una medida cuantitativa de la progresión de la diabetes un año después de la base de referencia.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Creación de un modelo de aprendizaje automático mediante el diseñador

Después de crear el proceso y los conjuntos de datos, puede usar el diseñador para crear el modelo de aprendizaje automático. En Azure Machine Learning Studio, seleccione **Diseñador** y, luego, **Nueva canalización**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Captura de pantalla que muestra cómo crear una canalización.":::

Puede ver un *lienzo* en blanco y un menú **Configuración**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Captura de pantalla que muestra cómo seleccionar un destino de proceso.":::

En el menú **Configuración**, elija **Select compute target** (Seleccionar destino de proceso). Seleccione la instancia de proceso que creó anteriormente y, a continuación, elija **Guardar**. Cambie el **nombre del borrador** por algo más fácil de recordar, como *diabetes-model*. Finalmente, escriba una descripción.

En la lista de recursos, expanda **Conjuntos de datos** y busque el conjunto de datos llamado **diabetes**. Arrastre este componente hasta el lienzo:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Captura de pantalla que muestra cómo arrastrar un componente al lienzo.":::

A continuación, arrastre los siguientes componentes al lienzo:

1. **Linear regression** (Regresión lineal) (ubicado en **Machine Learning Algorithms** [Algoritmos de aprendizaje automático]).
1. **Train model** (Entrenar modelo) (ubicado en **Model Training** [Entrenamiento de modelos]).

En el lienzo, observe los círculos que se encuentra en la parte superior e inferior de los componentes. Estos círculos son puertos.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Captura de pantalla que muestra los puertos en los componentes desconectados.":::
 
Ahora *conecte* los componentes. Seleccione el puerto situado en la parte inferior del conjunto de datos **diabetes**. Arrástrelo al puerto de la parte superior derecha del componente **Train Model** (Entrenar modelo). Seleccione el puerto situado en la parte inferior del componente **Linear Regression** (Regresión lineal). Arrástrelo al puerto de la parte superior izquierda del componente **Train Model** (Entrenar modelo).

Elija la columna del conjunto de datos que se va a usar como variable de etiqueta (de destino) que se predecirá. Seleccione el componente **Train model** (Entrenar modelo) seguido de **Editar columna**. 

En el cuadro de diálogo, seleccione **Enter column name** (Escribir el nombre de la columna) > **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Captura de pantalla que muestra cómo seleccionar una columna de etiqueta.":::

Seleccione **Guardar**. El *flujo de trabajo* de aprendizaje automático debe tener este aspecto:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Captura de pantalla que muestra los componentes conectados.":::

Seleccione **Submit** (Enviar). En **Experimento**, seleccione **Crear nuevo**. Asigne un nombre al experimento y, a continuación, seleccione **Enviar**.

>[!NOTE]
> La primera ejecución del experimento debe durar unos 5 minutos. Las ejecuciones posteriores son mucho más rápidas ya que el diseñador almacena en caché los componentes ya ejecutados para reducir la latencia.

Cuando el experimento finalice, verá esta vista:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Captura de pantalla que muestra una ejecución finalizada.":::

Para inspeccionar los registros del experimento, seleccione **Train Model** (Entrenar modelo) y, a continuación, seleccione **Resultados y registros**.

## <a name="deploy-the-model"></a>Implementación del modelo

Para implementar el modelo, en la parte superior del lienzo, seleccione **Create inference pipeline** > **Real-time inference pipeline** (Crear canalización de inferencia > Canalización de inferencia en tiempo real):

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Captura de pantalla que muestra dónde seleccionar una canalización de inferencia en tiempo real.":::
 
La canalización se simplifica a solo los componentes necesarios para puntuar el modelo. Al puntuar los datos, no conocerá los valores de las variables de destino. Por lo tanto, puede quitar **Y** del conjunto de datos. 

Para quitar **Y**, agregue al lienzo un componente **Select columns in Dataset** (Seleccionar columnas del conjunto de datos). Conecte el componente para que el conjunto de datos denominado "diabetes" sea la entrada. Los resultados son la salida del componente **Score Model** (Puntuar modelo):

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Captura de pantalla que muestra la eliminación de una columna.":::

En el lienzo, seleccione el componente **Select Columns in Dataset** (Selección de columnas del conjunto de datos) seguido de **Edit Columns** (Editar columnas). 

En el cuadro de diálogo **Seleccionar columnas**, elija **Por nombre**. A continuación, asegúrese de que todas las variables de entrada estén seleccionadas y que el destino *no* esté seleccionado:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Captura de pantalla que muestra la eliminación de la configuración de las columnas.":::

Seleccione **Guardar**. 

Por último, seleccione el componente **Score Model** (Puntuar modelo) y asegúrese de que la casilla **Append score columns to output** (Anexar columnas de puntuación a la salida) esté desactivada. Para reducir la latencia, las predicciones se devuelven sin las entradas.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Captura de pantalla que muestra la configuración del componente Score Model (Puntuar modelo).":::

En la parte superior del lienzo, seleccione **Enviar**.

Cuando haya ejecutado correctamente la canalización de inferencia, puede implementar el modelo en el clúster de inferencia. Seleccione **Implementar**. 

En el cuadro de diálogo **Set-up real-time endpoint** (Configurar punto de conexión en tiempo real), seleccione **Deploy new real-time endpoint** (Implementar nuevo punto de conexión en tiempo real). Asigne al punto de conexión el nombre *my-diabetes-model*. Seleccione la inferencia que creó anteriormente y, a continuación, seleccione **Implementar**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Captura de pantalla que muestra la configuración del punto de conexión en tiempo real.":::
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha visto cómo entrenar e implementar un modelo del diseñador. En la siguiente parte, aprenderá a usar (puntuar) este modelo desde Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumo de un modelo en Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

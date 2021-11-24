---
title: Configuración de AutoML con la interfaz de usuario de Estudio
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo configurar ejecuciones de entrenamiento de AutoML sin una sola línea de código con aprendizaje automático automatizado de Azure Machine Learning en Estudio de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: automl, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: d4c4188a04db444a153577ead82d79f32c9b137d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518260"
---
# <a name="set-up-no-code-automl-training-with-the-studio-ui"></a>Configuración del entrenamiento de AutoML sin código con la interfaz de usuario de Estudio 

En este artículo, obtenga información sobre cómo configurar ejecuciones de entrenamiento de AutoML sin una sola línea de código mediante aprendizaje automático automatizado de Azure Machine Learning en [Estudio de Azure Machine Learning](overview-what-is-machine-learning-studio.md).

El aprendizaje automático automatizado, AutoML, es un proceso en el que se selecciona automáticamente el mejor algoritmo de aprendizaje automático para sus datos específicos. Este proceso le permite generar modelos de aprendizaje automático rápidamente. [Más información sobre cómo Azure Machine Learning implementa el aprendizaje automático automatizado](concept-automated-ml.md).
 
Para obtener un ejemplo completo, pruebe el [Tutorial: Aprendizaje automático automatizado y entrenamiento de modelos de clasificación sin código](tutorial-first-experiment-automated-ml.md). 

Si prefiere una experiencia basada en código de Python, [configure sus experimentos de aprendizaje automático automatizado](how-to-configure-auto-train.md) con el SDK de Azure Machine Learning.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Un área de trabajo de Azure Machine Learning. Consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md). 

## <a name="get-started"></a>Introducción

1. Inicie sesión en [Azure Machine Learning Studio](https://ml.azure.com). 

1. Seleccione su suscripción y área de trabajo. 

1. Navegue al panel izquierdo. Seleccione **Automated ML** (ML automatizado) en la sección **Creación**.

[![Panel de navegación de Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Si es la primera vez que realiza algún experimento, verá una lista vacía y vínculos a la documentación. 

De lo contrario, verá una lista de los experimentos de aprendizaje automático automatizado recientes, incluidos los creados con el SDK. 

## <a name="create-and-run-experiment"></a>Creación y ejecución de un experimento

1. Seleccione **+ New automated ML run** (+ Nueva ejecución de ML automatizada) y rellene el formulario.

1. Seleccione un conjunto de datos del contenedor de almacenamiento o cree un nuevo conjunto de datos. Los conjuntos de datos se pueden crear a partir de archivos locales, direcciones URL web, almacenes de datos o Azure Open Datasets. Obtenga más información sobre la [creación de conjuntos de datos](how-to-create-register-datasets.md).  

    >[!Important]
    > Requisitos para los datos de entrenamiento:
    >* Los datos deben estar en formato tabular.
    >* El valor que quiere predecir (columna de destino) debe estar presente en los datos.

    1. Para crear un nuevo conjunto de datos a partir de un archivo del equipo local, seleccione **+Crear conjunto de datos** y seleccione **From local file** (Desde archivo local). 

    1. En el formulario **Información básica**, asígnele un nombre único al conjunto de datos e incluya una descripción opcional. 

    1. Seleccione **Siguiente** para abrir el formulario **Datastore and file selection** (Almacén de datos y selección de archivos). En este formulario, seleccione dónde quiere cargar el conjunto de datos: el contenedor de almacenamiento predeterminado que se crea automáticamente con el área de trabajo, o bien elija un contenedor de almacenamiento que quiera usar para el experimento. 
    
        1. Si los datos están detrás de una red virtual, debe habilitar la función de **omitir la validación** para asegurarse de que el área de trabajo pueda tener acceso a los datos. Para obtener más información, consulte [Uso de Azure Machine Learning en una red virtual de Azure](how-to-enable-studio-virtual-network.md). 
    
    1. Seleccione **Examinar** para cargar el archivo de datos del conjunto de datos. 

    1. Revise el formulario **Settings and preview** (Configuración y vista previa) para ver que todo está correcto. El formulario se rellena de forma inteligente según el tipo de archivo. 

        Campo| Descripción
        ----|----
        Formato de archivo| Define el diseño y el tipo de datos almacenados en un archivo.
        Delimitador| Uno o más caracteres para especificar el límite entre regiones independientes en texto sin formato u otros flujos de datos.
        Encoding| Identifica qué tabla de esquema de bit a carácter se va a usar para leer el conjunto de elementos.
        Encabezados de columna| Indica cómo se tratarán los encabezados del conjunto de datos, si existen.
        Omitir filas | Indica el número de filas, si hay alguna, que se omiten en el conjunto de datos.
    
        Seleccione **Next** (Siguiente).

    1. El formulario **Esquema** se rellena de forma inteligente en función de las selecciones realizadas en el formulario **Settings and preview** (Configuración y vista previa). Aquí se configura el tipo de datos para cada columna, se revisan los nombres de columna y se seleccionan las columnas que **no se van a incluir** en el experimento. 
            
        Seleccione **Siguiente**.

    1. En el formulario **Confirmar detalles** se muestra un resumen de la información que se ha rellenado anteriormente en los formularios **Información básica** y **Settings and preview** (Configuración y vista previa). También tiene la opción de crear un perfil de datos para el conjunto de datos mediante un proceso habilitado para la generación de perfiles. Más información acerca de la [generación de perfiles de datos](how-to-connect-data-ui.md#profile).

        Seleccione **Next** (Siguiente).
1. Seleccione el conjunto de datos recién creado cuando aparezca. También puede ver una vista previa del conjunto de datos y las estadísticas de ejemplo. 

1. En el formulario **Configurar ejecución**, seleccione **Crear nuevo** y escriba **Tutorial-automl-deploy** para el nombre del experimento.

1. Seleccione una columna de destino; esta es la columna en la que realizará las predicciones.

1. Seleccione un tipo de proceso para la generación de perfiles de los datos y el trabajo de entrenamiento. Puede seleccionar un [clúster de proceso](concept-compute-target.md#azure-machine-learning-compute-managed) o una [instancia de proceso](concept-compute-instance.md). 
    
1. Seleccione un proceso en la lista desplegable de los procesos existentes.  Para crear un nuevo proceso, siga las instrucciones del paso 8.

1. Seleccione **Create a new compute** (Crear un proceso) para configurar el contexto del proceso de este experimento.

    Campo|Descripción
    ---|---
    Nombre del proceso| Escriba un nombre único que identifique el contexto del proceso.
    Prioridad de la máquina virtual| Las máquinas virtuales de prioridad baja son más económicas, pero no garantizan nodos de proceso. 
    Tipo de máquina virtual| Seleccione la CPU o GPU para el tipo de máquina virtual.
    Tamaño de la máquina virtual| Seleccione el tamaño de la máquina virtual para el proceso.
    Nodos mín./máx.| Para generar perfiles de datos, debe especificar uno o más nodos. escriba el número máximo de nodos para el proceso. El valor predeterminado es seis nodos para un proceso de AML.
    Configuración avanzada | Esta configuración le permite configurar una cuenta de usuario y una red virtual existente para el experimento. 
    
    Seleccione **Crear**. La creación de un nuevo proceso puede tardar unos minutos.

    >[!NOTE]
    > Su nombre de proceso indicará si el proceso que selecciona o crea *admite la generación de perfiles* . (Consulte la sección sobre la [generación de perfiles de los datos](how-to-connect-data-ui.md#profile) para más detalles ).

    Seleccione **Next** (Siguiente).

1. En el formulario **Task type and settings** (Tipo de tarea y configuración), seleccione el tipo de tarea: clasificación, regresión o previsión. Para más información, vea los [tipos de tareas admitidos](concept-automated-ml.md#when-to-use-automl-classification-regression-forecasting--computer-vision).

    1. Para **classification** (clasificación), también puede habilitar el aprendizaje profundo.
    
        Si el aprendizaje profundo está habilitado, la validación se limita a _train_validation split_. [Obtenga más información sobre las opciones de validación](how-to-configure-cross-validation-data-splits.md).

    1. Para la **previsión**, puede: 
    
        1. Habilitar el aprendizaje profundo.
    
        1. Seleccionar la *columna de tiempo*: esta columna contiene los datos de tiempo que desea usar.

        1. Seleccionar el *horizonte de previsión*: Indique cuántas unidades de tiempo (minutos, horas, días, semanas, meses o años) será capaz predecir el modelo en el futuro. Cuanto más se exija al modelo que prediga en el futuro, menos preciso será. [Más información sobre la previsión y el horizonte de previsión](how-to-auto-train-forecast.md).

1. (Opcional) Ver el apartado sobre la adición de configuraciones: opciones de configuración adicionales que puede usar para controlar mejor el trabajo de entrenamiento. De lo contrario, los valores predeterminados se aplican en función de la selección y los datos del experimento. 

    Configuraciones adicionales|Descripción
    ------|------
    Métrica principal| Métrica principal usada para puntuar el modelo. [Más información sobre las métricas del modelo](how-to-configure-auto-train.md#primary-metric).
    Explicación del mejor modelo | Seleccione esta opción para habilitar o deshabilitar la visualización de explicaciones del mejor modelo recomendado. <br> Esta funcionalidad no está disponible actualmente para [algunos algoritmos de previsión](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Blocked algorithms (Algoritmos bloqueados)| Seleccione los algoritmos que desea excluir del trabajo de entrenamiento. <br><br> La opción para permitir los algoritmos solo está disponible para los [experimentos de SDK](how-to-configure-auto-train.md#supported-models). <br> Vea los [modelos admitidos para cada tipo de tarea](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Criterios de exclusión| Cuando se cumple alguno de estos criterios, se detiene el trabajo de entrenamiento. <br> *Tiempo de trabajo de entrenamiento (horas)* : cantidad de tiempo para permitir que el trabajo de entrenamiento se ejecute. <br> *Metric score threshold* (Umbral de puntuación de métrica):  puntuación mínima de métrica para todas las canalizaciones. Esto garantiza que si tiene una métrica objetivo definida que desee alcanzar, no dedicará más tiempo en el trabajo de entrenamiento que el necesario.
    Simultaneidad| *Número máximo de iteraciones simultáneas*: número máximo de canalizaciones (iteraciones) para probar en el trabajo de entrenamiento. El trabajo no ejecutará más iteraciones que el número especificado de ellas. Más información sobre el modo en que el aprendizaje automático automatizado realiza [múltiples ejecuciones secundarias en los clústeres](how-to-configure-auto-train.md#multiple-child-runs-on-clusters).

1. (Opcional) Consulte la configuración de caracterización: Si decide habilitar **Caracterización automática** en el formulario **Ver configuración de caracterización**, se aplican las técnicas de caracterización predeterminadas. En **Ver configuración de caracterización** puede cambiar estos valores predeterminados y personalizarlos según corresponda. Obtenga información sobre cómo [personalizar las caracterizaciones](#customize-featurization). 

    ![Captura de pantalla que muestra el cuadro de diálogo Select task type (Seleccionar tipo de tarea) con la opción View featurization settings (Ver configuración de caracterización) seleccionada.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)


1. Con el formulario **[Opcional] Validar y probar** puede hacer lo siguiente: 

    1. Especificar el tipo de validación que se usará para el trabajo de entrenamiento. [Más información sobre la validación cruzada](how-to-configure-cross-validation-data-splits.md#prerequisites). 
    
        1. La previsión de tareas solo admite la validación cruzada de k iteraciones.
    
    1. Proporcionar un conjunto de datos de prueba (versión preliminar) para evaluar el modelo recomendado que el aprendizaje automático automatizado genera automáticamente al final del experimento. Cuando se proporcionan datos de prueba, se desencadena automáticamente una serie de pruebas al final del experimento. Esta serie solo se ejecuta en el mejor modelo recomendado por el aprendizaje automático automatizado. Obtenga información sobre cómo obtener los [resultados de la serie de pruebas remotas](#view-remote-test-run-results-preview).
    
        >[!IMPORTANT]
        > La característica para proporcionar un conjunto de datos de prueba con el fin de evaluar los modelos generados está en versión preliminar. Esta funcionalidad es una característica [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) en versión preliminar y puede cambiar en cualquier momento.

        1. Los datos de prueba se consideran algo independiente del entrenamiento y la validación, con el fin de no sesgar los resultados de la serie de pruebas del modelo recomendado. [Obtenga más información sobre el sesgo durante la validación del modelo](concept-automated-ml.md#training-validation-and-test-data).
        1. Puede proporcionar su propio conjunto de datos de prueba u optar por usar un porcentaje de su conjunto de datos de entrenamiento.          
        1. El esquema del conjunto de datos de prueba debe coincidir con el conjunto de datos de entrenamiento. La columna de destino es opcional, pero, si no se indica, no se calcula ninguna métrica de prueba.
        1. El conjunto de datos de prueba no debe ser el mismo que el conjunto de datos de entrenamiento o de validación.
        1. La previsión de ejecuciones no admite la división entre entrenamiento y pruebas.
        
        ![Captura de pantalla en la que se muestra el formulario donde se seleccionan los datos de validación y de prueba](media/how-to-use-automated-ml-for-ml-models/validate-test-form.png)
        
## <a name="customize-featurization"></a>Personalización de la caracterización

En el formulario **Caracterización**, puede habilitar o deshabilitar la caracterización automática y personalizar la configuración de caracterización automática para su experimento. Para abrir este formulario, consulte el paso 10 de la sección [Creación y ejecución de un experimento](#create-and-run-experiment). 

En la tabla siguiente se resumen las personalizaciones disponibles actualmente a través de Studio. 

Columna| Personalización
---|---
Se incluye | Especifica las columnas que se van a incluir para el entrenamiento.
Tipo de característica| Cambia el tipo de valor de la columna seleccionada.
Imputar con| Selecciona el valor con los cuales imputar los valores que faltan en los datos.

![Ingeniería de características personalizadas de Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Ejecución del experimento y visualización de los resultados

Para ejecutar el experimento, seleccione **Finalizar**. El proceso de preparación del experimento puede tardar hasta 10 minutos. Los trabajos de entrenamiento pueden tardar de 2 a 3 minutos más para que cada canalización termine de ejecutarse.

> [!NOTE]
> Los algoritmos que el aprendizaje automático automatizado emplea llevan inherente la aleatoriedad, que puede provocar una ligera variación en la puntuación de las métricas finales del modelo recomendado, como la precisión. El aprendizaje automático automatizado también realiza operaciones en datos, como la división de la prueba de entrenamiento, la división de la validación de entrenamiento o la validación cruzada cuando es necesario. Por lo tanto, si ejecuta un experimento con las mismas opciones de configuración y métricas principales varias veces, es probable que vea una variación en las puntuaciones de las métricas finales de los experimentos debido a estos factores. 

### <a name="view-experiment-details"></a>Visualización de los detalles del experimento

Se abre la pantalla **Detalles de ejecución** en la pestaña **Detalles**. En esta pantalla se muestra un resumen de la ejecución del experimento, incluida una barra de estado en la parte superior, junto al número de ejecución. 

La pestaña **Modelos** contiene una lista de los modelos creados ordenados por la puntuación de la métrica. De forma predeterminada, el modelo que puntúa más alto en función de las métricas seleccionadas aparece en la parte superior de la lista. A medida que el trabajo de entrenamiento prueba más modelos, se agregan a la lista. Utilice esto para obtener una comparación rápida de las métricas para los modelos generados hasta ahora.

![Detalles de la ejecución](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Ver detalles de ejecución del entrenamiento

Explore en profundidad cualquiera de los modelos completados para ver los detalles de ejecución de entrenamiento. En la pestaña **Modelo**, puede ver detalles como un resumen del modelo y los hiperparámetros usados para el modelo seleccionado. 

[![Detalles de hiperparámetros](media/how-to-use-automated-ml-for-ml-models/hyperparameter-button.png)](media/how-to-use-automated-ml-for-ml-models/hyperparameter-details.png)

 También puede ver gráficos de métricas de rendimiento específicos del modelo en la pestaña **Métricas**. [Más información sobre los gráficos](how-to-understand-automated-ml.md).

![Detalles de la iteración](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

En la pestaña Transformación de datos, puede ver un diagrama del preprocesamiento de datos, la ingeniería de características, las técnicas de escalado y el algoritmo de aprendizaje automático que se aplicaron para generar este modelo.

>[!IMPORTANT]
> La pestaña Transformación De datos está en versión preliminar. Esta funcionalidad debe considerarse [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) y puede cambiar en cualquier momento.

![Transformación de datos](./media/how-to-use-automated-ml-for-ml-models/data-transformation.png)

## <a name="view-remote-test-run-results-preview"></a>Visualización de los resultados de la serie de pruebas remotas (versión preliminar)

Si ha especificado un conjunto de datos de prueba o ha optado por una división entre entrenamiento y prueba durante la configuración del experimento —en el formulario **Validar y probar**—, el aprendizaje automático automatizado prueba automáticamente el modelo recomendado de manera predeterminada. Como resultado, el aprendizaje automático automatizado calcula las métricas de prueba para determinar la calidad del modelo recomendado y sus predicciones. 

>[!IMPORTANT]
> La característica para probar modelos con un conjunto de datos de prueba con el fin de evaluar los modelos generados está en versión preliminar. Esta funcionalidad es una característica [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) en versión preliminar y puede cambiar en cualquier momento.

Para ver las métricas de la serie de pruebas del modelo recomendado, haga lo siguiente:
 
1. Vaya a la página **Modelos** y seleccione el mejor modelo. 
1. Seleccione la pestaña **Resultados de la prueba (versión preliminar)** . 
1. Seleccione la serie que desee y consulte la pestaña **Métricas**.  ![Pestaña "Resultados de la prueba" del modelo recomendado que se ha probado automáticamente](./media/how-to-use-automated-ml-for-ml-models/test-best-model-results.png)
    
Para ver las predicciones de prueba usadas para calcular las métricas de prueba, haga lo siguiente: 

1. Vaya a la parte inferior de la página y seleccione el vínculo de **Conjunto de datos de salidas** para abrir el conjunto de datos. 
1. En la página **Conjuntos de datos**, seleccione la pestaña **Explorar** para ver las predicciones de la serie de pruebas.
    1. El archivo de predicción también se puede ver o descargar desde la pestaña **Salidas y registros**. Expanda la carpeta **Predicciones** para localizar el archivo `predicted.csv`.

El archivo de predicción también se puede ver o descargar desde la pestaña "Salidas y registros". Expanda la carpeta "Predicciones" para localizar el archivo "predictions.csv".

## <a name="test-an-existing-automated-ml-model-preview"></a>Prueba de un modelo de aprendizaje automático automatizado ya existente (versión preliminar)

Una vez completado el experimento, puede probar los modelos que el aprendizaje automático automatizado genera por usted. Si desea probar otro modelo generado por el aprendizaje automático automatizado que no sea el recomendado, puede hacerlo mediante los siguientes pasos: 

1. Seleccione una serie de experimento de aprendizaje automático automatizado ya existente.  
1. Vaya a la pestaña **Modelos** de la serie y seleccione el modelo completado que quiera probar.
1. En la página **Detalles** del modelo, seleccione el botón **Modelo de prueba (versión preliminar)** para abrir el panel **Modelo de prueba**.
1. En el panel **Modelo de prueba**, seleccione el clúster de proceso y el conjunto de datos de prueba que quiera usar para la serie de pruebas. 
1. Seleccione el botón **Probar**. El esquema del conjunto de datos de prueba debe coincidir con el conjunto de datos de entrenamiento, pero la **columna de destino** es opcional.
1. Una vez que se cree la serie de pruebas del modelo, en la página **Detalles** se mostrará el mensaje correspondiente. Seleccione la pestaña **Resultados de la prueba** para ver el progreso de la serie.

1. Para ver los resultados de la serie de pruebas, abra la página **Detalles** y siga los pasos descritos en la sección [Visualización de los resultados de la serie de pruebas remotas](#view-remote-test-run-results-preview). 

    ![Formulario del modelo de prueba](./media/how-to-use-automated-ml-for-ml-models/test-model-form.png)
    

## <a name="model-explanations-preview"></a>Explicaciones de modelos (versión preliminar)

Para entender mejor el modelo, puede ver qué características de datos (con o sin diseño) han influido en las predicciones del modelo con el panel de explicaciones del modelo. 

El panel de explicaciones del modelo proporciona un análisis general del modelo entrenado junto con sus predicciones y explicaciones. También permite profundizar en un determinado punto de datos y la importancia de sus características concretas. [Obtenga más información sobre las visualizaciones del panel de explicaciones](how-to-machine-learning-interpretability-aml.md#visualizations).

Para obtener explicaciones de un modelo determinado: 

1. En la pestaña **Modelos**, seleccione el modelo del que quiere conocer más información. 
1. Seleccione el botón **Explicar modelo** y proporcione un proceso que se pueda usar para generar las explicaciones.
1. Compruebe el estado en la pestaña **Ejecuciones secundarias**. 
1. Cuando termine, vaya a la pestaña **Explicaciones (versión preliminar)** , que contiene el panel de explicaciones. 

    ![Panel de explicación del modelo](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Implementación del modelo

Una vez que tenga a mano el mejor modelo, es el momento de implementarlo como un servicio web para predecir los datos nuevos.

>[!TIP]
> Si va a implementar un modelo que se generó a través del paquete `automl` con el SDK de Python, debe [registrar el modelo](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) en el área de trabajo. 
>
> Una vez que se haya registrado el modelo, puede buscarlo en el estudio seleccionando **Modelos** en el panel izquierdo. Después de abrir el modelo, puede seleccionar el botón **Implementar** en la parte superior de la pantalla y, luego, seguir las instrucciones descritas en el **paso 2** de la sección **Implementación del modelo**.

ML automatizado le ayuda a implementar el modelo sin escribir código:

1. Tiene unas par de opciones de implementación. 

    + Opción 1: implementar el mejor modelo, según los criterios de métricas que haya definido. 
        1. Una vez finalizado el experimento, vaya a la página de ejecución primaria seleccionando **Ejecución 1** en la parte superior de la pantalla. 
        1.  Seleccione el modelo que aparece en la sección **Mejor resumen del modelo**. 
        1. Seleccione **Implementar** en la parte superior izquierda de la ventana. 

    + Opción 2: implementar una iteración del modelo específica de este experimento.
        1. Seleccione el modelo que quiera en la pestaña **Modelos**.
        1. Seleccione **Implementar** en la parte superior izquierda de la ventana.

1. Rellene el panel **Implementar modelo**.

    Campo| Valor
    ----|----
    Nombre| Escriba un nombre único para la implementación.
    Descripción| Escriba una descripción para saber mejor para qué sirve esta implementación.
    Compute type (Tipo de proceso)| Seleccione el tipo de punto de conexión que quiera implementar: [*Azure Kubernetes Service (AKS)*](../aks/intro-kubernetes.md) o [*Azure Container Instances (ACI)* ](../container-instances/container-instances-overview.md).
    Nombre del proceso| *Solo se aplica a AKS:* Seleccione el nombre del clúster de AKS en que desea realizar la implementación.
    Enable authentication (Habilitar autenticación) | Seleccione esta opción para permitir la autenticación basada en token o basada en clave.
    Use custom deployment assets (Usar recursos de implementación personalizados)| Habilite esta característica si desea cargar su propio archivo de entorno y script de puntuación. De lo contrario, el aprendizaje automático automatizado proporcionará estos recursos por usted de manera predeterminada. [Más información sobre los scripts de puntuación](how-to-deploy-and-where.md).

    >[!Important]
    > Los nombres de archivo deben tener menos de 32 caracteres y deben comenzar y terminar con caracteres alfanuméricos. Puede incluir guiones, caracteres de subrayado, puntos y caracteres alfanuméricos. No se permiten espacios.

    El menú *Avanzado* ofrece características de implementación predeterminadas como la [recopilación de datos](how-to-enable-app-insights.md) y la configuración del uso de recursos. Si desea reemplazar estos valores predeterminados, hágalo en este menú.

1. Seleccione **Implementar**. La implementación puede tardar unos 20 minutos en completarse.
    Una vez iniciada la implementación, aparece la pestaña **Resumen del modelo**. Consulte el progreso de la implementación en la sección **Estado de implementación**. 

Ya tiene un servicio web operativo para generar predicciones. Puede probar las predicciones consultando el servicio de [soporte técnico de Azure Machine Learning de Power BI](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Pasos siguientes

* [Obtenga información sobre cómo consumir un servicio web](how-to-consume-web-service.md).
* [Descripción de los resultados de aprendizaje automático automatizado](how-to-understand-automated-ml.md).
* [Más información sobre el aprendizaje automático automatizado](concept-automated-ml.md) y Azure Machine Learning.

---
title: Uso de puntos de conexión por lotes en Estudio
titleSuffix: Azure Machine Learning
description: En este artículo, aprenderá a crear un punto de conexión por lotes en Estudio de Azure Machine Learning. Los puntos de conexión por lotes se usan para puntuar por lotes datos de gran tamaño de forma continua.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
ms.date: 08/16/2021
ms.custom: how-to, studio, managed-batch-endpoints
ms.openlocfilehash: 290d13c050a624c660174e3d8e272c67e90a9207
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424149"
---
# <a name="how-to-use-batch-endpoints-preview-in-azure-machine-learning-studio"></a>Uso de puntos de conexión por lotes (versión preliminar) en Estudio de Azure Machine Learning

En este artículo, aprenderá a usar puntos de conexión por lotes (versión preliminar) para realizar la puntuación por lotes en [Estudio de Azure Machine Learning](https://ml.azure.com). Para más información, consulte [¿Qué son los puntos de conexión de Azure Machine Learning (versión preliminar)?](concept-endpoints.md).

En este artículo, aprenderá lo siguiente:

> [!div class="checklist"]
> * Creación de un punto de conexión por lotes con una experiencia sin código para el modelo MLflow
> * Comprobación de los detalles del punto de conexión por lotes
> * Inicio de un trabajo de puntuación por lotes
> * Introducción a las características de punto de conexión por lotes en Estudio de Azure Machine Learning

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: si aún no tiene ninguna, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* El repositorio de ejemplo: clone el [repositorio de ejemplo de AzureML](https://github.com/Azure/azureml-examples). En este artículo se usan los recursos de `/cli/endpoints/batch`.

* Un destino de proceso donde pueda ejecutar flujos de trabajo de puntuación por lotes. Para obtener información sobre cómo crear un destino de proceso, consulte [Creación de destinos de proceso en Estudio de Azure Machine Learning](how-to-create-attach-compute-studio.md).

* Registre el modelo de Machine Learning.

## <a name="create-a-batch-endpoint"></a>Creación de un punto de conexión por lotes

Hay dos maneras de crear puntos de conexión por lotes en Estudio de Azure Machine Learning:

* Desde la página **Puntos de conexión**, seleccione **Batch Endpoints** (Puntos de conexión por lotes) y, a continuación, seleccione **+ Crear**. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-endpoints.png" alt-text="Captura de pantalla de la creación de un punto de conexión o una implementación por lotes desde la página Puntos de conexión":::

O BIEN

* En la página **Modelos**, seleccione el modelo que desea implementar y, a continuación, seleccione **Deploy to batch endpoint (preview)** Implementar en el punto de conexión por lotes (versión preliminar).

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/models-page-deployment.png" alt-text="Captura de pantalla de la creación de un punto de conexión o una implementación por lotes desde la página Modelos":::

> [!TIP]
> Si usa un modelo MLflow, puede utilizar la creación de puntos de conexión por lotes sin código. Es decir, no es necesario preparar un script y un entorno de puntuación, ya que ambos se pueden generar automáticamente. Para más información, consulte [Entrenamiento y seguimiento de modelos de Machine Learning con MLflow y Azure Machine Learning (versión preliminar)](how-to-use-mlflow.md).
> 
> :::image type="content" source="media/how-to-use-batch-endpoints-studio/mlflow-model-wizard.png" alt-text="Captura de pantalla de la implementación de un modelo de MLflow":::

Complete todos los pasos del asistente para crear un punto de conexión y una implementación por lotes.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/review-batch-wizard.png" alt-text="Captura de pantalla de la pantalla de revisión de la implementación o los puntos de conexión por lotes":::

## <a name="check-batch-endpoint-details"></a>Comprobación de los detalles del punto de conexión por lotes

Después de crear un punto de conexión por lotes, selecciónelo en la página **Puntos de conexión** para ver los detalles.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/batch-endpoint-details.png" alt-text="Captura de pantalla de detalles la comprobación de los puntos de conexión por lotes y la implementación":::

## <a name="start-a-batch-scoring-job"></a>Inicio de un trabajo de puntuación por lotes

Una carga de trabajo de puntuación por lotes se ejecuta como un trabajo sin conexión. De forma predeterminada, la puntuación por lotes almacena las salidas de puntuación en almacenamiento de blobs. También puede configurar la ubicación de las salidas y sobrescribir algunas de las opciones para obtener el mejor rendimiento.

1. Seleccione **+ Crear trabajo**:

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-job.png" alt-text="Captura de pantalla de la opción de crear trabajo para iniciar la puntuación por lotes":::

1. Puede actualizar la implementación predeterminada al enviar un trabajo desde la lista desplegable:

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/job-setting-batch-scoring.png" alt-text="Captura de pantalla del uso de la implementación para enviar un trabajo por lotes":::

### <a name="overwrite-settings"></a>Sobrescritura de la configuración

Algunas opciones de configuración se pueden sobrescribir al iniciar un trabajo de puntuación por lotes. Por ejemplo, puede sobrescribir la configuración para optimizar el uso del recurso de proceso o para mejorar el rendimiento. Para invalidar la configuración, seleccione __Override deployment settings__ (Invalidar configuración de implementación) y proporcione la configuración. Para más información, consulte [Uso de puntos de conexión por lotes](how-to-use-batch-endpoint.md#overwrite-settings).

:::image type="content" source="media/how-to-use-batch-endpoints-studio/overwrite-setting.png" alt-text="Captura de pantalla de la configuración de sobrescritura al iniciar un trabajo por lotes":::

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>Inicio de un trabajo de puntuación por lotes con distintas opciones de entrada

Tiene dos opciones para especificar las entradas de datos en Estudio de Azure Machine Learning:

* Usar un **conjunto de datos registrado**:

    > [!NOTE]
    > Durante la versión preliminar, solo se admite FileDataset. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-dataset-for-job.png" alt-text="Captura de pantalla de la selección del conjunto de datos registrado como una opción de entrada":::

O BIEN

* Usar **un almacén de datos**:

    Puede especificar el almacén de datos registrado de AML o, si los datos están disponibles públicamente, especificar la ruta de acceso pública.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-datastore-job.png" alt-text="Captura de pantalla de la selección del almacén de datos como una opción de entrada":::

### <a name="configure-the-output-location"></a>Configuración de la ubicación de salida

De forma predeterminada, los resultados de la puntuación por lotes se almacenan en el almacén de blobs predeterminado del área de trabajo. Los resultados se encuentran en una carpeta que debe su nombre al nombre del trabajo (un GUID generado por el sistema).

Para cambiar dónde se almacenan los resultados, proporcione un almacén de blobs y una ruta de acceso de salida al iniciar un trabajo.

> [!IMPORTANT]
> Debe usar una ubicación de salida única. Si el archivo de salida existe, el trabajo de puntuación por lotes generará errores. 

:::image type="content" source="media/how-to-use-batch-endpoints-studio/configure-output-location.png" alt-text="Captura de pantalla de la configuración opcional de la ubicación de salida":::

### <a name="summary-of-all-submitted-jobs"></a>Resumen de todos los trabajos enviados

Para ver un resumen de todos los trabajos enviados para un punto de conexión, seleccione el punto de conexión y, a continuación, seleccione la pestaña **Ejecuciones**.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/summary-jobs.png" alt-text="Captura de pantalla del resumen de los trabajos enviados a un punto de conexión por lotes":::
## <a name="check-batch-scoring-results"></a>Comprobación de los resultados de la puntuación por lotes

Para obtener información sobre cómo ver los resultados de la puntuación, consulte [Uso de puntos de conexión por lotes](how-to-use-batch-endpoint.md#check-batch-scoring-results).

## <a name="add-a-deployment-to-an-existing-batch-endpoint"></a>Adición de una implementación a un punto de conexión por lotes existente

En Estudio de Azure Machine Learning, hay dos maneras de agregar una implementación a un punto de conexión por lotes existente:

* En la página **Puntos de conexión**, seleccione el punto de conexión por lotes al que quiere agregar una nueva implementación. Seleccione **+ Agregar implementación** y complete el asistente para agregar una nueva implementación.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-option.png" alt-text="Captura de pantalla de la opción Agregar nueva implementación":::

O BIEN

* En la página **Modelos**, seleccione el modelo que desea implementar. A continuación, seleccione la opción **Deploy to batch endpoint (preview)** Implementar en el punto de conexión por lotes (versión preliminar) de la lista desplegable. En el asistente, en la pantalla **Punto de conexión**, seleccione **Existente**. Complete el asistente para agregar la nueva implementación.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-models-page.png" alt-text="Captura de pantalla de la selección de un punto de conexión por lotes existente para agregar una nueva implementación":::

## <a name="update-the-default-deployment"></a>Actualización de la implementación predeterminada

Si un punto de conexión tiene varias implementaciones, una de las implementaciones es la *predeterminada*. La implementación predeterminada recibe el 100 % del tráfico al punto de conexión. Para cambiar la implementación predeterminada, siga estos pasos:

1. Seleccione el punto de conexión en la página **Puntos de conexión**.
1. Seleccione **Update default deployment** (Actualizar implementación predeterminada). En la pestaña **Detalles**, seleccione la implementación que desea establecer como predeterminada y, a continuación, elija **Actualizar**.
    :::image type="content" source="media/how-to-use-batch-endpoints-studio/update-default-deployment.png" alt-text="Captura de pantalla de la actualización de la implementación predeterminada":::

## <a name="delete-batch-endpoint-and-deployments"></a>Eliminación de implementaciones y puntos de conexión por lotes

Para eliminar un **punto de conexión**, selecciónelo en la página **Puntos de conexión** y, a continuación, seleccione Eliminar.

> [!WARNING]
> Al eliminar un punto de conexión también se eliminan todas sus implementaciones.

Para eliminar una **implementación**, seleccione el punto de conexión en la página **Puntos de conexión**, seleccione la implementación y, a continuación, elija Eliminar.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear e invocar puntos de conexión por lotes. Consulte estos otros artículos para obtener más información sobre Azure Machine Learning:

* [Solución de problemas de puntos de conexión por lotes](how-to-troubleshoot-batch-endpoints.md)
* [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md)

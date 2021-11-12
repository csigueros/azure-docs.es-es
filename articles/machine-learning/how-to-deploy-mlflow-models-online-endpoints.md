---
title: Implementación de modelos de MLflow en un punto de conexión en línea administrado (versión preliminar)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar el modelo de MLflow como un servicio web administrado automáticamente por Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, mlflow, devplatv2, no-code-deployment
ms.openlocfilehash: 9e0b4944ee077248b2be2a11fdd4bacbc5af9824
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842615"
---
# <a name="deploy-mlflow-models-to-managed-online-endpoint-preview"></a>Implementación de modelos de MLflow en un punto de conexión en línea administrado (versión preliminar)

En este artículo, aprenderá a implementar el modelo de [MLflow](https://www.mlflow.org) en un [punto de conexión en línea administrado](concept-endpoints.md#managed-online-endpoints) (versión preliminar). Cuando implementa el modelo de MLflow en un punto de conexión en línea administrado, se trata de una implementación sin código. No requiere el script de puntuación ni el entorno. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [basic cli prereqs](../../includes/machine-learning-cli-prereqs.md)]

* Debe tener un modelo de MLflow. Los ejemplos de este artículo se basan en los modelos de [https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow).

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

En los fragmentos de código usados en este artículo, la variable de entorno `ENDPOINT_NAME` contiene el nombre del punto de conexión que se va a crear y usar. Para establecerlo, use el siguiente comando de la CLI. Reemplace `<YOUR_ENDPOINT_NAME>` por el nombre del punto de conexión:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="set_endpoint_name":::

## <a name="deploy-using-cli-v2"></a>Implementación mediante la CLI (v2)

En este ejemplo se muestra cómo implementar un modelo de MLflow en un punto de conexión en línea administrado mediante la CLI (v2).

> [!IMPORTANT]
> En el caso de la implementación sin código de MLflow, actualmente no se admiten las **[pruebas a través de puntos de conexión locales](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** .

1. Cree un archivo de configuración de YAML para el punto de conexión. En el ejemplo siguiente se configuran el nombre y el modo de autenticación del punto de conexión:

    __create-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/mlflow/create-endpoint.yaml":::

1. Para crear un nuevo punto de conexión mediante la configuración de YAML, use el siguiente comando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_endpoint":::

1. Cree un archivo de configuración de YAML para la implementación. En el ejemplo siguiente se configura una implementación del modelo `sklearn-diabetes` en el punto de conexión creado en el paso anterior:

    > [!IMPORTANT]
    > Para que la implementación sin código (NCD) de MLflow funcione, es necesario establecer **`model_format`** en **`mlflow`** . Para obtener más información, consulte el [esquema de YAML del modelo de la CLI (v2)](reference-yaml-model.md).

    __sklearn-deployment.yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/mlflow/sklearn-deployment.yaml":::

1. Para crear la implementación mediante la configuración de YAML, use el siguiente comando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_sklearn_deployment":::

### <a name="invoke-the-endpoint"></a>Invocación del punto de conexión

Una vez completada la implementación, use el siguiente comando para realizar una solicitud de puntuación al punto de conexión implementado. El archivo [sample-request-sklearn.json](https://github.com/Azure/azureml-examples/blob/cli-preview/cli/endpoints/online/mlflow/sample-request-sklearn.json) usado en este comando se encuentra en el directorio `/cli/endpoints/online/mlflow` del repositorio azure-examples:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="test_sklearn_deployment":::

La respuesta será similar al texto siguiente:

```json
[ 
  11633.100167144921,
  8522.117402884991
]
```

### <a name="delete-endpoint"></a>Eliminación de un punto de conexión

Una vez que haya terminado con el punto de conexión, use el siguiente comando para eliminarlo:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-mlflow.sh" ID="delete_endpoint":::

## <a name="deploy-using-azure-machine-learning-studio"></a>Implementación con Azure Machine Learning Studio

En este ejemplo se muestra cómo puede implementar un modelo de MLflow en un punto de conexión en línea administrado mediante [Azure Machine Learning Studio](https://ml.azure.com).

1. Registre el modelo con el formato de MLflow mediante el siguiente comando de YAML y la CLI. YAML usa un modelo scikit-learn de MLflow de [https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/endpoints/online/mlflow).

    __sample-create-mlflow-model.yaml__

    ```yaml
    $schema: https://azuremlschemas.azureedge.net/latest/model.schema.json
    name: sklearn-diabetes-mlflow
    version: 1
    local_path: sklearn-diabetes/model
    model_format: mlflow
    description: Scikit-learn MLflow model.
    ```


    ```azurecli
    az ml model create -f sample-create-mlflow-model.yaml
    ```

2. En [Studio](https://ml.azure.com), seleccione el área de trabajo y, a continuación, use la página de __puntos de conexión__ o __modelos__ para crear la implementación del punto de conexión:

    # <a name="endpoints-page"></a>[Página Extremos](#tab/endpoint)

    1. En la página __Puntos de conexión__, seleccione **+ Crear (versión preliminar)** .

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" alt-text="Captura de pantalla que muestra la opción de creación en la página de la interfaz de usuario de puntos de conexión.":::

    1. Proporcione un nombre y un tipo de autenticación para el punto de conexión y, a continuación, elija __Siguiente__.
    1. Al seleccionar un modelo, elija el modelo de MLflow registrado anteriormente. Seleccione __Next__ (Siguiente) para continuar.

    1. Al seleccionar un modelo registrado con el formato de MLflow en el paso Entorno del asistente, no necesita el script de puntuación ni el entorno.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" alt-text="Captura de pantalla en la que se muestra que no se necesita código ni entorno para los modelos de MLflow.":::

    1. Complete el asistente para implementar el modelo en el punto de conexión.

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" alt-text="Captura de pantalla que muestra la pantalla de revisión de NCD.":::

    # <a name="models-page"></a>[Página Modelos](#tab/models)

    1. Seleccione el modelo de MLflow y, a continuación, elija __Implementar__. Cuando se le solicite, seleccione __Implementación de un punto de conexión en tiempo real (versión preliminar)__ .

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" alt-text="Captura de pantalla que muestra cómo implementar el modelo desde la interfaz de usuario de modelos.":::

    1. Complete el asistente para implementar el modelo en el punto de conexión.

    ---

## <a name="deploy-models-after-a-training-job"></a>Implementación de modelos después de un trabajo de entrenamiento

Esta sección le ayuda a comprender cómo implementar modelos en el punto de conexión en línea administrado una vez que ha completado el [trabajo de entrenamiento](how-to-train-cli.md).

1. Descargue las salidas del trabajo de entrenamiento. Las salidas contienen la carpeta del modelo.

    > [!NOTE]
    > Si ha usado `mlflow.autolog()` en el script de entrenamiento, verá los artefactos del modelo en el historial de ejecución del trabajo. Azure Machine Learning se integra con la funcionalidad de seguimiento de MLflow. Puede usar `mlflow.autolog()` para que varios marcos de Machine Learning comunes registren parámetros de modelo, métricas de rendimiento, artefactos de modelo e incluso gráficos de importancia de características.
    >
    > Para obtener más información, consulte [Entrenamiento de modelos con la CLI](how-to-train-cli.md#model-tracking-with-mlflow). Consulte también los [ejemplos de trabajo de entrenamiento](https://github.com/Azure/azureml-examples/tree/cli-preview/cli/jobs/single-step) en el repositorio de GitHub.

    # <a name="azure-machine-learning-studio"></a>[Azure Machine Learning Studio](#tab/studio)

    :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" alt-text="Captura de pantalla que muestra cómo descargar salidas y registros de la ejecución de experimentación.":::

    # <a name="cli"></a>[CLI](#tab/cli)

    ```azurecli
    az ml job download -n $run_id --outputs
    ```

2. Para realizar la implementación con los archivos descargados, puede usar Studio o la interfaz de la línea de comandos de Azure. Use la carpeta de modelos de las salidas para la implementación:

    * [Implementación con Azure Machine Learning Studio](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-azure-machine-learning-studio).
    * [Implementación con la CLI de Azure Machine Learning (v2)](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-cli-v2).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte estos artículos:

- [Implementación de modelos con REST (versión preliminar)](how-to-deploy-with-rest.md)
- [Creación y uso de puntos de conexión en línea administrados (versión preliminar) en Estudio](how-to-use-managed-online-endpoint-studio.md)
- [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md)
- [Escalabilidad automática de puntos de conexión administrados en línea](how-to-autoscale-endpoints.md)
- [Uso de puntos de conexión por lotes (versión preliminar) para la puntuación por lotes](how-to-use-batch-endpoint.md)
- [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md)
- [Acceso a recursos de Azure con un punto de conexión en línea administrado e identidad administrada (versión preliminar)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Solución de problemas de implementación de puntos de conexión en línea administrados](how-to-troubleshoot-managed-online-endpoints.md)
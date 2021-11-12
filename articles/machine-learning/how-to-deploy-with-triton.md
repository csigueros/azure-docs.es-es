---
title: Servicio del modelo de alto rendimiento con Triton (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo implementar su modelo con NVIDIA Triton Inference Server en Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.custom: deploy, devplatv2
ms.openlocfilehash: 521ebf79dd265b26958c36411c1d6aa281e11a5e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842594"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Servicio de alto rendimiento con Triton Inference Server (versión preliminar) 

Obtenga información sobre cómo usar [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) en Azure Machine Learning con [puntos de conexión en línea administrados](concept-endpoints.md#managed-online-endpoints).

Triton es un software de código abierto de varios marcos que está optimizado para la inferencia. Admite marcos de aprendizaje automático populares como TensorFlow, ONNX Runtime, PyTorch, NVIDIA TensorRT, etc. Se puede usar para las cargas de trabajo de CPU o GPU.

En este artículo, aprenderá a implementar Triton y un modelo en un punto de conexión en línea administrado. Se proporciona información sobre el uso de la CLI (línea de comandos) y Azure Machine Learning Studio.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) es un software de terceros de código abierto integrado en Azure Machine Learning.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [basic prereqs](../../includes/machine-learning-cli-prereqs.md)]

* Un entorno de Python 3.8 (o superior) en funcionamiento.

* Acceso a las VM de la serie NCv3 de su suscripción de Azure.

    > [!IMPORTANT]
    > Es posible que tenga que solicitar un aumento de cuota para su suscripción para poder usar esta serie de VM. Para más información, consulte [Serie NCv3](/azure/virtual-machines/ncv3-series).

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

NVIDIA Triton Inference Server requiere una estructura de repositorio de modelos específica, donde haya un directorio para cada modelo y subdirectorios para la versión del modelo. El contenido de cada subdirectorio de versión del modelo viene determinado por el tipo del modelo y los requisitos del back-end que admite el modelo. Para ver toda la estructura del repositorio de modelos [https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files](https://github.com/triton-inference-server/server/blob/main/docs/model_repository.md#model-files)

La información de este documento se basa en el uso de un modelo almacenado en formato ONNX, por lo que la estructura de directorios del repositorio de modelos es `<model-repository>/<model-name>/1/model.onnx`. En concreto, este modelo realiza la identificación de la imagen.

## <a name="deploy-using-cli-v2"></a>Implementación mediante la CLI (v2)

En esta sección se muestra cómo puede implementar Triton en un punto de conexión en línea administrado mediante el CLI de Azure con la extensión de Machine Learning (v2).

> [!IMPORTANT]
> En el caso de la implementación sin código de Triton, actualmente no se admiten las **[pruebas a través de puntos de conexión locales](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** .

1. Para evitar escribir una ruta de acceso para varios comandos, use el siguiente comando para establecer una variable de entorno `BASE_PATH`. Esta variable apunta al directorio donde se encuentran el modelo y los archivos de configuración de YAML asociados:

    ```azurecli
    BASE_PATH=endpoints/online/triton/single-model
    ```

1. Use el siguiente comando para establecer el nombre del punto de conexión que se creará. En este ejemplo, se crea un nombre aleatorio para el punto de conexión:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="set_endpoint_name":::

1. Instale los requisitos de Python mediante los siguientes comandos:

    ```azurecli
    pip install numpy
    pip install tritonclient[http]
    pip install pillow
    pip install gevent
    ```

1. Cree un archivo de configuración de YAML para el punto de conexión. En el ejemplo siguiente se configuran el nombre y el modo de autenticación del punto de conexión. El que se usa en los siguientes comandos se encuentra en `/cli/endpoints/online/triton/single-model/create-managed-endpoint.yml` el repositorio azureml-examples que clonó anteriormente:

    __create-managed-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/triton/single-model/create-managed-endpoint.yaml":::

1. Para crear un nuevo punto de conexión mediante la configuración de YAML, use el siguiente comando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="create_endpoint":::

1. Cree un archivo de configuración de YAML para la implementación. En el ejemplo siguiente se configura una implementación denominada __blue__ en el punto de conexión creado en el paso anterior. El que se usa en los siguientes comandos se encuentra en `/cli/endpoints/online/triton/single-model/create-managed-deployment.yml` en el repositorio azureml-examples que clonó anteriormente:

    > [!IMPORTANT]
    > Para que la implementación sin código (NCD) de Triton funcione, es necesario establecer **`model_format`** en **`Triton`** . Para obtener más información, [compruebe el esquema de YAML del modelo de la CLI (v2)](reference-yaml-model.md).
    >
    > Esta implementación usa una VM Standard_NC6s_v3. Es posible que deba solicitar un aumento de cuota para su suscripción para poder usar esta VM. Para más información, consulte [Serie NCv3](/azure/virtual-machines/ncv3-series).

    :::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/triton/single-model/create-managed-deployment.yaml":::

1. Para crear la implementación mediante la configuración de YAML, use el siguiente comando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="create_deployment":::

### <a name="invoke-your-endpoint"></a>Invocación del punto de conexión

Una vez completada la implementación, use el siguiente comando para realizar una solicitud de puntuación al punto de conexión implementado. 

> [!TIP]
> El archivo `/cli/endpoints/online/triton/single-model/triton_densenet_scoring.py` del repositorio azureml-examples se usa para la puntuación. La imagen que se pasa al punto de conexión necesita un procesamiento previo para cumplir los requisitos de tamaño, tipo y formato, y el procesamiento posterior para mostrar la etiqueta prevista. El objeto `triton_densenet_scoring.py` usa la biblioteca `tritonclient.http` para comunicarse con Triton Inference Server.

1. Para obtener el URI de puntuación del punto de conexión, use el siguiente comando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="get_scoring_uri":::

1. Para obtener un token de autenticación, use el siguiente comando:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="get_token":::

1. Para puntuar los datos con el punto de conexión, use el comando siguiente. Envía la imagen de un pavo real (https://aka.ms/peacock-pic) ) al punto de conexión:

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="check_scoring_of_model":::

    La respuesta del script es similar al texto siguiente:

    ```
    Is server ready - True
    Is model ready - True
    /azureml-examples/cli/endpoints/online/triton/single-model/densenet_labels.txt
    84 : PEACOCK
    ```

### <a name="delete-your-endpoint-and-model"></a>Eliminación del punto de conexión y el modelo

Una vez que haya terminado con el punto de conexión, use el siguiente comando para eliminarlo:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-triton-managed-online-endpoint.sh" ID="delete_endpoint":::

Use el siguiente comando para eliminar el modelo:

```azurecli
az ml model delete --name $MODEL_NAME --version $MODEL_VERSION
```

## <a name="deploy-using-azure-machine-learning-studio"></a>Implementación con Azure Machine Learning Studio

En esta sección se muestra cómo puede implementar Triton en un punto de conexión en línea administrado mediante [Azure Machine Learning Studio](https://ml.azure.com).

1. Registre el modelo con el formato de Triton mediante el siguiente comando de YAML y la CLI. YAML usa un modelo densenet-onnx de [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton/single-model)

    __create-triton-model.yaml__

    ```yml
    name: densenet-onnx-model
    version: 1
    local_path: ./models
    model_format: Triton
    description: Registering my Triton format model.
    ```

    ```azurecli
    az ml model create -f create-triton-model.yaml
    ```

    En la captura de pantalla siguiente se muestra el aspecto que tendrá el modelo registrado en la __página Modelos__ de Azure Machine Learning Studio.

    :::image type="content" source="media/how-to-deploy-with-triton/triton-model-format.png" lightbox="media/how-to-deploy-with-triton/triton-model-format.png" alt-text="Captura de pantalla que muestra el formato del modelo de Triton en la página Modelos.":::


1. En [Studio](https://ml.azure.com), seleccione el área de trabajo y, a continuación, use la página de __puntos de conexión__ o __modelos__ para crear la implementación del punto de conexión:

    # <a name="endpoints-page"></a>[Página Extremos](#tab/endpoint)

    1. En la página __Puntos de conexión__, seleccione **+ Crear (versión preliminar)** .

        :::image type="content" source="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" lightbox="media/how-to-deploy-with-triton/create-option-from-endpoints-page.png" alt-text="Captura de pantalla que muestra la opción de creación en la página de la interfaz de usuario de puntos de conexión.":::

    1. Proporcione un nombre y un tipo de autenticación para el punto de conexión y, a continuación, elija __Siguiente__.
    1. Al seleccionar un modelo, elija el modelo de Triton registrado anteriormente. Seleccione __Next__ (Siguiente) para continuar.

    1. Al seleccionar un modelo registrado con el formato de Triton, en el paso Entorno del asistente, no necesita el script de puntuación ni el entorno.

        :::image type="content" source="media/how-to-deploy-with-triton/ncd-triton.png" lightbox="media/how-to-deploy-with-triton/ncd-triton.png" alt-text="Captura de pantalla en la que se muestra que no se necesita código ni entorno para los modelos de Triton.":::

    1. Complete el asistente para implementar el modelo en el punto de conexión.

        :::image type="content" source="media/how-to-deploy-with-triton/review-screen-triton.png" lightbox="media/how-to-deploy-with-triton/review-screen-triton.png" alt-text="Captura de pantalla que muestra la pantalla de revisión de NCD.":::

    # <a name="models-page"></a>[Página Modelos](#tab/models)

    1. Seleccione el modelo de Triton y, a continuación, elija __Implementar__. Cuando se le solicite, seleccione __Implementación de un punto de conexión en tiempo real (versión preliminar)__ .

        :::image type="content" source="media/how-to-deploy-with-triton/deploy-from-models-page.png" lightbox="media/how-to-deploy-with-triton/deploy-from-models-page.png" alt-text="Captura de pantalla que muestra cómo implementar el modelo desde la interfaz de usuario de modelos.":::

    1. Complete el asistente para implementar el modelo en el punto de conexión.

    ---

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte estos artículos:

- [Implementación de modelos con REST (versión preliminar)](how-to-deploy-with-rest.md)
- [Creación y uso de puntos de conexión en línea administrados (versión preliminar) en Estudio](how-to-use-managed-online-endpoint-studio.md)
- [Implementación segura para puntos de conexión en línea (versión preliminar)](how-to-safely-rollout-managed-endpoints.md)
- [Escalabilidad automática de puntos de conexión administrados en línea](how-to-autoscale-endpoints.md)
- [Visualización de los costos de un punto de conexión en línea administrado de Azure Machine Learning (versión preliminar)](how-to-view-online-endpoints-costs.md)
- [Acceso a recursos de Azure con un punto de conexión en línea administrado e identidad administrada (versión preliminar)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Solución de problemas de implementación de puntos de conexión en línea administrados](how-to-troubleshoot-managed-online-endpoints.md)
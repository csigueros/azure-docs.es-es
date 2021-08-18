---
title: Servicio del modelo de alto rendimiento con Triton (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo implementar su modelo con NVIDIA Triton Inference Server en Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/17/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 894b95b1fb00402f9cfed2614639b29ac5412f4b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446447"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Servicio de alto rendimiento con Triton Inference Server (versión preliminar) 

Aprenda a usar [Triton Inference Server de NVIDIA](https://aka.ms/nvidia-triton-docs) para mejorar el rendimiento del servicio web que se usa para la inferencia de modelos.

Una de las formas de implementar un modelo para la inferencia es como servicio web. Por ejemplo, una implementación en Azure Kubernetes Service o Azure Container Instances. De manera predeterminada, Azure Machine Learning usa un marco web *de uso general* de un solo subproceso para las implementaciones de servicios web.

Triton es un marco que está *optimizado para la inferencia*. Ofrece un mejor uso de las GPU y una inferencia más rentable. Del lado servidor, procesa por lotes las solicitudes entrantes y envía estos lotes para inferencia. El procesamiento por lotes mejora el uso de los recursos de GPU y es una parte fundamental del rendimiento de Triton.

> [!IMPORTANT]
> El uso de Triton para la implementación desde Azure Machine Learning está actualmente en __versión preliminar__. Es posible que la funcionalidad de versión preliminar no esté incluida en el soporte técnico al cliente. Para obtener más información, consulte [Condiciones de uso complementarias de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!TIP]
> Los fragmentos de código de este documento tienen fines ilustrativos y puede que no muestren una solución completa. Para ver el código de ejemplo funcional, consulte los [ejemplos integrales de Triton en Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) es un software de terceros de código abierto integrado en Azure Machine Learning.

## <a name="prerequisites"></a>Prerrequisitos

* Una **suscripción de Azure**. Si no tiene una ya, pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
* Familiaridad con [cómo y dónde se implementa un modelo](how-to-deploy-and-where.md) con Azure Machine Learning.
* El [SDK de Azure Machine Learning para Python](/python/api/overview/azure/ml/) **o** la [CLI de Azure](/cli/azure/) y la [extensión de Machine Learning](reference-azure-machine-learning-cli.md).
* Una instalación en funcionamiento de Docker para las pruebas locales. Para obtener información sobre la instalación y validación de Docker, consulte [Orientation and setup](https://docs.docker.com/get-started/) (Orientación e instalación) en la documentación de Docker.

## <a name="architectural-overview"></a>Introducción a la arquitectura

Antes de intentar usar Triton para su propio modelo, es importante comprender cómo funciona con Azure Machine Learning y cómo se compara con una implementación predeterminada.

**Implementación predeterminada sin Triton**

* Se inician varios trabajos de [Gunicorn](https://gunicorn.org/) para controlar las solicitudes entrantes simultáneamente.
* Estos trabajos controlan el procesamiento previo, la llamada al modelo y el procesamiento posterior. 
* Los clientes usan el __identificador URI de puntuación de Azure ML__. Por ejemplo, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagrama de la arquitectura de implementación normal, sin Triton":::

**Implementación directa con Triton**

* Las solicitudes van directamente al servidor Triton.
* Triton procesa las solicitudes por lotes para maximizar el uso de la GPU.
* El cliente usa el __identificador URI de Triton__ para hacer solicitudes. Por ejemplo, `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Implementación de Inferenceconfig solo con Triton y sin middleware de Python":::


## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Implementación de Triton sin procesamiento previo y posterior de Python

En primer lugar, siga los pasos que se indican a continuación para comprobar que el servidor de inferencia de Triton puede servir a su modelo.

### <a name="optional-define-a-model-config-file"></a>(Opcional) Definición de un archivo de configuración de modelo

El archivo de configuración del modelo indica a Triton el número de entradas que espera y qué dimensiones tendrán esas entradas. Para obtener más información sobre cómo crear el archivo de configuración, consulte [Configuración del modelo](https://aka.ms/nvidia-triton-docs) en la documentación de NVIDIA.

> [!TIP]
> Usamos la opción `--strict-model-config=false` al iniciar Triton Inference Server, lo que significa que no es necesario proporcionar un archivo `config.pbtxt` para los modelos de ONNX ni TensorFlow.
> 
> Para obtener más información sobre esta opción, consulte [Configuración del modelo generada](https://aka.ms/nvidia-triton-docs) en la documentación de NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Uso de la estructura de directorios adecuada

Al registrar un modelo en Azure Machine Learning, puede registrar archivos individuales o una estructura de directorios. Para usar Triton, el registro del modelo debe ser para una estructura de directorios que contenga un directorio denominado `triton`. La estructura general de este directorio es:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Esta estructura de directorios es un repositorio de modelos de Triton y es necesario para que los modelos funcionen con Triton. Para obtener más información, consulte [Repositorios de modelos de Triton](https://aka.ms/nvidia-triton-docs) en la documentación de NVIDIA.

### <a name="register-your-triton-model"></a>Registro del modelo de Triton

# <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Para más información sobre `az ml model register`, consulte la [documentación de referencia](/cli/azure/ml/model).

Al registrar el modelo en Azure Machine Learning, el valor del parámetro `--model-path  -p` debe ser el nombre de la carpeta principal del repositorio de modelos Triton.
En el ejemplo anterior, `--model-path` es "models".

El valor del parámetro `--name  -n`, `my_triton_models` en el ejemplo, será el nombre del modelo conocido en el área de trabajo de Azure Machine Learning. 

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=register-model)]

Para más información, consulte la documentación de la [clase Model](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Implementación del modelo

# <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)

Si tiene un clúster de Azure Kubernetes Service habilitado para GPU llamado "aks-gpu" creado mediante Azure Machine Learning, puede usar el siguiente comando para implementar el modelo.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=deploy-webservice)]

---

Consulte [esta documentación para más información sobre la implementación de modelos](how-to-deploy-and-where.md).

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

### <a name="call-into-your-deployed-model"></a>Llamada al modelo implementado

En primer lugar, obtenga el identificador URI de puntuación y los tokens de portador.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)

```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=get-keys)]

---

Realice lo siguiente par asegurarse de que el servicio está en ejecución. 

# <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)

```azurecli
```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Este comando devuelve información similar a la siguiente. Tenga en cuenta el `200 OK`; este estado significa que el servidor web se está ejecutando.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=query-service)]

---


Después de realizar una comprobación de estado, puede crear un cliente para enviar datos a Triton para la inferencia. Para obtener más información sobre la creación de un cliente, consulte los [ejemplos de cliente](https://aka.ms/nvidia-client-examples) en la documentación de NVIDIA. También hay [ejemplos de Python en GitHub de Triton](https://aka.ms/nvidia-triton-docs).

## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir usando el área de trabajo de Azure Machine Learning, pero quiere deshacerse del servicio implementado, use una de las opciones siguientes:


# <a name="azure-cli"></a>[CLI de Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=delete-service)]

---
## <a name="troubleshoot"></a>Solución de problemas

* [Solucione problemas de una implementación con errores](how-to-troubleshoot-deployment.md) y obtenga información sobre cómo solucionar errores comunes que puede experimentar al implementar un modelo.

* Si los registros de implementación muestran que **TritonServer no se pudo iniciar**, consulte la [documentación de código abierto de Nvidia](https://github.com/triton-inference-server/server).

## <a name="next-steps"></a>Pasos siguientes

* [Consulte los ejemplos integrales de Triton en Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Consulte los [ejemplos de cliente de Triton](https://aka.ms/nvidia-client-examples)
* Lea la [documentación de Triton Inference Server](https://aka.ms/nvidia-triton-docs)
* [Implementación en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Actualización de servicios web](how-to-deploy-update-web-service.md)
* [Recopilar datos de modelos en producción](how-to-enable-data-collection.md)

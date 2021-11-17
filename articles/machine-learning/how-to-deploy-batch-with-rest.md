---
title: Implementación de modelos mediante puntos de conexión en lote con API REST (versión preliminar)
titleSuffix: Azure Machine Learning
description: Aprenda a implementar modelos mediante puntos de conexión en lotes con las API REST.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: tracych
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 351855c870f3f3658488c66c401cef1c3c4b17c4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059816"
---
# <a name="deploy-models-with-rest-preview-for-batch-scoring"></a>Implementación de modelos con REST (versión preliminar) para la puntuación por lotes 

Aprenda a usar las API REST de Azure Machine Learning para implementar modelos para la puntuación por lotes (versión preliminar).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

La API REST utiliza verbos HTTP estándar para crear, recuperar, actualizar y eliminar recursos. La API REST funciona con cualquier lenguaje o herramienta que pueda hacer solicitudes HTTP. La estructura sencilla de REST hace que sea una buena opción en entornos de scripting y en la automatización de MLOps.

En este artículo, aprenderá a usar las nuevas API REST para:

> [!div class="checklist"]
> * Crear recursos de aprendizaje automático
> * Crear un punto de conexión por lotes y una implementación por lotes
> * Invocar un punto de conexión por lotes para iniciar un trabajo de puntuación por lotes

## <a name="prerequisites"></a>Prerequisites

- Una **suscripción de Azure** en la que tenga derechos administrativos Si no tiene este tipo de suscripción, pruebe la [suscripción personal gratis o de pago](https://azure.microsoft.com/free/).
- Un [área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).
- Una entidad de servicio en el área de trabajo. Las solicitudes administrativas de REST usan la [autenticación de entidad de servicio](how-to-setup-authentication.md#use-service-principal-authentication).
- Un token de autenticación de entidad de servicio. Para recuperar este token, siga los pasos que se describen en la sección [Recuperación de un token de autenticación de entidad de servicio](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token). 
- La utilidad **curl**. El programa **curl** está disponible en el [subsistema de Windows para Linux](/windows/wsl/install-win10) o en cualquier distribución de UNIX. En PowerShell, **curl** es un alias de **Invoke-WebRequest** y `curl -d "key=val" -X POST uri` se convierte en `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 
- El procesador JSON [jq](https://stedolan.github.io/jq/).

> [!IMPORTANT]
> En los fragmentos de código de este artículo se supone que usa el shell de Bash.
>
> Los fragmentos de código se extraen del archivo `/cli/batch-score-rest.sh` en el [repositorio de ejemplos de AzureML](https://github.com/Azure/azureml-examples).

## <a name="set-endpoint-name"></a>Establecimiento de un nombre de punto de conexión

> [!NOTE]
> Los nombres de los puntos de conexión por lotes deben ser únicos a nivel de región de Azure. Por ejemplo, solo puede haber un punto de conexión por lotes con el nombre mipuntodeconexionporlotes en westus2.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-batch-endpoints"></a>Puntos de conexión por lotes en Azure Machine Learning

Los [puntos de conexión por lotes (versión preliminar)](concept-endpoints.md#what-are-batch-endpoints-preview) simplifican el proceso de hospedar los modelos para la puntuación por lotes, por lo que puede centrarse en el aprendizaje automático y dejar a un lado la infraestructura. En este artículo, creará un punto de conexión y una implementación por lotes, y lo invocará para iniciar un trabajo de puntuación por lotes. Pero primero tendrá que registrar los recursos necesarios para la implementación, incluidos el modelo, el código y el entorno.

Hay muchas formas de crear puntos de conexión por lotes de Azure Machine Learning, [incluida la CLI de Azure](how-to-use-batch-endpoint.md), y visualmente con [Studio](how-to-use-batch-endpoints-studio.md). En el ejemplo siguiente se crea un punto de conexión y una implementación por lotes con la API REST.

## <a name="create-machine-learning-assets"></a>Crear recursos de aprendizaje automático

En primer lugar, configure los recursos Azure Machine Learning para configurar el trabajo.

En las siguientes llamadas de API REST, usamos `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` y `WORKSPACE` como marcadores de posición. Reemplace los marcadores de posición por sus propios valores. 

Las REST administrativa solicita un [token de autenticación de entidad de servicio](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token). Reemplace `TOKEN` por su propio valor. Este token se puede recuperar con el siguiente comando:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="13":::

El proveedor de servicios utiliza el argumento `api-version` para garantizar la compatibilidad. El argumento `api-version` varía de un servicio a otro. Establezca la versión de la API como una variable para dar cabida a versiones futuras:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="11":::

### <a name="create-compute"></a>Creación del proceso
La puntuación por lotes solo se ejecuta en recursos de informática en la nube, no de forma local. El recurso de informática en la nube es un clúster de proceso virtual reutilizable donde puede ejecutar flujos de trabajo de puntuación por lotes.

Cree un clúster de proceso:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_compute":::

> [!TIP]
> Si en su lugar desea usar un proceso existente, debe especificar el identificador de Azure Resource Manager completo al [crear la implementación por lotes](#create-batch-deployment). El identificador completo usa el formato `/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes/<your-compute-name>`.

### <a name="get-storage-account-details"></a>Obtención de los detalles de una cuenta de almacenamiento

Para registrar el modelo y el código, primero deben cargarse en una cuenta de almacenamiento. Los detalles de la cuenta de almacenamiento están disponibles en el almacén de datos. En este ejemplo, se obtiene el almacén de datos predeterminado y la cuenta de Azure Storage del área de trabajo. Consulte el área de trabajo con una solicitud GET para obtener un archivo JSON con la información.

Puede usar la herramienta [jq](https://stedolan.github.io/jq/) para analizar el resultado de JSON y obtener los valores necesarios. También puede usar Azure Portal para buscar la misma información:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>Carga y registro del código

Ahora que tiene el almacén de datos, puede cargar el script de puntuación. Use Azure Storage CLI para cargar un blob en el contenedor predeterminado:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_code":::

> [!TIP]
> Para realizar dicha carga también se pueden usar otros métodos, como Azure Portal o el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

Una vez que cargue el código, puede especificarlo con una solicitud PUT:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>Carga y registro del modelo

De forma similar al código, cargue los archivos del modelo:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_model":::

Ahora, registre el modelo:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_model":::

### <a name="create-environment"></a>Creación del entorno
La implementación debe ejecutarse en un entorno que tenga las dependencias necesarias. Cree el entorno con una solicitud PUT. Use una imagen de Docker desde Microsoft Container Registry. La imagen de Docker se puede configurar con `image` y agregar dependencias de Conda con `condaFile`.

Ejecute el código siguiente para leer el `condaFile` definido en json. El archivo de origen se encuentra en `/cli/endpoints/batch/mnist/environment/conda.json` en el repositorio de ejemplos:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="read_condafile":::

Ahora, ejecute el siguiente fragmento de código para crear un entorno:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_environment":::

## <a name="deploy-with-batch-endpoints"></a>Implementación con puntos de conexión por lotes

A continuación, cree el punto de conexión por lotes, una implementación y establezca la implementación predeterminada.

### <a name="create-batch-endpoint"></a>Creación del punto de conexión por lotes

Cree el punto de conexión por lotes:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_endpoint":::

### <a name="create-batch-deployment"></a>Creación de la implementación por lotes

Cree una implementación por lotes debajo del punto de conexión:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_deployment":::

### <a name="set-the-default-batch-deployment-under-the-endpoint"></a>Definición de la implementación por lotes debajo del punto de conexión

Solo hay una implementación por lotes predeterminada bajo un punto de conexión, que se usará cuando se invoque para ejecutar el trabajo de puntuación por lotes.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_defaults":::

## <a name="run-batch-scoring"></a>Ejecución de la puntuación por lotes

La invocación de un punto de conexión por lotes desencadena un trabajo de puntuación por lotes. Se devuelve un `id` de trabajo de la respuesta, y se puede usar para realizar un seguimiento del progreso de la puntuación por lotes. En los fragmentos de código siguientes, se usa `jq` para obtener el `id` de trabajo.

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>Invocación del punto de conexión por lotes para iniciar un trabajo de puntuación por lotes

Obtenga el URI de la puntuación y el token de acceso para invocar el punto de conexión por lotes. En primer lugar, obtenga el identificador URI de la puntuación:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_endpoint":::

Obtenga el token de acceso del punto de conexión por lotes:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_access_token":::

Ahora, invoque el punto de conexión por lotes para iniciar un trabajo de puntuación por lotes. En el ejemplo siguiente se puntúan los datos disponibles públicamente en la nube:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_data_in_cloud":::

Si los datos se encuentran en un almacén de datos registrado de Azure Machine Learning, puede invocar el punto de conexión por lotes con un conjunto de datos. El siguiente código crea un conjunto de datos nuevo:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_dataset":::

A continuación, haga referencia al conjunto de datos al invocar el punto de conexión por lotes:

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_dataset":::

En el fragmento de código anterior, se proporciona una ubicación de salida personalizada mediante `datastoreId`, `path` y `outputFileName`. Esta configuración le permite configurar dónde almacenar los resultados de la puntuación por lotes.

> [!IMPORTANT]
> Debe indicar una ubicación de salida única. Si el archivo de salida ya existe, el trabajo de puntuación por lotes generará errores.

En este ejemplo, la salida se almacena en el almacenamiento de blobs predeterminado para el área de trabajo. El nombre de la carpeta es el mismo que el nombre del punto de conexión, y el nombre de archivo se genera aleatoriamente mediante el código siguiente:

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score-rest.sh" ID="unique_output" :::

### <a name="check-the-batch-scoring-job"></a>Comprobación del trabajo de puntuación por lotes

Los trabajos de puntuación por lotes suelen tardar algún tiempo en procesar todo el conjunto de entradas. Supervise el estado del trabajo y compruebe los resultados una vez que se complete:

> [!TIP]
> En el ejemplo se invoca la implementación predeterminada del punto de conexión por lotes. Para invocar una implementación no predeterminada, use el encabezado HTTP `azureml-model-deployment` y establezca el valor en el nombre de implementación. Por ejemplo, mediante un parámetro de `--header "azureml-model-deployment: $DEPLOYMENT_NAME"` con curl.

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="check_job":::

### <a name="check-batch-scoring-results"></a>Comprobación de los resultados de la puntuación por lotes

Para obtener información sobre cómo comprobar los resultados, consulte [Comprobación de los resultados de la puntuación por lotes](how-to-use-batch-endpoint.md#check-batch-scoring-results).

## <a name="delete-the-batch-endpoint"></a>Eliminación del punto de conexión por lotes

Si no va a usar el punto de conexión por lotes, debe eliminarlo con el siguiente comando (se eliminan el punto de conexión por lotes y todas las implementaciones subyacentes):

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a implementar el modelo para la puntuación por lotes mediante la [CLI de Azure](how-to-use-batch-endpoint.md).
* Aprenda a implementar el modelo para la puntuación por lotes mediante [Studio](how-to-use-batch-endpoints-studio.md).
* Aprenda a [solucionar problemas de puntos de conexión por lotes](how-to-troubleshoot-batch-endpoints.md).

---
title: Uso de puntos de conexión por lotes para la puntuación por lotes
titleSuffix: Azure Machine Learning
description: En este artículo, aprenderá a crear un punto de conexión por lotes para puntuar continuamente datos de gran tamaño por lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 10/21/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: 966b8abf60e0569a683932824045253ac32c724e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560497"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>Uso de puntos de conexión por lotes (versión preliminar) para la puntuación por lotes

Aprenda a usar puntos de conexión por lotes (versión preliminar) para realizar la puntuación por lotes. Los puntos de conexión por lotes simplifican el proceso de hospedar los modelos para la puntuación por lotes, por lo que puede centrarse en el aprendizaje automático y dejar a un lado la infraestructura. Para más información, consulte [¿Qué son los puntos de conexión de Azure Machine Learning (versión preliminar)?](concept-endpoints.md).

En este artículo, aprenderá a realizar las siguientes tareas:

> [!div class="checklist"]
> * Creación de un punto de conexión por lotes y una implementación por lotes predeterminada
> * Inicio de un trabajo de puntuación por lotes mediante la CLI de Azure
> * Supervisión del progreso de la ejecución de trabajos de puntuación por lotes y comprobar los resultados de puntuación
> * Implementación de un nuevo modelo de MLflow con código y entorno generados automáticamente en un punto de conexión existente sin afectar al flujo existente
> * Prueba de la nueva implementación y su establecimiento como implementación predeterminada
> * Eliminación del punto de conexión y la implementación sin usar

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos

* Debe tener una suscripción a Azure para usar Azure Machine Learning. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Instale la CLI de Azure y la extensión `ml`. Siga los pasos de instalación de [Instalación, configuración y uso de la CLI (v2) (versión preliminar)](how-to-configure-cli.md).

* Cree un grupo de recursos de Azure si no tiene uno. Deberá tener el permiso `Contributor`, usted o la entidad de servicio que use. Para la creación del grupo de recursos, consulte [Instalación, configuración y uso de la CLI (v2) (versión preliminar)](how-to-configure-cli.md). 

* Cree un área de trabajo de Azure Machine Learning si aún no tiene una. Para la creación del área de trabajo, consulte [Instalación, configuración y uso de la CLI (v2) (versión preliminar)](how-to-configure-cli.md). 

* Configure el área de trabajo y el grupo de recursos predeterminados para la CLI de Azure. Los comandos de la CLI de Machine Learning requieren los parámetros `--workspace/-w` y `--resource-group/-g`. Configurar los valores predeterminados puede evitar pasar los valores varias veces. Puede invalidar estos valores en la línea de comandos. Ejecute el código siguiente para configurar los valores predeterminados. Para más información, consulte [Instalación, configuración y uso de la CLI v2 (versión preliminar)](how-to-configure-cli.md).

```azurecli
az account set -s "<subscription ID>"
az configure --defaults group="<resource group>" workspace="<workspace name>" location="<location>"
```

### <a name="clone-the-example-repository"></a>Clonación del repositorio de ejemplos

Ejecute los siguientes comandos para clonar el [repositorio de ejemplo de AzureML](https://github.com/Azure/azureml-examples) y vaya al directorio `cli`. En este artículo se usan los recursos de `/cli/endpoints/batch` y el ejemplo de trabajo completo es `/cli/batch-score.sh`.

```azurecli
git clone https://github.com/Azure/azureml-examples 
cd azureml-examples/cli
```

Establezca el nombre del punto de conexión. Reemplace `YOUR_ENDPOINT_NAME` por un nombre único dentro de una región de Azure.

Para Unix, ejecute este comando:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="set_variables" :::

Para Windows, ejecute este comando:

```azurecli
set ENDPOINT_NAME="<YOUR_ENDPOINT_NAME>"
```

> [!NOTE]
> Los nombres de punto de conexión deben ser únicos dentro de una región de Azure. Por ejemplo, solo puede haber un punto de conexión por lotes con el nombre mybatchendpoint en westus2.

### <a name="create-compute"></a>Creación del proceso

El punto de conexión por lotes solo se ejecuta en recursos de informática en la nube, no de forma local. El recurso de informática en la nube es un clúster de proceso virtual reutilizable. Ejecute el código siguiente para crear un clúster de proceso de Azure Machine Learning. En los ejemplos siguientes de este artículo se usa el proceso creado aquí denominado `batch-cluster`. Realice los ajustes necesarios y haga referencia al proceso mediante `azureml:<your-compute-name>`.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_compute" :::

> [!NOTE]
> En este momento, no se le cobra por el proceso, ya que el clúster permanecerá en 0 nodos hasta que se invoque un punto de conexión por lotes y se envíe un trabajo de puntuación por lotes. Más información sobre cómo [administrar y optimizar el coste de AmlCompute](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute).

## <a name="understand-batch-endpoints-and-batch-deployments"></a>Información sobre los puntos de conexión por lotes y las implementaciones por lotes

Un punto de conexión por lotes es un punto de conexión HTTPS al que los clientes pueden llamar para desencadenar un trabajo de puntuación por lotes. Un trabajo de puntuación por lotes es un trabajo que puntúa varias entradas (para más información, consulte [¿Qué son los puntos de conexión por lotes?](concept-endpoints.md#what-are-batch-endpoints-preview)). Una implementación por lotes es un conjunto de recursos de proceso que hospedan el modelo que realiza la puntuación por lotes real. Un punto de conexión por lotes puede tener varias implementaciones. 

> [!TIP]
> Una de las implementaciones por lotes servirá como implementación predeterminada para el punto de conexión. La implementación predeterminada se usará para realizar la puntuación por lotes real cuando se invoque el punto de conexión. Más información sobre los [puntos de conexión por lotes y las implementaciones por lotes](concept-endpoints.md#what-are-batch-endpoints-preview).

En el siguiente archivo YAML se define un punto de conexión por lotes, que puede incluir en el comando de la CLI para la [creación del punto de conexión por lotes](#create-a-batch-endpoint). En el repositorio, este archivo se encuentra en `/cli/endpoints/batch/batch-endpoint.yml`.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/batch-endpoint.yml":::

En la tabla siguiente se describen las propiedades clave del YAML de punto de conexión. Para el esquema de YAML de punto de conexión por lotes completo, consulte [Esquema de YAML de punto de conexión por lotes de la CLI (v2)](./reference-yaml-endpoint-batch.md).

| Clave | Descripción |
| --- | ----------- |
| `$schema` | [Opcional] El esquema de YAML. Puede ver el esquema del ejemplo anterior en un explorador para examinar todas las opciones disponibles para un archivo YAML de punto de conexión por lotes. |
| `name` | El nombre del punto de conexión por lotes. Es preciso que sea único en el nivel de región de Azure.|
| `auth_mode` | El método de autenticación para el punto de conexión por lotes. Actualmente solo se admite la autenticación basada en tokens de Azure Active Directory (`aad_token`). |
| `defaults.deployment_name` | El nombre de la implementación que servirá de implementación predeterminada para el punto de conexión. |

Para crear una implementación por lotes, necesita todos los elementos siguientes:
* Archivos de modelo o un modelo registrado en el área de trabajo al que se haga referencia mediante `azureml:<model-name>:<model-version>`. 
* El código para puntuar el modelo.
* El entorno en el que se ejecuta el modelo. Puede ser una imagen de Docker con dependencias de Conda o un entorno ya registrado en el área de trabajo al que se hace referencia mediante `azureml:<environment-name>:<environment-version>`.
* El proceso creado previamente al que se hace referencia mediante `azureml:<compute-name>` y la configuración de los recursos.

Para más información sobre cómo hacer referencia a una entidad de Azure ML, consulte [Referencia a una entidad de Azure ML](reference-yaml-core-syntax.md#referencing-an-azure-ml-entity).

El repositorio de ejemplo contiene todos los archivos necesarios. El siguiente archivo YAML define una implementación por lotes con todas las entradas necesarias y la configuración opcional. Puede incluir este archivo en el comando de la CLI para [crear la implementación por lotes](#create-a-batch-deployment). En el repositorio, este archivo se encuentra en `/cli/endpoints/batch/nonmlflow-deployment.yml`. 

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/nonmlflow-deployment.yml":::

En la tabla siguiente se describen las propiedades clave del YAML de implementación. Para el esquema de YAML de implementación por lotes completo, consulte [Esquema de YAML de implementación por lotes de la CLI (v2)](./reference-yaml-deployment-batch.md).

| Clave | Descripción |
| --- | ----------- |
| `$schema` | [Opcional] El esquema de YAML. Puede ver el esquema del ejemplo anterior en un explorador para examinar todas las opciones disponibles para un archivo YAML de implementación por lotes. |
| `name` | Nombre de la implementación. |
| `endpoint_name` | El nombre del punto de conexión en el que se creará la implementación. |
| `model` | Modelo que se va a usar para la puntuación por lotes. En el ejemplo se define un modelo en línea mediante `local_path`. Los archivos de modelo se cargarán y registrarán de manera automática con un nombre y una versión generados automáticamente. Siga el [Esquema del modelo](reference-yaml-model.md#yaml-syntax) para más opciones. Como procedimiento recomendado para escenarios de producción, debe crear el modelo por separado y hacerle referencia aquí. Para hacer referencia a un modelo existente, use la sintaxis `azureml:<model-name>:<model-version>`. |
| `code_configuration.code.local_path` | El directorio que contiene todo el código fuente Python para puntuar el modelo. |
| `code_configuration.scoring_script` | El archivo Python en el directorio anterior. Este archivo debe tener una función `init()` y una función `run()`. Use la función `init()` para cualquier preparación costosa o común (por ejemplo, cargar el modelo en memoria). Solo se llamará a `init()` una vez al principio del proceso. Use `run(mini_batch)` para puntuar cada entrada; el valor de `mini_batch` es una lista de rutas de acceso de archivo. La función `run()` debe devolver un dataframe de Pandas o una matriz. Cada elemento devuelto indica una ejecución correcta del elemento de entrada en `mini_batch`. Asegúrese de que se incluyen suficientes datos en la respuesta de `run()` para correlacionar la entrada con la salida. |
| `environment` | El entorno en el que se va a puntuar el modelo. En el ejemplo se define un entorno en línea mediante `conda_file` y `image`. Las dependencias de `conda_file` se instalarán encima de `image`. El entorno se registrará de manera automática con un nombre y una versión generados automáticamente. Siga el [Esquema del entorno](reference-yaml-environment.md#yaml-syntax) para más opciones. Como procedimiento recomendado para escenarios de producción, debe crear el entorno por separado y hacerle referencia aquí. Para hacer referencia a un entorno existente, use la sintaxis `azureml:<environment-name>:<environment-version>`. |
| `compute` | El proceso para ejecutar la puntuación por lotes. En el ejemplo se usa el objeto `batch-cluster` creado al principio y se hace referencia a él mediante la sintaxis `azureml:<compute-name>`. |
| `resources.instance_count` | El número de instancias que se usarán para cada trabajo de puntuación por lotes. |
| `max_concurrency_per_instance` | [Opcional] El número máximo de ejecuciones de `scoring_script` paralelas por instancia. |
| `mini_batch_size` | [Opcional] Número de archivos que `scoring_script` puede procesar en una llamada a `run()`. |
| `output_action` | [Opcional] Cómo se debe organizar la salida en el archivo de salida. `append_row` combinará todos los resultados de salida devueltos por `run()` en un único archivo denominado `output_file_name`. `summary_only` no combinará los resultados de salida y solo calculará `error_threshold`. |
| `output_file_name` | [Opcional] El nombre del archivo de salida de puntuación por lotes para `append_row` `output_action`. |
| `retry_settings.max_retries` | [Opcional] Número máximo de intentos erróneos de una llamada a `run()` de `scoring_script`. |
| `retry_settings.timeout` | [Opcional] El tiempo de espera en segundos para un `scoring_script` `run()` para puntuar un mini lote. |
| `error_threshold` | [Opcional] El número de errores de puntuación de archivo de entrada que se deben omitir. Si el recuento de errores de toda la entrada supera este valor, el trabajo de puntuación por lotes se terminará. En el ejemplo se usa `-1`, que indica que se permite cualquier número de errores sin que se termine el trabajo de puntuación por lotes. | 
| `logging_level` | [Opcional] Nivel de detalle del registro. Los valores en el aumento del nivel de detalle son: WARNING, INFO y DEBUG. |

###  <a name="understand-the-scoring-script"></a>Descripción del script de puntuación

Como se mencionó anteriormente, `code_configuration.scoring_script` debe contener dos funciones:

- `init()`: use esta función en el caso de cualquier preparación costosa o común. Por ejemplo, para cargar el modelo en un objeto global. Se llamará a esta función una vez al principio del proceso.
-  `run(mini_batch)`: se llamará a esta función para cada `mini_batch` y se realizará la puntuación real. 
    -  `mini_batch`: El valor de `mini_batch` es una lista de rutas de acceso de archivo.
    -  `response`: el método `run()` debe devolver un dataframe de Pandas o una matriz. Cada elemento de salida devuelto indica una ejecución correcta del elemento de entrada en el `mini_batch` de entrada. Asegúrese de que se incluyen suficientes datos (por ejemplo, un identificador de cada elemento de entrada) en la respuesta de `run()` para correlacionar una entrada con un resultado de salida. 

En el ejemplo se utiliza `/cli/endpoints/batch/mnist/code/digit_identification.py`. El modelo se carga en `init()` desde `AZUREML_MODEL_DIR`, que es la ruta de acceso a la carpeta del modelo creada durante la implementación. `run(mini_batch)` itera cada archivo en `mini_batch`, realiza la puntuación del modelo real y, a continuación, devuelve los resultados de salida.

## <a name="deploy-with-batch-endpoints-and-run-batch-scoring"></a>Implementación con puntos de conexión por lotes y ejecución de la puntuación por lotes

Ahora, vamos a implementar el modelo con puntos de conexión por lotes y a ejecutar la puntuación por lotes.

### <a name="create-a-batch-endpoint"></a>Creación de un punto de conexión por lotes

La manera más sencilla de crear un punto de conexión por lotes es ejecutar el código siguiente proporcionando solo un elemento `--name`.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_batch_endpoint" :::

También puede crear un punto de conexión por lotes mediante un archivo YAML. Agregue el parámetro `--file` en el comando anterior y especifique la ruta de acceso del archivo YAML.

### <a name="create-a-batch-deployment"></a>Creación de una implementación por lotes

Ejecute el código siguiente para crear una implementación por lotes denominada `nonmlflowdp` en el punto de conexión por lotes y establézcala como la implementación predeterminada. 

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_batch_deployment_set_default" :::

> [!TIP]
> El parámetro `--set-default` establece la implementación recién creada como la implementación predeterminada del punto de conexión. Es una manera cómoda de crear una nueva implementación predeterminada del punto de conexión, especialmente para la primera creación de la implementación. Como procedimiento recomendado para escenarios de producción, puede crear una nueva implementación sin establecerla como predeterminada, comprobarla y actualizar la implementación predeterminada más adelante. Para más información, consulte la sección [Implementación de un modelo nuevo](#deploy-a-new-model).

### <a name="check-batch-endpoint-and-deployment-details"></a>Comprobación de los detalles de implementación y punto de conexión por lotes

Use `show` para comprobar los detalles de implementación y punto de conexión por lotes.

Para comprobar una implementación por lotes, ejecute el código siguiente:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="check_batch_deployment_detail" :::

Para comprobar un punto de conexión por lotes, ejecute el código siguiente. Como la implementación recién creada se establece como la implementación predeterminada, debe ver `nonmlflowdp` en `defaults.deployment_name` en la respuesta.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>Invocación del punto de conexión por lotes para iniciar un trabajo de puntuación por lotes

La invocación de un punto de conexión por lotes desencadena un trabajo de puntuación por lotes. Se devolverá un elemento `name` de trabajo de la respuesta de invocación y se puede usar para realizar un seguimiento del progreso de la puntuación por lotes. El trabajo de puntuación por lotes se ejecuta durante un período de tiempo. Divide todas las entradas en varios `mini_batch` y procesa en paralelo en el clúster de proceso. Un `scoring_scrip` `run()` toma un `mini_batch` y lo procesa mediante un proceso en una instancia. Las salidas del trabajo de puntuación por lotes se almacenarán en el almacenamiento en la nube, ya sea en el almacenamiento de blobs predeterminado del área de trabajo o en el almacenamiento que especifique.

#### <a name="invoke-the-batch-endpoint-with-different-input-options"></a>Invocación del punto de conexión por lotes con diferentes opciones de entrada

Puede usar la CLI o REST a fin de ejecutar `invoke` para el punto de conexión. Para una experiencia REST, consulte [Uso de puntos de conexión por lotes (versión preliminar) con REST](how-to-deploy-batch-with-rest.md).

Hay tres opciones para especificar las entradas de datos en la CLI `invoke`.

* __Opción 1: Datos en la nube__

    Use `--input-path` para especificar una carpeta (use el prefijo `folder:`) o un archivo (use el prefijo `file:`) en un almacén de datos registrado de Azure Machine Learning. La sintaxis del URI de datos es `folder:azureml://datastores/<datastore-name>/paths/<data-path>/` para una carpeta y `file:azureml://datastores/<datastore-name>/paths/<data-path>/<file-name>` para un archivo específico. Para más información sobre el URI de datos, vea [URI de referencia de datos de Azure Machine Learning](reference-yaml-core-syntax.md#azure-ml-data-reference-uri).

    En el ejemplo se usan datos disponibles públicamente en una carpeta de `https://pipelinedata.blob.core.windows.net/sampledata/mnist`, que contiene miles de dígitos escritos a mano. El nombre del trabajo de puntuación por lotes se devolverá en la respuesta de la invocación. Ejecute el código siguiente para invocar el punto de conexión por lotes con estos datos. `--query name` se agrega para devolver solo el nombre del trabajo de la respuesta de invocación y se usará más adelante para [supervisar el progreso de la ejecución del trabajo de puntuación por lotes](#monitor-batch-scoring-job-execution-progress) y [comprobar los resultados de puntuación por lotes](#check-batch-scoring-results). Quite `--query name -o tsv` si desea ver la respuesta de invocación completa. Para más información sobre el parámetro `--query`, vea [Consulta de la salida de los comandos de la CLI de Azure](/cli/azure/query-azure-cli).

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="start_batch_scoring_job" :::

* __Opción 2: Datos registrados__

    Utilice `--input-dataset` para pasar un conjunto datos registrado de Azure Machine Learning. Para crear un conjunto de datos, compruebe `az ml dataset create -h` para las instrucciones y siga el [Esquema de conjunto de datos](reference-yaml-dataset.md#yaml-syntax).

    > [!NOTE]
    > También se puede usar el objeto FileDataset que se crea con la versión anterior de la CLI y el SDK de Python. No se admite TabularDataset. 

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-dataset azureml:<dataset-name>:<dataset-version>
    ```

* __Opción 3: Datos almacenados localmente__

    Use `--input-local-path` para pasar archivos de datos almacenados localmente. Los archivos de datos se cargarán y registrarán de manera automática con un nombre y una versión generados automáticamente.

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-local-path <local-path>
    ```

#### <a name="configure-the-output-location-and-overwrite-settings"></a>Configuración de la ubicación de salida y sobrescritura

Los resultados de la puntuación por lotes se almacenan de forma predeterminada en el almacén de blobs predeterminado del área de trabajo, dentro de una carpeta designada por el nombre del trabajo (un GUID generado por el sistema). Puede configurar dónde almacenar las salidas de puntuación al invocar el punto de conexión por lotes. Use `--output-path` para configurar cualquier objeto `folder:` en un almacén de datos registrado de Azure Machine Learning. La sintaxis de `--output-path` `folder:` es la misma que la de `--input-path` `folder:`. Use `--set output_file_name=<your-file-name>` para configurar un nuevo nombre de archivo de salida si prefiere tener un archivo de salida que contenga todos los resultados de puntuación (`output_action=append_row` especificado en el YAML de implementación).

> [!IMPORTANT]
> Debe usar una ubicación de salida única. Si el archivo de salida existe, el trabajo de puntuación por lotes generará errores. 

Algunas opciones de configuración se pueden sobrescribir en la invocación para hacer un uso mejor de los recursos de proceso y mejorar el rendimiento: 

* Use `--instance-count` para sobrescribir `instance_count`. Por ejemplo, para un mayor volumen de entradas de datos, puede que quiera usar más instancias para acelerar la puntuación por lotes de un extremo a otro.
* Use `--mini-batch-size` para sobrescribir `mini_batch_size`. El número de mini lotes se decide por los recuentos totales de archivos de entrada y mini_batch_size. Los mini_batch_size más pequeños generan más mini lotes. Los mini lotes se pueden ejecutar en paralelo, pero puede haber una sobrecarga adicional de programación e invocación. 
* Use `--set` para sobrescribir otros valores, entre los que se incluyen `max_retries`, `timeout` y `error_threshold`. Esta configuración puede afectar al tiempo de la puntuación por lotes de un extremo a otro para distintas cargas de trabajo.

Para especificar la ubicación de salida y sobrescribir la configuración en la invocación, ejecute el código siguiente. En el ejemplo se almacenan las salidas en una carpeta con el mismo nombre que el punto de conexión en el almacenamiento de blobs predeterminado del área de trabajo y también se usa un nombre de archivo aleatorio para garantizar la exclusividad de la ubicación de salida. El código debe funcionar en Unix. Reemplácelos por sus propios nombres únicos de archivo y carpeta.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="start_batch_scoring_job_configure_output_settings" :::

### <a name="monitor-batch-scoring-job-execution-progress"></a>Supervisión del progreso de la ejecución del trabajo de puntuación por lotes

Los trabajos de puntuación por lotes suelen tardar algún tiempo en procesar todo el conjunto de entradas. 

Puede usar la CLI `job show` para ver el trabajo. Ejecute el código siguiente para comprobar el estado del trabajo de la invocación de punto de conexión anterior. Para más información sobre los comandos de trabajo, ejecute `az ml job -h`.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="check_job_status" :::

### <a name="check-batch-scoring-results"></a>Comprobación de los resultados de la puntuación por lotes

Siga los pasos siguientes para ver los resultados de puntuación en Explorador de Azure Storage cuando se complete el trabajo:

1. Ejecute el código siguiente para abrir el trabajo de puntuación por lotes en Estudio de Azure Machine Learning. El vínculo del estudio de trabajo se incluye también en la respuesta de `invoke`, como el valor de `interactionEndpoints.Studio.endpoint`.

    :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="show_job_in_studio" :::

1. En el grafo de la ejecución, seleccione el paso `batchscoring`.
1. Seleccione la pestaña __Resultados y registros__ y, después, **Show data outputs** (Mostrar salidas de datos).
1. En __Data outputs__ (Salidas de datos), seleccione el icono para abrir el __Explorador de Storage__.

:::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="Captura de pantalla de Studio que muestra la vista de la ubicación de salidas de datos" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::

Los resultados de puntuación del Explorador de Storage son similares a la página de ejemplo siguiente:

:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.png" alt-text="Captura de pantalla de la salida de puntuación" lightbox="media/how-to-use-batch-endpoint/scoring-view.png":::

## <a name="deploy-a-new-model"></a>Implementación de un nuevo modelo

Una vez que tenga un punto de conexión por lotes, puede seguir refinando el modelo y agregar nuevas implementaciones.

### <a name="create-a-new-batch-deployment-hosting-an-mlflow-model"></a>Creación de una nueva implementación por lotes que hospeda un modelo de MLflow

Para crear una nueva implementación por lotes en el punto de conexión por lotes existente, pero no establecerla como implementación predeterminada, ejecute el código siguiente:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="create_new_deployment_not_default" :::

Tenga en cuenta que no se utiliza `--set-default`. Si vuelve a ejecutar `show` para el punto de conexión por lotes, no debería ver ningún cambio en `defaults.deployment_name`. 

En el ejemplo se usa un modelo (`/cli/endpoints/batch/autolog_nyc_taxi`) entrenado y del que se ha hecho un seguimiento con MLflow. `scoring_script` y `environment` se pueden generar automáticamente mediante los metadatos del modelo, sin necesidad de especificar en el archivo YAML. Para más información sobre MLflow, consulte [Entrenamiento y seguimiento de modelos de Machine Learning con MLflow y Azure Machine Learning (versión preliminar)](how-to-use-mlflow.md).

A continuación, se muestra el archivo YAML que el ejemplo usa para implementar un modelo de MLflow, que solo contiene las propiedades mínimas necesarias. El archivo de origen del repositorio es `/cli/endpoints/batch/mlflow-deployment.yml`.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/mlflow-deployment.yml":::

> [!NOTE]
> La generación automática de `scoring_script` y `environment` solo admite el tipo de modelo de función de Python y la firma del modelo basado en columnas.

### <a name="test-a-non-default-batch-deployment"></a>Prueba de una implementación por lotes no predeterminada

Para probar la nueva implementación no predeterminada, ejecute el código siguiente. En el ejemplo se usa un modelo diferente que acepta un archivo csv disponible públicamente desde `https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv`.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="test_new_deployment" :::

Observe que `--deployment-name` se usa para especificar el nuevo nombre de implementación. Este parámetro le permite ejecutar `invoke` para una implementación no predeterminada y no actualizará la implementación predeterminada del punto de conexión por lotes.

### <a name="update-the-default-batch-deployment"></a>Actualización de la implementación por lotes predeterminada

Para actualizar la implementación por lotes predeterminada del punto de conexión, ejecute el código siguiente:

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="update_default_deployment" :::

Ahora, si vuelve a ejecutar `show` para el punto de conexión por lotes, debería ver que `defaults.deployment_name` está establecido en `mlflowdp`. Puede ejecutar `invoke` para el punto de conexión por lotes directamente sin el parámetro `--deployment-name`.

### <a name="optional-update-the-deployment"></a>(Opcional) Actualización de la implementación

Si quiere actualizar la implementación (por ejemplo, el código, el modelo, el entorno o la configuración), actualice el archivo YAML y ejecute `az ml batch-deployment update`. También puede realizar la actualización sin el archivo YAML mediante `--set`. Consulte `az ml batch-deployment update -h` para más información.

## <a name="delete-the-batch-endpoint-and-the-deployment"></a>Eliminación del punto de conexión y la implementación por lotes

Si no va a usar la implementación por lotes anterior, debe eliminarla ejecutando el código siguiente. `--yes` se usa para confirmar la eliminación.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="delete_deployment" :::

Ejecute el código siguiente para eliminar el punto de conexión por lotes y todas las implementaciones subyacentes. Los trabajos de puntuación por lotes no se eliminarán.

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/batch-score.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Pasos siguientes

* [Puntos de conexión de Batch en Studio](how-to-use-batch-endpoints-studio.md)
* [Implementación de modelos con REST (versión preliminar) para la puntuación por lotes](how-to-deploy-batch-with-rest.md)
* [Solución de problemas de puntos de conexión por lotes](how-to-troubleshoot-batch-endpoints.md)

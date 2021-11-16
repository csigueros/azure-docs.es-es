---
title: Entrenamiento de modelos con la CLI (v2)
titleSuffix: Azure Machine Learning
description: Aprenda a entrenar modelos (crear trabajos) mediante la extensión de la CLI de Azure para Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: amibp
ms.author: amipatel
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: b849293d94b435fa74378d80d8989e6075f78d86
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493673"
---
# <a name="train-models-with-the-cli-v2-preview"></a>Entrenamiento de modelos con la CLI (v2) (versión preliminar)

La CLI de Azure Machine Learning (v2) es una extensión de la CLI de Azure que permite acelerar el entrenamiento de modelos durante el escalado vertical y horizontal de un proceso de Azure, así como auditar el ciclo de vida y realizar su seguimiento.

El entrenamiento de un modelo de aprendizaje automático habitualmente es un proceso iterativo. Las herramientas modernas facilitan más que nunca el entrenamiento de modelos mayor en más datos es con más rapidez. Los procesos manuales que anteriormente eran tediosos, como el ajuste de hiperparámetros, e incluso la selección de algoritmos, a menudo se automatizan. Con la CLI de Azure Machine Learning (v2) puede realizar un seguimiento de los trabajos (y modelos) en un [área de trabajo](concept-workspace.md) con barridos de hiperparámetros, realizar un escalado vertical de proceso de Azure de alto rendimiento y un escalado horizontal mediante el entrenamiento distribuido.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerrequisitos

- Para usar la CLI (v2), debe tener una suscripción de Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
- [Instalación y configuración de la CLI (v2)](how-to-configure-cli.md).

> [!TIP]
> Para un entorno de desarrollo completo con validación de esquemas y autocompletar para los YAML de trabajo, use Visual Studio Code y la [extensión Azure Machine Learning](how-to-setup-vs-code.md).

### <a name="clone-examples-repository"></a>Clonación del repositorio de ejemplos

Para ejecutar los ejemplos de entrenamiento, primero clone el repositorio de ejemplos y cambie al directorio `cli`:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

Tenga en cuenta al utilizar `--depth 1` solamente se clona la confirmación más reciente en el repositorio, lo cual reduce el tiempo para completar la operación.

### <a name="create-compute"></a>Creación del proceso

Puede crear un clúster de proceso de Azure Machine Learning desde la línea de comandos. Por ejemplo, los siguientes comandos crearán un clúster denominado `cpu-cluster` y otro denominado `gpu-cluster`.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-compute.sh" id="create_computes":::

No se le cobrará por el proceso en este momento, ya que `cpu-cluster` y `gpu-cluster` permanecerán en 0 nodos hasta que se envíe un trabajo. Más información sobre cómo [administrar y optimizar el costo de AmlCompute](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute).

En los siguientes trabajos de ejemplo de este artículo se usa `cpu-cluster` o `gpu-cluster`. Ajuste estos nombres en los trabajos de ejemplo de este artículo según sea necesario para el nombre de el clúster o los clústeres. Use `az ml compute create -h` para más detalles sobre las opciones de creación de proceso.

## <a name="hello-world"></a>Hola a todos

En el caso de la CLI de Azure Machine Learning (v2), los trabajos se crean en formato YAML. Un trabajo agrega:

- Qué ejecutar
- Cómo ejecutarlo
- Dónde ejecutarlo

El trabajo "hola mundo" tiene los tres elementos:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

> [!WARNING]
> Python debe instalarse en el entorno que se usa para los trabajos. Ejecute `apt-get update -y && apt-get install python3 -y` en el Dockerfile para instalarlo, si es necesario, o derive desde una imagen base con Python ya instalado.

> [!TIP]
> Los ejemplos `$schema:` permiten la validación de esquemas y la función de autocompletar si se han creado archivos YAML en [VSCode con la extensión Azure Machine Learning](how-to-setup-vs-code.md).

Que puede ejecutar:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

> [!TIP]
> El parámetro `--web` intentará abrir el trabajo en Estudio de Azure Machine Learning mediante el explorador web predeterminado. El parámetro`--stream` se puede usar para transmitir registros a la consola y bloquear más comandos.

## <a name="overriding-values-on-create-or-update"></a>Invalidación de valores al crear o actualizar

Los valores de especificación de trabajo YAML se pueden invalidar mediante `--set` al crear o actualizar un trabajo. Por ejemplo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_set":::

## <a name="job-names"></a>Nombres de los trabajos

La mayoría de los comandos `az ml job`, excepto`create` y `list` requieren `--name/-n`, que es el nombre de un trabajo o "Id. de ejecución" en Studio. No debe establecer directamente la propiedad`name`de un trabajo durante la creación, ya que debe ser única por área de trabajo. Azure Machine Learning genera un GUID aleatorio para el nombre del trabajo si no se establece, que se puede obtener a partir de la salida de la creación del trabajo en la CLI o copiando la propiedad "Id. de ejecución" en las API de Studio y MLflow.

Para automatizar trabajos en scripts y flujos de CI/CD, puede capturar el nombre de un trabajo cuando se crea consultando y quitando la salida agregando `--query name -o tsv`. Los detalles variarán según el shell, pero para Bash:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_name":::

A continuación, utilice `$run_id` en comandos posteriores como`update`, `show`, o `stream`:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_show":::

## <a name="organize-jobs"></a>Organizar los trabajos

Para organizar los trabajos, puede establecer un nombre para mostrar, un nombre de experimento, una descripción y etiquetas. Las descripciones admiten la sintaxis de Markdown en Studio. Estas propiedades se pueden modificar después de crear un trabajo. Un ejemplo completo:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

Puede ejecutar este trabajo, donde estas propiedades estarán visibles inmediatamente en Studio:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org":::

Puede utilizar `--set` para actualizar los valores que se pueden modificar después de crear el trabajo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org_set":::

## <a name="environment-variables"></a>Variables de entorno

Puede establecer variables de entorno para utilizarlas en el trabajo:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

Puede ejecutar este trabajo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_env_var":::

> [!WARNING]
> Debería utilizar `inputs` para parametrizar los argumentos en `command`. Consulte [entradas y salidas](#inputs-and-outputs).

## <a name="track-models-and-source-code"></a>Seguimiento de modelos y código fuente

Los modelos de aprendizaje automático de producción deben ser auditables (si no se pueden reproducir). Es fundamental realizar un seguimiento del código fuente de un modelo determinado. Azure Machine Learning toma una instantánea del código fuente y la mantiene con el trabajo. Además, el repositorio de origen y la confirmación se mantienen si está ejecutando trabajos desde un repositorio Git.

> [!TIP]
> Si sigue y ejecuta desde el repositorio de ejemplos, puede ver el repositorio de origen y confirmar en Studio en cualquiera de los trabajos ejecutados hasta ahora.

Puede especificar la `code.local_path`clave en un trabajo con el valor como ruta de acceso a un directorio de código fuente. Se toma y carga una instantánea del directorio con el trabajo. El contenido del directorio está disponible directamente desde el directorio de trabajo del trabajo.

> [!WARNING]
> El código fuente no debe incluir entradas de datos grandes para el entrenamiento del modelo. En su lugar, [utilice las entradas de datos](#data-inputs). Puede usar un `.gitignore`archivo en el directorio de código fuente para excluir archivos de la instantánea. Los límites para el tamaño de instantánea son 300 MB o 2000 archivos.

Echemos un vistazo a un trabajo que especifica código:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-mlflow.yml":::

El script de Python está en el directorio de código fuente local. A continuación, el comando `python` invoca para ejecutar el script. S puede aplicar el mismo patrón a otros lenguajes de programación.

> [!WARNING]
> La familia de trabajos "hello" que se muestra en este artículo tiene fines de demostración y no sigue necesariamente los procedimientos recomendados. No se recomienda usar `&&`o similar para ejecutar muchos comandos en una secuencia; en su lugar, considere la posibilidad de escribir los comandos en un archivo de script en el directorio de código fuente e invocar el script en su `command`. No se recomienda instalar dependencias en `command`, como se muestra anteriormente a través de `pip install`; en su lugar, todas las dependencias de trabajo deben especificarse como parte del entorno. Consulte [Cómo administrar de entornos con la CLI (v2) ](how-to-manage-environments-v2.md)para obtener información adicional.

### <a name="model-tracking-with-mlflow"></a>Seguimiento de modelos con MLflow

Al iterar en modelos, los científicos de datos deben poder realizar un seguimiento de los parámetros del modelo y las métricas de entrenamiento. Azure Machine Learning se integra con el seguimiento de MLflow para habilitar el registro de modelos, artefactos, métricas y parámetros en un trabajo. Para usar MLflow en los scripts de Python, agregue y `import mlflow` llame a `mlflow.log_*`o las API `mlflow.autolog()`en el código de entrenamiento.

> [!WARNING]
> Los paquetes `mlflow`y `azureml-mlflow`deben instalarse en el entorno de Python para las características de seguimiento de MLflow.

> [!TIP]
> La llamada `mlflow.autolog()` es compatible con muchos marcos populares y se encarga de la mayoría del registro.

Echemos un vistazo al script de Python invocado en el trabajo anterior que usa `mlflow`para registrar un parámetro, una métrica y un artefacto:

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-mlflow.py":::

Puede ejecutar este trabajo en la nube a través de Azure Machine Learning, donde se realiza un seguimiento y se puede auditar:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_mlflow":::

### <a name="query-metrics-with-mlflow"></a>Consulta de métricas con MLflow

Después de ejecutar trabajos, es posible que quiera consultar los resultados de ejecución de los trabajos y sus métricas registradas. Python es más adecuado para esta tarea que una CLI. Puede consultar las ejecuciones y sus métricas a través de `mlflow` y cargar en objetos conocidos como Dataframes de Pandas para su análisis.

En primer lugar, recupere el URI de seguimiento de MLflow para el área de trabajo Azure Machine Learning:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="mlflow_uri":::

Use la salida de este comando en `mlflow.set_tracking_uri(<YOUR_TRACKING_URI>)`desde un entorno de Python con MLflow importado. Las llamadas de MLflow ahora se corresponderán con los trabajos del área de trabajo de Azure Machine Learning.

## <a name="inputs-and-outputs"></a>Entradas y salidas

Los trabajos suelen tener entradas y salidas. Las entradas pueden ser parámetros de modelo, que se pueden volver a calcular para la optimización de hiperparámetros, o entradas de datos en la nube que se montan o descargan en el destino de proceso. Las salidas (omitiendo las métricas) son artefactos que se pueden escribir o copiar en las salidas predeterminadas o en una salida de datos con nombre.

### <a name="literal-inputs"></a>Entradas literales

Las entradas literales se resuelven directamente en el comando. Puede modificar nuestro trabajo "hola mundo" para usar entradas literales:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

Puede ejecutar este trabajo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input":::

Puede usar `--set` para invalidar las entradas:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input_set":::

Las entradas literales a los trabajos se [pueden convertir en entradas de espacio de búsqueda ](#search-space-inputs)para realizar barridos de hiperparámetros en el entrenamiento del modelo.

### <a name="search-space-inputs"></a>Entradas de espacio de búsqueda

Para un trabajo de barrido, puede especificar un espacio de búsqueda para las entradas literales entre las que elegir. Para obtener toda la gama de opciones para las entradas de espacio de búsqueda, consulte la [referencia de sintaxis de YAML del trabajo de barrido](reference-yaml-job-sweep.md).

> [!WARNING]
> Los trabajos de barrido no se admiten actualmente en los trabajos de canalización.

Vamos a demostrar el concepto con un script de Python simple que toma argumentos y registra una métrica aleatoria:

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-sweep.py":::

Y crear un trabajo de barrido correspondiente:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

Y ejecutarlo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_sweep":::

### <a name="data-inputs"></a>Entradas de datos

Las entradas de datos se resuelven en una ruta de acceso en el sistema de archivos local del proceso de trabajo. Vamos a demostrar con el conjunto de datos iris clásico, que se hospeda públicamente en un contenedor de blobs en `https://azuremlexamples.blob.core.windows.net/datasets/iris.csv`.

Puede crear un script de Python que tome la ruta de acceso al archivo CSV iris como argumento, lo lea en un dataframe, imprima las cinco primeras líneas y lo guarde en el directorio `outputs`.

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-iris.py":::

Se pueden especificar entradas de URI de almacenamiento de Azure, que montarán o descargarán datos en el sistema de archivos local. Puede especificar un único archivo:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

Y ejecute:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_file":::

O bien especifique una carpeta completa:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

Y ejecute:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_folder":::

#### <a name="private-data"></a>Datos privados

Para los datos privados de Azure Blob Storage o Azure Data Lake Storage conectados a Azure Machine Learning a través de un almacén de datos, puede usar las URI de Azure Machine Learning del formato `azureml://datastores/<DATASTORE_NAME>/paths/<PATH_TO_DATA>`para introducir los datos. Por ejemplo, si carga iris CSV en un directorio denominado `/example-data/`en el contenedor de blobs correspondiente al almacén de datos denominado `workspaceblobstore`, puede modificar un trabajo anterior para usar el archivo en el almacén de datos:

> [!WARNING]
> Si no ha copiado iris CSV en la misma ubicación de `workspaceblobstore`, se producirá un error al ejecutar estos trabajos.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

O todo el directorio:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

### <a name="default-outputs"></a>Salidas predeterminadas

Los directorios `./outputs` y `./logs` reciben un tratamiento especial por parte de Azure Machine Learning. Si escribe archivos en estos directorios durante el trabajo, dichos archivos se cargarán en el historial de ejecución del trabajo, con el fin de que pueda seguir accediendo a ellos una vez completado el trabajo. La carpeta `./outputs` se carga al final del trabajo, mientras que los archivos escritos en `./logs` se cargan en tiempo real. Use la última opción si desea hacer streaming de los registros durante el trabajo, como por ejemplo los registros de TensorBoard.

Puede modificar el trabajo "hola mundo" para que se pueda generar en un archivo en el directorio de salidas predeterminado en lugar de imprimir en `stdout`:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

Puede ejecutar este trabajo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output":::

Y descargue los registros, donde `helloworld.txt` estarán presentes en el directorio`<RUN_ID>/outputs/`:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output_download":::

### <a name="data-outputs"></a>Salidas de datos

Puede especificar salidas de datos con nombre. Esto creará un directorio en el almacén de datos predeterminado que se montará como lectura o escritura de forma predeterminada.

Puede modificar el trabajo "hola mundo" anterior para escribir en una salida de datos con nombre:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="hello-pipelines"></a>Canalizaciones «Hola»

Los trabajos de canalización pueden ejecutar varios trabajos en paralelo o en secuencia. Si hay dependencias de entrada y salida entre los pasos de una canalización, el paso dependiente se ejecutará una vez completado el otro.

Puede dividir un trabajo de "hola mundo" en dos trabajos:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

Y ejecutarlo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline":::

Los trabajos «hola» y «mundo» respectivamente se ejecutarán en paralelo si el destino de proceso tiene los recursos disponibles para hacerlo.

Para pasar datos entre los pasos de una canalización, defina una salida de datos en el trabajo «hola» y una entrada correspondiente en el trabajo «mundo», que hace referencia a la salida del anterior:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

Y ejecutarlo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_io":::

Esta vez, el trabajo «mundo» se ejecutará después de que se complete el trabajo «hola».

Para evitar la duplicación de la configuración común entre los trabajos de una canalización, puede establecerlos fuera de los trabajos:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

Puede ejecutar este trabajo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_settings":::

La configuración correspondiente en un trabajo individual invalidará la configuración común de un trabajo de canalización. Los conceptos hasta ahora se pueden combinar en un trabajo de canalización de tres pasos con los trabajos «A», «B» y «C». El trabajo «C» tiene una dependencia de datos en el trabajo «B», mientras que el trabajo «A» se puede ejecutar de forma independiente. El trabajo «A» también usará un entorno establecido individualmente y enlazará una de sus entradas a una entrada de trabajo de canalización de nivel superior:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

Puede ejecutar este trabajo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_abc":::

## <a name="train-a-model"></a>Entrenamiento de un modelo

En este momento, todavía no se ha entrenado un modelo. Vamos a agregar código `sklearn`a un script de Python con seguimiento de MLflow para entrenar un modelo en iris CSV:

:::code language="python" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/src/main.py":::

El marco scikit-learn es compatible con MLflow para el registro automático, por lo que una sola llamada `mlflow.autolog()`en el script registrará todos los parámetros del modelo, las métricas de entrenamiento, los artefactos del modelo y algunos artefactos adicionales (en este caso, una imagen de matriz de confusión).

Para ejecutar esto en la nube, especifique como un trabajo:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

Y ejecutarlo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_iris":::

Para registrar un modelo, puede descargar las salidas y crear un modelo desde el directorio local:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_download_register_model":::

## <a name="sweep-hyperparameters"></a>Hiperparámetros de barrido

Puede modificar el trabajo previo para barrer los hiperparámetros:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

Y ejecutarlo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_sweep":::

> [!TIP]
> Compruebe la pestaña «Ejecuciones secundarias» en Studio para supervisar el progreso y ver los gráficos de parámetros.

Para obtener más opciones de barrido, consulte la [referencia de sintaxis DE YAML del trabajo de barrido](reference-yaml-job-sweep.md).

## <a name="distributed-training"></a>Entrenamiento distribuido

Azure Machine Learning admite el entrenamiento distribuido basado en PyTorch, TensorFlow y MPI. Consulte la sección [distribuida de la referencia de sintaxis DE YAML](reference-yaml-job-command.md#distribution-configurations) del trabajo de comandos para obtener más información.

Por ejemplo, puede entrenar una red neuronal convolucional (CNN) en el conjunto de datos CIFAR-10 mediante PyTorch distribuido. El script completo está [disponible en el repositorio de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step/pytorch/cifar-distributed).

El conjunto de datos CIFAR-10 en `torchvision` espera como entrada un directorio que contiene el directorio `cifar-10-batches-py`. Puede descargar el origen comprimido y extraerlo en un directorio local:

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="download_untar_cifar":::

A continuación, cree un conjunto de datos de Azure Machine Learning desde el directorio local, que se cargará en el almacén de datos predeterminado:

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="create_cifar":::

Opcionalmente, quite el archivo y el directorio local:

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="cleanup_cifar":::

Se puede hacer referencia a conjuntos de datos (solo archivo) en un trabajo mediante la `dataset`clave de una entrada de datos. El formato es `azureml:<DATASET_NAME>:<DATASET_VERSION>`, por lo que para el conjunto de datos CIFAR-10 que acaba de crear, es `azureml:cifar-10-example:1`.

Con el conjunto de datos en su lugar, puede crear un trabajo de PyTorch distribuido para entrenar nuestro modelo:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

Y ejecutarlo:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

## <a name="build-a-training-pipeline"></a>Creación de una canalización de entrenamiento

El ejemplo CIFAR-10 anterior se traduce bien en un trabajo de canalización. El trabajo anterior se puede dividir en tres trabajos para la orquestación en una canalización:

- «get-data» para ejecutar un script de Bash para descargar y extraer `cifar-10-batches-py`
- «train-model» para tomar los datos y entrenar un modelo con PyTorch distribuido
- «eval-model» para tomar los datos y el modelo entrenado y evaluar la precisión

Tanto «train-model» como «eval-model» tendrán una dependencia en la salida del trabajo «get-data». Además, «eval-model» tendrá una dependencia en la salida del trabajo «train-model». Por lo tanto, los tres trabajos se ejecutarán secuencialmente.

Puede organizar estos tres trabajos dentro de un trabajo de canalización:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

Y ejecute:

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pipeline_cifar":::

Las canalizaciones también se pueden escribir mediante componentes reutilizables. Para obtener información adicional, consulte [Creación y ejecución de canalizaciones de aprendizaje automático mediante componentes con la CLI de Azure Machine Learning (versión preliminar)](how-to-create-component-pipelines-cli.md).

## <a name="next-steps"></a>Pasos siguientes

- [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md)

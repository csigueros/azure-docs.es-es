---
title: Esquema YAML del trabajo de comando de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del trabajo de comando de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ecfd6d57d7f978e64bed0003253a05941effa910
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060971"
---
# <a name="cli-v2-command-job-yaml-schema"></a>Esquema YAML del trabajo de comando de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | const | Tipo de trabajo. | `command` | `command` |
| `name` | string | Nombre del trabajo. Debe ser único en todos los trabajos del área de trabajo. Si se omite, Azure Machine Learning genera automáticamente un GUID para el nombre. | | |
| `display_name` | string | Nombre para mostrar del trabajo en la interfaz de usuario de Studio. Puede no ser único dentro del área de trabajo. Si se omite, Azure Machine Learning genera automáticamente un identificador adjetivo-sustantivo legible para el nombre para mostrar. | | |
| `experiment_name` | string | Nombre del experimento en el que se va a organizar el trabajo. El registro de ejecución de cada trabajo se organizará en el experimento correspondiente en la pestaña "Experiments" de Studio. Si se omite, Azure Machine Learning tomará como valor predeterminado del nombre del directorio de trabajo en que se creó el trabajo. | | |
| `description` | string | Descripción del trabajo. | | |
| `tags` | object | Diccionario de etiquetas para el trabajo. | | |
| `command` | string | **Obligatorio.** El comando que se debe ejecutar. | | |
| `code.local_path` | string | Ruta de acceso local al directorio de código fuente que se va a cargar y usar para el trabajo. | | |
| `environment` | cadena u objeto | **Obligatorio.** El entorno que se usará para el trabajo. Puede ser una referencia a un entorno con versiones existente en el área de trabajo o una especificación de entorno en línea. <br><br> Para hacer referencia a un entorno existente, use la sintaxis `azureml:<environment_name>:<environment_version>`. <br><br> Para definir un entorno en línea, siga el [Esquema de entorno](reference-yaml-environment.md#yaml-syntax). Excluya las propiedades `name` y `version`, ya que no se admiten para entornos en línea. | | |
| `environment_variables` | object | Diccionario de pares de nombre-valor de variables de entorno que se establecerán en el proceso donde se ejecuta el comando. | | |
| `distribution` | object | La configuración de distribución para escenarios de entrenamiento distribuido. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration) o [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `compute` | string | Nombre del destino de proceso en el que se ejecutará el trabajo. Puede ser una referencia a un proceso existente en el área de trabajo (mediante la sintaxis `azureml:<compute_name>`) o `local` para designar la ejecución local. | | `local` |
| `resources.instance_count` | integer | El número de nodos que se usarán para el trabajo. | | `1` |
| `limits.timeout` | integer | Tiempo máximo en segundos durante el que se puede ejecutar el trabajo. Una vez alcanzado este límite, el sistema cancelará el trabajo. | | |
| `inputs` | object | Diccionario de entradas para el trabajo. La clave es un nombre para la entrada dentro del contexto del trabajo y el valor es el valor de entrada. <br><br> Se puede hacer referencia a las entradas en `command` mediante la expresión `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | number, integer, boolean, string u object | Un valor literal (de tipo number, integer, boolean o string) [JobInputUri](#jobinputuri) o [JobInputDataset](#jobinputdataset). | | |
| `outputs` | object | Diccionario de configuraciones de salida del trabajo. La clave es un nombre para la salida dentro del contexto del trabajo y el valor es la configuración de salida. <br><br> Se puede hacer referencia a las salidas en `command` mediante la expresión `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Puede especificar un elemento `mode` opcional o dejar el objeto vacío. Para cada salida con nombre especificada en el diccionario `outputs`, Azure Machine Learning generará automáticamente una ubicación de salida. | |
| `outputs.<output_name>.mode` | string | Modo de cómo se entregarán los archivos de salida al almacenamiento de destino. Para el modo de montaje de lectura y escritura, el directorio de salida será un directorio montado. En el modo de carga, los archivos escritos en el directorio de salida se cargan al final del trabajo. | `rw_mount`, `upload` | `rw_mount` |

### <a name="distribution-configurations"></a>Configuraciones de distribución

#### <a name="mpiconfiguration"></a>MpiConfiguration

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** Tipo de distribución.  | `mpi` |
| `process_count_per_instance` | integer | **Obligatorio.** El número de procesos por nodo que se inician para el trabajo.  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatorio.** Tipo de distribución.  | `pytorch` | |
| `process_count_per_instance` | integer | El número de procesos por nodo que se inician para el trabajo. | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatorio.** Tipo de distribución.  | `tensorflow` |
| `worker_count` | integer | El número de trabajos que se inician para el trabajo. | | Tiene como valor predeterminado `resources.instance_count`. |
| `parameter_server_count` | integer | El número de servidores de parámetros que se inician para el trabajo. | | `0` |

### <a name="job-inputs"></a>Entradas de trabajo

#### <a name="jobinputuri"></a>JobInputUri

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | URI para un único archivo que se usará como entrada. Los tipos de URI admitidos son `azureml`, `https`, `wasbs`, `abfss`, `adl`. Consulte [Sintaxis principal de YAML](reference-yaml-core-syntax.md) para más información sobre cómo usar el formato de URI `azureml://`. **Se requiere `file` o `folder`.**  | | |
| `folder` | string | URI para una carpeta que se usará como entrada. Los tipos de URI admitidos son `azureml`, `wasbs`, `abfss`, `adl`. Consulte [Sintaxis principal de YAML](reference-yaml-core-syntax.md) para más información sobre cómo usar el formato de URI `azureml://`. **Se requiere `file` o `folder`.**   | | |
| `mode` | string | Modo de cómo se deben entregar los datos al destino de proceso. Para el montaje de solo lectura y el montaje de lectura y escritura, los datos se consumirán como una ruta de acceso de montaje. Una carpeta se montará como una carpeta y un archivo se montará como un archivo. Para el modo de descarga, los datos se consumirán como una ruta de acceso descargada. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | cadena u objeto | **Obligatorio.** Un conjunto de datos que se usará como entrada. Puede ser una referencia a un conjunto de datos con versiones existente en el área de trabajo o una especificación de conjunto de datos en línea. <br><br> Para hacer referencia a un conjunto de datos existente, use la sintaxis `azureml:<dataset_name>:<dataset_version>`. <br><br> Para definir un conjunto de datos en línea, siga el [Esquema de conjunto de datos](reference-yaml-dataset.md#yaml-syntax). Excluya las propiedades `name` y `version`, ya que no se admiten para conjuntos de datos en línea. | | |
| `mode` | string | Modo de cómo se debe entregar el conjunto de datos al destino de proceso. Para el montaje de solo lectura, el conjunto de datos se consumirá como una ruta de acceso de montaje. Una carpeta se montará como una carpeta y un archivo se montará como una carpeta primaria. Para el modo de descarga, el conjunto de datos se consumirá como una ruta de acceso descargada. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Comentarios

El comando `az ml job` se puede usar para administrar los trabajos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). A continuación, se muestran varios.

## <a name="yaml-hello-world"></a>YAML: hola mundo

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

## <a name="yaml-display-name-experiment-name-description-and-tags"></a>YAML: nombre para mostrar, nombre del experimento, descripción y etiquetas

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

## <a name="yaml-environment-variables"></a>YAML: variables de entorno

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

## <a name="yaml-source-code"></a>YAML: código fuente

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-code.yml":::

## <a name="yaml-literal-inputs"></a>YAML: entradas literales

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

## <a name="yaml-write-to-default-outputs"></a>YAML: escritura en salidas predeterminadas

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

## <a name="yaml-write-to-named-data-output"></a>YAML: escritura en la salida de datos con nombre

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="yaml-datastore-uri-file-input"></a>YAML: entrada del archivo de URI del almacén de datos

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

## <a name="yaml-datastore-uri-folder-input"></a>YAML: entrada de la carpeta de URI del almacén de datos

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

## <a name="yaml-uri-file-input"></a>YAML: entrada del archivo de URI

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

## <a name="yaml-uri-folder-input"></a>YAML: entrada de la carpeta de URI

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

## <a name="yaml-notebook-via-papermill"></a>YAML: cuaderno a través de Papermill

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-notebook.yml":::

## <a name="yaml-basic-python-model-training"></a>YAML: entrenamiento básico del modelo de Python

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

## <a name="yaml-basic-r-model-training-with-local-docker-build-context"></a>YAML: entrenamiento básico del modelo de R con contexto de compilación de Docker local

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/r/iris/job.yml":::

## <a name="yaml-distributed-pytorch"></a>YAML: PyTorch distribuido

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

## <a name="yaml-distributed-tensorflow"></a>YAML: TensorFlow distribuido

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed/job.yml":::

## <a name="yaml-distributed-mpi"></a>YAML: MPI distribuido

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed-horovod/job.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

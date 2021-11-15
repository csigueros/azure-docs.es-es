---
title: Esquema YAML del trabajo de canalización de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del trabajo de canalización de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 73d5c79ac3e425039820101102ac91b3e3362d8e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565914"
---
# <a name="cli-v2-pipeline-job-yaml-schema"></a>Esquema YAML del trabajo de canalización de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | const | **Obligatorio.** Tipo de trabajo. | `pipeline` | |
| `name` | string | Nombre del trabajo. Debe ser único en todos los trabajos del área de trabajo. Si se omite, Azure ML genera automáticamente un GUID para el nombre. | | |
| `display_name` | string | Nombre para mostrar del trabajo en la interfaz de usuario de Studio. Puede no ser único dentro del área de trabajo. Si se omite, Azure ML genera automáticamente un identificador adjetivo-sustantivo legible para el nombre para mostrar. | | |
| `experiment_name` | string | Nombre del experimento en el que se va a organizar el trabajo. El registro de ejecución de cada trabajo se organizará en el experimento correspondiente en la pestaña "Experiments" de Studio. Si se omite, Azure ML tomará como valor predeterminado el nombre del directorio de trabajo en que se creó el trabajo. | | |
| `description` | string | Descripción del trabajo. | | |
| `tags` | object | Diccionario de etiquetas para el trabajo. | | |
| `settings` | object | Configuración predeterminada del trabajo de canalización. Consulte [Atributos de la clave `settings`](#attributes-of-the-settings-key) para ver el conjunto de propiedades configurables. | | |
| `jobs` | object | **Obligatorio.** Diccionario del conjunto de trabajos individuales que se ejecutarán como pasos dentro de la canalización. Estos trabajos se consideran trabajos secundarios del trabajo de canalización primario. <br><br> La clave es el nombre del paso en el contexto del trabajo de canalización. Este nombre es diferente del nombre de trabajo único del trabajo secundario. El valor es la especificación del trabajo, que puede seguir el [esquema de trabajo del comando](reference-yaml-job-command.md#yaml-syntax) o el [esquema de trabajo del componente](reference-yaml-job-component.md#yaml-syntax). Actualmente, solo los trabajos de comandos y los trabajos de componentes que ejecutan componentes de comando se pueden ejecutar en una canalización. Las versiones posteriores tendrán compatibilidad con otros tipos de trabajo. | | |
| `inputs` | object | Diccionario de entradas para el trabajo de canalización. La clave es un nombre para la entrada dentro del contexto del trabajo y el valor es el valor de entrada. <br><br> Las entradas de un trabajo de paso individual de la canalización pueden hacer referencia a estas entradas de canalización mediante la expresión `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | número, entero, booleano, cadena u objeto | Un valor literal (de tipo número,entero, booleano o cadena) [JobInputUri](#jobinputuri) o [JobInputDataset](#jobinputdataset). | | |
| `outputs` | object | Diccionario de configuraciones de salida del trabajo de canalización. La clave es un nombre para la salida dentro del contexto del trabajo y el valor es la configuración de salida. <br><br> Las salidas de un trabajo de paso individual de la canalización pueden hacer referencia a estas salidas de canalización mediante la expresión `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Puede especificar un elemento `mode` opcional o dejar el objeto vacío. Para cada salida con nombre especificada en el diccionario `outputs`, Azure ML generará automáticamente una ubicación de salida basada en la siguiente ruta de acceso con plantillas: `{default-datastore}/azureml/{job-name}/{output-name}/`. Los usuarios podrán proporcionar una ubicación personalizada en una versión posterior. | |
| `outputs.<output_name>.mode` | string | Modo de cómo se entregarán los archivos de salida al almacenamiento de destino. Para el modo de montaje de lectura y escritura, el directorio de salida será un directorio montado. En el modo de carga, los archivos escritos en el directorio de salida se cargan al final del trabajo. | `rw_mount`, `upload` | `rw_mount` |
| `compute` | string | Nombre del destino de proceso predeterminado en el que se va a ejecutar un trabajo de paso si el paso individual no tiene especificado el proceso. Este valor debe ser una referencia a un proceso existente en el área de trabajo mediante la sintaxis `azureml:<compute-name>`. |

### <a name="attributes-of-the-settings-key"></a>Atributos de la clave `settings`

| Clave | Tipo | Descripción |
| --- | ---- | ----------- |
| `datastore` | string | Nombre del almacén de datos que se usará como almacén de datos predeterminado para el trabajo de canalización. Este valor debe ser una referencia a un almacén de datos existente en el área de trabajo mediante la sintaxis `azureml:<datastore-name>`. Las salidas definidas en la propiedad `outputs` del trabajo de canalización primario o los trabajos de paso secundario se almacenarán en este almacén de datos. Si se omite, las salidas se almacenarán en el almacén de datos de blobs del área de trabajo. |

### <a name="job-inputs"></a>Entradas de trabajo

#### <a name="jobinputuri"></a>JobInputUri

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | URI para un único archivo que se usará como entrada. Los tipos de URI admitidos son `azureml`, `https`, `wasbs`, `abfss`, `adl`. Para más información sobre cómo usar el formato de URI `azureml`, consulte [Sintaxis principal de YAML](reference-yaml-core-syntax.md). **Uno de estos elementos, `file` o `folder`, es obligatorio.**  | | |
| `folder` | string | URI para una carpeta que se usará como entrada. Los tipos de URI admitidos son `azureml`, `wasbs`, `abfss`, `adl`. Para más información sobre cómo usar el formato de URI `azureml`, consulte [Sintaxis principal de YAML](reference-yaml-core-syntax.md). **Uno de estos elementos, `file` o `folder`, es obligatorio.**   | | |
| `mode` | string | Modo de cómo se deben entregar los datos al destino de proceso. Para el montaje de solo lectura y el montaje de lectura y escritura, los datos se consumirán como una ruta de acceso de montaje. Una carpeta se montará como una carpeta y un archivo se montará como un archivo. Para el modo de descarga, los datos se consumirán como una ruta de acceso descargada. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | cadena u objeto | **Obligatorio.** Un conjunto de datos que se usará como entrada. Este valor puede ser una referencia a un conjunto de datos con versiones existente en el área de trabajo o una especificación de conjunto de datos en línea. <br><br> Para hacer referencia a un conjunto de datos existente, use la sintaxis `azureml:<dataset-name>:<dataset-version>`. <br><br> Para definir un conjunto de datos en línea, siga el [Esquema de conjunto de datos](reference-yaml-dataset.md#yaml-syntax). Excluya las propiedades `name` y `version`, ya que no se admiten para conjuntos de datos en línea. | | |
| `mode` | string | Modo de cómo se debe entregar el conjunto de datos al destino de proceso. Para el montaje de solo lectura, el conjunto de datos se consumirá como una ruta de acceso de montaje. Una carpeta se montará como una carpeta y un archivo se montará como una carpeta primaria. Para el modo de descarga, el conjunto de datos se consumirá como una ruta de acceso descargada. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Comentarios

Los comandos `az ml job` se pueden usar para administrar los trabajos de canalización de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). A continuación se muestran varios.

## <a name="yaml-hello-pipeline"></a>YAML: canalización hello

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline.yml":::

## <a name="yaml-inputoutput-dependency"></a>YAML: dependencia de entrada/salida

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline-io.yml":::

## <a name="yaml-common-pipeline-job-settings"></a>YAML: configuración común del trabajo de canalización

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline-settings.yml":::

## <a name="yaml-top-level-input-and-overriding-common-pipeline-job-settings"></a>YAML: entrada de nivel superior e invalidación de la configuración común del trabajo de canalización

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-pipeline-abc.yml":::

## <a name="yaml-model-training-pipeline"></a>YAML: canalización de entrenamiento de modelos

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/pipelines/cifar-10/job.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

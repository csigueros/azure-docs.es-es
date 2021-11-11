---
title: Sintaxis básica de YAML de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Introducción a la sintaxis básica de YAML de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 943de3f7327c02e4bdb20bb364f530685bce2965
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565928"
---
# <a name="cli-v2-core-yaml-syntax"></a>Sintaxis básica de YAML de la CLI (v2)

Cada entidad de Azure Machine Learning tiene una representación de YAML esquematizada. Puede crear una nueva entidad a partir de un archivo de configuración YAML con una extensión `.yml` o `.yaml`.

En este artículo se proporciona una introducción a conceptos de la sintaxis básica que va a encontrar al configurar estos archivos YAML.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="referencing-an-azure-ml-entity"></a>Referencia a una entidad de Azure ML

Azure ML proporciona una sintaxis de referencia (que consta de un formato abreviado y largo) para hacer referencia a una entidad de Azure ML existente al configurar un archivo YAML. Por ejemplo, puede hacer referencia a un entorno registrado existente del área de trabajo para usarlo en el entorno de un trabajo.

### <a name="shorthand"></a>Abreviatura

La sintaxis abreviada consta de lo siguiente:

* Activos: `azureml:<asset-name>:<asset-version>`
* Recursos: `azureml:<resource-name>`

Azure ML resuelve esta referencia al activo o recurso especificado del área de trabajo.

### <a name="longhand"></a>Formato largo

La sintaxis larga consta del prefijo `azureml:` más el identificador de recurso de ARM de la entidad:

```
azureml:/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/environments/<environment-name>/versions/<environment-version>
```

## <a name="azure-ml-data-reference-uri"></a>URI de referencia de datos de Azure ML

Azure ML ofrece un cómodo formato de URI de referencia de datos para apuntar a datos de un servicio de almacenamiento de Azure. Se puede usar en escenarios en los que es necesario especificar una ubicación de almacenamiento en la nube en el archivo YAML, como al crear un modelo de Azure ML a partir de archivos en el almacenamiento, o al apuntar a datos para pasarlos como entrada para un trabajo.

Para usar este formato de URI de datos, el servicio de almacenamiento al que se quiere hacer referencia primero debe registrarse como almacén de datos del área de trabajo. Azure ML controla el acceso a los datos con las credenciales proporcionadas durante la creación del almacén de datos.

El formato consta de un almacén de datos del área de trabajo actual y la ruta de acceso en el almacén de datos al archivo o la carpeta a los que se quiere apuntar:

```
azureml://datastores/<datastore-name>/paths/<path-on-datastore>/
```

Por ejemplo:

* `azureml://datastores/workspaceblobstore/paths/example-data/`
* `azureml://datastores/workspaceblobstore/paths/example-data/iris.csv`

Además del URI de referencia de datos de Azure ML, Azure ML también admite los siguientes protocolos de URI de almacenamiento directo: `https`, `wasbs`, `abfss` y `adl`, así como URI públicos `http` y `https`.

## <a name="expression-syntax-for-configuring-azure-ml-jobs-and-components"></a>Sintaxis de expresión para configurar trabajos y componentes de Azure ML

Los archivos YAML de trabajos y componentes v2 permiten el uso de expresiones para enlazar a contextos de distintos escenarios. El caso de uso básico es el empleo de una expresión para un valor que podría no conocerse en el momento de crear la configuración, pero que debe resolverse en tiempo de ejecución.

Use la siguiente sintaxis para indicar a Azure ML que evalúe una expresión en lugar de tratarla como una cadena:

`${{ <expression> }}`

Los escenarios admitidos se tratan a continuación.

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-job"></a>Parametrización de `command` con los contextos `inputs` y `outputs` de un trabajo

Puede especificar valores literales, rutas de acceso URI y conjuntos de datos de Azure ML como entradas para un trabajo. Luego `command` se puede parametrizar con referencias a esas entradas mediante la sintaxis `${{inputs.<input-name>}}`. Las referencias a entradas literales se resuelven en el valor literal en tiempo de ejecución, mientras que las referencias a entradas de URI de datos o del conjunto de datos de Azure ML se resuelven en la ruta de acceso de descarga o la ruta de acceso de montaje (según el valor `mode` especificado).

Del mismo modo, en `command` también se puede hacer referencia a las salidas para el trabajo. Por cada salida con nombre especificada en el diccionario `outputs`, Azure ML genera automáticamente una ubicación de salida en el almacén de datos predeterminado donde se pueden escribir archivos. La ubicación de salida de cada salida con nombre se basa en la siguiente ruta de acceso con plantilla: `<default-datastore>/azureml/<job-name>/<output-name>/`. La parametrización de `command` con la sintaxis `${{outputs.<output-name>}}` resuelve esa referencia en la ruta de acceso generada automáticamente, de modo que el script puede escribir archivos en esa ubicación desde el trabajo.

En el ejemplo siguiente de un archivo YAML de trabajo de comandos, se parametriza `command` con dos entradas, una entrada literal y una entrada de URI, y una salida. En tiempo de ejecución, la expresión `${{inputs.learning_rate}}` se resuelve en `0.01` y la expresión `${{inputs.iris}}` se resuelve en la ruta de acceso de descarga del archivo `iris.csv`. `${{outputs.model_dir}}` se resuelve en la ruta de acceso de montaje de la ubicación de salida generada automáticamente.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
compute: azureml:cpu-cluster
inputs:
  learning_rate: 0.01
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
outputs:
  model_dir:
```

### <a name="parameterizing-the-command-with-the-search_space-context-of-a-sweep-job"></a>Parametrización de `command` con el contexto `search_space` de un trabajo de barrido

Esta sintaxis de expresión también se usa para realizar el ajuste de hiperparámetros mediante un trabajo de barrido, ya que los valores reales de los hiperparámetros no se conocen durante el tiempo de creación del trabajo. Al ejecutar un trabajo de barrido, Azure ML selecciona los valores de los hiperparámetros de cada evaluación en función de `search_space`. Para acceder a esos valores en el script de entrenamiento, debe pasarlos mediante los argumentos de la línea de comandos del script. Para ello, use la sintaxis `${{search_space.<hyperparameter>}}` de `trial.command`.

En el ejemplo siguiente de un archivo YAML de trabajo de barrido, las referencias `${{search_space.learning_rate}}` y `${{search_space.boosting}}` de `trial.command` se resuelven en los valores de los hiperparámetros reales seleccionados para cada evaluación al enviar el trabajo de evaluación para su ejecución.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json
type: sweep
sampling_algorithm: random
search_space:
  learning_rate:
    type: uniform
    min_value: 0.01
    max_value: 0.9
  boosting:
    type: choice
    values: ["gbdt", "dart"]
objective:
  goal: minimize
  primary_metric: test-multi_logloss
trial:
  code: 
    local_path: src 
  command: >-
    python train.py 
    --training-data ${{inputs.iris}}
    --lr ${{search_space.learning_rate}}
    --boosting ${{search_space.boosting}}
  environment: azureml:AzureML-Minimal:1
inputs:
  iris:
    file: https://azuremlexamples.blob.core.windows.net/datasets/iris.csv
    mode: download
compute: azureml:cpu-cluster
```

### <a name="binding-inputs-and-outputs-between-steps-in-a-pipeline-job"></a>Enlace de entradas y salidas entre pasos de un trabajo de canalización

Las expresiones también se usan para enlazar entradas y salidas entre pasos de un trabajo de canalización. Por ejemplo, puede enlazar la entrada de un trabajo (trabajo n.º 2) de una canalización a la salida de otro trabajo (trabajo n.º 1). Este uso indica a Azure ML el flujo de dependencias del grafo de canalización, y el trabajo n.º 2 se ejecuta después del trabajo n.º 1, ya que la salida del trabajo n.º 1 es necesaria como entrada del trabajo n.º 2.

En un archivo YAML de trabajo de canalización, las secciones `inputs` y `outputs` de cada trabajo secundario se evalúan dentro del contexto principal (el trabajo de canalización de nivel superior). Por otro lado, `command` se resuelve en el contexto actual (el trabajo secundario).

Hay dos maneras de enlazar entradas y salidas en un trabajo de canalización:

**1) Enlazar a las entradas y salidas de nivel superior del trabajo de canalización**

Puede enlazar las entradas o salidas de un trabajo secundario a las entradas o salidas del trabajo de canalización principal de nivel superior mediante la sintaxis siguiente: `${{inputs.<input-name>}}` o `${{outputs.<output-name>}}`. Esta referencia se resuelve en el contexto principal, es decir, las entradas o salidas de nivel superior. 

En el ejemplo siguiente, la salida (`model_dir`) del paso `train` final está enlazada a la salida del trabajo de canalización de nivel superior mediante `${{outputs.trained_model}}`

**2) Enlazar a las entradas y salidas de otro trabajo secundario (paso)**

Para enlazar las entradas o salidas de un paso a las entradas o salidas de otro paso, use la sintaxis siguiente: `${{jobs.<step-name>.inputs.<input-name>}}` o `${{jobs.<step-name>.outputs.<outputs-name>}}`. De nuevo, esta referencia se resuelve en el contexto principal, por lo que el contexto comienza por `jobs.<step-name>`.

En el ejemplo siguiente, la entrada (`clean_data`) del paso `train` se enlaza a la salida (`prep_data`) del paso `prep` mediante `${{jobs.prep.outputs.prep_data}}`. Los datos preparados del paso `prep` se usan como datos de entrenamiento del paso `train`.

Por otro lado, las referencias de contexto en las propiedades `command` se resuelven en el contexto actual. Por ejemplo, la referencia `${{inputs.raw_data}}` de `command` del paso `prep` se resuelve en las entradas del contexto actual, que es el trabajo secundario `prep`. La búsqueda se realiza en `prep.inputs`, por lo que se debe definir allí una entrada de nombre `raw_data`.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json
type: pipeline
inputs:
outputs:
  trained_model:
jobs:
  prep:
    type: command
    inputs:
      raw_data:
        folder:
        mode: rw_mount
    outputs:
      prep_data: 
        mode: upload
    code:
      local_path: src/prep
    environment: azureml:AzureML-Minimal:1
    command: >-
      python prep.py 
      --raw-data ${{inputs.raw_data}} 
      --prep-data ${{outputs.prep_data}}
    compute: azureml:cpu-cluster
  train:
    type: command
    inputs: 
      clean_data: ${{jobs.prep.outputs.prep_data}}
    outputs:
      model_dir: $${{outputs.trained_model}}
    code: 
      local_path: src/train
    environment: azureml:AzureML-Minimal:1
    compute: azureml:gpu-cluster
    command: >-
      python train.py 
      --training-data ${{inputs.clean_data}} 
      --model-output ${{outputs.model_dir}}
```

### <a name="parameterizing-the-command-with-the-inputs-and-outputs-contexts-of-a-component"></a>Parametrización de `command` con los contextos `inputs` y `outputs` de un componente

De forma similar al valor `command` de un trabajo, el valor `command` de un componente también se puede parametrizar con referencias a los contextos `inputs` y `outputs`. En este caso, la referencia es a las entradas y salidas del componente. Cuando el componente se ejecuta en un trabajo, Azure ML resuelve esas referencias en los valores de entrada y salida en tiempo de ejecución del trabajo especificados para las entradas y salidas respectivas del componente. A continuación se muestra un ejemplo del uso de la sintaxis de contexto en una especificación de YAML del componente de comandos.

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json
type: command
code:
  local_path: ./src
command: python train.py --lr ${{inputs.learning_rate}} --training-data ${{inputs.iris}} --model-dir ${{outputs.model_dir}}
environment: azureml:AzureML-Minimal:1
inputs:
  learning_rate:
    type: number
    default: 0.01
    optional: true
  iris:
    type: path
outputs:
  model_dir:
    type: path
```

## <a name="next-steps"></a>Pasos siguientes

* [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
* [Entrenamiento de modelos con la CLI (v2)](how-to-train-cli.md)
* [Esquemas YAML de la CLI (v2)](reference-yaml-overview.md)

---
title: Esquema YAML del trabajo de barrido de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del trabajo de barrido de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 90a69fceb96067ad92cc4d68f8b3e0929d2622a6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135110"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>Esquema YAML del trabajo de barrido de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | const | **Obligatorio.** Tipo de trabajo. | `sweep` | `sweep` |
| `name` | string | Nombre del trabajo. Debe ser único en todos los trabajos del área de trabajo. Si se omite, Azure Machine Learning genera automáticamente un GUID para el nombre. | | |
| `display_name` | string | Nombre para mostrar del trabajo en la interfaz de usuario de Studio. Puede no ser único dentro del área de trabajo. Si se omite, Azure Machine Learning genera automáticamente un identificador adjetivo-sustantivo legible para el nombre para mostrar. | | |
| `experiment_name` | string | Nombre del experimento en el que se va a organizar el trabajo. El registro de ejecución de cada trabajo se organizará en el experimento correspondiente en la pestaña "Experiments" de Studio. Si se omite, Azure Machine Learning tomará como valor predeterminado del nombre del directorio de trabajo en que se creó el trabajo. | | |
| `description` | string | Descripción del trabajo. | | |
| `tags` | object | Diccionario de etiquetas para el trabajo. | | |
| `sampling_algorithm` | string | **Obligatorio.** El algoritmo de muestreo de hiperparámetros que se usará en `search_space`. | `random`, `grid`, `bayesian` | |
| `search_space` | object | **Obligatorio.** Diccionario del espacio de búsqueda de hiperparámetros. La clave es el nombre del hiperparámetro y el valor es la expresión del parámetro. <br><br> Se puede hacer referencia a los hiperparámetros en `trial.command` mediante la expresión `${{ search_space.<hyperparameter> }}`. | | |
| `search_space.<hyperparameter>` | object | Vea [Expresiones de parámetro](#parameter-expressions) para el conjunto de expresiones posibles que se usarán. | | |
| `objective.primary_metric` | string | **Obligatorio.** El nombre de la métrica principal notificada por cada trabajo de prueba. La métrica debe registrarse en el script de entrenamiento del usuario mediante `mlflow.log_metric()` con el mismo nombre de métrica correspondiente. | | |
| `objective.goal` | string | **Obligatorio.** El objetivo de la optimización de `objective.primary_metric`. | `maximize`, `minimize` | |
| `early_termination` | object | La directiva de terminación anticipada que se va a usar. Un trabajo de prueba se cancela cuando se cumplen los criterios de la directiva especificada. Si se omite, no se aplicará ninguna directiva de terminación anticipada. [BanditPolicy](#banditpolicy), [MedianStoppingPolicy](#medianstoppingpolicy) o [TruncationSelectionPolicy](#truncationselectionpolicy). | | |
| `limits` | object | Límites del trabajo de barrido. Consulte [Atributos de la clave `limits`](#attributes-of-the-limits-key). | | |
| `compute` | string | **Obligatorio.** Nombre del destino de proceso en el que se ejecutará el trabajo, utilizando la sintaxis `azureml:<compute_name>`. | | |
| `trial` | object | **Obligatorio.** La plantilla de trabajo para cada prueba. Cada trabajo de prueba se proporcionará con una combinación diferente de valores de hiperparámetros que el sistema muestrea de `search_space`. Consulte [Atributos de la clave `trial`](#attributes-of-the-trial-key). | | |
| `inputs` | object | Diccionario de entradas para el trabajo. La clave es un nombre para la entrada dentro del contexto del trabajo y el valor es el valor de entrada. <br><br> Se puede hacer referencia a las entradas en `trial.command` mediante la expresión `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | number, integer, boolean, string u object | Un valor literal (de tipo number, integer, boolean o string) [JobInputUri](#jobinputuri) o [JobInputDataset](#jobinputdataset). | | |
| `outputs` | object | Diccionario de configuraciones de salida del trabajo. La clave es un nombre para la salida dentro del contexto del trabajo y el valor es la configuración de salida. <br><br> Se puede hacer referencia a las salidas en `trial.command` mediante la expresión `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Puede especificar un elemento `mode` opcional o dejar el objeto vacío. Para cada salida con nombre especificada en el diccionario `outputs`, Azure Machine Learning generará automáticamente una ubicación de salida. | |
| `outputs.<output_name>.mode` | string | Modo de cómo se entregarán los archivos de salida al almacenamiento de destino. Para el modo de montaje de lectura y escritura, el directorio de salida será un directorio montado. En el modo de carga, los archivos escritos en el directorio de salida se cargan al final del trabajo. | `rw_mount`, `upload` | `rw_mount` |

### <a name="early-termination-policies"></a>Directivas de terminación anticipada

#### <a name="banditpolicy"></a>BanditPolicy

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatorio.** El tipo de directiva. | `bandit` | |
| `slack_factor` | number | La proporción usada para calcular la distancia permitida de la prueba con mejor rendimiento. **Se requiere `slack_factor` o `slack_amount`.** | | |
| `slack_amount` | number | La distancia absoluta permitida de la prueba con mejor rendimiento. **Se requiere `slack_factor` o `slack_amount`.** | | |
| `evaluation_interval` | integer | La frecuencia con la que se aplica la directiva. | | `1` |
| `delay_evaluation` | integer | El número de intervalos que se va a retrasar la primera evaluación de directiva. Si se especifica, la directiva se aplica a cada múltiplo de `evaluation_interval` que sea mayor que o igual a `delay_evaluation`. | | `0` |

#### <a name="medianstoppingpolicy"></a>MedianStoppingPolicy

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatorio.** El tipo de directiva. | `median_stopping` | |
| `evaluation_interval` | integer | La frecuencia con la que se aplica la directiva. | | `1` |
| `delay_evaluation` | integer | El número de intervalos que se va a retrasar la primera evaluación de directiva. Si se especifica, la directiva se aplica a cada múltiplo de `evaluation_interval` que sea mayor que o igual a `delay_evaluation`. | | `0` |

#### <a name="truncationselectionpolicy"></a>TruncationSelectionPolicy

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatorio.** El tipo de directiva. | `truncation_selection` | |
| `truncation_percentage` | integer | **Obligatorio.** El porcentaje de trabajos de prueba que se cancelarán en cada intervalo de evaluación. | | |
| `evaluation_interval` | integer | La frecuencia con la que se aplica la directiva. | | `1` |
| `delay_evaluation` | integer | El número de intervalos que se va a retrasar la primera evaluación de directiva. Si se especifica, la directiva se aplica a cada múltiplo de `evaluation_interval` que sea mayor que o igual a `delay_evaluation`. | | `0` |

### <a name="parameter-expressions"></a>Expresiones de parámetro

#### <a name="choice"></a>choice

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `choice` |
| `values` | array | **Obligatorio.** La lista de valores discretos entre los que se puede elegir. | |

#### <a name="randint"></a>randint

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `randint` |
| `upper` | integer | **Obligatorio.** El límite superior exclusivo para el intervalo de enteros. | |

#### <a name="qlognormal-qnormal"></a>qlognormal, qnormal

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `qlognormal`, `qnormal` |
| `mu` | number | **Obligatorio.** La media de la distribución normal. | |
| `sigma` | number | **Obligatorio.** La desviación estándar de la distribución normal. | |
| `q` | integer | **Obligatorio.** El factor de suavizado. | |

#### <a name="qloguniform-quniform"></a>qloguniform, quniform

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `qloguniform`, `quniform` |
| `min_value` | number | **Obligatorio.** El valor mínimo del intervalo (inclusivo). | |
| `max_value` | number | **Obligatorio.** El valor máximo del intervalo (inclusivo). | |
| `q` | integer | **Obligatorio.** El factor de suavizado. | |

#### <a name="lognormal-normal"></a>lognormal, normal

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `lognormal`, `normal` |
| `mu` | number | **Obligatorio.** La media de la distribución normal. | |
| `sigma` | number | **Obligatorio.** La desviación estándar de la distribución normal. | |

#### <a name="loguniform"></a>loguniform

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `loguniform` |
| `min_value` | number | **Obligatorio.** El valor mínimo del intervalo será `exp(min_value)` (inclusivo). | |
| `max_value` | number | **Obligatorio.** El valor máximo del intervalo será `exp(max_value)` (inclusivo). | |

#### <a name="uniform"></a>uniforme

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** El tipo de expresión. | `uniform` |
| `min_value` | number | **Obligatorio.** El valor mínimo del intervalo (inclusivo). | |
| `max_value` | number | **Obligatorio.** El valor máximo del intervalo (inclusivo). | |

### <a name="attributes-of-the-limits-key"></a>Atributos de la clave `limits`

| Clave | Tipo | Descripción | Valor predeterminado |
| --- | ---- | ----------- | ------------- |
| `max_total_trials` | integer | Tiempo máximo en segundos durante el que se puede ejecutar el trabajo. Una vez alcanzado este límite, el sistema cancelará el trabajo. | `1000` |
| `max_concurrent_trials` | integer | | Tiene como valor predeterminado `max_total_trials`. |
| `timeout` | integer | El tiempo máximo en minutos durante el que se puede ejecutar todo el trabajo de barrido. Una vez alcanzado este límite, el sistema cancelará el trabajo de barrido, incluidas todas sus pruebas. | `10080` |
| `trial_timeout` | integer | El tiempo máximo en segundos durante el que se puede ejecutar cada trabajo de prueba. Una vez alcanzado este límite, el sistema cancelará la prueba. | |

### <a name="attributes-of-the-trial-key"></a>Atributos de la clave `trial`

| Clave | Tipo | Descripción | Valor predeterminado |
| --- | ---- | ----------- | ------------- |
| `command` | string | **Obligatorio.** El comando que se debe ejecutar. | |
| `code.local_path` | string | Ruta de acceso local al directorio de código fuente que se va a cargar y usar para el trabajo. | |
| `environment` | cadena u objeto | **Obligatorio.** El entorno que se usará para el trabajo. Puede ser una referencia a un entorno con versiones existente en el área de trabajo o una especificación de entorno en línea. <br> <br> Para hacer referencia a un entorno existente, use la sintaxis `azureml:<environment-name>:<environment-version>`. <br><br> Para definir un entorno en línea, siga el [Esquema de entorno](reference-yaml-environment.md#yaml-syntax). Excluya las propiedades `name` y `version`, ya que no se admiten para entornos en línea. | |
| `environment_variables` | object | Diccionario de pares de nombre-valor de variables de entorno que se establecerán en el proceso donde se ejecuta el comando. | |
| `distribution` | object | La configuración de distribución para escenarios de entrenamiento distribuido. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration) o [TensorFlowConfiguration](#tensorflowconfiguration). | |
| `resources.instance_count` | integer | El número de nodos que se usarán para el trabajo. | `1` |

#### <a name="distribution-configurations"></a>Configuraciones de distribución

##### <a name="mpiconfiguration"></a>MpiConfiguration

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatorio.** Tipo de distribución.  | `mpi` |
| `process_count_per_instance` | integer | **Obligatorio.** El número de procesos por nodo que se inician para el trabajo.  | |

##### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatorio.** Tipo de distribución.  | `pytorch` | |
| `process_count_per_instance` | integer | El número de procesos por nodo que se inician para el trabajo. | |  `1` |

##### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

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

## <a name="yaml-hello-sweep"></a>YAML: hello sweep

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

## <a name="yaml-basic-python-model-hyperparameter-tuning"></a>YAML: ajuste básico de hiperparámetros del modelo de Python

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

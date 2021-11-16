---
title: Esquema YAML de la implementación por lotes de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML de la implementación por lotes de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53e83f76e61038bc1fb95bfba653b18f63febede
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054512"
---
# <a name="cli-v2-batch-deployment-yaml-schema"></a>Esquema YAML de la implementación por lotes de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `name` | string | **Obligatorio.** Nombre de la implementación. | | |
| `description` | string | Descripción de la implementación. | | |
| `tags` | object | Diccionario de etiquetas para la implementación. | | |
| `endpoint_name` | string | **Obligatorio.** Nombre del punto de conexión en el que se creará la implementación. | | |
| `model` | cadena u objeto | **Obligatorio.** Modelo que se usará para la implementación. Este valor puede ser una referencia a un modelo con versiones existente en el área de trabajo o una especificación de modelo en línea. <br><br> Para hacer referencia a un modelo existente, use la sintaxis `azureml:<model-name>:<model-version>`. <br><br> Para definir un modelo en línea, siga el [Esquema del modelo](reference-yaml-model.md#yaml-syntax). <br><br> Como procedimiento recomendado para escenarios de producción, debe crear el modelo por separado y hacerle referencia aquí. | | |
| `code_configuration` | object | Configuración de la lógica de código de puntuación. <br><br> Esta propiedad no es necesaria si el modelo está en formato MLflow. | | |
| `code_configuration.code.local_path` | string | Ruta de acceso local al directorio de código fuente para la puntuación del modelo. | | |
| `code_configuration.scoring_script` | string | Ruta de acceso relativa al archivo de puntuación en el directorio de código fuente. | | |
| `environment` | cadena u objeto | El entorno que se usará para la implementación. Este valor puede ser una referencia a un entorno con versiones existente en el área de trabajo o una especificación de entorno en línea. <br><br> Esta propiedad no es necesaria si el modelo está en formato MLflow. <br><br> Para hacer referencia a un entorno existente, use la sintaxis `azureml:<environment-name>:<environment-version>`. <br><br> Para definir un entorno en línea, siga el [Esquema de entorno](reference-yaml-environment.md#yaml-syntax). <br><br> Como procedimiento recomendado para escenarios de producción, debe crear el entorno por separado y hacerle referencia aquí. | | |
| `compute` | string | **Obligatorio.** Nombre del destino de proceso en el que se ejecutarán los trabajos de puntuación por lotes. Este valor debe ser una referencia a un proceso existente en el área de trabajo mediante la sintaxis `azureml:<compute-name>`. | | |
| `resources.instance_count` | integer | Número de nodos que se usarán para cada trabajo de puntuación por lotes. | | `1` |
| `max_concurrency_per_instance` | integer | Número máximo de ejecuciones de `scoring_script` paralelas por instancia. | | `1` |
| `error_threshold` | integer | Número de errores de archivo que se deben omitir. Si el recuento de errores de toda la entrada supera este valor, el trabajo de puntuación por lotes se terminará. `error_threshold` es para toda la entrada y no para mini lotes individuales. Si se omite, se permitirá cualquier número de errores de archivo sin finalizar el trabajo.  | | `-1` |
| `logging_level` | string | Nivel de detalle del registro. | `warning`, `info`, `debug` | `info` |
| `mini_batch_size` | integer | Número de archivos que `code_configuration.scoring_script` puede procesar en una llamada a `run()`. | | `10` |
| `retry_settings` | object | Vuelva a intentar la configuración para puntuar cada mini lote. | | |
| `retry_settings.max_retries` | integer | Número máximo de reintentos para un mini lote con errores o con tiempo de espera. | | `3` |
| `retry_settings.timeout` | integer | Tiempo de espera en segundos para puntuar un mini lote. | | `30` |
| `output_action` | string | Indica cómo se debe organizar la salida en el archivo de salida. | `append_row`, `summary_only` | `append_row` |
| `output_file_name` | string | Nombre del archivo de salida de puntuación por lotes. | | `predictions.csv` |
| `environment_variables` | object | Diccionario de pares nombre-valor de variable de entorno que se establecerán para cada trabajo de puntuación por lotes. | | |

## <a name="remarks"></a>Observaciones

Los comandos `az ml batch-deployment` se pueden usar para administrar las implementaciones de lotes de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). A continuación se muestran varios.

## <a name="yaml-basic-mlflow"></a>YAML: básico (MLflow)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

## <a name="yaml-custom-model-and-scoring-code"></a>YAML: modelo personalizado y código de puntuación

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

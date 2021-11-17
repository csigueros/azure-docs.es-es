---
title: Esquema YAML del componente de comando de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del componente de comando de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53b64b804b07f60741be1a72e671e8ade4710e15
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055120"
---
# <a name="cli-v2-command-component-yaml-schema"></a>Esquema YAML del componente de comando de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | const | Tipo de componente. | `command` | `command` |
| `name` | string | **Obligatorio.** Nombre del componente. | | |
| `version` | string | Versión del componente. Si se omite, Azure ML genera automáticamente una versión. | | |
| `display_name` | string | Nombre para mostrar del componente en la interfaz de usuario de Studio. Puede no ser único dentro del área de trabajo. | | |
| `description` | string | Descripción del componente. | | |
| `tags` | object | Diccionario de etiquetas del componente. | | |
| `command` | string | **Obligatorio.** El comando que se debe ejecutar. | | |
| `code.local_path` | string | Ruta de acceso local al directorio de código fuente que se va a cargar y usar para el componente. | | |
| `environment` | cadena u objeto | **Obligatorio.** Entorno que se usará para el componente. Este valor puede ser una referencia a un entorno con versiones existente en el área de trabajo o una especificación de entorno en línea. <br><br> Para hacer referencia a un entorno existente, use la sintaxis `azureml:<environment-name>:<environment-version>`. <br><br> Para definir un entorno en línea, siga el [Esquema de entorno](reference-yaml-environment.md#yaml-syntax). Excluya las propiedades `name` y `version`, ya que no se admiten para entornos en línea. | | |
| `distribution` | object | La configuración de distribución para escenarios de entrenamiento distribuido. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration) o [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `resources.instance_count` | integer | El número de nodos que se usarán para el trabajo. | | `1` |
| `inputs` | object | Diccionario de entradas de componentes. La clave es un nombre para la entrada dentro del contexto del componente y el valor es la definición de entrada del componente. <br><br> Se puede hacer referencia a las entradas en `command` mediante la expresión `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | object | Definición de entrada del componente. Consulte [Entrada del componente](#component-input) para el conjunto de propiedades configurables. | | |
| `outputs` | object | Diccionario de salidas de componentes. La clave es un nombre para la salida dentro del contexto del componente y el valor es la definición de salida del componente. <br><br> Se puede hacer referencia a las salidas en `command` mediante la expresión `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Definición de salida del componente. Consulte [Salida del componente](#component-output) para el conjunto de propiedades configurables. | |

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

### <a name="component-input"></a>Entrada del componente

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | **Obligatorio.** Tipo de entrada del componente. <br><br> Use `type: path` si desea que el valor de entrada del trabajo en tiempo de ejecución sea un URI de datos o un conjunto de datos de Azure ML cuando se ejecute el componente. | `number`, `integer`, `boolean`, `string`, `path` | |
| `description` | string | Descripción de la entrada. | | |
| `default` | número, entero, booleano o cadena | El valor predeterminado de la entrada. | | |
| `optional` | boolean | Determina si la entrada es obligatoria. | | `false` |
| `min` | entero o número | Valor mínimo aceptado para la entrada. Este campo solo se puede especificar si el campo `type` es `number` o `integer`. | |
| `max` | entero o número | Valor máximo aceptado para la entrada. Este campo solo se puede especificar si el campo `type` es `number` o `integer`. | |
| `enum` | array | Lista de valores permitidos para la entrada. No es aplicable si el campo `type` es `boolean`.  | |

### <a name="component-output"></a>Salida del componente

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | **Obligatorio.** Tipo de salida del componente. | `path` | |
| `description` | string | Descripción de la salida. | | |

## <a name="remarks"></a>Observaciones

Los comandos `az ml component` se pueden usar para administrar los componentes de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos de componentes de comandos disponibles en el repositorio de GitHub de ejemplos. A continuación se muestran ejemplos seleccionados.

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipeline-with-components). A continuación se muestran varios.

## <a name="hello-world-command-component"></a>Componente de comando Hola mundo

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/2a_basic_component/component.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

---
title: Esquema YAML del trabajo de componente de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del trabajo de componente de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: fc1f9580b44132b172c7f58a93d75943dea63efd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565934"
---
# <a name="cli-v2-component-job-yaml-schema"></a>Esquema YAML del trabajo de componente de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | const | Tipo de trabajo. | `component` | |
| `component` | object | **Obligatorio.** Componente que se invocará y ejecutará en un trabajo. Este valor puede ser una referencia a un componente con control de versiones existente en el área de trabajo, una especificación de componente en línea o la ruta de acceso local a un archivo de especificación YAML de componente independiente. <br><br> Para hacer referencia a un componente existente, use la sintaxis `azureml:<component-name>:<component-version>`. <br><br> Para definir un componente en línea o en un archivo YAML independiente, siga el [esquema del componente de comando](reference-yaml-component-command.md#yaml-syntax). Excluya las propiedades `name` y `version`, ya que no son aplicables a las especificaciones de componentes en línea. | | |
| `compute` | string | Nombre del destino de proceso en el que se ejecutará el trabajo. Este valor debe ser una referencia a un proceso existente en el área de trabajo mediante la sintaxis `azureml:<compute-name>`. Si se omite, Azure ML usará el proceso definido en la propiedad `compute` del trabajo de canalización. | | |
| `inputs` | object | Diccionario de entradas para el trabajo. La clave corresponde al nombre de una de las entradas del componente y el valor es el valor de entrada en tiempo de ejecución. <br><br> Se puede hacer referencia a las entradas en `command` mediante la expresión `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | número, entero, booleano, cadena u objeto | Un valor literal (de tipo número,entero, booleano o cadena) [JobInputUri](#jobinputuri) o [JobInputDataset](#jobinputdataset). También puede hacer referencia a salidas de otro trabajo en la misma canalización a través de `jobs.<COMPONENT_NAME>.outputs.<OUTPUT_NAME>`. | | |
| `outputs` | object | Diccionario de configuraciones de salida del trabajo. La clave corresponde al nombre correspondiente al nombre de una de las salidas del componente y el valor es la configuración de salida en tiempo de ejecución. <br><br> Se puede hacer referencia a las salidas en `command` mediante la expresión `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Puede especificar un elemento `mode` opcional o dejar el objeto vacío. Para cada salida con nombre especificada en el diccionario `outputs`, Azure ML generará automáticamente una ubicación de salida basada en la siguiente ruta de acceso con plantillas: `{default-datastore}/azureml/{job-name}/{output-name}/`. Los usuarios podrán proporcionar una ubicación personalizada en una versión posterior. | |
| `outputs.<output_name>.mode` | string | Modo de cómo se entregarán los archivos de salida al almacenamiento de destino. Para el modo de montaje de lectura y escritura, el directorio de salida será un directorio montado. En el modo de carga, los archivos escritos en el directorio de salida se cargan al final del trabajo. | `rw_mount`, `upload` | `rw_mount` |
| `overrides` | object | Ciertas configuraciones de un componente se pueden reemplazar por otras opciones de tiempo de ejecución cuando el componente se ejecuta en un trabajo. Para un componente de comando, las propiedades `resources` y `distribution` se pueden invalidar a través de `overrides.resources` y `overrides.distribution`. | | |

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

Los trabajos de componentes se pueden ejecutar dentro de trabajos de canalización. Los comandos `az ml job` se pueden usar para administrar los trabajos de canalización de Azure Machine Learning.

Actualmente, los trabajos de componentes no se pueden ejecutar como trabajos independientes y solo se pueden ejecutar dentro de canalizaciones.

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

---
title: Esquema YAML del conjunto de datos de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del conjunto de datos de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 4a22fee30a28008fa440d606360623e156c332a0
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133685"
---
# <a name="cli-v2-dataset-yaml-schema"></a>Esquema YAML del conjunto de datos de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/dataset.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | |
| `name` | string | **Obligatorio.** Nombre del conjunto de datos. | |
| `version` | string | Versión del conjunto de datos. Si se omite, Azure Machine Learning genera automáticamente una versión. | |
| `description` | string | Descripción del conjunto de datos. | |
| `tags` | object | Diccionario de etiquetas para el conjunto de datos. | |
| `local_path` | string | Ruta de acceso absoluta o relativa de un único archivo o carpeta local a partir de los que se crea el conjunto de datos. **Se requiere `local_path` o `paths`.** | |
| `paths` | array | Una lista de orígenes de URI a partir de los cuales se crea el conjunto de datos. Cada entrada de la lista debe cumplir el esquema definido en [Ruta de acceso del origen del conjunto de datos](#dataset-source-path). Actualmente, solo se admite un único origen.  **Se requiere `local_path` o `paths`.** | |

### <a name="dataset-source-path"></a>Ruta de acceso del origen del conjunto de datos

| Clave | Tipo | Descripción |
| --- | ---- | ----------- |
| `file` | string | URI de un único archivo que se usa como origen para el conjunto de datos. Los tipos de URI admitidos son `azureml`, `https`, `wasbs`, `abfss` y `adl`. Consulte [Sintaxis principal de YAML](reference-yaml-core-syntax.md) para más información sobre cómo usar el formato de URI `azureml://`. **Se requiere `file` o `folder`.** |
| `folder` | string | URI de una carpeta que se usa como origen del conjunto de datos. Los tipos de URI admitidos son `azureml`, `https`, `wasbs`, `abfss` y `adl`. Consulte [Sintaxis principal de YAML](reference-yaml-core-syntax.md) para más información sobre cómo usar el formato de URI `azureml://`. **Se requiere `file` o `folder`.** |

## <a name="remarks"></a>Observaciones

Los comandos `az ml dataset` se pueden usar para administrar los conjuntos de datos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/assets/dataset). A continuación, se muestran varios.

## <a name="yaml-datastore-file"></a>YAML: archivo de almacén de datos

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file.yml":::

## <a name="yaml-datastore-folder"></a>YAML: carpeta de almacén de datos

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder.yml":::

## <a name="yaml-https-file"></a>YAML: archivo https

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-https.yml":::

## <a name="yaml-https-folder"></a>YAML: carpeta https

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-https.yml":::

## <a name="yaml-wasbs-file"></a>YAML: archivo wasbs

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-wasbs.yml":::

## <a name="yaml-wasbs-folder"></a>YAML: carpeta wasbs

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-wasbs.yml":::

## <a name="yaml-local-file"></a>YAML: archivo local

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-file.yml":::

## <a name="yaml-local-folder"></a>YAML: carpeta local

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-folder.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

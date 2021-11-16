---
title: Esquema YAML del modelo de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del modelo de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a9f3ac8ce87c3cec11c7bb5cb17e1004bce9f953
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556890"
---
# <a name="cli-v2-model-yaml-schema"></a>Esquema YAML del modelo de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/model.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | Esquema de YAML. | |
| `name` | string | **Obligatorio.** Nombre del modelo. | |
| `version` | string | Versión del modelo. Si se omite, Azure ML genera automáticamente una versión. | |
| `description` | string | Descripción del modelo. | |
| `tags` | object | Diccionario de etiquetas para el modelo. | |
| `local_path` | string | Ruta de acceso local al archivo o archivos de modelo. Esto puede apuntar a un archivo o a un directorio. **Se requiere `local_path` o `model_uri`.** | |
| `model_uri` | string | URI del archivo o archivos de modelo. Esto puede apuntar a un archivo o a un directorio. **Se requiere `local_path` o `model_uri`.** | |
| `model_format` | string | Formato de almacenamiento del modelo. Aplicable a escenarios de implementación sin código. | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | object | Tipos del modelo. Cada tipo de formato de almacenamiento del modelo puede tener uno o varios tipos admitidos. Aplicable a escenarios de implementación sin código. | |

## <a name="remarks"></a>Comentarios

El comando `az ml model` se puede usar para administrar los modelos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model). A continuación se muestran varios.

## <a name="yaml-local-file"></a>YAML: archivo local

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML: carpeta local en formato MLflow

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-mlflow.yml":::

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

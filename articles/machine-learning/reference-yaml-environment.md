---
title: Esquema YAML del entorno de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del entorno de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 8f812b9c02ef14d7e098c7f50e3a1c3cfabe97a6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058361"
---
# <a name="cli-v2-environment-yaml-schema"></a>Esquema YAML del entorno de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/environment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `name` | string | **Obligatorio.** Nombre del entorno. | | |
| `version` | string | Versión del entorno. Si se omite, Azure ML genera automáticamente una versión. | | |
| `description` | string | Descripción del entorno. | | |
| `tags` | object | Diccionario de etiquetas del entorno. | | |
| `image` | string | Imagen de Docker que se va a usar para el entorno. **Se requiere `image` o `build`.** | | |
| `conda_file` | cadena u objeto | Archivo de configuración YAML estándar de Conda de las dependencias de un entorno de Conda. Vea https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually. <br> <br> Si se ha especificado, también se debe especificar `image`. Azure ML compila el entorno de Conda sobre la imagen de Docker proporcionada. | | |
| `build` | object | Configuración del contexto de compilación de Docker que se usa para el entorno. **Se requiere `image` o `build`.** | | |
| `build.local_path` | string | Ruta de acceso local al directorio que se va a usar como contexto de compilación. | | |
| `build.dockerfile_path` | string | Ruta de acceso relativa al documento Dockerfile en el contexto de compilación. | | `Dockerfile` |
| `os_type` | string | El tipo de sistema operativo. | `linux`, `windows` | `linux` |  
| `inference_config` | object | Configuraciones de contenedor de inferencias. Solo se aplica si el entorno se usa para compilar un contenedor de servicios para implementaciones en línea. Vea [Atributos de la clave `inference_config`](#attributes-of-the-inference_config-key). | | |

### <a name="attributes-of-the-inference_config-key"></a>Atributos de la clave `inference_config`

| Clave | Tipo | Descripción |
| --- | ---- | ----------- |
| `liveness_route` | object | Ruta de ejecución del contenedor de servicios. |
| `liveness_route.path` | string | Ruta de acceso a la que enrutar solicitudes de ejecución. |
| `liveness_route.port` | integer | Puerto al que enrutar solicitudes de ejecución. |
| `readiness_route` | object | Ruta de preparación del contenedor de servicios. |
| `readiness_route.path` | string | Ruta de acceso a la que enrutar solicitudes de preparación. |
| `readiness_route.port` | integer | Puerto al que enrutar solicitudes de preparación. |
| `scoring_route` | object | Ruta de puntuación del contenedor de servicios. |
| `scoring_route.path` | string | Ruta de acceso a la que enrutar solicitudes de puntuación. |
| `scoring_route.port` | integer | Puerto al que enrutar solicitudes de puntuación. |

## <a name="remarks"></a>Comentarios

El comando `az ml environment` se puede usar para administrar los entornos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/assets/environment). A continuación se muestran varios.

## <a name="yaml-local-docker-build-context"></a>YAML: contexto de compilación local de Docker

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

## <a name="yaml-docker-image"></a>YAML: imagen de Docker

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

## <a name="yaml-docker-image-plus-conda-file"></a>YAML: imagen de Docker más archivo de Conda

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

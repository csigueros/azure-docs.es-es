---
title: Esquema YAML de punto de conexión por lotes de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML de punto de conexión por lotes de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: c91de82d4b0eec1256d34a28132ae3161c62690a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057772"
---
# <a name="cli-v2-batch-endpoint-yaml-schema"></a>Esquema YAML de punto de conexión por lotes de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `name` | string | **Obligatorio.** Nombre del punto de conexión. Es preciso que sea único en el nivel de región de Azure. | | |
| `description` | string | Descripción del punto de conexión. | | |
| `tags` | object | Diccionario de etiquetas del punto de conexión. | | |
| `auth_mode` | string | Método de autenticación del punto de conexión. Actualmente solo se admite la autenticación basada en tokens de Azure Active Directory (Azure AD). | `aad_token` | `aad_token` |
| `defaults` | object | Configuración predeterminada del punto de conexión. | | |
| `defaults.deployment_name` | string | Nombre de la implementación que va a actuar como implementación predeterminada del punto de conexión. | | |

## <a name="remarks"></a>Observaciones

Los comandos `az ml batch-endpoint` se pueden usar para administrar los puntos de conexión de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). A continuación se muestran varios.

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/batch-endpoint.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

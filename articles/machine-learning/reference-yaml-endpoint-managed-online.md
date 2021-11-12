---
title: Referencia de YAML sobre puntos de conexión en línea (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre los archivos YAML que se usan para implementar modelos como puntos de conexión en línea.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 462c781ae4f304d2b9cb46b9b89d2ff7fbf7eb22
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137819"
---
# <a name="cli-v2-online-endpoint-yaml-schema"></a>Esquema de YAML de punto de conexión en línea de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Como [referencia](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml), hay disponible un archivo YAML de ejemplo completamente especificado para los puntos de conexión en línea administrados.

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `name` | string | **Obligatorio.** Nombre del punto de conexión. Es preciso que sea único en el nivel de región de Azure. <br><br> Las reglas de nomenclatura se definen [aquí](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).| | |
| `description` | string | Descripción del punto de conexión. | | |
| `tags` | object | Diccionario de etiquetas del punto de conexión. | | |
| `auth_mode` | string | El método de autenticación para el punto de conexión. Se admite la autenticación basada en claves y la autenticación basada en tokens de Azure Machine Learning. La autenticación basada en claves no expira, pero la autenticación basada en tokens de Azure Machine Learning. | `key`, `aml_token` | `key` |
| `allow_public_access` | boolean | Si se permite el acceso público cuando Private Link está habilitado. | | `true` |
| `identity` | object | La configuración de identidad administrada a fin de acceder a recursos de Azure para el aprovisionamiento y la inferencia de puntos de conexión. | | |
| `identity.type` | string | El tipo de identidad administrada. Si el tipo es `user_assigned`, también se debe especificar la propiedad `identity.user_assigned_identities`. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | Lista de id. de recursos completos de las identidades asignadas por el usuario. | | |

## <a name="remarks"></a>Observaciones

Los comandos `az ml online-endpoint` se pueden usar para administrar los puntos de conexión en línea de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). A continuación se muestran varios.

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: identidad asignada por el sistema

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: identidad asignada por el usuario

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
- Aprenda a [implementar un modelo con un punto de conexión en línea administrado](how-to-deploy-managed-online-endpoints.md).
- [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](./how-to-troubleshoot-online-endpoints.md)
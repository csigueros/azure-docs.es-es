---
title: Esquema YAML del almacén de datos de blobs de Azure de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia para el esquema YAML del almacén de datos de blobs de Azure de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: dc2455212e2ab67d70dba5cce72a343f05fe2b81
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557972"
---
# <a name="cli-v2-azure-blob-datastore-yaml-schema"></a>Esquema YAML del almacén de datos de blobs de Azure de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | string | **Obligatorio.** Tipo de almacén de datos. | `azure_blob` | |
| `name` | string | **Obligatorio.** Nombre del almacén de datos. | | |
| `description` | string | Descripción del almacén de datos. | | |
| `tags` | object | Diccionario de etiquetas del almacén de datos. | | |
| `account_name` | string | **Obligatorio.** Nombre de la cuenta de almacenamiento de Azure. | | |
| `container_name` | string | **Obligatorio.** Nombre del contenedor. | | |
| `endpoint` | string | Sufijo del punto de conexión del servicio de almacenamiento, que se usa para crear la dirección URL del punto de conexión de la cuenta de almacenamiento mediante la combinación del nombre de la cuenta de almacenamiento y `endpoint`. Dirección URL de la cuenta de almacenamiento de ejemplo: `https://<storage-account-name>.blob.core.windows.net`. | | `core.windows.net` |
| `protocol` | string | Protocolo que se va a usar para conectarse al contenedor. | `https`, `wasbs` | `https` |
| `credentials` | object | Credenciales de autenticación basadas en credenciales para conectarse a la cuenta de almacenamiento de Azure. Puede proporcionar una clave de cuenta o un token de firma de acceso compartido (SAS). Los secretos de credenciales se almacenan en el almacén de claves del área de trabajo. | | |
| `credentials.account_key` | string | Clave de cuenta para acceder a la cuenta de almacenamiento. **Se requiere `credentials.account_key` o `credentials.sas_token` si se especifica `credentials`.** | | |
| `credentials.sas_token` | string | Token de SAS para acceder a la cuenta de almacenamiento. **Se requiere `credentials.account_key` o `credentials.sas_token` si se especifica `credentials`.** | | |

## <a name="remarks"></a>Comentarios

El comando `az ml datastore` se puede usar para administrar los almacenes de datos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). A continuación se muestran varios.

## <a name="yaml-identity-based-access"></a>YAML: acceso basado en identidad

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob-credless.yml":::

## <a name="yaml-account-key"></a>YAML: clave de cuenta

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob.yml":::

## <a name="yaml-wasbs-protocol"></a>YAML: protocolo wasbs

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob-protocol.yml":::

## <a name="yaml-sas-token"></a>YAML: token de SAS

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/blob-sas.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

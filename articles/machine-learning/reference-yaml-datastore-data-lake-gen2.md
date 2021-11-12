---
title: Esquema YAML del almacén de datos Azure Data Lake Gen2 de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia sobre el esquema YAML del almacén de datos Azure Data Lake Gen2 de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 56041a7fc16b09f1622d7090a3ef6e2d088742ee
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556072"
---
# <a name="cli-v2-azure-data-lake-gen2-yaml-schema"></a>Esquema YAML de Azure Data Lake Gen2 de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | string | **Obligatorio.** El tipo de almacén de datos. | `azure_data_lake_gen2` | |
| `name` | string | **Obligatorio.** Nombre del almacén de datos. | | |
| `description` | string | Descripción del almacén de datos. | | |
| `tags` | object | Diccionario de etiquetas para el almacén de datos. | | |
| `account_name` | string | **Obligatorio.** Nombre de la cuenta de almacenamiento de Azure. | | |
| `filesystem` | string | **Obligatorio.** Nombre del sistema de archivos. El directorio primario que contiene los archivos y carpetas. Equivale a un contenedor en Azure Blob Storage. | | |
| `endpoint` | string | Sufijo del punto de conexión del servicio de almacenamiento, que se usa para crear la dirección URL del punto de conexión de la cuenta de almacenamiento mediante la combinación del nombre de la cuenta de almacenamiento y `endpoint`. Dirección URL de la cuenta de almacenamiento de ejemplo: `https://<storage-account-name>.dfs.core.windows.net`. | | `core.windows.net` |
| `protocol` | string | Protocolo que se usará para conectarse al sistema de archivos. | `https`, `abfss` | `https` |
| `credentials` | object | Credenciales de entidad de servicio para conectarse a la cuenta de almacenamiento de Azure. Los secretos de credenciales se almacenan en el almacén de claves del área de trabajo. | | |
| `credentials.tenant_id` | string | El id. de inquilino de la entidad de servicio. **Obligatorio si se especifica `credentials`.** | | |
| `credentials.client_id` | string | El id. de cliente de la entidad de servicio. **Obligatorio si se especifica `credentials`.** | | |
| `credentials.client_secret` | string | El secreto de cliente de la entidad de servicio. **Obligatorio si se especifica `credentials`.** | | |
| `credentials.resource_url` | string | La dirección URL del recurso que determina qué operaciones se realizarán en la cuenta de Azure Data Lake Storage Gen2. | | `https://storage.azure.com/` |
| `credentials.authority_url` | string | La dirección URL de la autoridad que se usa para autenticar al usuario. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>Comentarios

El comando `az ml datastore` se puede usar para administrar los almacenes de datos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). A continuación, se muestran varios.

## <a name="yaml-identity-based-access"></a>YAML: acceso basado en identidad

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen2-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML: id. de inquilino, id. de cliente, secreto de cliente

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen2.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

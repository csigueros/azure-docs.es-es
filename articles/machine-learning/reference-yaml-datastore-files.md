---
title: Esquema YAML del almacén de datos de Azure Files de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia para el esquema YAML del almacén de datos de Azure Files de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: d70634b1df1ab194ef4f52c9b12ac982f023c53a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136322"
---
# <a name="cli-v2-azure-files-datastore-yaml-schema"></a>Esquema YAML del almacén de datos de Azure Files de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/azureFile.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | string | **Obligatorio.** Tipo de almacén de datos. | `azure_file` | |
| `name` | string | **Obligatorio.** Nombre del almacén de datos. | | |
| `description` | string | Descripción del almacén de datos. | | |
| `tags` | object | Diccionario de etiquetas del almacén de datos. | | |
| `account_name` | string | **Obligatorio.** Nombre de la cuenta de almacenamiento de Azure. | | |
| `file_share_name` | string | **Obligatorio.** Nombre del recurso compartido de archivos. | | |
| `endpoint` | string | Sufijo del punto de conexión del servicio de almacenamiento, que se usa para crear la dirección URL del punto de conexión de la cuenta de almacenamiento mediante la combinación del nombre de la cuenta de almacenamiento y `endpoint`. Dirección URL de la cuenta de almacenamiento de ejemplo: `https://<storage-account-name>.file.core.windows.net`. | | `core.windows.net` |
| `protocol` | string | Protocolo que se usará para conectarse al recurso compartido de archivos. | `https` | `https` |
| `credentials` | object | Credenciales de autenticación basadas en credenciales para conectarse a la cuenta de almacenamiento de Azure. Puede proporcionar una clave de cuenta o un token de firma de acceso compartido (SAS). Los secretos de credenciales se almacenan en el almacén de claves del área de trabajo. | | |
| `credentials.account_key` | string | Clave de cuenta para acceder a la cuenta de almacenamiento. **Se requiere `credentials.account_key` o `credentials.sas_token` si se especifica `credentials`.** | | |
| `credentials.sas_token` | string | Token de SAS para acceder a la cuenta de almacenamiento. **Se requiere `credentials.account_key` o `credentials.sas_token` si se especifica `credentials`.** | | |

## <a name="remarks"></a>Comentarios

El comando `az ml datastore` se puede usar para administrar los almacenes de datos de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). A continuación, se muestran varios.

## <a name="yaml-account-key"></a>YAML: clave de cuenta

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/file.yml":::

## <a name="yaml-sas-token"></a>YAML: token de SAS

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/file-sas.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

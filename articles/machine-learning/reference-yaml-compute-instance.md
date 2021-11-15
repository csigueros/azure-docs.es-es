---
title: Esquema YAML de la instancia de proceso de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML de la instancia de proceso de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ff13936785dc48fc8fb0c4b26a55651f0e7f827c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565927"
---
# <a name="cli-v2-compute-instance-yaml-schema"></a>Esquema YAML de la instancia de proceso de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | string | **Obligatorio.** Tipo de proceso. | `computeinstance` | |
| `name` | string | **Obligatorio.** Nombre del proceso. | | |
| `description` | string | Descripción del proceso. | | |
| `size` | string | Tamaño de máquina virtual que se usará para la instancia de proceso. Para más información, consulte [Tamaños y series de máquina virtual admitidos](concept-compute-target.md#supported-vm-series-and-sizes). Tenga en cuenta que no todos los tamaños de máquina virtual están disponibles en todas las regiones. | Para obtener la lista de tamaños admitidos en una región determinada, use el comando `az ml compute list-sizes`.  | `Standard_DS3_v2` |
| `create_on_behalf_of` | object | Configuración para crear la instancia de proceso en nombre de otro usuario. Asegúrese de que el usuario asignado tiene los permisos de RBAC correctos. |  |  |
| `create_on_behalf_of.user_tenant_id` | string | Identificador del inquilino de AAD del usuario asignado. |  |  |
| `create_on_behalf_of.user_object_id` | string | Identificador del objeto de AAD del usuario asignado. |  |  |
| `ssh_public_access_enabled` | boolean | Si se va a habilitar el acceso SSH público en la instancia de proceso. | | `false` |
| `ssh_settings` | object | Configuración de SSH para conectarse a la instancia de proceso. | | |
| `ssh_settings.ssh_key_value` | string | Clave pública SSH de la cuenta de usuario administrador. | | |
| `network_settings` | object | Configuración de seguridad de red. | | |
| `network_settings.vnet_name` | string | Nombre de la red virtual (VNet) al crear una nueva o hacer referencia a una existente. | | |
| `network_settings.subnet` | string | Nombre de la subred al crear una nueva red virtual o hacer referencia a una existente, o bien el identificador de recurso completo de una subred en una red virtual existente. No especifique `network_settings.vnet_name` si se especifica el identificador de subred. El identificador de subred puede hacer referencia a una red virtual o subred en otro grupo de recursos. | | |

## <a name="remarks"></a>Comentarios

El comando `az ml compute` se puede usar para administrar las instancias de proceso de Azure Machine Learning.

## <a name="yaml-minimal"></a>YAML: mínimo

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/instance-minimal.yml":::

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/compute/instance-basic.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

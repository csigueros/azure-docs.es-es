---
title: Esquema YAML de máquina virtual conectado a la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia para el esquema de máquina virtual conectado a la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: b639c4193a246ccf7f57520089132f0f49d16ebb
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062441"
---
# <a name="cli-v2-attached-virtual-machine-yaml-schema"></a>Esquema YAML de máquina virtual conectado a la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | string | **Obligatorio.** Tipo de proceso. | `virtualmachine` | |
| `name` | string | **Obligatorio.** Nombre del proceso. | | |
| `description` | string | Descripción del proceso. | | |
| `resource_id` | string | **Obligatorio.** Identificador de recurso completo de la máquina virtual de Azure que se asocia al área de trabajo como destino de proceso. | | |
| `ssh_settings` | object | Configuración de SSH para conectarse a la máquina virtual. | | |
| `ssh_settings.admin_username` | string | Nombre de la cuenta de usuario administrador que se puede usar para SSH en la máquina virtual. | | |
| `ssh_settings.admin_password` | string | Contraseña de la cuenta de usuario administrador. **Se requiere `admin_password` o `ssh_private_key_file`.** | | |
| `ssh_settings.ssh_private_key_file` | string | Ruta de acceso local al archivo de clave privada SSH de la cuenta de usuario de administrador. **Se requiere `admin_password` o `ssh_private_key_file`.** | | |
| `ssh_settings.ssh_port` | integer | Puerto SSH en la máquina virtual. | | `22` |

## <a name="remarks"></a>Observaciones

El comando `az ml compute` se puede usar para administrar Virtual Machines (VM) conectadas a un área de trabajo de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute). A continuación se muestran varios.

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/vm-attach.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

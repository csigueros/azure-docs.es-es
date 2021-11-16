---
title: Esquema YAML de clúster de proceso (AmlCompute) de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML de clúster de proceso de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: eac97f87de7edafc45f0e434188579df19d99c36
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057618"
---
# <a name="cli-v2-compute-cluster-amlcompute-yaml-schema"></a>Esquema YAML de clúster de proceso (AmlCompute) de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `type` | string | **Obligatorio.** Tipo de proceso. | `amlcompute` | |
| `name` | string | **Obligatorio.** Nombre del proceso. | | |
| `description` | string | Descripción del proceso. | | |
| `location` | string | Ubicación del proceso. Si se omite, el valor predeterminado es la ubicación del área de trabajo. | | |
| `size` | string | Tamaño de máquina virtual que se usará para el clúster. Para más información, consulte [Tamaños y series de máquina virtual admitidos](concept-compute-target.md#supported-vm-series-and-sizes). Tenga en cuenta que no todos los tamaños de máquina virtual están disponibles en todas las regiones. | Para obtener la lista de tamaños admitidos en una región determinada, use `az ml compute list-sizes`.  | `Standard_DS3_v2` |
| `tier` | string | Nivel de prioridad de máquina virtual que se usará para el clúster. Las máquinas virtuales de prioridad baja se pueden vaciar previamente, pero tienen un costo reducido en comparación con las máquinas virtuales dedicadas. | `dedicated`, `low_priority` | `dedicated` |
| `min_instances` | integer | Número mínimo de nodos que se usarán en el clúster. Establecer el número mínimo de nodos en `0` permite a Azure ML escalar automáticamente el clúster a cero nodos cuando no está en uso. Cualquier valor mayor que `0` mantendrá ese número de nodos en ejecución, aunque no estén en uso. | | `0` |
| `max_instances` | integer | Número máximo de nodos que se usarán en el clúster. | | `1` |
| `idle_time_before_scale_down` | integer | Tiempo de inactividad del nodo en segundos antes de reducir verticalmente el clúster. | | `120` |
| `ssh_public_access_enabled` | boolean | Si se debe habilitar el acceso SSH público en los nodos del clúster. | | `false` |
| `ssh_settings` | object | Configuración SSH para conectarse al clúster. | | |
| `ssh_settings.admin_username` | string | Nombre de la cuenta de usuario administrador que se puede usar para SSH en nodos. | | |
| `ssh_settings.admin_password` | string | Contraseña de la cuenta de usuario administrador. **Se requiere `admin_password` o `ssh_key_value`.** | | |
| `ssh_settings.ssh_key_value` | string | Clave pública SSH de la cuenta de usuario administrador. **Uno de estos elementos, `admin_password` o `ssh_key_value`, es obligatorio.** | | |
| `network_settings` | object | Configuración de seguridad de red. | | |
| `network_settings.vnet_name` | string | Nombre de la red virtual (VNet) al crear una nueva o hacer referencia a una existente. | | |
| `network_settings.subnet` | string | Nombre de la subred al crear una nueva red virtual o hacer referencia a una existente, o bien el identificador de recurso completo de una subred en una red virtual existente. No especifique `network_settings.vnet_name` si se especifica el identificador de subred. El identificador de subred puede hacer referencia a una red virtual o subred en otro grupo de recursos. | | |
| `identity` | object | Configuración de identidad administrada que se asignará al proceso. Los clústeres de AmlCompute solo admiten una identidad asignada por el sistema o varias identidades asignadas por el usuario, no ambas de forma simultánea. | | |
| `identity.type` | string | Tipo de identidad administrada que se asignará al proceso. Si el tipo es `user_assigned`, también se debe especificar la propiedad `identity.user_assigned_identities`. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | array | Lista de id. de recursos completos de las identidades asignadas por el usuario. | | |

## <a name="remarks"></a>Observaciones

Los comandos `az ml compute` se pueden usar para administrar los clústeres de proceso de Azure Machine Learning (AmlCompute).

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute). A continuación se muestran varios.

## <a name="yaml-minimal"></a>YAML: mínimo

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-minimal.yml":::

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-basic.yml":::

## <a name="yaml-custom-location"></a>YAML: ubicación personalizada

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-location.yml":::

## <a name="yaml-low-priority"></a>YAML: prioridad baja

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-low-priority.yml":::

## <a name="yaml-ssh-username-and-password"></a>YAML: nombre de usuario y contraseña de SSH

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-ssh-password.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)

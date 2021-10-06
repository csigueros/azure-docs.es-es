---
title: 'Ejemplo de script de la CLI de Azure: grupo de Windows en Batch | Microsoft Docs'
description: Conozca algunos de los comandos disponibles en la CLI de Azure para crear y administrar un grupo de nodos de proceso de Windows en Azure Batch.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: windows pool, azure cli samples, azure cli code samples, azure cli script samples
ms.openlocfilehash: 6c98d5ece314f6aa8603db73a0a5c0c9a420efde
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595176"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Ejemplo de la CLI: creación y administración de un grupo de Windows en Azure Batch

Este script muestra algunos de los comandos disponibles en la CLI de Azure para crear y administrar un grupo de nodos de proceso de Windows en Azure Batch. Un grupo de Windows se puede configurar de dos formas, ya sea con una configuración de Cloud Services o una configuración de Virtual Machine. En este ejemplo se muestra cómo crear un grupo de Windows con la configuración de Cloud Services.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.20 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="example-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Ejecute el siguiente comando para quitar el grupo de recursos y todos los recursos asociados a él.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crea la cuenta de Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Realiza la autenticación respecto de la cuenta de Batch especificada para una mayor interacción de la CLI. |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Crea un grupo de nodos de proceso.  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | Actualiza las propiedades de un grupo.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Habilita el escalado automático de un grupo y aplica una fórmula.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Muestra las propiedades de un grupo.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Deshabilita el escalado automático de un grupo. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

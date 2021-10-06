---
title: 'Ejemplo de script de la CLI de Azure: ejecución de un trabajo con Batch | Microsoft Docs'
description: Aprenda a crear un trabajo de Batch y agregarle una serie de tareas mediante la CLI de Azure. En este artículo también se muestra cómo supervisar un trabajo y sus tareas.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, batch job, monitor job, azure cli samples, azure cli code samples, azure cli script samples
ms.openlocfilehash: 293e517a528b6a1630ff8597f4fa17c87008a78f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595187"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Ejemplo de la CLI: ejecución de un trabajo y tareas con Azure Batch

Este script crea un trabajo de Batch y agrega una serie de tareas a dicho trabajo. También muestra cómo supervisar un trabajo y sus tareas. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.20 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="example-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Realiza la autenticación respecto de la cuenta de Batch especificada para una mayor interacción de la CLI.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Crea un grupo de nodos de proceso.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Crea un trabajo de Batch.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Agrega una tarea al trabajo de Batch especificado.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Actualiza las propiedades de un trabajo de Batch.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Recupera los detalles de un trabajo de Batch especificado.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Recupera los detalles de una tarea del trabajo de Batch especificado.  |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

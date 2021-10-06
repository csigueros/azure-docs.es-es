---
title: 'Ejemplo de script de la CLI de Azure: incorporación de una aplicación a Batch | Microsoft Docs'
description: Aprenda a agregar una aplicación para usarla con un grupo de Azure Batch o una tarea mediante la CLI de Azure.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, azure cli samples, azure cli code samples, azure cli script samples
ms.openlocfilehash: 1c20adef86e30df79a5de2d6229ca099875c98f0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595206"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Ejemplo la CLI: incorporación de una aplicación en una cuenta de Azure Batch

Este script demuestra cómo agregar una aplicación para usarla con un grupo o tarea de Azure Batch. Para configurar una aplicación que se agregará a su cuenta de Batch, cree un archivo .zip que incluya el ejecutable y cualquier dependencia. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requiere la versión 2.0.20 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="example-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Ejecute el siguiente comando para quitar el grupo de recursos y todos los recursos asociados a él.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos.
Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crea una cuenta de almacenamiento. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crea la cuenta de Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Realiza la autenticación respecto de la cuenta de Batch especificada para una mayor interacción de la CLI.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Crea una aplicación.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Agrega un paquete de aplicación a la aplicación especificada.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Actualiza las propiedades de una aplicación.  |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

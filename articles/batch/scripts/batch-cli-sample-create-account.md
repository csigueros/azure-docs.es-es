---
title: 'Ejemplo de script de la CLI de Azure: creación de una cuenta en Batch - servicio de Batch | Microsoft Docs'
description: Aprenda a crear una cuenta de Batch en modo de servicio Batch con este ejemplo de script de la CLI de Azure. Este script también muestra cómo consultar o actualizar varias propiedades de la cuenta.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, azure cli samples, azure cli code samples, azure cli script samples
ms.openlocfilehash: 0e909f644fddae91a664b11abdbba0ae8c71b2ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548396"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Ejemplo de la CLI: creación de una cuenta en Batch en modo de servicio de Batch

Este script crea una cuenta de Azure Batch en modo de servicio de Batch y muestra cómo se pueden consultar o actualizar las distintas propiedades de la cuenta. Cuando crea una cuenta de Batch en el modo de servicio de Batch predeterminado,el servicio de Batch asigna de forma interna sus nodos de proceso. Los nodos de proceso asignados están sujetos a una cuota de vCPU (núcleos) independiente y la cuenta se puede autenticar a través de credenciales clave compartidas o de un token de Azure Active Directory.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.20 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="example-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crea una cuenta de almacenamiento. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Actualiza las propiedades de la cuenta de Batch.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Recupera los detalles de la cuenta de Batch especificada.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Recupera las claves de acceso de la cuenta de Batch especificada.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Realiza la autenticación respecto de la cuenta de Batch especificada para una mayor interacción de la CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

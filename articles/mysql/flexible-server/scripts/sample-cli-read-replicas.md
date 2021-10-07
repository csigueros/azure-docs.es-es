---
title: 'Script de CLI: creación y administración de réplicas de lectura en un servidor Azure Database for MySQL: servidor flexible (versión preliminar)'
description: 'En este script de ejemplo de la CLI de Azure se muestra como crear y administrar réplicas de lectura en un servidor Azure Database for MySQL: servidor flexible'
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 02eea900b9e78b0a23bead45bd077902de7417eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702180"
---
# <a name="create-and-manage-read-replicas-in-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Creación y administración de réplicas de lectura en un servidor Azure Database for MySQL: servidor flexible (versión preliminar) mediante la CLI de Azure

Este script de la CLI de ejemplo crea y administra [réplicas de lectura](../concepts-read-replicas.md) en un servidor Azure Database for MySQL: servidor flexible.

>[!IMPORTANT]
>Cuando se crea la réplica de origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/create-manage-read-replicas.sh?highlight=7,10-12 "Create and manage Flexible Server Read Replicas.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server replica create](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_create)|Crea una réplica de lectura para un servidor.|
|[az mysql flexible-server replica list](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_list)|Enumera todas las réplicas de lectura de un servidor determinado.|
|[az mysql flexible-server replica stop-replication](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_stop_replication)|Detenga la replicación en una réplica de lectura y conviértala en un servidor de lectura y escritura.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe otros scripts: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
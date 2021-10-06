---
title: 'Script de la CLI: enumeración y cambio de los parámetros del servidor de un servidor Azure Database for MySQL: servidor flexible (versión preliminar)'
description: 'En este script de ejemplo de la CLI de Azure se muestra cómo enumerar y cambiar los parámetros de servidor de un servidor Azure Database for MySQL: servidor flexible'
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: bcb811c9143e97ae070873c4b94396d6a1308d4a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604664"
---
# <a name="list-and-change-server-parameters-of-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Enumeración y cambio de los parámetros del servidor de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar) mediante la CLI de Azure

Este script de la CLI de ejemplo enumera todos los [parámetros de servidor](../concepts-server-parameters.md) disponibles, así como sus valores permitidos para Azure Database for MySQL: servidor flexible y establece los parámetros *max_connections* y *time_zone* global en unos valores que no son los predeterminados.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/change-server-parameters.sh?highlight=7,10-11 "Change server parameters for Azure Database for MySQL - Flexible Server.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_list)|Enumera los valores de los parámetros de un servidor flexible.|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|Actualiza el parámetro de un servidor flexible.|
|[az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_show)|Obtiene un valor de parámetro específico para un servidor flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe otros scripts: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
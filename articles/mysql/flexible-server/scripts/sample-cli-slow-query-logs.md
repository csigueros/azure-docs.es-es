---
title: 'Script de la CLI: configuración de registros de consultas lentas en Azure Database for MySQL: servidor flexible (versión preliminar)'
description: 'En el script de ejemplo de la CLI de Azure se muestra cómo configurar registros de consultas lentas en Azure Database for MySQL: servidor flexible.'
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 82fcf0fb06ee1b2823e525b47fb581470dddd1cc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702115"
---
# <a name="configure-slow-query-logs-on-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Configuración de registros de consultas lentas en Azure Database for MySQL: servidor flexible (versión preliminar) mediante la CLI de Azure

Este script de ejemplo de la CLI configura [registros de consultas lentas](../concepts-slow-query-logs.md) en Azure Database for MySQL: servidor flexible. 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/configure-logs/configure-slow-query-logs.sh?highlight=7,10-11 "Configure slow-query logs on Azure Database for MySQL - Flexible Server.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/configure-logs/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|Actualiza el parámetro de un servidor flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe otros scripts: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
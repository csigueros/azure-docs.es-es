---
title: 'Script de la CLI: reinicio, inicio o detención de un servidor flexible de Azure Database for MySQL (versión preliminar)'
description: Este script de ejemplo de la CLI de Azure muestra cómo reiniciar, detener o iniciar un servidor flexible de Azure Database for MySQL.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 2c4d17b0bcbba1c231b06f6112ef8b43e9b4786a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604702"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Reinicio, inicio o detención de un servidor flexible de Azure Database for MySQL (versión preliminar) mediante la CLI de Azure

Este script de ejemplo de la CLI realiza operaciones de reinicio, inicio y detención en un servidor flexible de Azure Database for MySQL. 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]


> [!IMPORTANT]
> Cuando se **detiene** el servidor permanece en ese estado durante los siete días siguientes. Si no lo **inicia** de forma manual durante ese tiempo, se iniciará automáticamente al final de los siete días. Puede optar por volver a **detenerlo** si no va a usar el servidor.

Durante el tiempo en que el servidor está detenido, no se pueden realizar operaciones de administración en él. Para cambiar cualquier valor de la configuración del servidor, deberá iniciar el servidor. 

Consulte también las [limitaciones para detener o iniciar](../concepts-limitations.md#stopstart-operation) antes de realizar operaciones de detención o inicio.


[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/restart-start-stop.sh?highlight=7,10-11 "Create a server, perform restart / start / stop operations.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server stop](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_stop)|Detiene un servidor flexible.|
|[az mysql flexible-server start](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_start)|Inicia un servidor flexible.|
|[az mysql flexible-server restart](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restart)|Reinicia un servidor flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe otros scripts en: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
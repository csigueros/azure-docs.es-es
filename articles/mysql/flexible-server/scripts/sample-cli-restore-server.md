---
title: 'Script de la CLI: Restauración de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar)'
description: 'En este script de la CLI de Azure de ejemplo se muestra cómo restaurar una única instancia de Azure Database for MySQL: servidor flexible a un momento anterior.'
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: d2e554bfa4d9851687542bbae8d66d51bf270bbd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702216"
---
# <a name="restore-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Restauración de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar) mediante la CLI de Azure

Azure Database for MySQL: servidor flexible crea automáticamente copias de seguridad del servidor y las almacena de forma segura en almacenamiento con redundancia local en la región.

Este script de la CLI de ejemplo realiza una [restauración a un momento dado](../concepts-backup-restore.md) y crea un servidor a partir de las copias de seguridad del servidor flexible. 

El nuevo servidor flexible se crea con la configuración del servidor original y también hereda las etiquetas y la configuración, como la red virtual y el firewall, del servidor de origen. El nivel de proceso y la capa de almacenamiento del servidor restaurado, la configuración y los valores de seguridad se pueden cambiar una vez completada la restauración.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/restore-server.sh?highlight=7,10-12 "Perform point-in-time-restore of a source server to a new server.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server restore](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restore)|Restaura un servidor flexible a partir de la copia de seguridad.|
|[az mysql flexible-server show](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_show)|Obtiene los detalles de un servidor flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe scripts adicionales en: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
---
title: 'Script de la CLI: configuración de la alta disponibilidad en la misma zona en un servidor flexible de Azure Database for MySQL (versión preliminar)'
description: Este script de ejemplo de la CLI de Azure muestra cómo configurar la alta disponibilidad en la misma zona en un servidor flexible de Azure Database for MySQL.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 115baa8d181bd7ebef125e7a666216efe50680d2
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740791"
---
# <a name="configure-same-zone-high-availability-in-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Configuración de la alta disponibilidad en la misma zona en un servidor flexible de Azure Database for MySQL (versión preliminar) mediante la CLI de Azure

Este script de ejemplo de la CLI configura y administra la [alta disponibilidad en la misma zona](../concepts-high-availability.md) en un servidor flexible de Azure Database for MySQL. Actualmente, la alta disponibilidad en la misma zona solo se admite para los planes de tarifa Uso general y Optimizado para memoria.


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/same-zone-ha.sh?highlight=7,10-11 "Configure Same-Zone High Availability.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|Actualiza un servidor flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe scripts adicionales en: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

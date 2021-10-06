---
title: 'Script de la CLI: Creación de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar) y habilitación de la conectividad de acceso público'
description: 'En este script de ejemplo de la CLI de Azure se muestra cómo crear una instancia de Azure Database for MySQL: servidor flexible, configurar una regla de firewall de nivel de servidor (método de conectividad de acceso público) y conectarse al servidor.'
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: cc9710576eef20505a9ae27b74ce93897e7889b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702087"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-preview-and-enable-public-access-connectivity-using-azure-cli"></a>Creación de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar) y habilitación de la conectividad de acceso público mediante la CLI de Azure

En este script de ejemplo de la CLI de Azure se muestra cómo crear una instancia de Azure Database for MySQL: servidor flexible, configurar una regla de firewall de nivel de servidor ([método de conectividad de acceso público](../concepts-networking-public.md)) y conectarse al servidor. 

Una vez que el script se ejecute correctamente, todos los servicios de Azure y la dirección IP configurada podrán acceder al servidor flexible de MySQL, y se conectará al servidor en modo interactivo.

> [!NOTE] 
> El método de conectividad no se puede cambiar después de crear el servidor. Por ejemplo, si crea el servidor con *Public access (allowed IP addresses)* (Acceso público [direcciones IP permitidas]), no podrá cambiar a *Private access (VNet Integration)* (Acceso privado [integración con red virtual]) después. Para más información sobre los métodos de conectividad, consulte [Conceptos de redes](../concepts-networking.md).


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/create-connect-burstable-server-public-access.sh?highlight=8,11-12 "Create Flexible Server and enable public access.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az mysql flexible-server firewall-rule create](/cli/azure/mysql/flexible-server/firewall-rule#az_mysql_flexible_server_firewall_rule_create)|Crea una regla de firewall para permitir el acceso al servidor flexible y sus bases de datos desde el intervalo de direcciones IP especificado.|
|[az mysql flexible-server connect](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_connect)|Permite conectar con un servidor flexible para realizar operaciones de servidor o base de datos.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Elimina un servidor flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe scripts adicionales en: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
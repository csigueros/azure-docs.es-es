---
title: 'Script de la CLI: Creación de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar) en una red virtual'
description: 'En este script de ejemplo de la CLI de Azure se muestra cómo crear una instancia de Azure Database for MySQL: servidor flexible en una red virtual (método de conectividad de acceso privado) y conectarse al servidor desde una máquina virtual dentro de la red virtual.'
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: eef5a814fd25d30a96ad3fd445d30e9daeadc596
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604663"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-preview-in-a-vnet-using-azure-cli"></a>Creación de una instancia de Azure Database for MySQL: servidor flexible (versión preliminar) en una red virtual mediante la CLI de Azure

En este script de ejemplo de la CLI de Azure se muestra cómo crear una instancia de Azure Database for MySQL: servidor flexible en una red virtual ([método de conectividad de acceso privado](../concepts-networking-vnet.md)) y conectarse al servidor desde una máquina virtual dentro de la red virtual.

> [!NOTE] 
> El método de conectividad no se puede cambiar después de crear el servidor. Por ejemplo, si crea el servidor con *Private access (VNet Integration)* (Acceso privado [integración con red virtual]), no podrá cambiar a *Public access (allowed IP addresses)* (Acceso público [direcciones IP permitidas]) después. Para más información sobre los métodos de conectividad, consulte [Conceptos de redes](../concepts-networking.md).

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. 

## <a name="sample-script"></a>Script de ejemplo

Edite las líneas resaltadas en el script con los valores de las variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/create-connect-server-in-vnet.sh?highlight=7,10 "Create and Connect to an Azure Database for MySQL - Flexible Server (General Purpose SKU) in VNet")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el siguiente fragmento de código para limpiar los recursos.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/clean-up-resources.sh "Clean up resources.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos en el que se almacenan todos los recursos.|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crea un servidor flexible que hospeda las bases de datos.|
|[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)|Crea una subred dentro de la red virtual.|
|[az vm create](/cli/azure/vm#az_vm_create)|Crea una máquina virtual de Azure.|
|[az vm open-port](/cli/azure/vm#az_vm_open_port)|Abre una máquina virtual al tráfico entrante en los puertos especificados.|
|[az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados.|

## <a name="next-steps"></a>Pasos siguientes

- Pruebe scripts adicionales en: [Ejemplos de la CLI de Azure para Azure Database for MySQL: servidor flexible (versión preliminar)](../sample-scripts-azure-cli.md).
- Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).
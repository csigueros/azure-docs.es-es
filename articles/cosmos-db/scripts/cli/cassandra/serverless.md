---
title: Creación de una cuenta, un espacio de claves y una tabla sin servidor de Cassandra para Azure Cosmos DB
description: Cree una cuenta, un espacio de claves y una tabla sin servidor de Cassandra para Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 11/15/2021
ms.openlocfilehash: b0cb3dcffc17779f0befb4eb2cb8617628a8191d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725424"
---
# <a name="create-an-azure-cosmos-cassandra-api-serverless-account-keyspace-and-table-using-azure-cli"></a>Creación de una cuenta, un espacio de claves y una tabla sin servidor de Cassandra API para Azure Cosmos mediante la CLI de Azure

[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/serverless.sh "Create an Azure Cosmos DB Cassandra API serverless account, keyspace, and table.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crea una cuenta de Azure Cosmos DB. |
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | Crea un espacio de claves de Cassandra para Azure Cosmos. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | Crea una tabla de Cassandra para Azure Cosmos. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [documentación de la CLI de Azure Cosmos DB](/cli/azure/cosmosdb).

Encontrará más ejemplos de scripts de la CLI de Azure Cosmos DB en el [repositorio de la CLI de Azure Cosmos DB en GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

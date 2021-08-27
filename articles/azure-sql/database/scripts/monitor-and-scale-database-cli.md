---
title: 'CLI de Azure: Supervisión y escalado de una sola base de datos en Azure SQL Database'
description: Use un script de ejemplo de la CLI de Azure para supervisar y escalar una base de datos única en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 06/25/2019
ms.openlocfilehash: 6fa95640ff047882c8c54ab86a37edb602745550
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752248"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Uso de la CLI de Azure para supervisar y escalar una base de datos única en Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este ejemplo de script de la CLI de Azure escala una base de datos única en Azure SQL Database a un tamaño de proceso distinto después de consultar la información del tamaño de la base de datos.

Si decide instalar y usar la CLI de Azure localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de ejemplo

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Ejecute el script.

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Use [az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list) para obtener una lista de las operaciones realizadas en la base de datos y utilice [az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel) para cancelar una operación de actualización en la base de datos.

### <a name="clean-up-deployment"></a>Limpieza de la implementación

Use el siguiente comando para quitar el grupo de recursos y todos los recursos que tenga asociados.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Referencia de ejemplo

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Script | Descripción |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandos de servidor. |
| [az sql db show-usage](/cli/azure/sql#az_sql_show_usage) | Muestra la información de uso del tamaño de una base de datos. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Puede encontrar más ejemplos de scripts de la CLI en [Scripts de ejemplo de la CLI de Azure](../az-cli-script-samples-content-guide.md).

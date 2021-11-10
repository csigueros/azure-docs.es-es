---
title: Administración de réplicas de lectura en Azure Database for PostgreSQL mediante Azure PowerShell
description: Aprenda a configurar y administrar réplicas de lectura en Azure Database for PostgreSQL mediante PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c260f4ba164bfce60bde939bd8dbde10a6659326
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137865"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Creación y administración de réplicas de lectura en Azure Database for PostgreSQL mediante PowerShell

En este artículo aprenderá a crear y administrar réplicas de lectura en el servicio Azure Database for PostgreSQL mediante PowerShell. Para más información acerca de las réplicas de lectura, consulte la [introducción](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Puede crear y administrar réplicas de lectura mediante PowerShell.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía, necesita:

- El [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente o [Azure Cloud Shell](https://shell.azure.com/) en el explorador
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md).

> [!IMPORTANT]
> Mientras el módulo Az.PostgreSql PowerShell se encuentre en versión preliminar, debe instalarlo por separado del módulo Az PowerShell mediante el siguiente comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Una vez que el módulo Az.PostgreSql PowerShell esté disponible con carácter general, formará parte de las futuras versiones del módulo Az PowerShell y estará disponible de forma nativa en Azure Cloud Shell.

Si decide usar PowerShell de forma local, conéctese a su cuenta de Azure con el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> La característica de réplica de lectura solo está disponible para servidores de Azure Database for PostgreSQL en los planes de tarifa de uso general u optimizada para memoria. Asegúrese de que el servidor principal esté en uno de estos planes de tarifa.

### <a name="create-a-read-replica"></a>Creación de una réplica de lectura

Un servidor de réplica de lectura se puede crear mediante el comando siguiente:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

El comando `New-AzPostgreSqlReplica` requiere los siguientes parámetros:

| Configuración | Valor de ejemplo | Descripción  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupo de recursos donde se crea el servidor de réplica.  |
| Nombre | mydemoreplicaserver | Nombre del nuevo servidor de réplica que se crea. |

Para crear una réplica de lectura entre regiones, use el parámetro **Location**. En el siguiente ejemplo, se crea una réplica en la región **Oeste de EE. UU.** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para más información sobre las regiones en las que puede crear una réplica, consulte el [artículo sobre los conceptos de la réplica de lectura](concepts-read-replicas.md).

De forma predeterminada, las réplicas de lectura se crean con la misma configuración de servidor que el servidor principal, a menos que se especifique el parámetro **Sku**.

> [!NOTE]
> Se recomienda mantener la configuración del servidor de réplica con valores iguales o mayores que el principal para asegurarse de que la réplica trabaja al mismo nivel que el servidor maestro.

### <a name="list-replicas-for-a-primary-server"></a>Enumeración de las réplicas de un servidor principal

Para ver todas las réplicas de un servidor principal determinado, ejecute el siguiente comando:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

El comando `Get-AzPostgreSQLReplica` requiere los siguientes parámetros:

| Configuración | Valor de ejemplo | Descripción  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupo de recursos donde se creará el servidor de réplica.  |
| nombreDeServidor | mydemoserver | El nombre o el identificador del servidor principal. |

### <a name="stop-a-replica-server"></a>Detención de un servidor de réplica

La detención de un servidor de réplica de lectura promueve la réplica de lectura para que sea un servidor independiente. Se puede llevar a cabo ejecutando el cmdlet `Update-AzPostgreSqlServer` y estableciendo el valor de ReplicationRole en `None`.

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Eliminación de un servidor de réplica

La eliminación de un servidor de réplica de lectura se puede realizar mediante la ejecución del cmdlet `Remove-AzPostgreSqlServer`.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Eliminación de un servidor principal

> [!IMPORTANT]
> Al eliminar un servidor principal, se detiene la replicación en todos los servidores de réplica y se elimina el propio servidor principal. Los servidores de réplica se convierten en servidores independientes que ahora admiten tanto lectura como escritura.

Para eliminar un servidor principal, puede ejecutar el cmdlet `Remove-AzPostgreSqlServer`.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Reinicio de un servidor de Azure Database for PostgreSQL mediante PowerShell](howto-restart-server-powershell.md).

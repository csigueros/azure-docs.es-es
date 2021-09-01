---
title: Administración de réplicas de lectura en Azure Database for MySQL mediante Azure PowerShell
description: Aprenda a configurar y administrar réplicas de lectura en Azure Database for MySQL mediante PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f4980692be64c2a8b3918d2dbaab5ef9c983f453
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779851"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Creación y administración de réplicas de lectura en Azure Database for MySQL mediante PowerShell

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este artículo aprenderá a crear y administrar réplicas de lectura en el servicio Azure Database for MySQL mediante PowerShell. Para más información acerca de las réplicas de lectura, consulte la [introducción](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Puede crear y administrar réplicas de lectura mediante PowerShell.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía, necesita:

- El [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente o [Azure Cloud Shell](https://shell.azure.com/) en el explorador
- Un [servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mientras el módulo Az.MySql PowerShell se encuentra en versión preliminar, debe instalarlo por separado desde el módulo Az PowerShell con el siguiente comando: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Una vez que el módulo Az.MySql PowerShell esté disponible con carácter general, formará parte de las futuras versiones del módulo Az PowerShell y estará disponible de forma nativa en Azure Cloud Shell.

Si decide usar PowerShell de forma local, conéctese a su cuenta de Azure con el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> La característica de réplica de lectura solo está disponible para servidores de Azure Database for MySQL en los planes de tarifa De uso general u Optimizada para memoria. Asegúrese de que el servidor de origen esté en uno de estos planes de tarifa.
>
>Si GTID está habilitado en un servidor principal (`gtid_mode` = ON), las réplicas recién creadas también tendrán GTID habilitado y usarán la replicación basada en GTID. Para más información, consulte [Identificador de transacción global (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid).

### <a name="create-a-read-replica"></a>Creación de una réplica de lectura

> [!IMPORTANT]
> Cuando se crea una réplica para un origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad.

Un servidor de réplica de lectura se puede crear mediante el comando siguiente:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

El comando `New-AzMySqlReplica` requiere los siguientes parámetros:

| Configuración | Valor de ejemplo | Descripción  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupo de recursos donde se crea el servidor de réplica.  |
| Nombre | mydemoreplicaserver | Nombre del nuevo servidor de réplica que se crea. |

Para crear una réplica de lectura entre regiones, use el parámetro **Location**. En el siguiente ejemplo, se crea una réplica en la región **Oeste de EE. UU.** .

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

> [!NOTE]
> Para más información sobre las regiones en las que puede crear una réplica, consulte el [artículo sobre los conceptos de la réplica de lectura](concepts-read-replicas.md). 

De forma predeterminada, las réplicas de lectura se crean con la misma configuración de servidor que el origen, a menos que se especifique el parámetro **Sku**.

> [!NOTE]
> Se recomienda mantener la configuración del servidor de réplica con valores iguales o mayores que el origen para asegurarse de que la réplica trabaja al mismo nivel que el servidor maestro.

### <a name="list-replicas-for-a-source-server"></a>Enumeración de las réplicas de un servidor de origen

Para ver todas las réplicas de un determinado servidor de origen, ejecute el siguiente comando:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

El comando `Get-AzMySqlReplica` requiere los siguientes parámetros:

| Configuración | Valor de ejemplo | Descripción  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Grupo de recursos donde se creará el servidor de réplica.  |
| nombreDeServidor | mydemoserver | El nombre o el identificador del servidor de origen. |

### <a name="delete-a-replica-server"></a>Eliminación de un servidor de réplica

La eliminación de un servidor de réplica de lectura se puede realizar mediante la ejecución del cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Eliminación de un servidor de origen

> [!IMPORTANT]
> Al eliminar un servidor de origen, se detiene la replicación en todos los servidores de réplica y se elimina el propio servidor de origen. Los servidores de réplica se convierten en servidores independientes que ahora admiten tanto lectura como escritura.

Para eliminar un servidor de origen, puede ejecutar el cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

### <a name="known-issue"></a>Problema conocido

Hay dos generaciones de almacenamiento que usan los servidores de los niveles De uso general y Optimizado para memoria, Almacenamiento de uso general v1 (admite hasta 4 TB) y Almacenamiento de uso general v2 (admite almacenamiento de hasta 16 TB).
El servidor de origen y el servidor de réplica deben tener el mismo tipo de almacenamiento. Como el [Almacenamiento de uso general v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) no está disponible en todas las regiones, asegúrese de elegir la región de réplica correcta mientras usa la ubicación con PowerShell para la creación de réplicas de lectura. Para saber cómo identificar el tipo de almacenamiento del servidor de origen, consulte el vínculo [¿Cómo puedo determinar en qué tipo de almacenamiento se ejecuta mi servidor?](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on). 

Si elige una región en la que no puede crear una réplica de lectura para el servidor de origen, se producirá el problema por el que la implementación seguirá ejecutándose como se muestra en la ilustración siguiente y, a continuación, se agotará el tiempo de espera con el error *"La operación de aprovisionamiento de recursos no se ha completado dentro del período de tiempo de espera permitido"* .

[ :::image type="content" source="media/howto-read-replicas-powershell/replcia-ps-known-issue.png" alt-text="Error de la CLI de réplica de lectura":::](media/howto-read-replicas-powershell/replcia-ps-known-issue.png#lightbox)


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Reinicio de un servidor de Azure Database for MySQL mediante PowerShell](howto-restart-server-powershell.md)

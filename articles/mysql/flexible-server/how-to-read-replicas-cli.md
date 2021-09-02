---
title: Administre réplicas de lectura en el servidor flexible de Azure Database for MySQL mediante la CLI de Azure.
description: Aprenda a configurar y administrar réplicas de lectura en el servidor flexible de Azure Database for MySQL mediante la CLI de Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: adffb4edf7f689002cab7eae86388ff18ac04027
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652063"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Creación y administración de réplicas de lectura en el servidor flexible de Azure Database for MySQL mediante la CLI de Azure

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Réplicas de lectura en Azure Database for MySQL: servidor flexible en versión preliminar.

En este artículo, obtendrá información sobre cómo crear y administrar las réplicas de lectura en el servidor flexible de Azure Database for MySQL mediante la CLI de Azure. Para más información acerca de las réplicas de lectura, consulte la [introducción](concepts-read-replicas.md).

> [!Note]
>
> * La réplica no se admite en el servidor con alta disponibilidad habilitada. 
>
> * Si GTID está habilitado en un servidor principal (`gtid_mode` = ON), las réplicas recién creadas también tendrán GTID habilitado y usarán la replicación basada en GTID. Para más información, consulte [Identificador de transacción global (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid).

## <a name="azure-cli"></a>Azure CLI

Puede crear y administrar réplicas de lectura mediante la CLI de Azure.

### <a name="prerequisites"></a>Requisitos previos

- [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli)
- Un [servidor flexible de Azure Database for MySQL](quickstart-create-server-cli.md) que se usará como servidor de origen.

### <a name="create-a-read-replica"></a>Creación de una réplica de lectura

> [!IMPORTANT]
>Cuando se crea una réplica para un origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad.

Un servidor de réplica de lectura se puede crear mediante el comando siguiente:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Las réplicas de lectura se crean con la misma configuración de servidor que el origen. Una vez creado, se puede cambiar la configuración del servidor de réplica. El servidor de réplica siempre se crea en el mismo grupo de recursos, en la misma ubicación y en la misma suscripción que el servidor de origen. Si desea crear un servidor réplica en otro grupo de recursos o en una suscripción diferente, puede [mover el servidor réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) después de la creación. Se recomienda mantener la configuración del servidor de réplica con valores iguales o mayores que el de origen para asegurarse de que la réplica funciona al mismo nivel que el servidor de origen.


### <a name="list-replicas-for-a-source-server"></a>Enumeración de las réplicas de un servidor de origen

Para ver todas las réplicas de un determinado servidor de origen, ejecute el siguiente comando: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Detención de la replicación en un servidor de réplica

> [!IMPORTANT]
>La detención la replicación en un servidor es irreversible. Una vez detenida la replicación entre un origen y una réplica, la operación no se puede deshacer. Después, el servidor de réplica se convierte en un servidor independiente que admite operaciones de lectura y escritura. Este servidor no puede volver a convertirse en una réplica.

La replicación de un servidor de réplica de lectura se puede detener mediante el comando siguiente:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Eliminación de un servidor de réplica

La eliminación de un servidor de réplica de lectura se puede realizar mediante la ejecución del comando **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Eliminación de un servidor de origen

> [!IMPORTANT]
>Al eliminar un servidor de origen, se detiene la replicación en todos los servidores de réplica y se elimina el propio servidor de origen. Los servidores de réplica se convierten en servidores independientes que ahora admiten tanto lectura como escritura.

Para eliminar un servidor de origen, puede ejecutar el comando **[az mysql flexible-server delete](/cli/azure/mysql/flexible-server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [las réplicas de lectura](concepts-read-replicas.md)
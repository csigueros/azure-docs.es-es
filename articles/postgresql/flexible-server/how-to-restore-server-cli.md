---
title: 'Restauración de Azure Database for PostgreSQL: servidor flexible con la CLI de Azure'
description: En este artículo se explica cómo realizar operaciones de restauración en Azure Database for PostgreSQL mediante la CLI de Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 05/29/2021
ms.openlocfilehash: f14bae80cad6fd9fc6ee534e8c8d1d9b6df617db
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026717"
---
# <a name="point-in-time-restore-of-a-azure-database-for-postgresql---flexible-server-with-azure-cli"></a>Recuperación a un momento dado de una instancia de Azure Database for PostgreSQL: servidor flexible con la CLI de Azure


> [!IMPORTANT]
> Actualmente, Servidor flexible de Azure Database for PostgreSQL se encuentra en versión preliminar.

En este artículo se proporciona un procedimiento detallado para llevar a cabo recuperaciones a un momento dado en servidores flexibles mediante copias de seguridad.

## <a name="prerequisites"></a>Requisitos previos
- Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
- Instale la CLI de Azure más reciente o actualice la que ya tiene a la versión más reciente. Consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).
-  Inicie sesión en la cuenta de Azure mediante el comando [az login](/cli/azure/reference-index#az_login). Tenga en cuenta la propiedad **id**, que hace referencia al **identificador de suscripción** para su cuenta de Azure.

    ```azurecli-interactive
    az login
    ````

- Si tiene varias suscripciones, elija la más adecuada en la que quiera crear el servidor mediante el comando ```az account set```.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Si aún no ha creado un servidor flexible de PostgreSQL, hágalo ahora con el comando ```az postgres flexible-server create```.

    ```azurecli
    az postgres flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Restauración de un servidor de la copia de seguridad en un nuevo servidor

Puede ejecutar el siguiente comando para restaurar un servidor de la copia de seguridad existente más temprana.

**Uso**
```azurecli
az postgres flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Ejemplo:** Restauración de un servidor a partir de esta instantánea de copia de seguridad ```2021-03-03T13:10:00Z```.

```azurecli
az postgres server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-05-05T13:10:00Z" \
--source-server mydemoserver
```

El tiempo que lleve la restauración dependerá del tamaño de los datos almacenados en el servidor.

## <a name="perform-post-restore-tasks"></a>Tareas posteriores a la restauración
Cuando finalice la restauración, debe realizar las siguientes tareas para que los usuarios y las aplicaciones vuelvan a estar en funcionamiento:

- Si el nuevo servidor está destinado a reemplazar al servidor original, redirija los clientes y las aplicaciones de cliente al nuevo servidor.
- Asegúrese de que haya vigentes reglas de red virtual adecuadas para que los usuarios se conecten. Estas reglas no se copian desde el servidor original.
- No se olvide de emplear los permisos de nivel de base de datos y los inicios de sesión apropiados.
- Configuración de alertas según corresponda para el servidor recién restaurado

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la [continuidad empresarial](concepts-business-continuity.md)
* Más información sobre las [copias de seguridad y la recuperación](concepts-backup-restore.md)  


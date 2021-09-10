---
title: 'Tutorial: Replicación geográfica y conmutación por error en el portal'
description: Aprenda a configurar la replicación geográfica en una base de datos SQL con Azure Portal o la CLI de Azure, y a iniciar la conmutación por error.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: e94b9cb0628fca112f3bb9181035d6d60bbd7b74
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866760"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-azure-sql-database"></a>Tutorial: Configuración de la replicación geográfica activa y la conmutación por error (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En este artículo, aprenderá a configurar la [replicación geográfica activa en Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) utilizando [Azure Portal](https://portal.azure.com) o la CLI de Azure y a iniciar la conmutación por error.

Para ver los procedimientos recomendados para usar grupos de conmutación por error automática, consulte [Procedimientos recomendados para Azure SQL Database](auto-failover-group-overview.md#best-practices-for-sql-database) y [Procedimientos recomendados para Instancia administrada de Azure SQL](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Requisitos previos

# <a name="portal"></a>[Portal](#tab/portal)

Para configurar la replicación geográfica activa mediante Azure Portal, necesita el siguiente recurso:

* Una base de datos de Azure SQL Database: la base de datos principal que quiere replicar en una región geográfica diferente.

> [!Note]
> Al usar Azure Portal, solo se puede crear una base de datos secundaria en la misma suscripción que la principal. Si una base de datos secundaria debe estar en una suscripción diferente, use la [API de REST Create Database](/rest/api/sql/databases/createorupdate) o la [API de Transact-SQL ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para configurar la replicación geográfica activa, necesita una base de datos de Azure SQL Database. Esta base de datos será la base de datos principal que va a replicar en una región geográfica diferente.

Prepare el entorno para la CLI de Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="add-a-secondary-database"></a>Adición de una base de datos secundaria

Los pasos siguientes crean otra base de datos secundaria en una asociación de replicación geográfica.  

Para agregar una base de datos secundaria, debe ser el propietario o copropietario de la suscripción.

La base de datos secundaria tiene el mismo nombre que la principal y, de forma predeterminada, presentan el mismo nivel de servicio y tamaño de proceso. La base de datos secundaria puede ser una base de datos única o una base de datos agrupada. Para obtener más información, consulte los artículos sobre el [modelo de compra basado en DTU](service-tiers-dtu.md) y el [modelo de compra basado en núcleo virtual](service-tiers-vcore.md).
Después de crear e inicializar la base de datos secundaria, los datos comienzan a replicarse desde la base de datos principal a la nueva base de datos secundaria.

> [!NOTE]
> Si la base de datos asociada ya existe (por ejemplo, como resultado de la terminación de una relación de replicación geográfica anterior), se produce un error en el comando.

# <a name="portal"></a>[Portal](#tab/portal)

1. En [Azure Portal](https://portal.azure.com), vaya a la base de datos que desea configurar para replicación geográfica.
2. En la página SQL Database, seleccione la base de datos, desplácese hasta **Administración de datos**, seleccione **Réplicas** y haga clic en **Crear réplica.**

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-cli-create-geo-replica.png" alt-text="Configuración de la replicación geográfica":::

3. Seleccione o cree el servidor de la base de datos secundaria y configure las opciones de **Proceso y almacenamiento**, si es necesario. Puede seleccionar cualquier región para el servidor secundario, pero es recomendable que utilice la [región emparejada](../../best-practices-availability-paired-regions.md).

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-create-and-configure-replica.png" alt-text="{alt-text}":::

    También puede agregar una base de datos secundaria a un grupo elástico. Para crear la base de datos secundaria en un grupo, seleccione **Sí** junto a **¿Quiere usar un grupo elástico de SQL?** y seleccione un grupo en el servidor de destino. Ya debe existir un grupo en el servidor de destino. Este flujo de trabajo no crea ningún grupo.

4. Haga clic en **Revisar y crear**, consulte la información y haga clic en **Crear**.
5. Se crea la base de datos secundaria y comienza el proceso de implementación.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-geo-replica-deployment.png" alt-text="Instantánea en la que se muestra el estado de implementación de la base de datos secundaria.":::

6. Cuando se completa la implementación, aparece el estado de la base de datos secundaria.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-sql-database-secondary-status.png" alt-text="Instantánea en la que se muestra el estado de la base de datos secundaria tras su implementación.":::

7. Vuelva a la página de la base de datos principal y seleccione **Réplicas**. La base de datos secundaria aparece en **Réplicas geográficas**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-sql-db-geo-replica-list.png" alt-text="Instantánea en la que se muestra la instancia principal y las réplicas geográficas de la base de datos SQL.":::

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Seleccione la base de datos en la que desee configurar la replicación geográfica. Necesitará la siguiente información:
- El nombre de la base de Azure SQL original
- El nombre del servidor de Azure SQL
- El nombre del grupo de recursos
- El nombre del servidor en el que se va a crear la nueva réplica

> [!NOTE]
> La base de datos secundaria debe tener el mismo nivel de servicio que la principal.

Puede seleccionar cualquier región para el servidor secundario, pero es recomendable que utilice la [región emparejada](../../best-practices-availability-paired-regions.md).

Ejecute el comando [az sql db replica create](/cli/azure/sql/db/replica#az_sql_db_replica_create).

```azurecli
az sql db replica create --resource-group ContosoHotel --server contosoeast --name guestlist --partner-server contosowest --family Gen5 --capacity 2 --secondary-type Geo
```

También puede agregar una base de datos secundaria a un grupo elástico. Para crear la base de datos secundaria en un grupo, use el parámetro `--elastic-pool`. Ya debe existir un grupo en el servidor de destino. Este flujo de trabajo no crea ningún grupo.

Se crea la base de datos secundaria y comienza el proceso de implementación.

Una vez completada la implementación, puede comprobar el estado de la base de datos secundaria ejecutando el comando [az sql db replica list-links](/cli/azure/sql/db/replica#az_sql_db_replica_list-links).
    
```azurecli
az sql db replica list-links --name guestlist --resource-group ContosoHotel --server contosowest
```

---

## <a name="initiate-a-failover"></a>Inicio de una conmutación por error

La base de datos secundaria se puede cambiar para convertirse en la principal.

# <a name="portal"></a>[Portal](#tab/portal)  

1. En [Azure Portal](https://portal.azure.com), vaya a la base de datos principal de la asociación de replicación geográfica.
2. Desplácese hasta **Administración de datos** y seleccione **Réplicas**.
3. En la lista **Réplicas geográficas,** seleccione la base de datos que desea convertir en la nueva principal, el botón de puntos suspensivos y la opción **Conmutación por error forzada.**

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-forced-failover.png" alt-text="Instantánea en la que se muestra cómo seleccionar la conmutación por error forzada en el menú desplegable":::.
4. Haga clic en **Sí** para iniciar la conmutación por error.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Ejecute el comando [az sql db replica set-primary](/cli/azure/sql/db/replica#az_sql_db_replica_set-primary).

```azurecli
az sql db replica set-primary --name guestlist --resource-group ContosoHotel --server contosowest
```

---

El comando cambiará inmediatamente la base de datos secundaria al rol principal. Este proceso debería de tardar 30 segundos o menos.

Mientras se cambian los roles, las dos bases de datos dejarán de estar disponibles (entre 0 y 25 segundos). Si la base de datos principal tiene varias bases de datos secundarias, el comando reconfigura automáticamente las demás secundarias para conectarse a la nueva principal. En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse.

> [!NOTE]
> Este comando está diseñado para una rápida recuperación de la base de datos en el caso de una interrupción. Desencadena la conmutación por error sin sincronización de datos o la conmutación por error forzada.  Si la principal está conectada y confirmando transacciones cuando se emite el comando, es posible que produzca alguna pérdida de datos.

## <a name="remove-secondary-database"></a>Quitar una base de datos secundaria

Esta operación detiene por completo la replicación en la base de datos secundaria y cambia el rol de la base de datos secundaria por el de una base de datos de lectura y escritura normal. Si se interrumpe la conectividad con la base de datos secundaria, el comando se ejecutará correctamente, pero la base de datos secundaria no pasará a ser de lectura y escritura hasta que se restaure la conectividad.

# <a name="portal"></a>[Portal](#tab/portal)  

1. En [Azure Portal](https://portal.azure.com), vaya a la base de datos principal de la asociación de replicación geográfica.
2. Seleccione **Réplicas**.
3. En la lista **Réplicas geográficas**, seleccione la base de datos que desea quitar de la asociación de replicación geográfica, seleccione los puntos suspensivos y haga clic en **Detener replicación**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-stop-replication.png" alt-text="Instantánea en la que se muestra la opción Detener replicación seleccionada en el menú desplegable.":::
5. Se abrirá una ventana de confirmación. Haga clic en **Sí** para quitar la base de datos de la asociación de replicación geográfica. (Establezca el valor en una base de datos de lectura y escritura que no forme parte de ninguna replicación).
 
# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Ejecute el comando [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete-link).

```azurecli
az sql db replica delete-link --name guestlist --resource-group ContosoHotel --server contosoeast --partner-server contosowest
```

Confirme que desea realizar la operación.

---

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre la replicación geográfica activa, consulte [Replicación geográfica activa](active-geo-replication-overview.md).
* Para información sobre los grupos de conmutación por error automática, consulte [Grupos de conmutación por error automática](auto-failover-group-overview.md).
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](business-continuity-high-availability-disaster-recover-hadr-overview.md).
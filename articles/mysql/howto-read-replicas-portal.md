---
title: Administración de réplicas de lectura (Azure Portal) - Azure Database for MySQL
description: Aprenda a crear y administrar réplicas de lectura en Azure Database for MySQL mediante Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: a1e8f9975a6b87767a1c8dfef6603d21a3fe80ec
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652091"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Procedimiento para crear y administrar réplicas de lectura en Azure Database for MySQL mediante Azure Portal

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este artículo, obtendrá información sobre cómo crear y administrar las réplicas de lectura en el servicio Azure Database for MySQL mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

- Un [servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que se usará como servidor de origen.

> [!IMPORTANT]
> La característica de réplica de lectura solo está disponible para servidores de Azure Database for MySQL en los planes de tarifa De uso general u Optimizada para memoria. Asegúrese de que el servidor de origen esté en uno de estos planes de tarifa.

## <a name="create-a-read-replica"></a>Creación de una réplica de lectura

> [!IMPORTANT]
> Cuando se crea una réplica para un origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad.
>
>Si GTID está habilitado en un servidor principal (`gtid_mode` = ON), las réplicas recién creadas también tendrán GTID habilitado y usarán la replicación basada en GTID. Para más información, consulte [Identificador de transacción global (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid).

Para crear un servidor de réplica de lectura, puede seguir estos siguientes pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Seleccione el servidor de Azure Database for MySQL existente que desea utilizar como servidor maestro. Esta acción abre la página **Información general**.

3. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.

4. Seleccione **Agregar réplica**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL: Replicación":::

5. Escriba un nombre para el servidor de réplica.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL: nombre de réplica":::

6. Seleccione la ubicación del servidor de réplica. La ubicación predeterminada es la misma que la del servidor de origen.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL: ubicación de la réplica":::

   > [!NOTE]
   > Para más información sobre las regiones en las que puede crear una réplica, consulte el [artículo sobre los conceptos de la réplica de lectura](concepts-read-replicas.md). 

7. Seleccione **Aceptar** para confirmar la creación de la réplica.

> [!NOTE]
> Las réplicas de lectura se crean con la misma configuración de servidor que el servidor maestro. Una vez creado, se puede cambiar la configuración del servidor de réplica. El servidor de réplica siempre se crea en el mismo grupo de recursos y en la misma suscripción que el servidor de origen. Si desea crear un servidor réplica en otro grupo de recursos o en una suscripción diferente, puede [mover el servidor réplica](../azure-resource-manager/management/move-resource-group-and-subscription.md) después de la creación. Se recomienda mantener la configuración del servidor de réplica con valores iguales o mayores que el origen para asegurarse de que la réplica trabaja al mismo nivel que el servidor maestro.

Una vez creado el servidor de réplica, puede verlo en la hoja **Replicación**.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL: Lista de réplicas":::

## <a name="stop-replication-to-a-replica-server"></a>Detención de la replicación en un servidor de réplica

> [!IMPORTANT]
> La detención la replicación en un servidor es irreversible. Una vez detenida la replicación entre un origen y una réplica, la operación no se puede deshacer. Después, el servidor de réplica se convierte en un servidor independiente que admite operaciones de lectura y escritura. Este servidor no puede volver a convertirse en una réplica.

Para detener la replicación entre un servidor de origen y un servidor de réplicas desde Azure Portal, siga estos pasos:

1. En Azure Portal, seleccione el servidor de Azure Database for MySQL como origen. 

2. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.

3. Seleccione el servidor de réplica para el que desea detener la replicación.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL: Seleccionar el servidor para detener la replicación":::

4. Seleccione **Detener replicación**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL: Detener replicación":::

5. Para confirmar que desea detener la replicación, haga clic en **Aceptar**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL: Confirmar la detención de la replicación":::

## <a name="delete-a-replica-server"></a>Eliminación de un servidor de réplica

Para eliminar un servidor de réplica de lectura en Azure Portal, siga estos pasos:

1. En Azure Portal, seleccione el servidor de Azure Database for MySQL como origen.

2. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.

3. Seleccione el servidor de réplica que desea eliminar.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL: Seleccionar el servidor para eliminar las réplicas":::

4. Seleccione **Eliminar réplica**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL: Eliminar réplica":::

5. Escriba el nombre de la réplica y haga clic en **Eliminar** para confirmar la eliminación.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL: Confirmar la eliminación de la réplica":::

## <a name="delete-a-source-server"></a>Eliminación de un servidor de origen

> [!IMPORTANT]
> Al eliminar un servidor de origen, se detiene la replicación en todos los servidores de réplica y se elimina el propio servidor de origen. Los servidores de réplica se convierten en servidores independientes que ahora admiten tanto lectura como escritura.

Para eliminar un servidor de origen en Azure Portal, siga estos pasos:

1. En Azure Portal, seleccione el servidor de Azure Database for MySQL como origen.

2. En la página **Información general**, seleccione **Eliminar**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL: Eliminar servidor maestro":::

3. Escriba el nombre del servidor de origen y haga clic en **Eliminar** para confirmar la eliminación.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL: confirmación de eliminación del servidor maestro":::

## <a name="monitor-replication"></a>Supervisión de la replicación

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de réplica de Azure Database for MySQL que desea supervisar.

2. En la sección **Supervisión** de la barra lateral, seleccione **Métricas**:

3. Seleccione **Intervalo de replicación en segundos** en la lista desplegable de métricas disponibles.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Seleccionar el intervalo de replicación":::

4. Seleccione el intervalo de tiempo que desea ver. En la imagen siguiente se ha seleccionado un intervalo de tiempo de 30 minutos.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Seleccionar intervalo de tiempo":::

5. Ver el intervalo de replicación para el intervalo de tiempo seleccionado. En la imagen siguiente se muestran los últimos 30 minutos.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Seleccionar un intervalo de tiempo de 30 minutos":::

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [las réplicas de lectura](concepts-read-replicas.md)
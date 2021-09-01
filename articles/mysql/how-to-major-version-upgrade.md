---
title: Actualización de versión principal de Azure Database for MySQL con un servidor único
description: En este artículo se describe cómo actualizar la versión principal de Azure Database for MySQL con un servidor único.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 0ab9eb86f6a482e167d5476205143a1e2f76ad66
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652131"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Actualización de versión principal de Azure Database for MySQL con un servidor único

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

> [!NOTE]
> Este artículo contiene referencias al término _esclavo_, un término que Microsoft ya no usa. Cuando el término se elimine del software, se eliminará también de este artículo.
>

> [!IMPORTANT]
> La actualización de versión principal para Azure Database for MySQL con servidor único se encuentra en versión preliminar.

En este artículo se describe cómo actualizar la versión principal local de Azure Database for MySQL con un servidor único.

Esta característica permitirá a los clientes realizar actualizaciones en contexto de sus servidores MySQL 5.6 a MySQL 5.7 con el clic de un botón sin necesidad de movimiento de datos o de cualquier cambio en la cadena de conexión de la aplicación.

> [!Note]
> * La actualización de versión principal solo está disponible para la actualización de versión principal de MySQL 5.6 a MySQL 5.7.
> * El servidor no estará disponible durante la operación de actualización. Por lo tanto, se recomienda realizar las actualizaciones durante la ventana de mantenimiento planeado. Puede considerar la posibilidad de [realizar una actualización de la versión principal con un tiempo de inactividad mínimo desde MySQL 5.6 a MySQL 5.7 mediante la réplica de lectura.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Actualización de la versión principal de MySQL 5.6 a MySQL 5.7

Siga estos pasos para realizar la actualización de la versión principal de su servidor de Azure Database for MySQL 5.6 mediante Azure Portal.

> [!IMPORTANT]
> Se recomienda realizar la actualización primero en la copia restaurada del servidor, en lugar de actualizar directamente la copia de producción. Consulte [Procedimientos para realizar una restauración a un momento dado](howto-restore-server-portal.md#point-in-time-restore).

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de Azure Database for MySQL 5.6 existente.

2. En la página **Información general**, haga clic en el botón **Actualizar** de la barra de herramientas.

3. En la sección **Actualizar**, seleccione **Aceptar** para actualizar el servidor de Azure Database for MySQL 5.6 a un servidor 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Opción Actualizar en la información general de Azure Database for MySQL":::

4. Una notificación confirmará que la actualización se ha realizado correctamente.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Actualización de la versión principal de MySQL 5.6 a MySQL 5.7 mediante la CLI de Azure

Siga estos pasos para realizar la actualización de la versión principal de su servidor de Azure Database for MySQL 5.6 mediante la CLI de Azure.

> [!IMPORTANT]
> Se recomienda realizar la actualización primero en la copia restaurada del servidor, en lugar de actualizar directamente la copia de producción. Consulte [Procedimientos para realizar una restauración a un momento dado](howto-restore-server-cli.md#server-point-in-time-restore).

1. Instale la [CLI de Azure para Windows](/cli/azure/install-azure-cli) o use la CLI de Azure en [Azure Cloud Shell](../cloud-shell/overview.md) para ejecutar los comandos de actualización. 
 
   En actualización necesita la versión 2.16.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente. Ejecute az version para buscar cuál es la versión y las bibliotecas dependientes que están instaladas. Para realizar la actualización a la versión más reciente, ejecute az upgrade.

2. Después de iniciar sesión, ejecute el comando [az mysql server upgrade](/cli/azure/mysql/server#az_mysql_server_upgrade):

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   En el símbolo del sistema se muestra el mensaje "-Running". Una vez que ya no se muestre este mensaje, se completa la actualización de la versión.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Realización de una actualización de la versión principal de MySQL 5.6 a MySQL 5.7 en una réplica de lectura mediante Azure Portal

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de réplica de lectura de Azure Database for MySQL 5.6 existente.

2. En la página **Información general**, haga clic en el botón **Actualizar** de la barra de herramientas.

3. En la sección **Actualizar**, seleccione **Aceptar** para actualizar el servidor de réplica de lectura de Azure Database for MySQL 5.6 a un servidor 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Opción Actualizar en la información general de Azure Database for MySQL":::

4. Una notificación confirmará que la actualización se ha realizado correctamente.

5. En la página **información general** , confirme que la versión del servidor de réplica de lectura de Azure Database for MySQL es la 5.7.

6. Ahora, vaya al servidor principal y [realice una actualización de la versión principal](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) en él.

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Realice una actualización de la versión principal con un tiempo de inactividad mínimo desde MySQL 5.6 a MySQL 5.7 mediante réplicas de lectura

Puede realizar una actualización de la versión principal con un tiempo de inactividad mínimo desde MySQL 5.6 a MySQL 5.7 mediante réplicas de lectura. La idea es actualizar en primer lugar la réplica de lectura del servidor a la versión 5.7 primero y, posteriormente, conmutar por error la aplicación para que apunte a la réplica de lectura y convertirla en una nueva versión principal.

1. En [Azure Portal](https://portal.azure.com/), seleccione la instancia de Azure Database for MySQL 5.6 existente.

2. Cree una [réplica de lectura](./concepts-read-replicas.md#create-a-replica) desde el servidor principal.

3. [Actualice la réplica de lectura](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) a la versión 5.7.

4. Una vez que confirme que el servidor réplica se ejecuta en la versión 5.7, detenga la aplicación para que deje de conectarse al servidor principal.
 
5. Compruebe el estado de la replicación y asegúrese de que la réplica esté actualizada con la principal, de modo que todos los datos estén sincronizados y asegúrese de que no se realicen nuevas operaciones en el servidor principal.

   Llame al comando [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) en el servidor de réplica para ver el estado de replicación.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Si el estado de `Slave_IO_Running` y `Slave_SQL_Running` es "yes" y el valor de `Seconds_Behind_Master` es "0", la replicación funciona correctamente. `Seconds_Behind_Master` indica el tiempo de retraso de la réplica. Si el valor no es "0", significa que la réplica procesa actualizaciones. Una vez que confirme que `Seconds_Behind_Master` es "0", la replicación se puede detener de forma segura.

6. Promocione la réplica de lectura a principal. Para ello debe [detener la replicación](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server).

7. Apunte la aplicación a la nueva réplica principal (réplica anterior) que ejecuta la versión 5.7 del servidor. Cada servidor tiene una cadena de conexión única. Actualice la aplicación para que apunte a la réplica (anterior) en lugar de al origen.

> [!Note]
> Este escenario solo tendrá tiempo de inactividad durante los pasos 4, 5 y 6.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>¿Cuándo estará en disponibilidad general esta característica de actualización, ya que tenemos MySQL versión 5.6 en nuestro entorno de producción y necesitamos actualizar?

La disponibilidad general de esta característica está planeada antes de la retirada de MySQL versión 5.6. Sin embargo, la característica está preparada para producción y es totalmente compatible con Azure, por lo que puede ejecutarla con confianza en su entorno. Como procedimiento recomendado, sugerimos que la ejecute y pruebe primero en una copia restaurada del servidor para que pueda calcular el tiempo de inactividad de la actualización, así como que realice una prueba de compatibilidad de aplicaciones antes de ejecutarla en producción. Para obtener más información, consulte cómo realizar una [restauración a un momento dado](howto-restore-server-portal.md#point-in-time-restore) para crear una copia del servidor en un momento dado. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>¿El servidor tendrá un tiempo de inactividad del servidor? Si es este el caso, ¿cuánto tiempo dura?

Sí, el servidor no estará disponible durante el proceso de actualización, por lo que se recomienda realizar esta operación durante la ventana de mantenimiento planeado. El tiempo de inactividad estimado depende del tamaño de la base de datos, el tamaño de almacenamiento aprovisionado (IOPS aprovisionado) y el número de tablas en la base de datos. El tiempo que demora la actualización es directamente proporcional al número de tablas del servidor. Se espera que las actualizaciones de los servidores de SKU básicos tarden más tiempo en la plataforma de almacenamiento estándar. Para calcular el tiempo de inactividad del entorno de su servidor, se recomienda realizar primero la actualización en la copia restaurada del servidor. Puede considerar la posibilidad de [realizar una actualización de la versión principal con un tiempo de inactividad mínimo desde MySQL 5.6 a MySQL 5.7 mediante réplicas de lectura](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas).

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>¿Qué ocurrirá si decidimos no actualizar el servidor de MySQL versión 5.6 antes del 5 de febrero de 2021?

Todavía podrá seguir ejecutando el servidor de MySQL versión 5.6 como antes. Azure **nunca** realizará una actualización forzada en el servidor. Sin embargo, se aplicarán las restricciones documentadas en la [directiva de versión de Azure Database for MySQL](concepts-version-policy.md).

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de [Directiva de versión de Azure Database for MySQL](concepts-version-policy.md).
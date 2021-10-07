---
title: Administración de Azure Cache for Redis
description: Aprenda a realizar tareas de administración, como a reiniciar y programar actualizaciones, en Azure Cache for Redis.
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: cauribeg
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4532b13389e85be2b506bdc433cf3dc650fdb38
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538885"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Administración de Azure Cache for Redis

En este artículo se explica cómo realizar tareas de administración, como [reiniciar](#reboot) y [programar actualizaciones](#schedule-updates), en las instancias de Azure Cache for Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Reiniciar

A la izquierda, **Reiniciar** le permite reiniciar uno o varios nodos de la memoria caché. Esta funcionalidad de reinicio le permite probar la resistencia de la aplicación en caso de que haya un error de un nodo de la caché.

:::image type="content" source="media/cache-administration/cache-administration-reboot-2.png" alt-text="Captura de pantalla que resalta la opción de menú Arrancar":::.

Seleccione los nodos que se van a reiniciar y seleccione **Reiniciar**.

:::image type="content" source="media/cache-administration/redis-cache-reboot-2.png" alt-text="Captura de pantalla que muestra los nodos que se pueden arrancar":::.

Si tiene una caché premium con la agrupación en clústeres habilitada, puede seleccionar qué particiones de la memoria caché se reiniciarán.

:::image type="content" source="media/cache-administration/redis-cache-reboot-cluster-2.png" alt-text="Captura de pantalla de las opciones de partición":::

Para reiniciar uno o varios nodos de la caché, seleccione los nodos y seleccione **Reiniciar**. Si tiene una caché prémium con la agrupación en clústeres habilitada, seleccione las particiones que se van a reiniciar y seleccione **Reiniciar**. Al cabo de unos minutos, los nodos seleccionados se reiniciarán y volverán a estar en línea poco tiempo después.

El impacto en las aplicaciones cliente varía en función de los nodos que se reinicien.

* **Maestro**: cuando el nodo principal se reinicia, Azure Cache for Redis realiza una conmutación por error en el nodo de la réplica y lo promueve para que se convierta en el principal. Durante esta conmutación por error puede haber un breve intervalo en el que se puede producir un error de conexión a la memoria caché.
* **Réplica**: el reinicio de un nodo de réplica no suele afectar a los clientes de caché.
* **Ambos, principal y réplica**: cuando se reinician ambos nodos de la caché, se pierden todos los datos de la caché y las conexiones a esta producen un error hasta que el nodo principal vuelve a estar en línea. Si ha configurado la [persistencia de datos](cache-how-to-premium-persistence.md), se restaura la copia de seguridad más reciente cuando la caché vuelva a estar en línea. Sin embargo, se perderán las escrituras de caché que se produjeron después de la copia de seguridad más reciente.
* **Nodes of a premium cache with clustering enabled** (Nodos de una caché premium con agrupamiento en clústeres habilitado): cuando reinicie uno o varios nodos de una caché premium con el agrupamiento en clústeres habilitado, el comportamiento de los nodos seleccionados es el mismo que al reiniciar el nodo o nodos correspondientes de una caché sin agrupar.

## <a name="reboot-faq"></a>Preguntas más frecuentes sobre el reinicio

* [¿Qué nodo que debo reiniciar para probar mi aplicación?](#which-node-should-i-reboot-to-test-my-application)
* [¿Puedo reiniciar la memoria caché para borrar las conexiones de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [¿Se pierden los datos de mi memoria caché si reinicio?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [¿Puedo reiniciar la caché con PowerShell, CLI u otras herramientas de administración?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>¿Qué nodo que debo reiniciar para probar mi aplicación?

Para probar la resistencia de la aplicación frente a errores del nodo principal de la memoria caché, reinicie el nodo **Master** (Maestro). Para probar la resistencia de la aplicación frente a errores del nodo de réplica, reinicie el nodo **Réplica**. Para probar la resistencia de la aplicación frente a errores de toda la caché, reinicie ambos nodos, **Both** (Ambos).

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>¿Puedo reiniciar la memoria caché para borrar las conexiones de cliente?

Sí, si reinicia la memoria caché se borran todas las conexiones de cliente. El reinicio puede resultar útil en caso de que estén agotadas todas las conexiones de cliente debido a un error lógico o a un error en la aplicación cliente. Cada plan de tarifa tiene diferentes [límites de conexión de clientes](cache-configure.md#default-redis-server-configuration) para los distintos tamaños y una vez alcanzados, no se aceptan más conexiones de cliente. Reiniciar la memoria caché es una forma de borrar todas las conexiones de cliente.

> [!IMPORTANT]
> Si reinicia la caché para borrar las conexiones de cliente, StackExchange.Redis vuelve a conectarse automáticamente una vez que el nodo de Redis esté de nuevo en línea. Si no se resuelve el problema subyacente, las conexiones de cliente pueden continuar agotándose.
>
>

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>¿Se pierden los datos de mi memoria caché si reinicio?

Si reinicia los nodos **Master** (Maestro) y **Replica** (Réplica), se podrían perder todos los datos de la caché (o de esa partición si utiliza una caché premium con el agrupamiento en clústeres habilitado). Sin embargo, es posible que los datos no se pierdan. Si ha configurado la [persistencia de datos](cache-how-to-premium-persistence.md), se restaura la copia de seguridad más reciente cuando la caché vuelva a estar en línea. Sin embargo, se perderán las escrituras de caché que se han producido después de realizar la copia de seguridad.

Si reinicia solo uno de los nodos, no se suelen perder datos, pero podría pasar. Por ejemplo, si se reinicia el nodo principal y se está escribiendo en la caché, los datos escritos se pierden. Otro escenario de pérdida de datos se produce si reinicia un nodo y el otro nodo deja de funcionar debido a un error simultáneo. Para obtener más información sobre las posibles causas de pérdida de datos, consulte [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (¿Qué ha ocurrido con mis datos en Redis?).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>¿Puedo reiniciar la caché con PowerShell, CLI u otras herramientas de administración?

Sí. Para ver instrucciones de PowerShell, consulte [Reinicio de una instancia de Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Programar actualizaciones

A la izquierda, **Programar actualizaciones** le permite elegir una ventana de mantenimiento para la instancia de caché. Una ventana de mantenimiento le permite controlar los días y las horas de una semana durante los que se pueden actualizar las máquinas virtuales que hospedan la memoria caché. Azure Cache for Redis hará todo lo posible por iniciar y finalizar la actualización del software del servidor de Redis en el período de tiempo especificado que defina.

> [!NOTE]
> La ventana de mantenimiento se aplica a las actualizaciones del servidor de Redis y a las actualizaciones del sistema operativo de las máquinas virtuales que hospedan la memoria caché. La ventana de mantenimiento no se aplica a las actualizaciones del sistema operativo host en los hosts que hospedan las máquinas virtuales de la memoria caché ni otros componentes de redes de Azure. En raras ocasiones, en las que las memorias caché se hospedan en modelos más antiguos (puede saber si la memoria caché está en un modelo anterior si el nombre DNS de la memoria caché se resuelve con el sufijo "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" o "cloudapi.de"), la ventana de mantenimiento no se aplicará a las actualizaciones del sistema operativo invitado.
>
> Actualmente, no hay ninguna opción disponible para configurar un reinicio ni actualizaciones programadas para una caché de nivel Enterprise.
>

:::image type="content" source="media/cache-administration/redis-schedule-updates-2.png" alt-text="Captura de pantalla que muestra las actualizaciones programadas":::

Para especificar una ventana de mantenimiento, compruebe los días deseados y especifique la hora de inicio de la ventana de mantenimiento para cada día. Después, seleccione **Aceptar**. La hora del período de mantenimiento está en formato UTC.

La ventana de mantenimiento predeterminada, y mínima, para las actualizaciones es de cinco horas. Este valor no es configurable desde Azure Portal, pero puede configurarlo en PowerShell mediante el parámetro `MaintenanceWindow` del cmdlet [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry). Para obtener más información, consulte [¿Se pueden administrar las actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Preguntas más frecuentes sobre la programación de actualizaciones

* [¿Cuándo se realizan las actualizaciones si no se usa la característica de programación de actualizaciones?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [¿Qué tipo de actualizaciones se actualizan durante el período programado de mantenimiento?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [¿Se pueden administrar las actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [¿Se puede producir una actualización cubierta y administrada por la característica "Actualizaciones programadas" fuera de la ventana "Actualizaciones programadas"?](#can-an-update-that-is-covered-and-managed-by-the-scheduled-updates-feature-happen-outside-the-scheduled-updates-window)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>¿Cuándo se realizan las actualizaciones si no se usa la característica de programación de actualizaciones?

Si no especifica un período de mantenimiento, las actualizaciones pueden realizarse en cualquier momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>¿Qué tipo de actualizaciones se actualizan durante el período programado de mantenimiento?

Las actualizaciones del servidor Redis solo se realizan durante el período programado de mantenimiento. La ventana de mantenimiento no se aplica a las actualizaciones de Azure ni a las actualizaciones del sistema operativo de la máquina virtual.

### <a name="can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>¿Se pueden administrar las actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?

Sí, puede administrar sus actualizaciones programadas con los siguientes cmdlets de PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="can-an-update-that-is-covered-and-managed-by-the-scheduled-updates-feature-happen-outside-the-scheduled-updates-window"></a>¿Se puede producir una actualización cubierta y administrada por la característica "Actualizaciones programadas" fuera de la ventana "Actualizaciones programadas"?

Sí. En general, las actualizaciones no se aplican fuera de la ventana Actualizaciones programadas configurada. Las actualizaciones de seguridad críticas poco frecuentes se pueden aplicar fuera de la programación de aplicación de revisiones como parte de nuestra directiva de seguridad.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las características de Azure Cache for Redis.

* [Niveles de servicio de Azure Cache for Redis](cache-overview.md#service-tiers)

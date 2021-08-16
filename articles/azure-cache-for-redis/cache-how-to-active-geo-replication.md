---
title: Configuración de la replicación geográfica activa para las instancias de Azure Cache for Redis Enterprise
description: Aprenda a replicar las instancias de Azure Cache for Redis Enterprise entre regiones de Azure.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 1c3bcfea0e703de28c79048380f8389fa93014b3
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110585346"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Configuración de la replicación geográfica activa para las instancias de Azure Cache for Redis Enterprise (versión preliminar)

En este artículo, aprenderá a configurar una instancia de Azure Cache con replicación geográfica activa mediante Azure Portal.

La replicación geográfica activa agrupa dos instancias de Azure Cache for Redis Enterprise en una sola caché que abarca varias regiones de Azure. Ambas instancias actúan como las principales locales. Una aplicación decide qué instancias se van a utilizar para las solicitudes de lectura y escritura.

> [!NOTE]
> La transferencia de datos entre regiones de Azure se cobrará según las [tarifas de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/) estándar.

## <a name="create-or-join-an-active-geo-replication-group"></a>Creación de un grupo de replicación geográfica activa o unión con él

> [!IMPORTANT]
> La replicación geográfica activa debe estar habilitada en el momento en que se crea una caché de Azure Cache for Redis.
>
>

1. En la pestaña **Avanzado** de la interfaz de usuario de creación de **Redis Cache nueva**, seleccione **Enterprise** para la **Directiva de agrupación en clústeres**.

    ![Configuración de replicación geográfica activa](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Haga clic en **Configurar** para configurar **la replicación geográfica activa**.

1. Cree un nuevo grupo de replicación, para una primera instancia de caché, o seleccione uno existente en la lista.

    ![Vincular cachés](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Haga clic en **Configurar** para finalizar.

    ![Replicación geográfica activa configurada](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Espere a que el área de trabajo se cree correctamente. Repita los pasos anteriores para cada instancia de caché adicional en el grupo de replicación geográfica.

## <a name="remove-from-an-active-geo-replication-group"></a>Eliminación de un grupo de replicación geográfica activa

Para quitar una instancia de caché de un grupo de replicación geográfica activa, solo tiene que eliminar la instancia. Las instancias restantes se volverán a configurar automáticamente.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las características de Azure Cache for Redis.

* [Niveles de servicio de Azure Cache for Redis](cache-overview.md#service-tiers)
* [Alta disponibilidad en Azure Cache for Redis](cache-high-availability.md)

---
title: Traslado de instancias de Azure Cache for Redis a regiones diferentes
description: Aprenda a mover instancias de Azure Cache for Redis a otra región de Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 8/27/2021
ms.openlocfilehash: 931c4aec854b2512c649570b5fe8fc24bfe11c81
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894910"
---
# <a name="move-azure-cache-for-redis-instances-to-different-regions"></a>Traslado de instancias de Azure Cache for Redis a regiones diferentes

En este artículo, aprenderá a mover instancias de Azure Cache for Redis a otra región de Azure. Puede mover sus recursos a otra región por varios motivos:
- Para aprovechar las ventajas de una nueva región de Azure.
- Para implementar características o servicios que solo están disponibles en regiones determinadas.
- Para cumplir requisitos internos de gobernanza y directivas.
- Para responder a los requisitos de planeamiento de capacidad.

El nivel de Azure Cache for Redis que usa determina la opción más adecuada para usted.

| Nivel de caché | Opciones  | 
| ------------ |  ------- | 
| Premium | Replicación geográfica, creación de una nueva caché, escritura doble en dos cachés o exportación e importación de datos mediante un archivo RDB| 
| Básico o Estándar | Creación de una nueva caché o escritura doble en dos cachés| 
| Enterprise o Enterprise Flash | Creación de una nueva caché o exportación e importación de datos con un archivo RDB | 

## <a name="geo-replication-premium"></a>Replicación geográfica (Premium)

### <a name="prerequisites"></a>Requisitos previos 

Para configurar la replicación geográfica entre dos cachés, se deben cumplir los siguientes requisitos previos:

- Ambas deben ser cachés de [nivel Premium](cache-overview.md#service-tiers).
- Ambas cachés deben estar en la misma suscripción de Azure.
- La caché vinculada secundaria debe tener el mismo tamaño de caché o un tamaño de caché mayor que la caché vinculada principal.
- Ambas cachés ya existen y se están ejecutando.

### <a name="prepare"></a>Preparación

Para mover la instancia de caché a otra región, debe [crear una segunda instancia de caché Premium](quickstart-create-redis.md) en la región deseada. Una vez que se estén ejecutando ambas cachés, puede configurar la replicación geográfica entre las dos instancias de caché. 

> [!NOTE]
> La transferencia de datos entre regiones de Azure se cobrará según las [tarifas de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/) estándar.

Algunas características no son compatibles con la replicación geográfica:

- La redundancia de zona no es compatible con la replicación geográfica.
- La persistencia no es compatible con la replicación geográfica.

Condiciones para la compatibilidad con replicaciones geográficas:

- Se admite la agrupación en clústeres si ambas cachés tienen la agrupación en clústeres habilitada y tienen el mismo número de particiones.
- Las memorias caché de distintas redes virtuales son compatibles con advertencias. Consulte [¿Puedo usar la replicación geográfica con cachés en una VNet?](cache-how-to-geo-replication.md#can-i-use-geo-replication-with-my-caches-in-a-vnet) para obtener más información.

Una vez que se configura la replicación geográfica, se aplican las siguientes restricciones al par de cachés vinculadas:

- La caché vinculada secundaria es de solo lectura. Puede leer desde ella, pero no puede escribir ningún dato en ella. 
    - Si decide leer desde la instancia de replicación geográfica secundaria, cada vez que se produce una sincronización de datos completa entre las instancias principal y secundaria de replicación geográfica (como cuando se actualiza alguna de las dos y también en algunos escenarios de reinicio), la instancia secundaria de replicación geográfica genera errores en cualquier operación de Redis que realice en ella hasta que se complete la sincronización de datos completa entre las instancias principal y secundaria de replicación geográfica.
    - Las aplicaciones que leen desde la instancia de replicación geográfica secundaria deben crearse para revertir a la instancia de replicación geográfica principal cada vez que la secundaria genere tales errores.
- Se quitarán los datos existentes en la caché vinculada secundaria antes de la vinculación. Sin embargo, si la replicación geográfica se quita posteriormente, los datos replicados permanecen en la caché vinculada secundaria.
- No puede [escalar](cache-how-to-scale.md) ninguna de las cachés mientras están vinculadas.
- No puede [cambiar el número de particiones](cache-how-to-premium-clustering.md) si la caché tiene la agrupación en clústeres habilitada.
- No puede habilitar la persistencia en ninguna de las cachés.
- Puede [exportar](cache-how-to-import-export-data.md#export) desde cualquier caché.
- No puede [importar](cache-how-to-import-export-data.md#import) en la caché vinculada secundaria.
- No puede eliminar la caché vinculada, ni el grupo de recursos que las contiene, hasta que se desvinculen las cachés. Para más información, vea [¿Por qué no se pudo realizar la operación al intentar eliminar la memoria caché vinculada?](cache-how-to-geo-replication.md#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Si las memorias caché se encuentran en regiones diferentes, los costes de salida de red se aplican a los datos que se mueven entre regiones. Para más información, consulte el artículo sobre [cuánto cuesta replicar datos entre regiones de Azure](cache-how-to-geo-replication.md#how-much-does-it-cost-to-replicate-my-data-across-azure-regions).
- La conmutación automática por error no se produce entre la caché vinculada principal y secundaria. Para obtener más información y conocer cómo realizar la conmutación por error de una aplicación cliente, consulte [¿Cómo funciona la conmutación por error a la caché vinculada secundaria?](cache-how-to-geo-replication.md#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="move"></a>Move

1. Para vincular dos cachés para la replicación geográfica, primero haga clic en **Replicación geográfica** en el menú Recursos de la caché que vaya a ser la caché vinculada principal. A continuación, haga clic en **Agregar vínculo de replicación de caché** desde **Replicación geográfica** en la izquierda.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-menu.png" alt-text="Agregar vínculo":::

1. Seleccione el nombre de la caché secundaria deseada en la lista **Cachés compatibles**. Si la caché secundaria no aparece en la lista, compruebe que se cumplen los [requisitos previos de replicación geográfica](#prerequisites) para la caché secundaria. Para filtrar las cachés por región, seleccione la región en el mapa para mostrar solo esas cachés en la lista de **cachés disponibles**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link.png" alt-text="Cachés compatibles de replicación geográfica":::

    También puede iniciar el proceso de vinculación o ver detalles sobre la caché secundaria mediante el menú contextual.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png" alt-text="Menú contextual de replicación geográfica":::

1. Seleccione **Vincular** para vincular las dos cachés y comenzar el proceso de replicación.
   
    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png" alt-text="Vincular cachés":::

### <a name="verify"></a>Comprobar

1. Puede ver el progreso del proceso de replicación por medio de **Replicación geográfica** en la parte izquierda.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-linking.png" alt-text="Estado de la vinculación":::

    También puede consultar el estado de la vinculación en la parte izquierda, desde **Información general**, para las dos cachés, la principal y la secundaria.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-status.png" alt-text="Captura de pantalla que destaca cómo ver el estado de vinculación de las memorias caché principales y secundarias.":::

    Una vez que se completa el proceso de replicación, el **estado del vínculo** cambia a **Correcto**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-successful.png" alt-text="Estado de cachés":::

    La caché vinculada principal sigue estando disponible para su uso durante el proceso de vinculación. La caché vinculada secundaria no está disponible hasta que se complete el proceso de vinculación.

### <a name="clean-up-source-resources"></a>Limpieza de los recursos de origen 

Una vez que la nueva caché de la región de destino se rellene con todos los datos necesarios, quite el vínculo entre las dos cachés y elimine la instancia original.

1. Para quitar el vínculo entre dos cachés y detener la replicación geográfica, haga clic en **Desvincular cachés** desde **Replicación geográfica** en la parte izquierda.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-unlink.png" alt-text="Desvincular cachés":::

    Una vez que se completa el proceso de desvinculación, la caché secundaria queda disponible tanto para lecturas como para escrituras.

>[!NOTE]
>Cuando se quita el vínculo de replicación geográfica, los datos replicados de la caché vinculada principal se mantienen en la caché secundaria.
>
>

2. Elimine la instancia original.

## <a name="create-a-new-cache-all-tiers"></a>Creación de una nueva caché (todos los niveles)

### <a name="prerequisites"></a>Requisitos previos
- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)

### <a name="prepare"></a>Preparación
Si no necesita mantener los datos durante el traslado, la manera más fácil de mover entre regiones es crear una nueva instancia de caché en la región de destino y conectar la aplicación a ella. Por ejemplo, si usa Redis como una caché de búsqueda de registros de base de datos, puede volver a generar fácilmente la memoria caché desde cero.

### <a name="move"></a>Move

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

Por último, actualice la aplicación para que use las nuevas instancias. 

### <a name="clean-up-source-resources"></a>Limpieza de los recursos de origen 
Una vez que se esté ejecutando la nueva caché en la región de destino, elimine la instancia original.


## <a name="export-and-import-data-with-an-rdb-file-premium-enterprise-enterprise-flash"></a>Exportación e importación de datos con un archivo RDB (Premium, Enterprise y Enterprise Flash)
La instancia de Redis de código abierto define un mecanismo estándar para tomar una instantánea del conjunto de datos en memoria de la caché y guardarla en un archivo. Otra caché en Redis puede leer este archivo, denominado RDB. [Los niveles Premium y Enterprise de Azure Cache for Redis](cache-overview.md#service-tiers) admiten la importación de datos en una instancia de caché mediante archivos RDB. Puede usar un archivo RDB para transferir datos de una caché existente a Azure Cache for Redis.

> [!IMPORTANT]
> El formato de archivo RDB puede presentar cambios entre versiones de Redis y podría no mantener la compatibilidad con versiones anteriores. La versión de Redis de la memoria caché desde la que se exporta debe ser la misma o menor que la versión de la nueva instancia de caché.
>

### <a name="prerequisites"></a>Requisitos previos
- Ambas cachés deben ser de [nivel Premium o Enterprise](cache-overview.md#service-tiers).
- La segunda caché debe tener el mismo tamaño de caché o un tamaño de caché mayor que la caché original.
- La versión de Redis de la memoria caché desde la que se exporta debe ser la misma o menor que la versión de la nueva instancia de caché.

### <a name="prepare"></a>Preparación
Para mover la instancia de caché a otra región, debe crear [una segunda instancia de caché Premium](quickstart-create-redis.md) o [una segunda instancia de caché Enterprise](quickstart-create-redis-enterprise.md) en la región deseada.

### <a name="move"></a>Move
1. Consulte [este artículo](cache-how-to-import-export-data.md) para obtener más información sobre cómo importar y exportar datos en Azure Cache for Redis.

2. Actualice la aplicación para que use la nueva instancia de caché.

### <a name="verify"></a>Comprobar
Puede supervisar el progreso de la operación de importación siguiendo las notificaciones de Azure Portal o viendo los eventos en el [registro de auditoría](../azure-monitor/essentials/activity-log.md).

### <a name="clean-up-source-resources"></a>Limpieza de los recursos de origen 
Una vez que se esté ejecutando la nueva caché en la región de destino, elimine la instancia original.

## <a name="dual-write-to-two-caches-basic-standard-and-premium"></a>Escritura doble en dos cachés (Básico, Estándar y Premium)
En lugar de mover los datos directamente entre cachés, puede usar su aplicación para escribir datos en una caché existente y en una nueva que esté configurando. Inicialmente, la aplicación lee los datos de la caché existente. Cuando la nueva caché tenga los datos necesarios, la aplicación pasará a esa caché y se retirará la anterior. Supongamos, por ejemplo, que usa Redis como almacén de sesión y las sesiones de la aplicación son válidas durante siete días. Después de escribir en las dos cachés durante una semana, estará seguro de que la nueva caché contiene toda la información de sesión que no ha expirado. Podrá confiar en ella con total seguridad a partir de ese momento sin preocuparse por la pérdida de datos.

### <a name="prerequisites"></a>Requisitos previos
- La segunda caché debe tener el mismo tamaño de caché o un tamaño de caché mayor que la caché original.

### <a name="prepare"></a>Preparación
Para mover la instancia de caché a otra región, debe [crear una segunda instancia de caché](quickstart-create-redis.md) en la región deseada.

### <a name="move"></a>Move
Los pasos generales para implementar esta opción son los siguientes:

1. Modifique el código de la aplicación para que escriba tanto en la instancia nueva como en la original.

2. Siga leyendo los datos de la instancia original hasta que la instancia nueva se haya rellenado con suficientes datos.

3. Actualice el código de la aplicación para que lea y escriba únicamente desde la nueva instancia.

### <a name="clean-up-source-resources"></a>Limpieza de los recursos de origen 
Una vez que se esté ejecutando la nueva caché en la región de destino, elimine la instancia original.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las características de Azure Cache for Redis.
- [Preguntas más frecuentes sobre la replicación geográfica](cache-how-to-geo-replication.md#geo-replication-faq)
- [Niveles de servicio de Azure Cache for Redis](cache-overview.md#service-tiers)
- [Alta disponibilidad en Azure Cache for Redis](cache-high-availability.md)



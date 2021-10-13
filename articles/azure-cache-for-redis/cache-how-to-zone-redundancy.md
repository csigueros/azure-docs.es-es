---
title: Habilitación de la redundancia de zona para Azure Cache for Redis
description: Obtenga información sobre cómo configurar la redundancia de zona para el nivel Premium y Enterprise de las instancias de Azure Cache for Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: ee4acc6e8e7abf8ece1c6808a810b022745fb66a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612339"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis"></a>Habilitación de la redundancia de zona para Azure Cache for Redis
En este artículo, aprenderá a configurar una instancia de Azure Cache con redundancia de zona mediante Azure Portal.

Los niveles Estándar, Premium y Enterprise de Azure Cache for Redis hospedan cada caché en dos máquinas virtuales dedicadas para proporcionar redundancia integrada. Aunque estas máquinas virtuales se encuentran en distintos [dominios de error y actualización de Azure](../virtual-machines/availability.md) y tienen alta disponibilidad, son susceptibles a tener errores en el nivel del centro de datos. Azure Cache for Redis también admite la redundancia de zona en los niveles Premium y Enterprise. Una instancia de caché con redundancia de zona se ejecuta en máquinas virtuales distribuidas en varias [instancias de Availability Zones](../availability-zones/az-overview.md). Proporciona mayor resistencia y disponibilidad.

> [!NOTE]
> La transferencia de datos entre Azure Availability Zones se cobrará según las [tarifas de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/) estándar.

## <a name="prerequisites"></a>Prerrequisitos
* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Creación de una caché
Para crear una instancia de caché, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después seleccione **Crear un recurso**.
  
1. En la página **Nuevo**, seleccione **Base de datos** y, a continuación, seleccione **Azure Cache for Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selección de Azure Cache for Redis.":::
   
1. En la página **Basics** (Información básica), configure las opciones de la nueva instancia de caché.
   
    | Configuración      | Valor sugerido  | Descripción |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Suscripción** | Seleccione su suscripción. | La suscripción en la que se creará esta nueva instancia de Azure Cache for Redis. | 
    | **Grupos de recursos** | Seleccione un grupo de recursos o **Crear nuevo**, y escriba un nombre nuevo para el grupo de recursos. | Nombre del grupo de recursos en el que se van a crear la caché y otros recursos. Al colocar todos los recursos de la aplicación en un grupo de recursos, puede administrarlos o eliminarlos fácilmente. | 
    | **Nombre DNS** | Escriba un nombre único global. | El nombre de la memoria caché debe ser una cadena de entre 1 y 63 caracteres, y solo puede contener números, letras o guiones. El nombre debe comenzar y terminar por un número o una letra y no puede contener guiones consecutivos. El *nombre de host* de la instancia de caché será *\<DNS name>.redis.cache.windows.net*. | 
    | **Ubicación** | Seleccione una ubicación. | Seleccione una [región](https://azure.microsoft.com/regions/) cerca de otros servicios que vayan a usar la memoria caché. |
    | **Tipo de caché** | Seleccione una caché de [nivel Premium o Enterprise](https://azure.microsoft.com/pricing/details/cache/). |  El plan de tarifa determina el tamaño, el rendimiento y las características disponibles para la memoria caché. Para más información, consulte la [introducción a Azure Redis Cache](cache-overview.md). |
   
1. En la página **Avanzado**, en el caso de una caché de nivel Premium, elija **Recuento de réplicas**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Recuento de réplicas":::

1. Seleccione **Zonas de disponibilidad**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Zonas de disponibilidad":::

1. Configure los valores para la agrupación en clústeres o la persistencia de RDB.  

    > [!NOTE]
    > La redundancia de zona no admite la persistencia de AOF ni funciona actualmente con la replicación geográfica.
    >

1. Seleccione **Crear**. 
   
    La caché tarda un tiempo en crearse. Puede supervisar el progreso en la página **Información general** de Azure Cache for Redis. Cuando **Estado** se muestra como **En ejecución**, la memoria caché está lista para su uso.
   
    > [!NOTE]
    > Las zonas de disponibilidad no se pueden cambiar ni habilitar una vez creada la instancia de caché. 
    >

## <a name="zone-redundancy-faq"></a>Preguntas más frecuentes sobre la redundancia de zona

- [¿Por qué no puedo habilitar la redundancia de zona al crear una caché Premium?](#why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache)
- [¿Por qué no puedo seleccionar las tres zonas durante la creación de la caché?](#why-cant-i-select-all-three-zones-during-cache-create)
- [¿Puedo actualizar mi caché Premium existente para usar la redundancia de zona?](#can-i-update-my-existing-premium-cache-to-use-zone-redundancy)
- [¿Cuánto cuesta replicar mis datos entre instancias de Azure Availability Zones?](#how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones)

### <a name="why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache"></a>¿Por qué no puedo habilitar la redundancia de zona al crear una caché Premium?

La redundancia de zona solo está disponible en las regiones de Azure que tienen Availability Zones. Consulte [Regiones de Azure con Availability Zones](../availability-zones/az-region.md#azure-regions-with-availability-zones) para obtener la lista más reciente.

### <a name="why-cant-i-select-all-three-zones-during-cache-create"></a>¿Por qué no puedo seleccionar las tres zonas durante la creación de la caché?

Una caché Premium tiene un nodo principal y uno de réplica de forma predeterminada. Para configurar la redundancia de zona para más de dos instancias de Availability Zones, debe agregar [más réplicas](cache-how-to-multi-replicas.md) a la memoria caché que va a crear.

### <a name="can-i-update-my-existing-premium-cache-to-use-zone-redundancy"></a>¿Puedo actualizar mi caché Premium existente para usar la redundancia de zona?

No, en la actualidad no se admite.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones"></a>¿Cuánto cuesta replicar mis datos entre instancias de Azure Availability Zones?

Al usar la redundancia de zona, configurada con varias instancias de Availability Zones, los datos se replican desde el nodo de caché principal de una zona a los otros nodos de otras zonas. El cargo por transferencia de datos es el costo de salida de red de los datos que se mueven a través de las instancias de Availability Zones seleccionadas. Para más información, consulte [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="next-steps"></a>Pasos siguientes
Más información sobre las características de Azure Cache for Redis.

> [!div class="nextstepaction"]
> [Niveles de servicio de Azure Cache for Redis Premium](cache-overview.md#service-tiers)

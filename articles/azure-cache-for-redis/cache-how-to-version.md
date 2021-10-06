---
title: Establecimiento de la versión de Redis para Azure Cache for Redis (versión preliminar)
description: Aprenda cómo configurar la versión de Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 4bcd5b98011c5e65714c3690f90a1c1bcb52ede1
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538090"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Establecimiento de la versión de Redis para Azure Cache for Redis (versión preliminar)
En este artículo, aprenderá a configurar la versión del software de Redis que se usará con la instancia de caché. Azure Cache for Redis ofrece la versión principal más reciente de Redis y al menos una versión anterior. Actualizará estas versiones con regularidad a medida que se publique el software de Redis más reciente. Puede elegir entre las dos versiones disponibles. Tenga en cuenta que la instancia de caché se actualizará a la siguiente versión automáticamente si ya no se admite la versión que usa actualmente.

> [!NOTE]
> Redis 6 se encuentra actualmente en versión preliminar. En este momento, Redis 6 no admite la agrupación en clústeres, la redundancia de zona, ACL, PowerShell, la CLI de Azure, Terraform ni la replicación geográfica entre las versiones de caché de Redis 4.0 y 6.0. Tampoco se puede cambiar la versión de Redis una vez creada una memoria caché. 
>

> [!IMPORTANT]
> Una vez que Redis 6.0 esté disponible con carácter general (GA), esta será la versión predeterminada de Redis para las nuevas cachés. Seguirá teniendo la opción de crear cachés de Redis 4.0. 
>

## <a name="prerequisites"></a>Requisitos previos
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
    | **Tipo de caché** | Seleccione un [nivel y tamaño de caché](https://azure.microsoft.com/pricing/details/cache/). |  El plan de tarifa determina el tamaño, el rendimiento y las características disponibles para la memoria caché. Para más información, consulte la [introducción a Azure Redis Cache](cache-overview.md). |
   
1. En la página **Advanced** (Avanzado), elija la versión de Redis que se va a usar.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Versión de Redis.":::

1. Seleccione **Crear**. 
   
    La caché tarda un tiempo en crearse. Puede supervisar el progreso en la página **Información general** de Azure Cache for Redis. Cuando **Estado** se muestra como **En ejecución**, la memoria caché está lista para su uso.

    > [!NOTE]
    > En este momento, no se puede cambiar la versión de Redis una vez creada la instancia de caché.
    >

## <a name="faq"></a>Preguntas más frecuentes

### <a name="what-features-arent-supported-with-redis-6"></a>¿Qué características no se admiten con Redis 6?

Actualmente, Redis 6 no admite la agrupación en clústeres, la redundancia de zona, ACL, PowerShell, la CLI de Azure, Terraform ni la replicación geográfica entre las versiones de caché de Redis 4.0 y 6.0. 

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>¿Puedo cambiar la versión de la caché después de crearla?

Actualmente, no puede cambiar la versión de la caché una vez que se ha creado.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre las características de Azure Cache for Redis.

> [!div class="nextstepaction"]
> [Niveles de servicio de Azure Cache for Redis Premium](cache-overview.md#service-tiers)

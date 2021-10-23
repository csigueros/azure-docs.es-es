---
title: Establecimiento de la versión de Redis para Azure Cache for Redis
description: Aprenda cómo configurar la versión de Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 41d61ccce3602b1d3e823eb6a4c46afe39159c46
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728242"
---
# <a name="set-redis-version-for-azure-cache-for-redis"></a>Establecimiento de la versión de Redis para Azure Cache for Redis
En este artículo, aprenderá a configurar la versión del software de Redis que se usará con la instancia de caché. Azure Cache for Redis ofrece la versión principal más reciente de Redis y al menos una versión anterior. Actualizará estas versiones con regularidad a medida que se publique el software de Redis más reciente. Puede elegir entre las dos versiones disponibles. Tenga en cuenta que la instancia de caché se actualizará a la siguiente versión automáticamente si ya no se admite la versión que usa actualmente.

> [!NOTE]
> Por el momento, Redis 6 no admite ACL ni la replicación geográfica de una caché entre las versiones de Redis 4 y 6.
>

## <a name="prerequisites"></a>Prerrequisitos
* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)

## <a name="create-a-cache-using-the-azure-portal"></a>Creación de una caché mediante Azure Portal
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


## <a name="create-a-cache-using-azure-powershell"></a>Creación de una caché mediante Azure PowerShell

```azurepowershell
    New-AzRedisCache -ResourceGroupName "ResourceGroupName" -Name "CacheName" -Location "West US 2" -Size 250MB -Sku "Standard" -RedisVersion "6"
```
Para más información sobre cómo administrar Azure Cache for Redis con Azure PowerShell, consulte [aquí](cache-how-to-manage-redis-cache-powershell.md).

## <a name="create-a-cache-using-azure-cli"></a>Creación de una caché mediante la CLI de Azure

```azurecli-interactive
az redis create --resource-group resourceGroupName --name cacheName --location westus2 --sku Standard --vm-size c0 --redisVersion="6"
```
Para más información sobre cómo administrar Azure Cache for Redis con la CLI de Azure, consulte [aquí](cli-samples.md).

## <a name="upgrade-an-existing-redis-4-cache-to-redis-6"></a>Actualización de una caché de Redis 4 existente a Redis 6
Azure Cache for Redis admite la actualización de la versión principal del servidor de caché de Redis 4 a Redis 6. Tenga en cuenta que la actualización es permanente y puede provocar una breve interrupción de la conexión. Como medida de precaución, se recomienda exportar los datos de la caché de Redis 4 existente y probar la aplicación cliente con una caché de Redis 6 en un entorno inferior antes de actualizar. Consulte [aquí](cache-how-to-import-export-data.md) para ver detalles sobre cómo exportar.

> [!NOTE]
> Tenga en cuenta que la actualización no está admitida en una caché con un vínculo de replicación geográfica, por lo que tendrá que desvincular manualmente las instancias de caché antes de actualizar. 
>

Para actualizar la caché, siga estos pasos:

1. En Azure Portal, busque **Azure Cache for Redis**. Después, presione Entrar o selecciónelo en las sugerencias de búsqueda.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Busque Azure Cache for Redis.":::

1. Seleccione la instancia de caché que desea actualizar de Redis 4 a Redis 6.

1. En el lado izquierdo de la pantalla, seleccione **Configuración avanzada**. 

1. Si la instancia de caché es apta para actualizarse, debería ver el siguiente banner azul. Si desea continuar, seleccione el texto del banner.

    :::image type="content" source="media/cache-how-to-version/blue-banner-upgrade-cache.png" alt-text="Banner azul que indica que puede actualizar a la de caché de Redis 6 con características y comandos adicionales que mejoran la productividad del desarrollador y la facilidad de uso. No se puede invertir la actualización de la instancia de caché.":::
    
1. A continuación, aparecerá un cuadro de diálogo emergente que le notificará que la actualización es permanente y puede provocar una breve interrupción de la conexión. Seleccione Sí si desea actualizar la instancia de caché.

    :::image type="content" source="media/cache-how-to-version/dialog-version-upgrade.png" alt-text="Cuadro de diálogo con más información sobre cómo actualizar la caché.":::

1. Para comprobar el estado de la actualización, vaya a **Información general**.

    :::image type="content" source="media/cache-how-to-version/upgrade-status.png" alt-text="Información general muestra el estado de la caché que se está actualizando.":::

## <a name="faq"></a>Preguntas más frecuentes

### <a name="what-features-arent-supported-with-redis-6"></a>¿Qué características no se admiten con Redis 6?

Por el momento, Redis 6 no admite ACL ni la replicación geográfica de una caché entre las versiones de Redis 4 y 6.

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>¿Puedo cambiar la versión de la caché después de crearla?

Puede actualizar las cachés de Redis 4 existentes a Redis 6; consulte [aquí](#upgrade-an-existing-redis-4-cache-to-redis-6) para más información. Tenga en cuenta que la actualización de la instancia de caché es permanente y no se pueden cambiar las cachés de Redis 6 a otras de Redis 4.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las características de Redis 6, consulte [Diving Into Redis 6.0 by Redis](https://redis.com/blog/diving-into-redis-6/) (Profundizar en Redis 6.0 de Redis).
- Más información sobre las características de Azure Cache for Redis:

> [!div class="nextstepaction"]
> [Niveles de servicio de Azure Cache for Redis Premium](cache-overview.md#service-tiers)

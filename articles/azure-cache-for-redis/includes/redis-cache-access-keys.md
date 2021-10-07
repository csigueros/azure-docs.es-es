---
title: Archivo de inclusión
description: archivo de inclusión
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: c1f5aae79daf4cf8fb3a447eba5538212e3dc327
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533806"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Recuperación del nombre de host, los puertos y las claves de acceso desde Azure Portal

Para conectar con una instancia de Azure Cache for Redis, los clientes de dicha caché necesitan el nombre de host, los puertos y una clave para la caché. Es posible que algunos clientes utilicen nombres ligeramente diferentes para estos elementos. Puede obtener el nombre de host, los puertos y las claves de [Azure Portal](https://portal.azure.com).

- Para obtener las claves de acceso, en el panel de navegación izquierdo de Azure Cache for Redis, seleccione **Claves de acceso**. 
  
  ![Claves de Azure Redis Cache](media/redis-cache-access-keys/redis-cache-keys.png)

- Para obtener el nombre de host y los puertos, en el panel de navegación izquierdo de Azure Cache for Redis, seleccione **Propiedades**. El nombre de host tiene el formato *\<DNS name>.redis.cache.windows.net*.

  ![Propiedades de Azure Redis Cache](media/redis-cache-access-keys/redis-cache-hostname-ports.png)


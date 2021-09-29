---
title: Procedimientos recomendados de pruebas de rendimiento
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo probar el rendimiento de Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 748e7e09332e9feded9af0bc6cfa9a38d27086f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615213"
---
# <a name="performance-testing"></a>Pruebas de rendimiento

1. Empiece por usar `redis-benchmark.exe` para comprobar las características generales de rendimiento y latencia de la caché antes de escribir pruebas de rendimiento propias. Para obtener más información, vea [Redis-benchmark](#redis-benchmark-utility).

1. El cliente para pruebas de VM debe estar en la *misma región* que la instancia de caché de Redis.

1. Asegúrese de que la VM cliente que use tenga *al menos tantos procesos y ancho de banda* como la caché que se va a probar.

1. Es importante no probar el rendimiento de la caché solo en condiciones de estado estable. *Realice pruebas también en condiciones de conmutación por error* y mida la carga de la CPU o el servidor en la caché durante ese tiempo. Puede iniciar una conmutación por error [reiniciando el nodo principal](cache-administration.md#reboot). Las pruebas en condiciones de conmutación por error permiten ver el rendimiento y la latencia de la aplicación durante condiciones de conmutación por error. La conmutación por error puede producirse durante las actualizaciones o durante un evento no planeado. Idealmente, no es deseable ver el pico de carga de CPU o servidor a más de un 80 % incluso durante una conmutación por error, ya que esto puede afectar al rendimiento.

1. Considere la posibilidad de usar instancias de Azure Cache for Redis de n nivel Premium. Estos tamaños de caché tendrán mejor latencia de red y rendimiento porque se ejecutan en un hardware mejor de CPU y red.

   > [!NOTE]
   > Los resultados de rendimiento observados se [publicarán aquí](./cache-planning-faq.yml#azure-cache-for-redis-performance) para que los tenga como referencia. Además, tenga en cuenta que SSL/TLS agrega cierta sobrecarga, por lo que puede obtener diferentes latencias o rendimiento si está usando el cifrado de transporte.

## <a name="redis-benchmark-utility"></a>Utilidad Redis-benchmark

La documentación de **Redis-benchmark** se puede encontrar [aquí](https://redis.io/topics/benchmarks).

`redis-benchmark.exe` no admite TLS. Tendrá que [habilitar el puerto no TLS a través del portal](cache-configure.md#access-ports) antes de ejecutar la prueba. [Aquí](https://github.com/MSOpenTech/redis/releases) se puede encontrar una versión de redis-benchmark.exe compatible con Windows.

## <a name="redis-benchmark-examples"></a>Ejemplos de Redis-benchmark

**Configuración anterior a la prueba**: prepare la instancia de caché con los datos necesarios para las pruebas de latencia y rendimiento:

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024
```

**Para probar la latencia**: pruebe las solicitudes GET con una carga de 1 k:

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4
```

**Para probar el rendimiento:** solicitudes GET canalizadas con carga de 1 K:

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
```

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo](cache-best-practices-development.md)
- [Preguntas frecuentes sobre el desarrollo para Azure Cache for Redis](cache-development-faq.yml)
- [Conmutación por error y aplicación de revisiones para Azure Cache for Redis](cache-failover.md)
---
title: Procedimientos recomendados para la administración de memoria
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo administrar la memoria de Azure Cache for Redis efectivamente.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 36fe87e03a78a4dee34c2016b8f4723cb8aa95be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598734"
---
# <a name="memory-management"></a>Administración de memoria

## <a name="eviction-policy"></a>Directiva de expulsión

Elija una [directiva de expulsión](https://redis.io/topics/lru-cache) que funcione para su aplicación. La directiva predeterminada de Azure Cache for Redis es `volatile-lru`, lo que significa que solo las claves que tienen un valor de TTL establecido se podrán usar en la expulsión.  Si ninguna de las claves tiene un valor de TTL, el sistema no expulsará ninguna clave.  Si quiere que el sistema permita que se expulse cualquier clave si está bajo presión de memoria, puede usar la directiva `allkeys-lru`.

## <a name="keys-expiration"></a>Expiración de claves

Establezca un valor de expiración en sus claves. Una expiración quitará las claves de forma proactiva en lugar de esperar hasta que haya presión de memoria.  Cuando la expulsión ocurre debido a la presión en la memoria, puede causar más carga en el servidor. Para más información, consulte la documentación de los comandos [Expire](https://redis.io/commands/expire) y [EXPIREAT](https://redis.io/commands/expireat).

## <a name="minimize-memory-fragmentation"></a>Minimización de la fragmentación de memoria

Los valores grandes pueden dejar la memoria fragmentada durante la expulsión y provocar un uso elevado de la memoria y la carga del servidor.

## <a name="monitor-memory-usage"></a>Supervisión del uso de la memoria

Agregue supervisión sobre el uso de memoria para asegurarse de que no se queda sin memoria y tiene la oportunidad de escalar la memoria caché antes de ver problemas.

## <a name="configure-your-maxmemory-reserved-setting"></a>Configuración de la opción maxmemory-reserved

Configure la [opción maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para mejorar la capacidad de respuesta del sistema en situaciones de presión de memoria.

* Una configuración de reserva suficiente es especialmente importante para las cargas de trabajo con muchas operaciones de escritura o si almacena valores mayores de 100 KB en la caché. Empiece con el 10 % del tamaño de la memoria caché y aumente este porcentaje si tiene cargas con mucha actividad de escritura.

* La opción  `maxmemory-reserved`  configura la cantidad de memoria (en MB por instancia en un clúster) que se reserva para las operaciones ajenas a la memoria caché, como la replicación durante la conmutación por error. Esta opción le permite tener una experiencia más coherente de servidor Redis cuando varía la carga. Este valor debe establecerse más alto para las cargas de trabajo que escriben grandes cantidades de datos. Cuando se reserva memoria para dichas operaciones, no está disponible para el almacenamiento de los datos en la caché.

* La opción  `maxfragmentationmemory-reserved`  configura la cantidad de memoria (en MB por instancia en un clúster) que se reserva para adaptarse a la fragmentación de memoria. Si se establece este valor, la experiencia del servidor Redis es más coherente cuando la caché está llena o prácticamente llena, y la proporción de fragmentación es elevada. Cuando se reserva memoria para dichas operaciones, no está disponible para el almacenamiento de los datos en la caché.

* Al elegir un nuevo valor de reserva de memoria (`maxmemory-reserved` o `maxfragmentationmemory-reserved`) hay que tener en cuenta cómo podría afectar este cambio a una memoria caché que ya se está ejecutando con grandes cantidades de datos en ella. Por ejemplo, si tiene una memoria caché de 53 GB con 49 GB de datos, al cambiar el valor de reserva a 8 GB, la memoria máxima disponible para el sistema caerá a 45 GB. Si los valores actuales de  `used_memory` o `used_memory_rss` son mayores que el nuevo límite de 45 GB, entonces el sistema tendrá que expulsar datos hasta que `used_memory` y `used_memory_rss`  estén por debajo de 45 GB. La expulsión puede aumentar la carga del servidor y la fragmentación de memoria. Para más información sobre las métricas de caché como `used_memory` y `used_memory_rss` [, consulte](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)Métricas disponibles e intervalos de informes.

## <a name="next-steps"></a>Pasos siguientes

* [Procedimientos recomendados para el desarrollo](cache-best-practices-development.md)
* [Preguntas frecuentes sobre el desarrollo para Azure Cache for Redis](cache-development-faq.yml)
* [Opción maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)
* [Procedimientos recomendados de escalado](cache-best-practices-scale.md)

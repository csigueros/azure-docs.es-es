---
title: Procedimientos recomendados para el desarrollo
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo desarrollar código para Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 20725796abed454aaccdea73f13d898ca48f615c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626121"
---
# <a name="development"></a>Desarrollo

## <a name="connection-resilience-and-server-load"></a>Resistencia de la conexión y carga del servidor

Al desarrollar aplicaciones cliente, asegúrese de tener en cuenta los procedimientos recomendados sobre [resistencia de la conexión](cache-best-practices-connection.md) y [administración de la carga del servidor](cache-best-practices-server-load.md).

## <a name="consider-more-keys-and-smaller-values"></a>Consideración de más claves y valores más pequeños

Redis funciona mejor con valores más pequeños. Considere la posibilidad de dividir los fragmentos de datos más grandes en otros más pequeños para repartirlos entre varias claves. Para obtener más información sobre el tamaño de valor ideal, consulte [este artículo](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/).

En este artículo de Redis se enumeran algunas cuestiones a las que se debe prestar especial atención. En [Tamaño de solicitud o respuesta grande](cache-troubleshoot-client.md#large-request-or-response-size) puede consultar un problema de ejemplo que podría estar provocado por valores grandes.

## <a name="key-distribution"></a>Distribución de las claves

Si planea usar una agrupación en clústeres de Redis, consulte primero [Procedimientos recomendados de agrupación en clústeres de Redis con claves](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/).

## <a name="use-pipelining"></a>Uso de canalización

Intente elegir un cliente de Redis que admita la [canalización de Redis](https://redis.io/topics/pipelining). La canalización ayuda a hacer un uso eficaz de la red y a obtener el mejor rendimiento posible.

## <a name="avoid-expensive-operations"></a>Evita operaciones costosas

Algunas operaciones de Redis, como el comando [KEYS](https://redis.io/commands/keys) son muy costosas y deben evitarse. En [Comandos de larga duración](cache-troubleshoot-server.md#long-running-commands) encontrará algunos aspectos para tener en cuenta con relación a estos comandos.

## <a name="choose-an-appropriate-tier"></a>Elija un nivel apropiado:
Use el nivel Estándar o Premium para sistemas de producción.  No use el nivel Básico en producción. El nivel básico es un sistema de nodo único sin replicación de datos ni Acuerdo de Nivel de Servicio. Además, utilice al menos una caché de C1. Las cachés de C0 solo están diseñadas para escenarios sencillos de desarrollo o prueba por los siguientes motivos:

- Comparten un núcleo de CPU.
- Usan poca memoria.
- Son propensos a tener problemas relacionados con *vecinos ruidosos*.

Se recomienda realizar pruebas de rendimiento para elegir el nivel correcto y validar la configuración de conexión. Para obtener más información, consulte [Pruebas de rendimiento](cache-best-practices-performance.md).

## <a name="client-in-same-region-as-cache"></a>Cliente en la misma región que la caché

Localice su instancia de la caché y su aplicación en la misma región. El establecimiento de una conexión con una memoria caché de otra región puede aumentar considerablemente la latencia y reducir la confiabilidad.  

Si bien puede conectarse desde fuera de Azure, no es recomendable, *especialmente cuando se usa Redis como caché*.  Si solo usa el servidor de Redis como almacén de clave/valor, es posible que la latencia no sea su principal preocupación.

## <a name="use-tls-encryption"></a>Uso de cifrado TLS

Azure Cache for Redis requiere de manera predeterminada comunicaciones cifradas mediante TLS. Actualmente se admiten las versiones de TLS 1.0, 1.1 y 1.2. Sin embargo, TLS 1.0 y 1.1 están en proceso de desuso en todo el sector, por lo que se recomienda TLS 1.2 si es posible.

Si la biblioteca o la herramienta cliente no admiten TLS, es posible habilitar las conexiones sin cifrar mediante [Azure Portal](cache-configure.md#access-ports) o [API de administración](/rest/api/redis/redis/update). En casos en los que no es posible establecer conexiones cifradas, se recomienda colocar la caché y la aplicación cliente en una red virtual. Para más información sobre los puertos que se usan en el escenario de caché de la red virtual, consulte esta [tabla](cache-how-to-premium-vnet.md#outbound-port-requirements).

## <a name="client-library-specific-guidance"></a>Guía específica de bibliotecas cliente

* [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
* [Java: ¿Qué cliente debo usar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
* [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
* [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
* [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
* [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
* [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
* [Proveedor de estado de sesión de ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas frecuentes sobre el desarrollo para Azure Cache for Redis](cache-development-faq.yml)
- [Pruebas de rendimiento](cache-best-practices-performance.md)
- [Conmutación por error y aplicación de revisiones para Azure Cache for Redis](cache-failover.md)

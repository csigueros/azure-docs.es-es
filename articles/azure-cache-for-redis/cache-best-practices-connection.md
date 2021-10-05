---
title: Procedimientos recomendados para la resistencia de conexiones
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo hacer que las conexiones de Azure Cache for Redis sean resistentes.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: a0dd6e3e8f4c2a7645da1ceccf77f7607d2b84b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656956"
---
# <a name="connection-resilience"></a>Resistencia de la conexión

## <a name="retry-commands"></a>Reintento de comandos

Configure las conexiones cliente para el reintento de comandos con retroceso exponencial. Para obtener más información, vea [Instrucciones de reintento](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis).

## <a name="test-resiliency"></a>Prueba de la resistencia

Pruebe la resistencia del sistema a las interrupciones de conexión mediante un [reinicio](cache-administration.md#reboot) para simular una aplicación de revisiones. Para obtener más información sobre las pruebas de rendimiento, vea [Pruebas de rendimiento](cache-best-practices-performance.md).

## <a name="configure-appropriate-timeouts"></a>Configuración de tiempos de espera adecuados

Dos valores de tiempo de espera que es importante tener en cuenta para la resistencia de la conexión: [tiempo de espera de conexión](#connect-timeout) y [tiempo de espera del comando](#command-timeout).

### <a name="connect-timeout"></a>Tiempo de espera de conexión

El `connect timeout` es el tiempo que el cliente espera para establecer una conexión con el servidor de Redis. Configure la biblioteca cliente para usar un valor `connect timeout` de cinco segundos, lo que da al sistema tiempo suficiente para conectarse incluso en condiciones mucho uso de la CPU.

Un valor de `connection timeout` bajo no garantiza que se establezca una conexión en ese plazo de tiempo. Si algo va mal (CPU de cliente alta, CPU de servidor alta, etc.), un valor `connection timeout` corto provoca un error del intento de conexión. Este comportamiento suele empeorar una situación que ya es mala de por si. En lugar de mejorar el problema, los tiempos de espera más cortos lo agravan, ya que obligan al sistema a reiniciar el proceso de conexión, lo que puede llevar a un bucle *conectar -> error -> reintentar*.

### <a name="command-timeout"></a>Tiempo de espera del comando

La mayoría de las bibliotecas cliente tienen otra configuración de tiempo de espera para los `command timeouts`, que es el tiempo que el cliente espera una respuesta del servidor Redis. Aunque se recomienda una configuración inicial de menos de cinco segundos, considere la posibilidad de establecer el valor `command timeout` más alto o más bajo en función del escenario y los tamaños de los valores almacenados en la memoria caché.

Si el `command timeout` es demasiado bajo, la conexión puede parecer inestable. Pero si el valor `command timeout` es demasiado elevado, es posible que la aplicación tenga que esperar mucho tiempo para averiguar si el comando va a agotar el tiempo de espera o no.

## <a name="avoid-client-connection-spikes"></a>Evitar picos de conexión cliente

Evite crear muchas conexiones al mismo tiempo al volver a conectarse después de una pérdida de conexión. Al igual que los [tiempos de espera de conexión cortos](#configure-appropriate-timeouts) pueden dar lugar a interrupciones más largas, el inicio de muchos intentos de reconexión al mismo tiempo también puede aumentar la carga del servidor y extender el tiempo que tardan todos los clientes en volver a conectarse correctamente.

Si va a volver a conectar muchas instancias de cliente, considere la posibilidad de escalonar las nuevas conexiones para evitar un pico fuerte en el número de clientes conectados.

> [!NOTE]
> Si usa la biblioteca cliente `StackExchange.Redis`, establezca `abortConnect` en `false` en la cadena de conexión.  Se recomienda permitir que `ConnectionMultiplexer` controle la reconexión. Para obtener más información, vea [Procedimientos recomendados de StackExchange.Redis](/azure/azure-cache-for-redis/cache-management-faq#stackexchangeredis-best-practices).

## <a name="avoid-leftover-connections"></a>Evitar conexiones sobrantes

Las memorias caché tienen límites en cuanto al número de conexiones cliente por nivel de caché. Asegúrese de que cuando la aplicación cliente vuelva a crear las conexiones, cierre y elimine las conexiones antiguas.

## <a name="advance-maintenance-notification"></a>Notificación de mantenimiento avanzado

Use notificaciones para obtener información sobre el próximo mantenimiento. Para obtener más información, vea [¿Se puede recibir una notificación previa a un mantenimiento planeado?](cache-failover.md#can-i-be-notified-in-advance-of-planned-maintenance).

## <a name="schedule-maintenance-window"></a>Programación de la ventana de mantenimiento

Ajuste la configuración de la caché para dar cabida al mantenimiento. Para obtener más información sobre cómo crear una ventana de mantenimiento para reducir los efectos negativos en la caché, vea [Programación de actualizaciones](cache-administration.md#schedule-updates).

## <a name="more-design-patterns-for-resilience"></a>Más patrones de diseño para la resistencia

Aplique patrones de diseño para la resistencia. Para obtener más información, vea [Cómo hacer que mi aplicación sea resistente](cache-failover.md#how-do-i-make-my-application-resilient).

## <a name="idle-timeout"></a>Tiempo de espera inactividad

Actualmente, Azure Cache for Redis tiene un tiempo de espera de inactividad de 10 minutos para las conexiones, por lo que la configuración de tiempo de espera de inactividad en la aplicación cliente debe ser inferior a 10 minutos. Las bibliotecas cliente más comunes tienen una configuración que permite que las bibliotecas cliente envíe comandos `PING` de Redis a un servidor de Redis de manera automática y periódica. Sin embargo, al usar bibliotecas cliente sin este tipo de configuración, las propias aplicaciones cliente son responsables de mantener activa la conexión.

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados para el desarrollo](cache-best-practices-development.md)
- [Preguntas frecuentes sobre el desarrollo para Azure Cache for Redis](cache-development-faq.yml)
- [Conmutación por error y aplicación de revisiones](cache-failover.md)

---
title: Procedimientos recomendados para la resistencia de conexiones
titleSuffix: Azure Cache for Redis
description: Obtenga información sobre cómo hacer que las conexiones de Azure Cache for Redis sean resistentes.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: e071298ce1ed191f79e071f18916d8afba10d625
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478813"
---
# <a name="connection-resilience"></a>Resistencia de la conexión

## <a name="retry-commands"></a>Reintento de comandos

Configure las conexiones cliente para el reintento de comandos con retroceso exponencial. Para obtener más información, vea [Instrucciones de reintento](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis).

## <a name="test-resiliency"></a>Prueba de la resistencia

Pruebe la resistencia del sistema a las interrupciones de conexión mediante un [reinicio](cache-administration.md#reboot) para simular una aplicación de revisiones. Para obtener más información sobre las pruebas de rendimiento, vea [Pruebas de rendimiento](cache-best-practices-performance.md).

## <a name="configure-appropriate-timeouts"></a>Configuración de tiempos de espera adecuados

Configure la biblioteca cliente para usar un *tiempo de espera de conexión* de entre 10 y 15 segundos, y un *tiempo de espera de comandos* de 5 segundos. El *tiempo de espera de conexión* es el tiempo que el cliente espera para establecer una conexión con el servidor de Redis. La mayoría de las bibliotecas cliente tienen otra configuración de tiempo de espera para los *tiempos de espera de comandos*, que es el tiempo que el cliente espera una respuesta del servidor Redis.

En algunas bibliotecas el *tiempo de espera de comandos* se establece en 5 segundos de forma predeterminada. Considere la posibilidad de aumentarlo o reducirlo en función del escenario y los tamaños de los valores almacenados en la caché.

Si el *tiempo de espera de comandos* es demasiado bajo, la conexión puede parecer inestable. Pero si el *tiempo de espera de comandos* es demasiado elevado, es posible que la aplicación tenga que esperar mucho tiempo para averiguar si el comando va a agotar el tiempo de espera o no.

Configure la biblioteca cliente para usar un *tiempo de espera de conexión* de al menos 15 segundos, lo que le dará al sistema tiempo suficiente para conectarse incluso en condiciones de uso elevado de la CPU. Un valor de *tiempo de espera de conexión* bajo no garantiza que se establezca una conexión en ese plazo de tiempo.

Si se produce algún error (CPU de cliente alta, CPU de servidor alta, etc.) un valor de tiempo de espera de conexión bajo hace que el intento de conexión falle. Este comportamiento suele empeorar una situación que ya es mala de por si. En lugar de mejorar el problema, los tiempos de espera más cortos lo agravan, ya que obligan al sistema a reiniciar el proceso de conexión, lo que puede llevar a un bucle *conectar -> error -> reintentar*.

En general, se recomienda dejar el *tiempo de espera de conexión* en 15 segundos o más. Es mejor dejar que el intento de conexión sea correcto después de 15 o 20 segundos que hacer que falle rápidamente para volver a intentarlo. Un bucle de reintentos de este tipo puede hacer que la interrupción dure más que si otorgara inicialmente más tiempo al sistema.

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

---
title: 'Conmutación por error y aplicación de revisiones: Azure Cache for Redis'
description: Obtenga información sobre la conmutación por error, la aplicación de revisiones y el proceso de actualización de Azure Cache for Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 3ede36ef718fbe4ef535e9999edf55a0381cfd2e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538560"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Conmutación por error y aplicación de revisiones para Azure Cache for Redis

Para crear aplicaciones cliente resistentes y correctas, es fundamental comprender la conmutación por error en el servicio Azure Cache for Redis. Una conmutación por error puede formar parte de las operaciones de administración planeadas o puede ser el resultado de errores de red o hardware no planeados. Un uso habitual de la conmutación por error de la caché tiene lugar cuando el servicio de administración aplica parches a los archivos binarios de Azure Cache for Redis.

En este artículo, encontrará esta información:  

- ¿Qué es la conmutación por error?
- Cómo se produce una conmutación por error durante la aplicación de revisiones.
- Cómo crear una aplicación cliente resistente.

## <a name="what-is-a-failover"></a>¿Qué es la conmutación por error?

Comencemos con información general de la conmutación por error para Azure Cache for Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Un resumen rápido de la arquitectura de la caché

Una memoria caché se construye con varias máquinas virtuales que tienen direcciones IP privadas independientes. Cada máquina virtual, también conocida como nodo, se conecta a un equilibrador de carga compartido con una sola dirección IP virtual. Cada nodo ejecuta el proceso de servidor de Redis y es accesible a través del nombre de host y los puertos de Redis. Cada nodo se considera un nodo principal o un nodo réplica. Cuando una aplicación cliente se conecta a una caché, su tráfico pasa a través de este equilibrador de carga y se enruta automáticamente al nodo principal.

En una memoria caché Básica, el nodo único es siempre uno principal. En una caché Estándar o Premium hay dos nodos: uno se elige como principal y el otro es la réplica. Dado que las cachés Estándar y Premium tienen varios nodos, es posible que uno de los nodos no esté disponible, en tanto que el otro sigue procesando las solicitudes. Las cachés en clúster se componen de muchas particiones, cada una con nodos principales y réplica distintos. Puede que una partición esté inactiva mientras otras permanecen disponibles.

> [!NOTE]
> Una memoria caché Básica no tiene varios nodos y no ofrece un Acuerdo de Nivel de Servicio (SLA) para su disponibilidad. Las memorias caché Básicas solo se recomiendan para fines de desarrollo y pruebas. Use una caché Estándar o Premium en una implementación de varios nodos para aumentar la disponibilidad.

### <a name="explanation-of-a-failover"></a>Explicación de una conmutación por error

Una conmutación por error se produce cuando un nodo réplica se promueve para convertirse en un nodo principal y el nodo principal anterior cierra las conexiones existentes. Cuando el nodo principal vuelve a estar disponible, observa el cambio en los roles y disminuye su nivel para convertirse en una réplica. A continuación, se conecta al nuevo principal y sincroniza los datos. Una conmutación por error puede ser planeada o no planeada.

Una *conmutación por error planeada* tiene lugar durante dos momentos diferentes:

- Actualizaciones del sistema, como aplicación de revisiones de Redis o actualizaciones del sistema operativo.  
- Operaciones de administración, como el escalado y el reinicio.

Dado que los nodos reciben un aviso por adelantado de la actualización, pueden intercambiar roles de forma cooperativa y actualizar rápidamente el equilibrador de carga del cambio. Una conmutación por error planeada suele finalizar en menos de un segundo.

Una *conmutación por error no planeada* puede producirse debido a un error de hardware, a un error de red o a otras interrupciones inesperadas en el nodo principal. El nodo réplica se promueve a principal, pero el proceso tardará más. Un nodo réplica debe detectar primero que su nodo principal no está disponible antes de que pueda iniciar el proceso de conmutación por error. El nodo réplica también debe comprobar si este error no planeado es transitorio o local, para evitar una conmutación por error innecesaria. Este retraso en la detección significa que una conmutación por error no planeada finaliza normalmente en 10 o 15 segundos.

## <a name="how-does-patching-occur"></a>¿Cómo se realiza la aplicación de la revisión?

El servicio Azure Cache for Redis actualiza regularmente la memoria caché con las características y correcciones de plataforma más recientes. Para aplicar una revisión en una caché, el servicio lleva a cabo estos pasos:

1. El servicio de administración selecciona el nodo en el que va a aplicar la revisión.
1. Si el nodo seleccionado es un nodo principal, el nodo réplica correspondiente se promueve de forma cooperativa. Esta promoción se considera una conmutación por error planeada.
1. El nodo seleccionado se reinicia para recibir los nuevos cambios y se vuelve a poner en marcha como un nodo réplica.
1. El nodo réplica se conecta al nodo principal y sincroniza los datos.
1. Cuando se completa la sincronización de datos, el proceso de aplicación de la revisión se repite en los nodos restantes.

Como la aplicación de la revisión es una conmutación por error planeada, el nodo réplica se promueve rápidamente para convertirse en principal. A continuación, el nodo comienza a atender las solicitudes y las nuevas conexiones. Las memorias caché Básicas no tienen un nodo réplica y no están disponibles hasta que se completa la actualización. La aplicación de la revisión se realiza por separado en cada partición de una caché en clúster y las conexiones a otra partición no se cierran.

> [!IMPORTANT]
> La revisión se aplica de uno a uno en los nodos, para evitar la pérdida de datos. Las memorias caché Básicas tendrán pérdida de datos. En las cachés en clúster, la aplicación de la revisión se realiza en una partición a la vez.

Cuando hay varias cachés en el mismo grupo de recursos o en la misma región, la aplicación de la revisión también se realiza en una partición a la vez.  En las memorias caché que se encuentran en distintos grupos de recursos o en regiones diferentes, la aplicación de la revisión se puede realizar simultáneamente.

Dado que se produce una sincronización de datos completa antes de que se repita el proceso, es poco probable que se produzca una pérdida de datos cuando se usa una caché Estándar o Premium. Puede protegerse aún más contra la pérdida de datos mediante la [exportación](cache-how-to-import-export-data.md#export) de datos y la habilitación de la [persistencia](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Carga adicional de caché

Siempre que se produce una conmutación por error, las caché Estándar y Premium deben replicar los datos de un nodo a otro. Esta replicación provoca un aumento de la carga en la CPU y en la memoria del servidor. Si la instancia de caché ya está muy cargada, puede que haya mayor latencia en las aplicaciones cliente. En casos extremos, las aplicaciones cliente pueden recibir excepciones de tiempo de espera. Para ayudar a mitigar el efecto de más carga, [configure](cache-configure.md#memory-policies) la opción `maxmemory-reserved` de la memoria caché.

## <a name="how-does-a-failover-affect-my-client-application"></a>¿Cómo afecta una conmutación por error a mi aplicación cliente?

Las aplicaciones cliente podrían recibir algunos errores de la instancia de Azure Cache for Redis. El número de errores detectados por una aplicación cliente depende del número de operaciones pendientes en esa conexión en el momento de la conmutación por error. Cualquier conexión que se enrute mediante el nodo que cerró sus conexiones verá errores.

Muchas bibliotecas cliente pueden emitir distintos tipos de errores al interrumpirse las conexiones, entre ellos:

- excepciones de tiempo de espera
- excepciones de conexión
- excepciones de socket

El número y el tipo de excepciones dependen de dónde se encuentre la solicitud en la ruta de acceso al código cuando la memoria caché cierra sus conexiones. Por ejemplo, una operación que envía una solicitud pero que no ha recibido una respuesta cuando se produce la conmutación por error puede obtener una excepción de tiempo de espera. Las nuevas solicitudes en el objeto de conexión cerrado reciben excepciones de conexión hasta que la reconexión se produce correctamente.

La mayoría de las bibliotecas cliente intentan volver a conectarse a la memoria caché si están configuradas para ello. Sin embargo, en ocasiones, errores imprevistos pueden dejar los objetos de biblioteca en un estado irrecuperable. Si los errores continúan durante más tiempo del preconfigurado, se debe volver a crear el objeto de conexión. En Microsoft .NET y otros lenguajes orientados a objetos, se puede volver a crear la conexión sin necesidad de reiniciar la aplicación mediante [un patrón Lazy\<T\>](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="can-i-be-notified-in-advance-of-planned-maintenance"></a>¿Puedo recibir una notificación por adelantado sobre un mantenimiento planeado?

Azure Cache for Redis publica notificaciones en un canal de publicación y suscripción (pub/sub) llamado [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) unos 30 segundos antes de las actualizaciones planeadas. Las notificaciones se realizan en tiempo de ejecución.

Las notificaciones son para las aplicaciones que usan disyuntores para omitir la memoria caché o las aplicaciones que almacenan en búfer los comandos. Por ejemplo, la memoria caché podría omitirse durante las actualizaciones planeadas.

El canal `AzureRedisEvents` no es un mecanismo que pueda notificarle con días u horas de antelación. El canal puede notificar a los clientes los próximos eventos de mantenimiento planeados del servidor que podrían afectar a la disponibilidad del servidor.

Muchas bibliotecas cliente de Redis populares admiten la suscripción a canales pub/sub. La recepción de notificaciones desde el canal `AzureRedisEvents` suele ser una adición sencilla a la aplicación cliente.

Una vez que la aplicación se suscribe a `AzureRedisEvents`, recibe una notificación 30 segundos antes de que cualquier nodo se vea afectado por un evento de mantenimiento. La notificación incluye detalles sobre el próximo evento e indica si afecta a un nodo principal o de réplica.

Se envía otra notificación minutos más tarde cuando se completa la operación de mantenimiento.

La aplicación usa el contenido de la notificación para tomar medidas para evitar el uso de la memoria caché mientras se realiza el mantenimiento. Una memoria caché podría implementar un patrón de disyuntor en el que el tráfico se enruta fuera de la memoria caché durante la operación de mantenimiento. En su lugar, el tráfico se envía directamente a un almacén persistente. La notificación no está pensada para dar tiempo a que una persona sea alertada y tomar medidas manuales.

En la mayoría de los casos, la aplicación no necesita suscribirse a `AzureRedisEvents` ni responder a notificaciones. En su lugar, se recomienda implementar la [creación de resistencia](#build-in-resiliency).

Con la suficiente resistencia, las aplicaciones controlan correctamente cualquier pérdida de conexión breve o falta de disponibilidad de la memoria caché, como la experimentada durante el mantenimiento del nodo. También es posible que la aplicación pierda inesperadamente su conexión a la memoria caché sin previo aviso de `AzureRedisEvents` debido a errores de red u otros eventos.

Solo se recomienda suscribirse a `AzureRedisEvents` en algunos casos destacados:

- Aplicaciones con requisitos de rendimiento extremos, en las que incluso se deben evitar retrasos menores. En estos escenarios, el tráfico podría redirigirse sin problemas a una memoria caché de reserva antes de que comience el mantenimiento en la memoria caché actual.
- Aplicaciones que leen explícitamente datos de la réplica en lugar de los nodos principales. Durante el mantenimiento en un nodo de réplica, la aplicación podría cambiar temporalmente a leer los datos de los nodos principales.
- Las aplicaciones que no pueden correr el riesgo de que las operaciones de escritura no se puedan realizar de forma silenciosa o correcta sin confirmación, lo que puede ocurrir cuando las conexiones se cierran por mantenimiento. Si esos casos pueden dar lugar a una pérdida de datos peligrosa, la aplicación puede pausar o redirigir de forma proactiva los comandos de escritura antes de que se programe el inicio del mantenimiento.

### <a name="client-network-configuration-changes"></a>Cambios en la configuración de red del cliente

Algunos cambios en la configuración de red del lado cliente pueden desencadenar errores de tipo "Ninguna conexión disponible". Estos cambios podrían incluir:

- Intercambio de la dirección IP virtual de una aplicación cliente entre los espacios de ensayo y producción.
- Escalado del tamaño o número de instancias de su aplicación.

Estos cambios pueden dar lugar a un problema de conectividad con una duración inferior a un minuto. Es probable que la aplicación cliente pierda la conexión a otros recursos de red externos, además del servicio Azure Cache for Redis.

## <a name="build-in-resiliency"></a>Creación de resistencia

No se pueden evitar completamente las conmutaciones por error. En su lugar, escriba las aplicaciones cliente para que sean resistentes a las interrupciones de la conexión y a las solicitudes con error. La mayoría de las bibliotecas cliente se vuelven a conectar automáticamente al punto de conexión de la memoria caché, pero pocas intentan enviar de nuevo las solicitudes con error. En función del escenario de la aplicación, es posible que la lógica de reintento con retroceso tenga sentido.

### <a name="how-do-i-make-my-application-resilient"></a>¿Cómo hago que mi aplicación sea resistente?

Consulte estos patrones de diseño para crear clientes resistentes, sobre todo los patrones de reintento e interruptores:

- [Patrones de confiabilidad: patrones de diseño en la nube](/azure/architecture/framework/resiliency/reliability-patterns#resiliency)
- [Guía de reintentos para los servicios de Azure: procedimientos recomendados para aplicaciones en la nube](/azure/architecture/best-practices/retry-service-specific)
- [Implementar reintentos con retroceso exponencial](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)

Para probar la resistencia de una aplicación cliente, use un [reinicio](cache-administration.md#reboot) como desencadenador manual para las interrupciones de conexión.

Además, se recomienda [programar actualizaciones](cache-administration.md#schedule-updates) en una caché para aplicar revisiones en tiempo de ejecución de Redis durante períodos semanales específicos. Normalmente, estas ventanas se establecen en períodos en los que el tráfico de la aplicación cliente es bajo, para evitar posibles incidentes.

Para más información, consulte [Resistencia de la conexión](cache-best-practices-connection.md).

## <a name="next-steps"></a>Pasos siguientes

- [Programe actualizaciones](cache-administration.md#schedule-updates) para la memoria caché.
- Pruebe la resistencia de la aplicación mediante un [reinicio](cache-administration.md#reboot).
- [Configure](cache-configure.md#memory-policies) las reservas y las directivas de memoria.

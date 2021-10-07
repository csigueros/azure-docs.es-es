---
title: Supervisión de Azure Cache for Redis
description: Aprenda a supervisar el estado y el rendimiento de las instancias de Azure Redis Cache for Redis
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 64234292051c5f780c33fd55fabf3305f45b756d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538416"
---
# <a name="monitor-azure-cache-for-redis"></a>Supervisión de Azure Cache for Redis

Azure Cache for Redis usa [Azure Monitor](../azure-monitor/index.yml) para proporcionar varias opciones para la supervisión de instancias de caché. Estas herramientas permiten supervisar el estado de las instancias de Azure Cache for Redis y le ayudan a administrar sus aplicaciones de almacenamiento en caché.

Use Azure Monitor para:

- ver métricas
- anclar gráficos de métricas al Panel de inicio
- personalizar el intervalo de fechas y horas de los gráficos de supervisión
- agregar y quitar métricas de los gráficos
- y establecer alertas cuando se cumplen ciertas condiciones.

Las métricas de las instancias de Azure Cache for Redis se recopilan mediante el comando [INFO](https://redis.io/commands/info) de Redis. Las métricas se recopilan dos veces por minuto aproximadamente y se almacenan de forma automática durante 30 días para que se puedan mostrar en los gráficos de métricas y que las reglas de alertas puedan evaluarlas.

Para configurar una directiva de retención diferente, consulte [Exportación de métricas de caché](#export-cache-metrics).  

Para obtener más información sobre los distintos valores INFO que se utilizan para las métricas de caché, consulte [Métricas disponibles e intervalos de informes](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Para ver las métricas de caché, [busque](cache-configure.md#configure-azure-cache-for-redis-settings) la instancia de su memoria caché en [Azure Portal](https://portal.azure.com).  Azure Cache for Redis proporciona varios gráficos integrados a la izquierda mediante **Introducción** y **Métricas de Redis**. Se puede personalizar cada gráfico agregando o quitando métricas y cambiando el intervalo de informes.

![Se muestran seis gráficos. Uno de ellos es Aciertos y errores de caché la última hora](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visualización de gráficos de métricas preconfigurados

A la izquierda, **Introducción** tiene los siguientes gráficos de supervisión preconfigurados.

- [Gráficos de supervisión](#monitoring-charts)
- [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de supervisión

La sección **Supervisión** de **Introducción** a la izquierda contiene los gráficos **Aciertos y errores**, **Obtenciones y configuraciones**, **Conexiones** y **Comandos totales**.

![Gráficos de supervisión](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de uso

La sección **Uso** de **Introducción** a la izquierda contiene los gráficos **Carga del servidor Redis**, **Uso de memoria**, **Ancho de banda de la red** y **Uso de CPU**, y muestra el **Plan de tarifa** de la instancia de caché.

![Gráficos de uso](./media/cache-how-to-monitor/redis-cache-usage-part.png)

El **Nivel de precios** muestra el nivel de precios de caché y se puede utilizar para [escalar](cache-how-to-scale.md) la memoria caché a un nivel de precios diferente.

## <a name="view-metrics-charts-for-all-your-caches-with-azure-monitor-for-azure-cache-for-redis"></a>Visualización de gráficos de métricas para todas las cachés con Azure Monitor para Azure Cache for Redis

Use [Azure Monitor para Azure Cache for Redis](../azure-monitor/insights/redis-cache-insights-overview.md) (versión preliminar) para obtener una vista del rendimiento general, los errores, la capacidad y el estado operativo de todos los recursos de Azure Cache for Redis. Vea las métricas en una experiencia personalizable, unificada e interactiva que le permite explorar en profundidad los detalles de los recursos individuales. Azure Monitor para Azure Cache for Redis se basa en la [característica de libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) que proporciona visualizaciones enriquecidas para métricas y otros datos. Para más información, consulte el artículo [Exploración de Azure Monitor para Azure Cache for Redis](../azure-monitor/insights/redis-cache-insights-overview.md).

## <a name="view-metrics-with-azure-monitor-metrics-explorer"></a>Visualización de métricas con el explorador de métricas de Azure Monitor

En aquellos escenarios en los que no necesite toda la flexibilidad de Azure Monitor para Azure Cache for Redis, puede ver las métricas y crear gráficos personalizados mediante el explorador de métricas de Azure Monitor. Seleccione **Métricas** en el **menú Recursos** y personalice el gráfico con las métricas preferidas, el intervalo de generación de informes, el tipo de gráfico, etc.

![En el panel de navegación izquierdo de contoso55, Métricas es una opción en Supervisión y aparece resaltada. En Métricas hay una lista de métricas. Aciertos y errores de caché la última hora aparece seleccionado.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para más información acerca de cómo trabajar con métricas mediante Azure Monitor, consulte [Información general sobre las métricas en Microsoft Azure](../azure-monitor/data-platform.md).

<a name="enable-cache-diagnostics"></a>

## <a name="export-cache-metrics"></a>Exportación de métricas de caché

De manera predeterminada, en Azure Monitor las métricas de caché se [almacenan durante 30 días](../azure-monitor/essentials/data-platform-metrics.md) y después se eliminan. Para conservar las métricas de memoria caché durante más de 30 días, puede [designar una cuenta de almacenamiento](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) y especifique un **retención (días)** directiva para las métricas de memoria caché.

Para configurar una cuenta de almacenamiento para las métricas de caché:

1. En la página **Azure Cache for Redis**, en el encabezado **Monitoring** (Supervisión), seleccione **Diagnostics** (Diagnósticos).
1. Seleccione **+ Agregar configuración de diagnóstico**.
1. Asigne un nombre a la configuración.
1. Seleccione **Archivar en una cuenta de almacenamiento**. Cuando envíe diagnósticos a una cuenta de almacenamiento, se le cobrará de acuerdo a las tarifas de datos normales relativas a almacenamiento y transacciones.
1. Seleccione **Configurar** para elegir la cuenta de almacenamiento en la que va a almacenar las métricas de caché.
1. En el encabezado de tabla **Métrica**, active la casilla junto a los elementos de línea que desea almacenar, como **AllMetrics** (Todas las métricas). Especifique una directiva **Retención (días)** . La retención máxima de días que puede especificar es **365 días**. Sin embargo, si desea conservar los datos de las métricas para siempre, establezca **Retención (días)** en **0**.
1. Seleccione **Guardar**.

![Diagnósticos de Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Además de archivar las métricas de caché en el almacenamiento, también puede [transmitirlas a un centro de eventos o enviarlas a registros de Azure Monitor](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Para acceder a las métricas, puede verlas en Azure Portal como ya se ha descrito anteriormente en este mismo artículo. También puede acceder a ellas mediante la [API de REST de métricas de Azure Monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Si cambia las cuentas de almacenamiento, los datos de la cuenta de almacenamiento configurada anteriormente siguen estando disponibles para su descarga, pero no se muestran en el Portal de Azure.  
>

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponibles e intervalos de informes

Los informes de las métricas de caché se generan utilizando diferentes intervalos, como **Última hora**, **Hoy**, **Semana anterior** y **Personalizado**. A la izquierda, se encuentra la selección **Métrica** para cada gráfico de métricas que muestra los valores promedio, mínimos y máximo para cada métrica del gráfico, y algunas métricas muestran un total para el intervalo de informes.

Cada métrica incluye dos versiones. Una métrica mide el rendimiento de toda la memoria caché. En el caso de las memorias caché que utilizan la [agrupación en clústeres](cache-how-to-premium-clustering.md), se usa otra versión de la métrica que incluye `(Shard 0-9)` en el nombre para medir el rendimiento de una sola partición de la memoria caché. Por ejemplo, si una memoria caché tiene 4 particiones, `Cache Hits` es el número total de resultados en toda la memoria caché y `Cache Hits (Shard 3)` refleja simplemente los resultados para esa partición de la memoria caché.

> [!NOTE]
> Cuando vea el tipo de agregación:
>
> - "Count" que muestra 2, indica que la métrica recibió 2 puntos de datos para la granularidad de tiempo (1 minuto).
> - "Max" muestra el valor máximo de un punto de datos en la granularidad de tiempo.
> - "Min" muestra el valor mínimo de un punto de datos en la granularidad de tiempo.
> - "Average" muestra el valor medio de todos los puntos de datos en la granularidad de tiempo.
> - "Sum" muestra la suma de todos los puntos de datos en la granularidad de tiempo y puede ser engañoso en función de la métrica específica.
> En condiciones normales, "Average" y "Max" serán muy similares porque solo un nodo emite estas métricas (el nodo maestro). En un escenario en el que el número de clientes conectados cambia rápidamente, "Max", "Average" y "Min" mostrarían valores muy diferentes y este es también el comportamiento esperado.
>
> Por lo general, "Average" mostrará un gráfico sin problemas de la métrica deseada y reaccionará bien a los cambios en la granularidad del tiempo. "Max" y "Min" pueden ocultar cambios grandes en la métrica si la granularidad de tiempo es grande, pero se puede usar con una granularidad de tiempo pequeña para ayudar a identificar el tiempo exacto en el que se producen cambios grandes en la métrica.
>
> "Count" y "Sum" pueden ser engañosos para determinadas métricas (clientes conectados incluidos).
>
> Por lo tanto, le sugerimos que consulte las métricas Average y no las métricas Sum.

> [!NOTE]
> Incluso cuando la memoria caché está inactiva sin ninguna aplicación de cliente activa conectada, puede que vea alguna actividad de caché, como clientes conectados, uso de memoria y operaciones que se están realizando. Esta actividad es normal durante la operación de una instancia de Azure Cache for Redis.
>
>

| Métrica | Descripción |
| --- | --- |
| Aciertos de caché |El número de búsquedas de claves correctas durante el intervalo de informes. Esta cifra se asigna a `keyspace_hits` desde el comando [INFO](https://redis.io/commands/info) de Redis. |
| Latencia de caché (versión preliminar) | Latencia de la memoria caché calculada mediante la latencia entre nodos de la memoria caché. Esta métrica se mide en microsegundos y tiene tres dimensiones: `Avg`, `Min` y `Max`. Las dimensiones representan la latencia promedio, mínima y máxima de la memoria caché durante el intervalo de informes especificado. |
| Errores de caché |El número de búsquedas de claves incorrectas durante el intervalo de informes. Esta cifra se asigna a `keyspace_misses` desde el comando INFO de Redis. Los errores de caché no significan necesariamente que haya un problema con la memoria caché. Por ejemplo, cuando se utiliza el modelo de programación cache-aside, una aplicación busca un elemento en primer lugar en la memoria caché. Si el elemento no está allí (error de caché), se recupera de la base de datos y se agrega a la caché para la próxima vez. Los errores de caché son un comportamiento normal del modelo de programación cache-aside. Si el número de errores de caché es mayor de lo esperado, examine la lógica de aplicación que rellena y lee de la memoria caché. Si se expulsan los elementos de la memoria caché debido a la presión de memoria, puede haber algunos errores de caché; una métrica mejor para supervisar la presión de memoria sería `Used Memory` o `Evicted Keys`. |
| Lectura de caché |La cantidad de datos que se leen de la memoria caché en megabytes por segundo (MB/s) durante el intervalo de informes especificado. Este valor se deriva de las tarjetas de interfaz de red que admiten la máquina virtual que hospeda la caché y no es específica de Redis. **Este valor corresponde al ancho de banda de red que emplea esta caché. Si desea configurar alertas para los límites de ancho de banda de red del lado servidor, hágalo mediante este contador `Cache Read`. Consulte [esta tabla](./cache-planning-faq.yml#azure-cache-for-redis-performance) para conocer los límites de ancho de banda de los diferentes tamaños y planes de tarifa de caché.** |
| Escritura de caché |La cantidad de datos que se escriben en la memoria caché en megabytes por segundo (MB/s) durante el intervalo de informes especificado. Este valor se deriva de las tarjetas de interfaz de red que admiten la máquina virtual que hospeda la caché y no es específica de Redis. Este valor corresponde al ancho de banda de red de los datos enviados a la memoria caché desde el cliente. |
| Clientes conectados |El número de conexiones de clientes a la caché durante el intervalo de informes especificado. Esta cifra se asigna a `connected_clients` desde el comando INFO de Redis. Cuando se alcanza el [límite de conexión](cache-configure.md#default-redis-server-configuration), se producirá un error en los intentos posteriores de conexión a la caché. Incluso si no hay ninguna aplicación de cliente activa, puede haber algunas instancias de clientes conectadas debido a procesos y conexiones internos. |
| CPU |El uso de CPU del servidor de Azure Cache for Redis como porcentaje durante el intervalo de informes especificado. Este valor se asigna al contador de rendimiento `\Processor(_Total)\% Processor Time` del sistema operativo. |
| Errors | Errores específicos y problemas de rendimiento que puede experimentar la memoria caché durante un intervalo de notificación especificado. Esta métrica tiene ocho dimensiones que representan los diferentes tipos de errores, pero se podrían agregar más en el futuro. Los tipos de error que se representan ahora son los siguientes: <br/><ul><li>**Conmutación por error**: cuando una caché conmuta por error (un elemento subordinado promociona a un primario)</li><li>**Dataloss**: se produce una pérdida de datos en la memoria caché.</li><li>**UnresponsiveClients**: los clientes no leen los datos del servidor lo suficientemente rápido.</li><li>**AOF**: hay un problema relacionado con la persistencia de AOF.</li><li>**RDB**: hay un problema relacionado con la persistencia de RDB.</li><li>**Import**: hay un problema relacionado con la importación de RDB.</li><li>**Export**: hay un problema relacionado con la exportación de RDB.</li></ul> |
| Claves expulsadas |El número de elementos que se elimina de la caché durante el intervalo de informes especificado debido al límite `maxmemory`. Esta cifra se asigna a `evicted_keys` desde el comando INFO de Redis. |
| Claves expiradas |El número de elementos expirados de la caché durante el intervalo de informes especificado. Este valor se asigna a `expired_keys` desde el comando INFO de Redis.|
| Gets |El número de operaciones get de la caché durante el intervalo de informes especificado. Este valor es la suma de los siguientes valores de todos los comandos INFO de Redis: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` y `cmdstat_getrange`, y es equivalente a la suma de aciertos y errores de caché durante el intervalo de informes. |
| Operaciones por segundo | El número total de comandos procesados por segundo por el servidor de caché durante el intervalo de informes especificado.  Este valor se asigna a "instantaneous_ops_per_sec" desde el comando INFO de Redis. |
| Carga de servidor de Redis |El porcentaje de ciclos en el que el servidor de Redis está ocupado procesando y no esperando a los mensajes inactivo. Si este contador llega a 100, significa que el servidor de Redis ha llegado a un límite de rendimiento y la CPU no puede procesar el trabajo más rápidamente. Si ve alta carga del servidor de Redis, verá las excepciones de tiempo de espera en el cliente. En este caso, debería considerar la posibilidad de un escalado vertical o el particionamiento de los datos en varias cachés. |
| Conjuntos |El número de operaciones set a la caché durante el intervalo de informes especificado. Este valor es la suma de los siguientes valores de todos los comandos INFO de Redis: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` y `cmdstat_setnx`. |
| Total de claves  | El número máximo de claves en la memoria caché durante el período de generación de informes anterior. Esta cifra se asigna a `keyspace` desde el comando INFO de Redis. Debido a una limitación del sistema de métricas subyacente para las memorias caché con la agrupación en clústeres habilitada, Total de claves devuelve el número máximo de claves de la partición que tenía el número máximo de claves durante el intervalo de generación de informes.  |
| Total de operaciones |El número total de comandos procesados por el servidor de caché durante el intervalo de informes especificado. Este valor se asigna a `total_commands_processed` desde el comando INFO de Redis. Cuando se use Azure Cache for Redis simplemente para publicación y suscripción, no habrá ninguna métrica para `Cache Hits`, `Cache Misses`, `Gets` o `Sets`, pero habrá métricas `Total Operations` que reflejen el uso de la memoria caché para operaciones de publicación y suscripción. |
| Memoria usada |La cantidad de memoria caché en MB usada para pares clave-valor en la memoria caché durante el intervalo de informes especificado. Este valor se asigna a `used_memory` desde el comando INFO de Redis. Este valor no incluye los metadatos o la fragmentación. |
| Porcentaje de memoria usado | % del total de memoria que se usa durante el intervalo de notificación especificado.  Este valor hace referencia al valor de `used_memory` del comando INFO de Redis para calcular el porcentaje. |
| Memoria RSS usada |La cantidad de memoria caché usada en MB durante el intervalo de informes especificado, incluida la fragmentación y los metadatos. Este valor se asigna a `used_memory_rss` desde el comando INFO de Redis. |

<a name="operations-and-alerts"></a>

## <a name="alerts"></a>Alertas

Puede configurar la recepción de alertas en función de métricas y registros de actividad. Azure Monitor permite configurar una alerta que haga lo siguiente cuando se desencadena:

- Enviar una notificación por correo electrónico
- Llamar a un webhook
- Invocar una aplicación lógica de Azure

Para configurar reglas de alerta para la memoria caché, seleccione **Reglas de alerta** en el **menú Recursos**.

![Supervisión](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para más información acerca de la configuración y uso de alertas, consulte [Información general de las alertas](../azure-monitor/alerts/alerts-classic-portal.md).

<!-- 
## Activity Logs

Activity logs provide insight into the operations that completed on your Azure Cache for Redis instances. It was previously known as "audit logs" or "operational logs". Using activity logs, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) taken on your Azure Cache for Redis instances.

> [!NOTE]
> Activity logs do not include read (GET) operations.
> 

To view activity logs for your cache, select **Activity logs** from the **Resource menu**.

For more information about Activity logs, see [Overview of the Azure Activity Log](../azure-monitor/essentials/platform-logs-overview.md).
-->

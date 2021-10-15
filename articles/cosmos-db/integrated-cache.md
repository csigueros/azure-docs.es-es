---
title: Caché integrada de Azure Cosmos DB
description: La caché integrada de Azure Cosmos DB es una caché en memoria que le ayuda a garantizar costos administrables y baja latencia a medida que crece el volumen de solicitudes.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: tisande
ms.openlocfilehash: ebf9eb5e06b98bdd573d91f0a57daeb9d81b1f50
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230577"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Caché integrada de Azure Cosmos DB: información general (versión preliminar)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La caché integrada de Azure Cosmos DB es una caché en memoria que le ayuda a garantizar costos administrables y baja latencia a medida que crece el volumen de solicitudes. La memoria caché integrada es fácil de configurar y no es necesario dedicar tiempo a escribir código personalizado para la invalidación de caché ni administrar la infraestructura de back-end. La memoria caché integrada usa una [puerta de enlace dedicada](dedicated-gateway.md) dentro de la cuenta de Azure Cosmos DB. La caché integrada es la primera de muchas características de Azure Cosmos DB que usarán una puerta de enlace dedicada para mejorar el rendimiento. Puede elegir entre tres posibles tamaños de puerta de enlace dedicada en función del número de núcleos y memoria necesarios para la carga de trabajo.

Una caché integrada se configura automáticamente dentro de la puerta de enlace dedicada. La memoria caché integrada tiene dos partes: 

* Una caché de elementos para lecturas puntuales 
* Una caché de consulta para consultas

La caché integrada es una caché de lectura y escritura a través con una directiva de expulsión de menos usados recientemente (LRU). La caché de elementos y la caché de consulta comparten la misma capacidad dentro de la caché integrada y la directiva de expulsión de LRU se aplica a ambas. En otras palabras, los datos se expulsan de la memoria caché estrictamente en función de los menos usados recientemente, independientemente de si se trata de una consulta o una lectura puntual.

> [!NOTE]
> ¿Tiene comentarios sobre la caché integrada? Queremos conocerlos. No dude en compartir sus comentarios directamente con el equipo de ingeniería de Azure Cosmos DB: cosmoscachefeedback@microsoft.com.

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>Cargas de trabajo que se benefician de la memoria caché integrada

El objetivo principal de la caché integrada es reducir los costos de las cargas de trabajo con mucha actividad de lectura. La baja latencia, aunque útil, no es la principal ventaja de la caché integrada porque Azure Cosmos DB ya es rápido sin almacenamiento en caché.

Las lecturas puntuales y las consultas que alcanzaron la memoria caché integrada no usarán ninguna RU. En otras palabras, los aciertos de caché tendrán un cargo de RU de 0. Los aciertos de caché tendrán un costo por operación mucho menor que las lecturas de la base de datos back-end.

Las cargas de trabajo que se ajusten a las siguientes características deben evaluar si la memoria caché integrada ayudará a reducir los costos:

-   Cargas de trabajo con mucha actividad de lectura
-   Muchas lecturas puntuales repetidas en elementos grandes
-   Muchas consultas de RU elevadas repetidas
-   Clave de partición activa para lecturas

El factor más importante en el ahorro esperado es el grado en que las lecturas se repiten. Si la carga de trabajo ejecuta de forma coherente las mismas lecturas puntuales o consultas en un breve período de tiempo, es una excelente candidata para la caché integrada. Cuando se usa la memoria caché integrada para lecturas repetidas, solo se usan RU para la primera lectura. Las lecturas posteriores enrutadas a través del mismo nodo de puerta de enlace dedicado (dentro de la ventana `MaxIntegratedCacheStaleness` y si no se han expulsado los datos) no usarán el rendimiento.

Algunas cargas de trabajo no deben tener en cuenta la memoria caché integrada, entre las que se incluyen:

-   Cargas de trabajo con mucha actividad de escritura
-  Consultas o lecturas puntuales que rara vez se repiten

## <a name="item-cache"></a>Caché de elementos

Puede usar la caché de elementos para las lecturas puntuales (es decir, las búsquedas de clave-valor basadas en el identificador de elemento y la clave de partición).

### <a name="populating-the-item-cache"></a>Rellenado de la caché de elementos

- Las nuevas escrituras, actualizaciones y eliminaciones se rellenan automáticamente en la caché de elementos
- Si la aplicación intenta leer un elemento específico que no estaba anteriormente en la caché (error de caché), el elemento se almacenaría ahora en la caché de elementos.

### <a name="item-cache-invalidation-and-eviction"></a>Invalidación y expulsión de la caché de elementos

- Actualización o eliminación del elemento
- Menos usados recientemente (LRU)
- Tiempo de retención en caché (es decir, `MaxIntegratedCacheStaleness`)

## <a name="query-cache"></a>Caché de consultas

La caché de consulta se puede usar para almacenar en caché las consultas. La caché de consulta transforma una consulta en una búsqueda clave-valor donde la clave es el texto de la consulta y el valor son los resultados de la consulta. La memoria caché integrada no tiene un motor de consultas, solo almacena la búsqueda de clave-valor para cada consulta.

### <a name="populating-the-query-cache"></a>Rellenado de la caché de consulta

- Si la caché no tiene un resultado para esa consulta (error de caché), la consulta se envía al back-end. Una vez ejecutada la consulta, la caché almacenará sus resultados.

### <a name="query-cache-eviction"></a>Expulsión de la caché de consulta

- Menos usados recientemente (LRU)
- Tiempo de retención en caché (es decir, `MaxIntegratedCacheStaleness`)

### <a name="working-with-the-query-cache"></a>Uso de la caché de consulta

No necesita código especial al trabajar con la caché de consulta, incluso si las consultas tienen varias páginas de resultados. Los procedimientos recomendados y el código para la paginación de consultas son los mismos, tanto si la consulta alcanza la caché integrada como si se ejecuta en el motor de consulta back-end.

La caché de consulta almacenará en caché automáticamente los tokens de continuación de consultas, si procede. Si tiene una consulta con varias páginas de resultados, las páginas almacenadas en la caché integrada tendrán un cargo de RU de 0. Si las páginas posteriores de los resultados de la consulta requieren la ejecución del back-end, tendrán un token de continuación de la página anterior para que puedan evitar duplicar el trabajo anterior.

> [!NOTE]
> Las instancias de caché integradas dentro de distintos nodos de puerta de enlace dedicados tienen cachés independientes entre sí. Si los datos se almacenan en caché dentro de un nodo, no necesariamente se almacenan en caché en los demás.

## <a name="integrated-cache-consistency"></a>Coherencia de la caché integrada

La caché integrada solo admite la [coherencia](consistency-levels.md) posible y de sesión. Si una lectura tiene un prefijo coherente, obsolescencia limitada o coherencia alta, siempre omitirá la caché integrada.

La manera más fácil de configurar la coherencia posible o de sesión para todas las lecturas consiste en [establecerla en el nivel de cuenta](consistency-levels.md#configure-the-default-consistency-level). Sin embargo, si solo desea que algunas de las lecturas tengan coherencia específica, también puede configurar la coherencia en el [nivel de solicitud](how-to-manage-consistency.md#override-the-default-consistency-level).

### <a name="session-consistency"></a>Coherencia de sesión

La [coherencia de sesión](consistency-levels.md#session-consistency) es el nivel de coherencia más usado para regiones únicas, así como para cuentas de Azure Cosmos DB distribuidas globalmente. Al usar la coherencia de sesión, las sesiones de cliente único pueden leer sus propias escrituras. Al usar la caché integrada, los clientes que están fuera de la sesión que realiza escrituras verán la coherencia posible.

## <a name="maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness

`MaxIntegratedCacheStaleness` es la mayor obsolescencia aceptable para las lecturas y consultas de punto en caché, independientemente de la coherencia seleccionada. `MaxIntegratedCacheStaleness` se puede configurar en el nivel de solicitud. Por ejemplo, si establece un `MaxIntegratedCacheStaleness` de 2 horas, la solicitud solo devolverá datos en caché si los datos tienen menos de 2 horas de antigüedad. Para aumentar la probabilidad de que las lecturas repetidas utilicen la caché integrada, debe establecer el `MaxIntegratedCacheStaleness` a la altura máxima que permitan los requisitos empresariales.

Es importante saber que `MaxIntegratedCacheStaleness`, cuando se configura en una solicitud que acaba rellenando la caché, no afecta al tiempo que esa solicitud se almacenará en caché. `MaxIntegratedCacheStaleness` aplica coherencia al intentar usar datos en caché. No hay ningún valor de retención de caché o TTL global, por lo que los datos solo se expulsarán de la caché si la caché integrada está llena o se ejecuta una nueva lectura con una `MaxIntegratedCacheStaleness` inferior a la antigüedad de la entrada almacenada en caché actual.

Se trata de una mejora con respecto al funcionamiento de la mayoría de las memorias caché y permite la siguiente personalización adicional:

- Puede establecer distintos requisitos de estancamiento para cada lectura o consulta puntuales.
- Los diferentes clientes, incluso si ejecutan la misma lectura o consulta puntual, pueden configurar valores `MaxIntegratedCacheStaleness` diferentes.
- Si deseara modificar la coherencia de lectura al usar datos en caché, el cambio de `MaxIntegratedCacheStaleness` tendrá un efecto inmediato en la coherencia de lectura.

> [!NOTE]
> Cuando no se configura explícitamente, el valor predeterminado de MaxIntegratedCacheStaleness es 5 minutos.

Para conocer mejor el parámetro `MaxIntegratedCacheStaleness`, considere el ejemplo siguiente:

| Time       | Solicitud                                         | Response                                                     |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| t = 0 s  | Ejecutar Consulta A con MaxIntegratedCacheStaleness = 30 segundos | Devolver resultados de una base de datos de back-end (cargos de RU normales) y llenar la caché     |
| t = 0 s  | Ejecutar Consulta B con MaxIntegratedCacheStaleness = 60 segundos | Devolver resultados de una base de datos de back-end (cargos de RU normales) y llenar la caché     |
| t = 20 s | Ejecutar Consulta A con MaxIntegratedCacheStaleness = 30 segundos | Devolver resultados de la caché integrada (cargo de 0 RU)           |
| t = 20 s | Ejecutar Consulta B con MaxIntegratedCacheStaleness = 60 segundos | Devolver resultados de la caché integrada (cargo de 0 RU)           |
| t = 40 s | Ejecutar Consulta A con MaxIntegratedCacheStaleness = 30 segundos | Devolver resultados de una base de datos de back-end (cargos de RU normales) y actualizar la caché |
| t = 40 s | Ejecutar Consulta B con MaxIntegratedCacheStaleness = 60 segundos | Devolver resultados de la caché integrada (cargo de 0 RU)           |
| t = 50 s | Ejecutar Consulta B con MaxIntegratedCacheStaleness = 20 segundos | Devolver resultados de una base de datos de back-end (cargos de RU normales) y actualizar la caché |

> [!NOTE]
> La personalización de `MaxIntegratedCacheStaleness` solo se admite en los SDK de versión preliminar de .NET y Java más recientes.

[Aprenda a configurar `MaxIntegratedCacheStaleness`.](how-to-configure-integrated-cache.md#adjust-maxintegratedcachestaleness)

## <a name="metrics"></a>Métricas

Al usar la caché integrada, resulta útil supervisar algunas métricas clave. Las métricas de la caché integrada incluyen:

- `DedicatedGatewayAverageCpuUsage`: uso medio de CPU en nodos de puerta de enlace dedicada.
- `DedicatedGatewayMaxCpuUsage`: uso máximo de CPU en nodos de puerta de enlace dedicada.
- `DedicatedGatewayAverageMemoryUsage`: uso medio de la memoria en los nodos de puerta de enlace dedicada, que se usan tanto para las solicitudes de enrutamiento como para los datos de almacenamiento en caché.
- `DedicatedGatewayRequests`: número total de solicitudes de puerta de enlace dedicada en todas las instancias de puerta de enlace dedicada.
- `IntegratedCacheEvictedEntriesSize`: la cantidad media de datos expulsados debido a la LRU de la caché integrada en los nodos de puerta de enlace dedicada. Este valor no incluye los datos que hayan expiraron debido a que se superó el tiempo de `MaxIntegratedCacheStaleness`.
- `IntegratedCacheItemExpirationCount`: el número de elementos que se expulsan de la caché integrada debido a que las lecturas puntuales almacenadas en caché superan el tiempo `MaxIntegratedCacheStaleness`. Este valor es un promedio de las instancias de caché integradas en todos los nodos de puerta de enlace dedicada.
- `IntegratedCacheQueryExpirationCount`: el número de consultas que se expulsan de la caché integrada debido a que las consultas almacenadas en caché superan el tiempo `MaxIntegratedCacheStaleness`. Este valor es un promedio de las instancias de caché integradas en todos los nodos de puerta de enlace dedicada.
- `IntegratedCacheItemHitRate`: la proporción de lecturas puntuales que usaban la caché (de todas las lecturas puntuales enrutadas a través de la puerta de enlace dedicada con coherencia final o de sesión). Este valor es un promedio de las instancias de caché integradas en todos los nodos de puerta de enlace dedicada.
- `IntegratedCacheQueryHitRate`: la proporción de consultas que usaban la caché (de todas las consultas enrutadas a través de la puerta de enlace dedicada con coherencia final o de sesión). Este valor es un promedio de las instancias de caché integradas en todos los nodos de puerta de enlace dedicada.

Todas las métricas existentes están disponibles, de manera predeterminada, en la hoja **Métricas** (no en la hoja de métricas clásica):

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="Imagen que muestra la ubicación de las métricas de la caché integrada" border="false":::

Las métricas son un promedio, el máximo o la suma en todos los nodos de puerta de enlace dedicada. Por ejemplo, si aprovisiona un clúster de puerta de enlace dedicada con cinco nodos, las métricas reflejan el valor agregado en los cinco nodos. No es posible determinar los valores de métrica de cada nodo individual.

## <a name="troubleshooting-common-issues"></a>Solucionar los problemas comunes

En los ejemplos siguientes, se muestra cómo depurar algunos escenarios comunes:

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>No sé si mi aplicación usa la puerta de enlace dedicada

Active `DedicatedGatewayRequests`. Esta métrica incluye todas las solicitudes que usan la puerta de enlace dedicada, independientemente de si han alcanzado la caché integrada. Si la aplicación usa la puerta de enlace estándar o el modo directo con la cadena de conexión original, no verá un mensaje de error, pero el valor de `DedicatedGatewayRequests` será cero.

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>No sé si mis solicitudes alcanzan la memoria caché integrada

Compruebe `IntegratedCacheItemHitRate` y `IntegratedCacheQueryHitRate`. Si ambos valores son cero, las solicitudes no alcanzan la caché integrada. Compruebe que usa la cadena de conexión de puerta de enlace dedicada, que [se conecta con el modo de puerta de enlace](sql-sdk-connection-modes.md) y que [ha establecido la coherencia de sesión o posible](consistency-levels.md#configure-the-default-consistency-level).

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>Quiero saber si mi puerta de enlace dedicada es demasiado pequeña

Compruebe `IntegratedCacheItemHitRate` y `IntegratedCacheQueryHitRate`. Si estos valores son altos (por ejemplo, por encima de 0,7-0,8), esto es una buena señal de que la puerta de enlace dedicada es suficientemente grande.

Si `IntegratedCacheItemHitRate` o `IntegratedCacheEvictedEntriesSize` son bajos, observe `IntegratedCacheQueryHitRate`. Si `IntegratedCacheEvictedEntriesSize` es alto, puede significar que un mayor tamaño de puerta de enlace dedicada sería beneficioso. Puede experimentar aumentando el tamaño de la puerta de enlace dedicada y comparando los nuevos `IntegratedCacheItemHitRate` y `IntegratedCacheQueryHitRate`. Si una puerta de enlace dedicada mayor no mejora `IntegratedCacheItemHitRate` o `IntegratedCacheQueryHitRate`, es posible que las lecturas simplemente no se repitan lo suficiente como para que la caché integrada resulte afectada.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>Quiero saber si mi puerta de enlace dedicada es demasiado grande

Es más difícil medir si una puerta de enlace dedicada es demasiado grande que medir si una puerta de enlace dedicada es demasiado pequeña. En general, debe empezar por valores pequeños y aumentar lentamente el tamaño de la puerta de enlace dedicada hasta que `IntegratedCacheItemHitRate` y `IntegratedCacheQueryHitRate` dejen de mejorar. En algunos casos, solo una de las dos métricas de aciertos de caché serán importantes, no ambas. Por ejemplo, si la carga de trabajo es principalmente consultas, en lugar de lecturas puntuales, `IntegratedCacheQueryHitRate` es mucho más importante que `IntegratedCacheItemHitRate`.

Si la mayoría de los datos se expulsa de la memoria caché debido a que se supera `MaxIntegratedCacheStaleness`, en lugar de LRU, la memoria caché podría ser mayor de lo necesario. Si la combinación de `IntegratedCacheItemExpirationCount` y `IntegratedCacheQueryExpirationCount` es casi tan grande como `IntegratedCacheEvictedEntriesSize`, puede experimentar con un tamaño de puerta de enlace dedicada menor y comparar el rendimiento.

### <a name="i-want-to-understand-if-i-need-to-add-more-dedicated-gateway-nodes"></a>Quiero saber si necesito agregar más nodos de puerta de enlace dedicada

En algunos casos, si la latencia es inesperadamente alta, puede que necesite más nodos de puerta de enlace dedicada, en lugar de nodos mayores. Consulte que `DedicatedGatewayMaxCpuUsage` y `DedicatedGatewayAverageMemoryUsage` para determinar si agregar más nodos de puerta de enlace dedicada reduciría la latencia. Es bueno tener en cuenta que, dado que todas las instancias de la caché integrada son independientes entre sí, la incorporación de más nodos de puerta de enlace dedicada no reducirá `IntegratedCacheEvictedEntriesSize`. Sin embargo, la incorporación de más nodos mejorará el volumen de solicitudes que puede controlar el clúster de puerta de enlace dedicado.

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes sobre caché integrada](integrated-cache-faq.md)
- [Configuración de la memoria caché integrada](how-to-configure-integrated-cache.md)
- [Puerta de enlace dedicada](dedicated-gateway.md)
- ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de bases de datos existente.
    - Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    - Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
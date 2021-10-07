---
title: Escalado de una instancia de Azure Cache for Redis
description: Aprenda a escalar sus instancias de Azure Cache for Redis mediante Azure Portal y herramientas tales como Azure PowerShell y la CLI de Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 5c8bf3839c7bae9b1c93b201bb52a4e7be371904
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538153"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Escalado de una instancia de Azure Cache for Redis

Azure Cache for Redis tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la memoria caché. En el caso de una caché Básica, Estándar o Prémium, puede cambiar su tamaño y nivel después de crearla para que se ajuste a las necesidades de la aplicación. En este artículo se muestra cómo escalar la memoria caché en Azure Portal o con herramientas tales como Azure PowerShell y la CLI de Azure.

## <a name="when-to-scale"></a>Cuándo se debe escalar

Puede utilizar las características de [supervisión](cache-how-to-monitor.md) de Azure Cache for Redis para supervisar el estado y el rendimiento de la memoria caché. Use esa información para determinar cuándo escalar la memoria caché.

Puede supervisar las métricas siguientes para ayudar a determinar si necesita escalado.

- Carga de servidor de Redis
  - El servidor de Redis es un proceso de un solo subproceso. Una carga elevada del servidor de Redis significa que el servidor no puede seguir el ritmo de las solicitudes de todas las conexiones de cliente. En estas situaciones, ayuda a habilitar la agrupación en clústeres o a aumentar el recuento de particiones para que las funciones de sobrecarga se distribuyan entre varios procesos de Redis. La agrupación en clústeres y los recuentos de particiones más grandes distribuyen el cifrado y descifrado de TLS, y la conexión y desconexión de TLS.
  - Para más información, consulte [Configuración de la agrupación en clústeres](cache-how-to-premium-clustering.md#set-up-clustering).
- Uso de la memoria
  - Un uso elevado de memoria indica que el tamaño de los datos es demasiado grande para el tamaño de caché actual. Considere la posibilidad de escalar a un tamaño de caché con más memoria.
- Conexiones de cliente
  - Cada tamaño de caché tiene un límite en el número de conexiones de cliente que puede admitir. Si las conexiones de cliente están cerca del límite del tamaño de caché, considere la posibilidad de escalar verticalmente a un nivel mayor o de escalar horizontalmente para habilitar la agrupación en clústeres y aumentar el recuento de particiones. Su elección depende del uso de memoria y la carga del servidor de Redis.
  - Para obtener más información sobre los límites de conexión por tamaño de caché, consulte [Preguntas frecuentes sobre Azure Cache for Redis](./cache-planning-faq.yml).
- Ancho de banda de red
  - Si el servidor de Redis supera el ancho de banda disponible, las solicitudes de los clientes podrían agotar el tiempo de espera debido a la incapacidad del servidor para insertar datos en el cliente lo suficientemente rápido. Consulte las métricas de "Lectura de caché" y "Escritura de caché" para ver el ancho de banda del lado servidor que se está usando. Si el servidor de Redis supera el ancho de banda de red disponible, considere la posibilidad de escalar verticalmente a un tamaño de caché mayor con más ancho de banda de red.
  - Para obtener más información sobre el ancho de banda de red disponible por tamaño de caché, consulte [Preguntas frecuentes sobre Azure Cache for Redis](./cache-planning-faq.yml).

Si determina que la memoria caché ya no cumple los requisitos de su aplicación, puede escalar a un plan de tarifa de caché adecuado para su aplicación. Puede elegir una memoria caché más grande o más pequeña para satisfacer sus necesidades.

Para obtener más información sobre cómo determinar el plan de tarifa de caché que se va a usar, consulte [Elección del nivel correcto](cache-overview.md#choosing-the-right-tier) y [Preguntas frecuentes sobre Azure Cache for Redis](./cache-planning-faq.yml).

## <a name="scale-a-cache"></a>Escalado de una caché

Para escalar la memoria caché, [vaya a la caché](cache-configure.md#configure-azure-cache-for-redis-settings) en [Azure Portal](https://portal.azure.com) y seleccione **Escalar** en el lado izquierdo.

:::image type="content" source="media/cache-how-to-scale/scale-a-cache.png" alt-text="Escalado en el menú de recursos":::

Elija un plan de tarifa a la derecha y, a continuación, elija **Seleccionar**.

:::image type="content" source="media/cache-how-to-scale/select-a-tier.png" alt-text="Niveles de Azure Cache for Redis":::

Puede escalar a un plan de tarifa diferente con las siguientes restricciones:

- No se puede escalar desde un plan de tarifa superior a un plan de tarifa inferior.
  - No puede cambiar de una memoria caché **Premium** a una memoria caché **Estándar** o **Básica**.
  - No puede cambiar de una memoria caché **Estándar** a una memoria caché **Básica**.
- Puede cambiar de una memoria caché **Básica** a una memoria caché **Estándar**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, después puede realizar una operación de escalado hasta el tamaño deseado.
- No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. En primer lugar, escale desde **Básica** a **Estándar** en una operación de escalado y, después, desde **Estándar** a **Prémium** en la siguiente operación de escalado.
- No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)** . Sin embargo, puede reducir verticalmente a cualquier otro tamaño dentro del mismo plan de tarifa. Por ejemplo, puede reducir verticalmente del estándar C5 al estándar C1.

Durante la operación de escalado de la memoria caché al nuevo plan de tarifa, se muestra la notificación **Escalando Redis Cache**.

:::image type="content" source="media/cache-how-to-scale/scaling-notification.png" alt-text="Notificación de escalado":::

Cuando se completa el escalado, el estado cambia de **Escalado** a **En ejecución**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatización de una operación de escalado

Puede escalar las instancias de caché en el Azure Portal. Además, puede escalar mediante cmdlets de PowerShell, la CLI de Azure y las Bibliotecas de administración de Microsoft Azure (MAML).

- [Escalado mediante PowerShell](#scale-using-powershell)
- [Escalado con la CLI de Azure](#scale-using-azure-cli)
- [Escalado mediante MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Escalado mediante PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Las instancias de Azure Cache for Redis se pueden escalar con PowerShell con el cmdlet [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) cuando se modifican las propiedades `Size`, `Sku` o `ShardCount`. En el ejemplo siguiente se muestra cómo escalar una caché denominada `myCache` a una caché de 2,5 GB.

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Para obtener más información sobre cómo escalar con PowerShell, consulte [Escalado de una instancia de Azure Cache for Redis con PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Escalado con la CLI de Azure

Para escalar las instancias de Azure Cache for Redis con la CLI de Azure, llame al comando `azure rediscache set` y pase los cambios de configuración que quiera que incluyen un nuevo tamaño, SKU o tamaño de clúster, en función de la operación de escalado deseada.

Para obtener más información sobre el escalado con la CLI de Azure, consulte [Modifique la configuración de una instancia existente de Azure Cache for Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Escalado mediante MAML

Para escalar las instancias de Azure Cache for Redis mediante [Microsoft Azure Management Libraries (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), llame al método `IRedisOperations.CreateOrUpdate` y pase el nuevo tamaño para `RedisProperties.SKU.Capacity`.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Para obtener más información, consulte el ejemplo [Manage Azure Cache for Redis using MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) (Administración de Azure Caché for Redis mediante MAML).

## <a name="scaling-faq"></a>Preguntas frecuentes de escalado

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Azure Cache for Redis.

- [¿Puedo realizar operaciones de escalado en una memoria caché Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
- [Después de escalar, ¿tengo que cambiar el nombre de la memoria caché o las teclas de acceso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
- [¿Cómo funciona el escalado?](#how-does-scaling-work)
- [¿Se pierden los datos de mi memoria caché durante el escalado?](#will-i-lose-data-from-my-cache-during-scaling)
- [¿Mi configuración de bases de datos personalizada se ve afectada durante el escalado?](#is-my-custom-databases-setting-affected-during-scaling)
- [¿La caché estará disponible durante el escalado?](#will-my-cache-be-available-during-scaling)
- [¿Qué limitaciones de escalado se aplican a la replicación geográfica?](#are-there-scaling-limitations-with-geo-replication)
- [Operaciones que no se admiten](#operations-that-arent-supported)
- [¿Cuánto tarda el escalado?](#how-long-does-scaling-take)
- [¿Cómo puedo saber si el escalado ha terminado?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>¿Puedo realizar operaciones de escalado en una memoria caché Premium?

- No puede escalar desde una caché **Premium** a un plan de tarifa **Básico** o **Estándar**.
- Puede escalar desde un plan de tarifa de caché **Premium** a otro.
- No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. En primer lugar, escale desde **Básica** a **Estándar** en una operación de escalado y, después, desde **Estándar** a **Prémium** en una operación de escalado posterior.
- Si ha habilitado la agrupación en clústeres cuando creó su caché **Premium**, puede [cambiar el tamaño de clúster](cache-how-to-premium-clustering.md#cluster-size). Si su caché se creó sin habilitar la agrupación en clústeres, puede configurar la agrupación en clústeres después.

Para más información, consulte [How to configure clustering for a Premium Azure Cache for Redis](cache-how-to-premium-clustering.md) (Configuración de la agrupación en clústeres para una instancia de Azure Cache for Redis Prémium).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Después de escalar, ¿tengo que cambiar el nombre de la memoria caché o las teclas de acceso?

No, el nombre de la memoria caché y las claves no se cambian durante una operación de escalado.

### <a name="how-does-scaling-work"></a>¿Cómo funciona el escalado?

- Cuando se escala una memoria caché **Básica** a un tamaño diferente, esta se cierra y se aprovisiona una nueva caché con el nuevo tamaño. Durante este tiempo, la caché no está disponible y se pierden todos los datos en la memoria caché.
- Cuando se escala una memoria caché del plan **Básico** al plan **Estándar**, se aprovisiona una caché de réplica y los datos se copian de la caché principal a la de réplica. La memoria caché permanece disponible durante el proceso de escalado.
- Cuando se escala una memoria caché del plan **Estándar** a un tamaño diferente o a una del plan **Prémium**, se cierra una de las réplicas y se vuelve a aprovisionar para el nuevo tamaño y los datos se transfieren a través de ella y, después, la otra realiza una conmutación por error antes de volverse a aprovisionar, un proceso que es similar al que se produce durante un error en uno de los nodos de la memoria caché.
- Al escalar horizontalmente una caché en clúster, se aprovisionan nuevas particiones y se agregan al clúster de servidores de Redis. A continuación, los datos se vuelven a particionar en todas las particiones.
- Cuando se reduce horizontalmente una caché en clúster, primero se vuelven a particionar los datos y, a continuación, el tamaño del clúster se reduce a las particiones necesarias.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>¿Se pierden los datos de mi memoria caché durante el escalado?

- Cuando se escala una memoria caché **Básica** a un nuevo tamaño, se pierden todos los datos y la memoria caché no está disponible durante la operación de escalado.
- Cuando se escala una memoria caché del plan **Básico** al plan **Estándar**, normalmente se conservan los datos de la memoria caché.
- Cuando se escala una memoria caché del plan **Estándar** a un tamaño o plan superior, o cuando una del plan **Prémium** se escala a un tamaño superior, normalmente se conservan todos los datos. Al escalar una caché estándar o Prémium a un tamaño más pequeño, los datos se pueden perder si el tamaño de los datos supera el nuevo tamaño más pequeño cuando se escala verticalmente. Si se pierden datos al reducir, las claves se expulsan mediante el directiva de expulsión [allkeys-lru](https://redis.io/topics/lru-cache) .

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>¿Mi configuración de bases de datos personalizada se ve afectada durante el escalado?

Si ha configurado un valor personalizado para el parámetro `databases` al crear la memoria caché, tenga en cuenta que algunos planes de tarifa tienen diferentes [límites de bases de datos](cache-configure.md#databases). Estos son algunas de los aspectos que considerar al escalar en este escenario:

- Cuando se escala a un plan de tarifa con un límite de `databases` menor que el nivel actual:
  - Si usa el número predeterminado de `databases`, que es 16 para todos los planes de tarifa, no se pierden datos.
  - Si utiliza un número personalizado de `databases` que está dentro de los límites del plan al que va a escalar, se mantiene la configuración de `databases` y no se pierden datos.
  - Si usa un número personalizado de `databases` que supera los límites del nuevo plan, la configuración de `databases` se reduce a los límites del nuevo plan y se pierden todos los datos de las bases de datos quitadas.
- Cuando se escala a un plan de tarifa con el mismo límite de `databases` o mayor que el plan actual, la configuración de `databases` se mantiene y no se pierden datos.

Mientras que las cachés Estándar y Prémium tienen un Acuerdo de Nivel de Servicio del 99,9 % de disponibilidad, no hay ningún Acuerdo de Nivel de Servicio para la pérdida de datos.

### <a name="will-my-cache-be-available-during-scaling"></a>¿La caché estará disponible durante el escalado?

- Las memorias caché de los planes **Estándar** y **Premium** permanecen disponibles durante la operación de escalado. Sin embargo, pueden producirse interrupciones momentáneas de conexión mientras se escalan las memorias caché Estándar y Premium, así como al escalar de Básica a Estándar. Estas interrupciones momentáneas de conexión deberían ser breves y los clientes de Redis, por lo general, deberían poder volver a establecer su conexión al instante.
- Las memorias caché **Básicas** están sin conexión durante las operaciones de escalado a un tamaño diferente. Las memorias caché Básicas siguen estando disponibles al escalar del plan **Básico** al **Estándar**, pero pueden experimentar una breve interrupción momentánea de conexión. En caso de producirse una interrupción momentánea de la conexión, por lo general los clientes de Redis pueden volver a establecer conexión al instante.

### <a name="are-there-scaling-limitations-with-geo-replication"></a>¿Qué limitaciones de escalado se aplican a la replicación geográfica?

Con la replicación geográfica configurada, es posible que observe que no puede escalar una caché o cambiar las particiones de un clúster. Un vínculo de replicación geográfica entre dos cachés impide la operación de escalado o el cambio del número de particiones de un clúster. Debe desvincular la memoria caché para emitir estos comandos. Para obtener más información, consulte [Configuración de replicación geográfica](cache-how-to-geo-replication.md).

### <a name="operations-that-arent-supported"></a>Operaciones que no se admiten

- No se puede escalar desde un plan de tarifa superior a un plan de tarifa inferior.
  - No puede cambiar de una memoria caché **Premium** a una memoria caché **Estándar** o **Básica**.
  - No puede cambiar de una memoria caché **Estándar** a una memoria caché **Básica**.
- Puede cambiar de una memoria caché **Básica** a una memoria caché **Estándar**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado hasta el tamaño deseado en un momento posterior.
- No puede escalar de una memoria caché **Básica** directamente a una memoria caché **Premium**. En primer lugar, escale del plan **Básico** al **Estándar** en una operación de escalado y, después, del plan **Estándar** al **Prémium** en una operación posterior.
- No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)** .

Si se produce un error en una operación de escalado, el servicio intenta revertir la operación y la memoria caché se restablecerá al tamaño original.

### <a name="how-long-does-scaling-take"></a>¿Cuánto tarda el escalado?

El tiempo de escalado depende de algunos factores. Estos son algunos factores que pueden afectar al tiempo que tarda el escalado.

- Cantidad de datos: las grandes cantidades de datos tardan más tiempo en replicarse.
- Solicitudes de escritura elevadas: un mayor número de escrituras significa más replicaciones de datos entre nodos o particiones.
- Carga elevada del servidor: una mayor carga de servidor significa que el servidor de Redis está ocupado y tiene ciclos de CPU limitados para completar la redistribución de datos.

Por lo general, al escalar una memoria caché sin datos, la operación tarda aproximadamente 20 minutos. En el caso de las memorias caché en clúster, el escalado tarda aproximadamente 20 minutos por partición con una cantidad de datos mínima.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>¿Cómo puedo saber si el escalado ha terminado?

En Azure Portal puede ver la operación de escalado en curso. Cuando se completa el escalado, el estado de la memoria caché cambia de **En ejecución**.

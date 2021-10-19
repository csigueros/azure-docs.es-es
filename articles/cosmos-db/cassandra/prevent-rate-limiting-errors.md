---
title: Prevención de errores de limitación de velocidad de Azure Cosmos DB API para Cassandra
description: Impida que se produzcan errores de limitación de velocidad en las operaciones de Azure Cosmos DB API para Cassandra con la característica Reintento del lado servidor (SSR).
author: dileepraotv-github
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/11/2021
ms.author: turao
ms.openlocfilehash: 469dc3f87bb7c783f2c3138e2bcf592c85312006
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754753"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-cassandra-operations"></a>Prevención de errores de limitación de velocidad de operaciones de Azure Cosmos DB API para Cassandra
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB normaliza el coste de todas las operaciones de base de datos, que se expresa en unidades de solicitud (RU). Las unidades de solicitud son como una moneda de rendimiento, que abstrae los recursos del sistema, como CPU, IOPS y memoria, necesarios para realizar las operaciones de base de datos compatibles con Azure Cosmos DB.

Es posible que se produzcan errores de limitación de velocidad (429) en las operaciones de Cassandra API de Azure Cosmos DB si superan el límite de rendimiento de una tabla (RU). Esta situación se puede gestionar en el lado cliente tal y como se describe en la directiva de reintentos, en el [documento de recomendaciones de Cassandra API](https://devblogs.microsoft.com/cosmosdb/cassandra-api-java/#retry-policy). Si no se puede implementar la directiva de reintentos de cliente para gestionar el fallo debido a un error de limitación de velocidad, podemos usar la característica Reintento del lado servidor (SSR), donde las operaciones que superen el límite de rendimiento de una tabla se reintentarán automáticamente transcurrido un breve intervalo. Se trata de un valor de nivel de cuenta que se aplica a todos los espacios de claves y tablas de la cuenta.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Vaya a su cuenta de Azure Cosmos DB API para Cassandra.

3. Vaya al panel **Características** debajo de la sección **Configuración**.

4. Seleccione **Reintento del lado servidor**.

5. Haga clic en **Habilitar** para habilitar esta característica para todas las colecciones de su cuenta.

:::image type="content" source="./media/prevent-rate-limiting-errors/prevent-rate-limiting-errors.png" alt-text="Captura de pantalla de la característica Reintento del lado servidor de Azure Cosmos DB API para Cassandra":::

## <a name="use-the-azure-cli"></a>Uso de la CLI de Azure

1. Compruebe si SSR ya está habilitado para su cuenta:

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

2. **Habilite** SSR para todas las tablas de su cuenta de base de datos. Este cambio puede tardar un máximo de 15 minutos en surtir efecto.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra DisableRateLimitingResponses
   ```

3. El siguiente comando **deshabilitará** Reintento del lado servidor en todas las tablas de la cuenta de base de datos, al eliminar `DisableRateLimitingResponses` de la lista de funcionalidades. Este cambio puede tardar un máximo de 15 minutos en surtir efecto.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra
   ```

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-are-requests-retried"></a>¿Cómo se reintentan las solicitudes?

Las solicitudes se reintentan continuamente (una y otra vez) hasta que se alcanza un tiempo de espera de 60 segundos. Si se alcanza el tiempo de espera, el cliente recibirá el error de tiempo de espera de lectura o escritura correspondiente.

### <a name="when-is-ssr-most-beneficial"></a>¿Cuándo resulta más beneficioso usar SSR?

Reintento del lado servidor (SSR) resulta más beneficioso cuando hay un pico repentino durante un breve período de menos de 1 minuto en el que se pueden evitar errores de limitación. Si la carga de trabajo ha aumentado y permanece constantemente por encima de la RU especificada, SSR no será de mucha ayuda. La sugerencia es aumentar la RU según corresponda.

### <a name="suggested-client-side-settings"></a>¿Valor sugerido del lado cliente?

Después de habilitar SSR, la aplicación cliente debe aumentar el tiempo de espera de lectura más allá del valor de servidor de 60 segundos. Para mayor seguridad, se recomienda un valor de 90 segundos.

SocketOptions setReadTimeoutMillis DefaultDriverOption.REQUEST_TIMEOUT


### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>¿Cómo se pueden supervisar los efectos de un reintento en el servidor?

Puede ver los errores de limitación de velocidad (429) que se reintentan en el servidor en el panel de métricas de Cosmos DB. Estos errores no van al cliente cuando SSR está habilitado, ya que se controlan y se reintentan en el lado servidor.

Puede buscar entradas de registro que contengan *estimatedDelayFromRateLimitingInMilliseconds* en los [registros de recursos de Cosmos DB](../cosmosdb-monitor-resource-logs.md).

### <a name="will-server-side-retry-affect-my-consistency-level"></a>¿Afectará el reintento en el servidor a mi nivel de consistencia?

Reintento en el lado servidor no afecta a los niveles de coherencia. Las solicitudes se reintentan en el lado servidor si tienen la velocidad limitada (error 429).

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>¿Afecta el reintento en el servidor a cualquier tipo de error que pueda recibir mi cliente?

No, solo afecta a los errores de limitación de velocidad (429) al volver a intentar en el servidor. Esta característica evita que tenga que controlar los errores de limitación de velocidad en la aplicación cliente. Todos los [otros errores](troubleshoot-common-issues.md) irán al cliente.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la solución de errores comunes, consulte este artículo:

* [Solución de errores habituales de Azure Cosmos DB API para Cassandra](troubleshoot-common-issues.md)


Consulte los siguientes artículos para aprender sobre el aprovisionamiento del rendimiento en Azure Cosmos DB:

* [Rendimiento y unidades de solicitud en Azure Cosmos DB](../request-units.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](../how-to-provision-throughput-cassandra.md) 
* [Procedimientos recomendados de las claves de partición](../cassandra-partitioning.md)


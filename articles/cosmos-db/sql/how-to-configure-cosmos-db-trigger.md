---
title: Desencadenador de Azure Functions para la configuración avanzada de Cosmos DB
description: Aprenda a configurar el registro y la directiva de conexión que utiliza el desencadenador de Azure Functions para Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/04/2021
ms.author: maquaran
ms.openlocfilehash: 409cf65dc9b79707a7508590b370b63325101f67
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427388"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Configuración del registro y la conectividad con el desencadenador de Azure Functions para Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se describen las opciones de configuración avanzadas que se pueden establecer al usar el desencadenador de Azure Functions para Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Habilitación de registros específicos del desencadenador

El desencadenador de Azure Functions para Cosmos DB utiliza la [biblioteca de procesadores de fuente de cambios](change-feed-processor.md) internamente, y esta genera un conjunto de registros de mantenimiento que se pueden usar para supervisar las operaciones internas con [fines de solución de problemas](./troubleshoot-changefeed-functions.md).

Los registros de mantenimiento describen cómo se comporta el desencadenador de Azure Functions para Cosmos DB cuando se intentan realizar operaciones durante escenarios de equilibrio de carga o de inicialización.

### <a name="enabling-logging"></a>Habilitación del registro

Para habilitar el registro cuando se usa el desencadenador de Azure Functions para Cosmos DB, busque el archivo `host.json` en el proyecto de Azure Functions o en la aplicación de Azure Functions y [configure el nivel de registro necesario](../../azure-functions/functions-monitoring.md#log-levels-and-categories). Habilite el seguimiento de `Host.Triggers.CosmosDB`, tal como se muestra en el ejemplo siguiente:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Después de implementar la función de Azure con la configuración actualizada, verá los registros del desencadenador de Azure Functions para Cosmos DB como parte de los seguimientos. Puede ver los registros en el proveedor de registro configurado en la *categoría* `Host.Triggers.CosmosDB`.

### <a name="query-the-logs"></a>Consulta de los registros

Ejecute la siguiente consulta para obtener los registros generados por el desencadenador de Azure Functions para Cosmos DB en [Analytics de Azure Application Insights](../../azure-monitor/logs/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Configuración de la directiva de conexión

Existen dos modos de conexión: modo directo y modo de puerta de enlace. Para más información acerca de estos modos de conexión, consulte el artículo acerca de los [modos de conexión](sql-sdk-connection-modes.md). De forma predeterminada, el modo de **puerta de enlace** se utiliza para establecer todas las conexiones en el desencadenador de Azure Functions para Cosmos DB. Sin embargo, es posible que no sea la mejor opción para escenarios en los que prime el rendimiento.

### <a name="changing-the-connection-mode-and-protocol"></a>Cambio del modo y protocolo de conexión

Hay dos valores de configuración clave disponibles para configurar la directiva de conexión de cliente: el **modo de conexión** y el **protocolo de conexión**. Tanto el modo de conexión como el protocolo predeterminados que usa el desencadenador de Azure Functions para Cosmos DB se pueden cambiar, al igual que todos los [enlaces de Azure Cosmos DB](../../azure-functions/functions-bindings-cosmosdb-v2-output.md). Para cambiar la configuración predeterminada, es preciso localizar el archivo `host.json` en el proyecto de Azure Functions o en la aplicación de Azure Functions y agregar el siguiente [valor extra](../../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Donde `connectionMode` debe tener el modo de conexión deseado (directo o puerta de enlace) y `protocol` el protocolo de conexión deseado (TCP para el modo directo o HTTP para el modo de puerta de enlace).

Si el proyecto de Azure Functions funciona con el runtime de Azure Functions V1, la configuración tiene una pequeña diferencia en el nombre, debe usar `documentDB` en lugar de `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Al hospedar la aplicación de funciones en un plan de consumo, cada instancia tiene un límite en cuanto a la cantidad de conexiones de socket que puede mantener. Cuando se trabaja con el modo directo/TCP, se crean más conexiones de forma intencionada y se puede llegar al [límite del plan de consumo](../../azure-functions/manage-connections.md#connection-limit), en cuyo caso puede usar el modo de puerta de enlace u hospedar la aplicación de funciones en un [plan Prémium](../../azure-functions/functions-premium-plan.md) o en un [plan dedicado (App Service)](../../azure-functions/dedicated-plan.md).

## <a name="next-steps"></a>Pasos siguientes

* [Límites de conexiones en Azure Functions](../../azure-functions/manage-connections.md#connection-limit)
* [Habilite la supervisión](../../azure-functions/functions-monitoring.md) en las aplicaciones de Azure Functions.
* Aprenda a [diagnosticar y solucionar problemas comunes](./troubleshoot-changefeed-functions.md) cuando se usa el desencadenador de Azure Functions para Cosmos DB.
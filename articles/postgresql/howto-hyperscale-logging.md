---
title: 'Registros: Hiperescala (Citus) en Azure Database for PostgreSQL'
description: 'Procedimiento para acceder a los registros de base de datos para Azure Database for PostgreSQL: Hiperescala (Citus)'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/13/2021
ms.openlocfilehash: 72996a44892d17a44ea58405e92bb9ac6cc52c68
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546444"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Registros en Hiperescala (Citus) para Azure Database for PostgreSQL

Los registros de servidor de bases de datos de PostgreSQL están disponibles en cada nodo de un grupo de servidores de Hiperescala (Citus). Puede enviar registros a un servidor de almacenamiento o a un servicio de análisis. Los registros se pueden usar para identificar, solucionar y reparar errores de configuración y casos de rendimiento no óptimo.

## <a name="capturing-logs"></a>Registros capturados

Para acceder a los registros de PostgreSQL para un coordinador o un nodo de trabajo de Hiperescala (Citus), debe habilitar la configuración de diagnóstico PostgreSQLLogs. En Azure Portal, abra la **configuración de diagnóstico** y seleccione **+ Agregar configuración de diagnóstico**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Botón Agregar configuración de diagnóstico":::

Seleccione un nombre para la nueva configuración de diagnóstico, marque la casilla **PostgreSQLLogs** y, después, la casilla **Enviar a área de trabajo de Log Analytics**.  Después, seleccione **Guardar**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Elección de los registros de PostgreSQL":::

## <a name="viewing-logs"></a>Visualización de los registros

Para ver y filtrar los registros, usaremos consultas de Kusto. En Azure Portal, abra **Registros** para el grupo de servidores de Hiperescala (Citus). Si aparece un cuadro de diálogo de selección de consulta, ciérrelo:

:::image type="content" source="media/howto-hyperscale-logging/logs-dialog.png" alt-text="Página Registros con el cuadro de diálogo abierto":::

A continuación, verá un cuadro de entrada para escribir consultas.

:::image type="content" source="media/howto-hyperscale-logging/logs-query.png" alt-text="Cuadro de entrada para consultar registros":::

Escriba la siguiente consulta y seleccione el botón **Ejecutar**.

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
```

En la consulta anterior se enumeran los mensajes de registro de todos los nodos, junto con su gravedad y marca de tiempo. Puede agregar cláusulas `where` para filtrar los resultados. Por ejemplo, para ver los errores solo del nodo de coordinación, filtre el nivel de error y el nombre del servidor de la siguiente manera:

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
| where LogicalServerName_s == 'example-server-group-c'
| where errorLevel_s == 'ERROR'
```

Reemplace el nombre del servidor en el ejemplo anterior por el nombre de su servidor. El nombre del nodo de coordinación tiene el sufijo `-c` y los nodos de trabajo se nombran con un sufijo de `-w0`, `-w1`, etc.

Hay varias formas de filtrar los registros de Azure. Aquí se muestra cómo buscar registros del último día cuyos mensajes coinciden con una expresión regular.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(24h)
| order by TimeGenerated desc
| where Message matches regex ".*error.*"
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a las consultas de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
- Información acerca de [Azure Event Hubs](../event-hubs/event-hubs-about.md)

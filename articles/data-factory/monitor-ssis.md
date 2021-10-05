---
title: Supervisión de operaciones de SSIS con Azure Monitor
description: Aprenda a usar Azure Monitor para supervisar las operaciones de SSIS en Azure Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 5c03304933bb9c89b276f4950aea355436393ba2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837938"
---
# <a name="monitor-ssis-operations-with-azure-monitor"></a>Supervisión de operaciones de SSIS con Azure Monitor

Para migrar mediante lift-and-shift las cargas de trabajo de SSIS, puede [aprovisionar una instancia de SSIS IR en ADF](./tutorial-deploy-ssis-packages-azure.md) que admita:

- La ejecución de paquetes implementados en el catálogo de SSIS (SSISDB) hospedados por un servidor de Azure SQL Database o por Instancia administrada (modelo de implementación de proyectos)
- La ejecución de paquetes implementados en el sistema de archivos, en Azure Files o en una base de datos de SQL Server (MSDB) hospedados por Instancia administrada de Azure SQL (modelo de implementación de paquetes)

Una vez aprovisionado, puede [comprobar el estado operativo de SSIS IR mediante Azure PowerShell o en el centro de **Monitor** del portal de ADF](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Con el modelo de implementación de proyectos, los registros de ejecución de paquetes SSIS se almacenan en tablas o vistas internas de SSISDB, de modo que puede consultarlas, analizarlas y presentarlas visualmente mediante herramientas designadas, como SSMS. Con el modelo de implementación de paquetes, los registros de ejecución de paquetes SSIS se pueden almacenar en el sistema de archivos o en Azure Files como archivos CSV que todavía debe analizar y procesar con otras herramientas designadas antes de que pueda consultarlas, analizarlas y presentarlas visualmente.

Ahora, con la integración de [Azure Monitor](../azure-monitor/data-platform.md), puede consultar, analizar y presentar visualmente todas las métricas y registros generados a partir de operaciones de SSIS IR y ejecuciones de paquetes SSIS en Azure Portal. Además, puede generar alertas sobre dichas métricas.

## <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Configuración de las opciones de diagnóstico y del área de trabajo para las operaciones de SSIS

Para enviar todas las métricas y los registros generados por las operaciones de SSIS IR y las ejecuciones de paquetes SSIS a Azure Monitor, debe [configurar las opciones de diagnóstico y el área de trabajo para la instancia de ADF](monitor-configure-diagnostics.md).

## <a name="ssis-operational-metrics"></a>Métricas operativas de SSIS

Las [métricas](../azure-monitor/essentials/data-platform-metrics.md) operativas de SSIS son contadores de rendimiento o valores numéricos que describen el estado de las operaciones de inicio y detención de SSIS IR, así como de las ejecuciones de paquetes SSIS en un momento determinado. Forman parte de las [métricas de ADF en Azure Monitor](monitor-metrics-alerts.md).

Al configurar las opciones de diagnóstico y el área de trabajo de la instancia de ADF en Azure Monitor, al activar la casilla _AllMetrics_, las métricas operativas de SSIS estarán disponibles para los [análisis interactivos con el Explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md), la [presentación en el panel de Azure](../azure-monitor/app/tutorial-app-dashboards.md) y las [alertas casi en tiempo real](../azure-monitor/alerts/alerts-metric.md).

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Asignación de un nombre a la configuración y selección de un área de trabajo de Log Analytics":::

## <a name="ssis-operational-alerts"></a>Alertas operativas de SSIS

Para generar alertas relativas a las métricas operativas de SSIS desde el portal de ADF, [seleccione la página **Alertas y métricas** del centro **Monitor** de ADF y siga las instrucciones detalladas proporcionadas](./monitor-visually.md#alerts).

:::image type="content" source="media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png" alt-text="Generación de alertas operativas de SSIS desde el portal de ADF":::

Para generar alertas relativas a las métricas operativas de SSIS desde Azure Portal, [seleccione la página **Alertas** del centro de Azure **Monitor** y siga las instrucciones detalladas proporcionadas](monitor-metrics-alerts.md).

:::image type="content" source="media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png" alt-text="Generación de alertas operativas de SSIS desde Azure Portal":::

## <a name="ssis-operational-logs"></a>Registros operativos de SSIS

Los [registros](../azure-monitor/logs/data-platform-logs.md) operativos de SSIS son eventos generados por las operaciones de SSIS IR y las ejecuciones de paquetes SSIS que proporcionan suficiente contexto sobre los problemas identificados y son útiles para el análisis de la causa principal. 

Al configurar las opciones de diagnóstico y el área de trabajo de la instancia de ADF en Azure Monitor, puede seleccionar los registros operativos de SSIS correspondientes y enviarlos a Log Analytics, que se basa en Azure Data Explorer. Allí, estarán disponibles para [realizar análisis mediante el lenguaje de consulta enriquecido](../azure-monitor/logs/log-query-overview.md), [presentarlos en el panel de Azure](../azure-monitor/app/tutorial-app-dashboards.md) y [realizar alertas casi en tiempo real](../azure-monitor/alerts/alerts-log.md).

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Asignación de un nombre a la configuración y selección de un área de trabajo de Log Analytics":::

Los esquemas y el contenido de los registros de ejecución de paquetes SSIS en Azure Monitor y Log Analytics son similares a los esquemas de las tablas o vistas internas de SSISDB.

| Categorías de registros de Azure Monitor          | Tablas de Log Analytics                     | Tablas o vistas internas de SSISDB              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Para más información sobre los atributos y las propiedades de los registros operativos de SSIS, vea [Esquemas de Azure Monitor y Log Analytics para ADF](monitor-schema-logs-events.md).

Los registros de ejecución de los paquetes SSIS seleccionados siempre se envían a Log Analytics, independientemente de los métodos de invocación. Por ejemplo, puede invocar las ejecuciones de paquetes en SSDT habilitado para Azure mediante T-SQL en SSMS, el Agente SQL Server u otras herramientas designadas, y como ejecuciones desencadenadas o de depuración de las actividades de ejecución de paquetes SSIS en canalizaciones de ADF.

Al consultar los registros de operaciones de SSIS IR en Log Analytics, puede usar las propiedades **OperationName** y **ResultType** que están establecidas en `Start/Stop/Maintenance/Heartbeat` y `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy`, respectivamente.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query.png" alt-text="Consulta de los registros de operaciones de SSIS IR en Log Analytics":::

Para consultar el estado de un nodo de SSIS IR, puede establecer la propiedad **OperationName** en `Heartbeat`. Cada nodo suele enviar un registro `Heartbeat` por minuto a Log Analytics con la propiedad **ResultType** reflejando su estado, que es `Healthy` cuando está disponible para ejecuciones de paquete y `Unhealthy` cuando no. Por ejemplo, si su instancia de SSIS IR tiene dos nodos disponibles, usted verá en todo momento dos registros `Heartbeat` con la propiedad **ResultType** establecida en `Healthy`, en cualquier período de un minuto.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query-3.png" alt-text="Consulta de los latidos de SSIS IR en Log Analytics":::

Puede consultar los siguientes patrones para detectar la no disponibilidad de sus nodos de SSIS IR:

* Faltan registros `Heartbeat` en muchos períodos de un minuto a pesar de que su instancia de SSIS IR sigue en ejecución.
* Hay registros `Heartbeat` con la propiedad **ResultType** establecida en `Unhealthy` en muchos períodos de un minuto a pesar de que su instancia de SSIS IR sigue en ejecución.

Puede convertir las consultas anteriores en [alertas](../azure-monitor/alerts/alerts-unified-log.md) y acceder a su [página de supervisión de SSIS IR](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) para confirmar cuándo las recibe.

Al consultar los registros de ejecución de paquetes SSIS en Logs Analytics, puede combinarlos mediante las propiedades **OperationId**/**ExecutionId**/**CorrelationId**. **OperationId**/**ExecutionId** siempre se establecen en `1` para todas las operaciones o ejecuciones relacionadas con los paquetes que **no** están almacenados en SSISDB o se invocan mediante T-SQL.

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query2.png" alt-text="Consulta de los registros de ejecución de paquetes SSIS en Log Analytics":::

## <a name="next-steps"></a>Pasos siguientes

[Esquema de registros y eventos](monitor-schema-logs-events.md)

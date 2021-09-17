---
title: Supervisión de Azure Batch
description: Más información sobre los servicios de supervisión de Azure, las métricas, los registros de diagnóstico y otras características de supervisión para Azure Batch.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b2b73261b07a7e5eb269c1f58a45f97d99133ac4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768891"
---
# <a name="monitor-batch-solutions"></a>Supervisión de soluciones de Batch

[Azure Monitor](../azure-monitor/overview.md) y el servicio Batch proporcionan una gama de servicios, herramientas y API para supervisar las soluciones de Batch. Este artículo de información general le ayudará a elegir una estrategia de supervisión que se adapte a sus necesidades.

## <a name="subscription-level-monitoring"></a>Supervisión a nivel de suscripción

El nivel de suscripción, que incluye las cuentas de Batch, el [registro de actividad de Azure](../azure-monitor/essentials/activity-log.md) recopila datos de eventos operativos de varias categorías.

Para las cuentas de Batch en concreto, el registro de actividad recopila eventos relacionados con la creación y la eliminación de cuentas, y la administración de claves.

Puede ver el registro de actividad en Azure Portal o consultar eventos mediante la CLI de Azure, los cmdlets de PowerShell o la API REST de Azure Monitor. También puede exportar el registro de actividad o configurar [alertas del registro de actividad](../azure-monitor/alerts/alerts-activity-log.md).

## <a name="batch-account-level-monitoring"></a>Supervisión a nivel de la cuenta de Batch

Supervise las cuentas de Batch con características de [Azure Monitor](../azure-monitor/overview.md). Azure Monitor recopila [métricas](../azure-monitor/essentials/data-platform-metrics.md) y, opcionalmente, [registros de diagnóstico](../azure-monitor/essentials/resource-logs.md) para los recursos de una cuenta de Batch, como grupos, trabajos y tareas. Recopile y consuma estos datos manualmente o mediante programación para supervisar las actividades de la cuenta de Batch y para diagnosticar problemas. Para más información, consulte [Métricas, alertas y registros de Batch para la evaluación diagnóstica y la supervisión](batch-diagnostics.md).

> [!NOTE]
> Las métricas están disponibles de manera predeterminada en la cuenta de Batch sin configuración adicional y su historial se acumula durante 30 días. Debe crear una configuración de diagnóstico para una cuenta de Batch con el fin de enviar sus registros de recursos a un área de trabajo de Log Analytics, y puede incurrir en costos adicionales para almacenar o procesar datos de registro de recursos.

## <a name="batch-resource-monitoring"></a>Supervisión de recursos de Batch

En las aplicaciones de Batch, utilice las API de Batch para supervisar o consultar el estado de los recursos, como los trabajos, las tareas, los nodos y los grupos. Por ejemplo:

- [Recuento de tareas y nodos de proceso por estado](batch-get-resource-counts.md)
- [Creación de consultas para enumerar los recursos de Batch con eficacia](batch-efficient-list-queries.md)
- [Creación de dependencias de tareas](batch-task-dependencies.md)
- Uso de una [tarea del administrador de trabajos](/rest/api/batchservice/job/add#jobmanagertask)
- Supervisión del [estado de las tareas](/rest/api/batchservice/task/list#taskstate)
- Supervisión del [estado de los nodos](/rest/api/batchservice/computenode/list#computenodestate)
- Supervisión del [estado de los grupos](/rest/api/batchservice/pool/get#poolstate)
- Supervisión del [uso de grupos en la cuenta](/rest/api/batchservice/pool/listusagemetrics)
- Número de [nodos de grupo por estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="additional-monitoring-solutions"></a>Soluciones de supervisión adicionales

Use [Application Insights](../azure-monitor/app/app-insights-overview.md) para supervisar la disponibilidad, el rendimiento y el uso de los trabajos y las tareas de Batch mediante programación. Application Insights permite supervisar los contadores de rendimiento de los nodos de proceso (VM) y recuperar información personalizada para las tareas que se ejecutan en ellos.

Para un ejemplo, consulte [Monitor and debug a Batch .NET application with Application Insights](monitor-application-insights.md) (Supervisión y depuración de una aplicación .NET de Batch con Application Insights) y el [código de ejemplo](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) que lo acompaña.

> [!NOTE]
> El uso de Application Insights puede suponer costos adicionales. Consulte la [información sobre precios](https://azure.microsoft.com/pricing/details/application-insights/).

[Batch Explorer](https://github.com/Azure/BatchExplorer) es una herramienta de cliente independiente, completa y gratuita que puede ayudarle a crear, depurar y supervisar aplicaciones de Azure Batch. Descargue un [paquete de instalación](https://azure.github.io/BatchExplorer/) para Mac, Linux o Windows. Opcionalmente, use [Azure Batch Insights](https://github.com/Azure/batch-insights) para obtener estadísticas del sistema para los nodos de Batch, como los contadores de rendimiento de la máquina virtual, en Batch Explorer.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [API y herramientas de Batch](batch-apis-tools.md) disponibles para la creación de soluciones de Batch.
- Más información sobre el [registro de diagnóstico](batch-diagnostics.md) con Batch.

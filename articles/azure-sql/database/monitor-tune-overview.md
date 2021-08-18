---
title: Optimización de la supervisión y el rendimiento
description: Información general sobre las funcionalidades de ajuste y la metodología de la supervisión y el rendimiento en Azure SQL Database e Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: AlainDormehlMSFT
ms.author: aldorme
ms.reviewer: mathoma, urmilano, wiassaf
ms.date: 03/17/2021
ms.openlocfilehash: 428bc56ed2bcd4cf3da434bff1e399e04706db7d
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121771"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Supervisión y ajuste del rendimiento en Azure SQL Database e Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Para supervisar el rendimiento de una base de datos en Azure SQL Database e Instancia administrada de Azure SQL, empiece por supervisar los recursos de CPU y de E/S usados por la carga de trabajo en relación con el nivel de rendimiento de la base de datos elegido al seleccionar un nivel de servicio y un nivel de rendimiento determinados. Para ello, Azure SQL Database e Instancia administrada de Azure SQL emiten métricas de recursos que se pueden ver en Azure Portal o mediante una de estas herramientas de administración de SQL Server: [Azure Data Studio](/sql/azure-data-studio/what-is) o [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database ofrece una serie de asesores de base de datos para proporcionar recomendaciones de ajuste del rendimiento y opciones de ajuste automáticas para mejorar el rendimiento. Además, en Información de rendimiento de consultas se muestran los detalles de las consultas responsables de la mayor parte del uso de la CPU y la E/S de las bases de datos individuales y agrupadas.

Azure SQL Database e Instancia administrada de Azure SQL cuentan con funcionalidades avanzadas de supervisión y ajuste respaldadas por inteligencia artificial para ayudarle a solucionar los problemas de las bases de datos y soluciones, y maximizar su rendimiento. Puede optar por configurar la [exportación de streaming](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) de los resultados de [Intelligent Insights](intelligent-insights-overview.md) y de otras métricas y registros de recursos de bases de datos a uno de varios destinos para su consumo y análisis, especialmente mediante [SQL Analytics](../../azure-monitor/insights/azure-sql.md). Azure SQL Analytics es una solución de supervisión en la nube que se utiliza para supervisar el rendimiento de todas las bases de datos a escala, endistintas suscripciones y en una vista única. Para obtener una lista de los registros y las métricas que puede exportar, consulte el artículo sobre la [telemetría de diagnóstico para la exportación](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export).

SQL Server tiene sus propias funcionalidades de supervisión y diagnóstico que aprovechan SQL Database y SQL Managed Instance, como el [almacén de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) y las [vistas de administración dinámica (DMV)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Consulte [Supervisión del rendimiento en Azure SQL Database con vistas de administración dinámica](monitoring-with-dmvs.md) para que los scripts supervisen diversos problemas de rendimiento.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Funcionalidades de supervisión y optimización en Azure Portal

En Azure Portal, Azure SQL Database e Instancia administrada de Azure SQL permiten la supervisión de las métricas de recursos. Además, Azure SQL Database ofrece asesores de bases de datos, e Información de rendimiento de consultas proporciona recomendaciones para el ajuste y el análisis del rendimiento de las consultas. Por último, en Azure Portal, puede habilitar de forma automática el ajuste para los [servidores SQL lógicos](logical-servers.md) y sus bases de datos individuales y agrupadas.

> [!NOTE]
> Las bases de datos con un uso muy bajo pueden mostrarse en el portal con un uso inferior al real. Debido a la forma en que se emite la telemetría al convertir un valor doble al entero más próximo, algunas cantidades de uso inferiores a 0,5 se redondean a 0, lo que provoca una pérdida en la granularidad de la telemetría emitida. Para obtener más información, consulte [Redondeo a cero de métricas de grupos elásticos y bases de datos bajos](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="monitor-with-sql-insights"></a>Supervisión con SQL Insights

[Azure Monitor SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) es una herramienta para supervisar las instancias administradas de Azure SQL, las bases de datos de Azure SQL y las instancias de SQL Server en VM de Azure SQL. Este servicio usa un agente remoto para capturar los datos de vistas de administración dinámica (DMV) y enrutarlos a Azure Log Analytics, donde se pueden supervisar y analizar. Puede ver estos datos de [Azure Monitor](../../azure-monitor/overview.md) en las vistas proporcionadas, o puede acceder directamente a los datos de registro para ejecutar consultas y analizar tendencias. Para empezar a usar Azure Monitor SQL Insights, consulte [Habilitación de SQL Insights](../../azure-monitor/insights/sql-insights-enable.md).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Supervisión de recursos de Azure SQL Database e Instancia administrada de Azure SQL

Puede supervisar rápidamente las siguientes métricas de recursos en Azure Portal en la vista **Métricas**. Estas métricas le permiten ver si una base de datos está llegando al 100 % del procesador, la memoria o los recursos de E/S. Un porcentaje alto de DTU o del procesador, así como un porcentaje alto de E/S, indican que la carga de trabajo podría necesitar más recursos de CPU o de E/S. También puede indicar consultas que deben optimizarse.

  ![Métricas de los recursos](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Asesores de bases de datos de Azure SQL Database

Azure SQL Database incluye [asesores de bases de datos](database-advisor-implement-performance-recommendations.md) que proporcionan recomendaciones para el ajuste del rendimiento de las bases de datos únicas y agrupadas. Estas recomendaciones están disponibles en Azure Portal y en [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor). También puede habilitar el [ajuste automático](automatic-tuning-overview.md) para que Azure SQL Database pueda implementar automáticamente estas recomendaciones de ajuste.

### <a name="query-performance-insight-in-azure-sql-database"></a>Información de rendimiento de consultas en Azure SQL Database

En [Información de rendimiento de consultas](query-performance-insight-use.md) se muestra el rendimiento en Azure Portal de las consultas que más consumen y de mayor tamaño para bases de datos únicas y agrupadas.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Redondeo a cero de métricas de grupos elásticos y bases de datos bajos

A partir de septiembre de 2020, las bases de datos con un uso muy bajo pueden mostrarse en el portal con un uso inferior al real. Debido a la forma en que se emite la telemetría al convertir un valor doble al entero más próximo, algunas cantidades de uso inferiores a 0,5 se redondearán a 0, lo que provoca una pérdida en la granularidad de la telemetría emitida.

Por ejemplo: Considere una ventana de 1 minuto con los cuatro puntos de datos siguientes: 0,1, 0,1, 0,1 y 0,1: estos valores bajos se redondean a 0, 0, 0 y 0, y presentan un promedio de 0. Si alguno de los puntos de datos es superior a 0,5, por ejemplo: 0,1, 0,1, 0,9 y 0,1 se redondean a 0, 0, 1 y 0, y muestran un promedio de 0,25.

Métricas afectadas de bases de datos:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Métricas afectadas de grupos elásticos:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generación de evaluaciones inteligentes de problemas de rendimiento

[Intelligent Insights](intelligent-insights-overview.md) de Azure SQL Database e Instancia administrada de Azure SQL usa inteligencia integrada para supervisar continuamente el uso de las bases de datos mediante inteligencia artificial y detectar eventos potencialmente perjudiciales que provoquen un rendimiento insuficiente. Intelligent Insights detecta automáticamente los problemas de rendimiento con las bases de datos en función de los tiempos de espera o los errores en la ejecución de consultas. Una vez detectados, se realiza un análisis detallado que genera un registro de recursos (denominado SQLInsights) con una [evaluación inteligente de los problemas](intelligent-insights-troubleshoot-performance.md). Esta evaluación está formada por un análisis de la causa raíz del problema de rendimiento de la base de datos y, si es posible, recomendaciones para mejorar el rendimiento.

Intelligent Insights es una funcionalidad única de inteligencia integrada de Azure que permite lo siguiente:

- Supervisión proactiva
- Información reveladora personalizada acerca del rendimiento
- Detección temprana de degradación del rendimiento de la base de datos
- El análisis de la causa raíz de los problemas detectados
- Recomendaciones de mejora del rendimiento
- Funcionalidad de escalabilidad horizontal de cientos de miles de bases de datos
- Impacto positivo para los recursos de DevOps y el costo total de propiedad

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Habilitación de la exportación de streaming de métricas y registros de recursos

Puede habilitar y configurar la [exportación de streaming de la telemetría de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) a uno de varios destinos, incluido el registro de recursos de Intelligent Insights. Use [SQL Analytics](../../azure-monitor/insights/azure-sql.md) y otras funcionalidades para consumir esta telemetría de diagnóstico adicional para identificar y resolver problemas de rendimiento.

Puede configurar ajustes de diagnóstico para transmitir categorías de métricas y registros de recursos a bases de datos únicas, bases de datos agrupadas, grupos elásticos, instancias administradas y bases de datos de instancia a uno de los siguientes recursos de Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Área de trabajo de Log Analytics en Azure Monitor

Puede transmitir métricas y registros de recursos a un [área de trabajo de Log Analytics en Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Los datos transmitidos aquí los puede consumir [SQL Analytics](../../azure-monitor/insights/azure-sql.md), que es una solución de supervisión en la nube que proporciona supervisión inteligente de las bases de datos, lo que incluye informes de rendimiento, alertas y recomendaciones de mitigación. Los datos que se transmiten a un área de trabajo de Log Analytics se pueden analizar con otros datos de supervisión recopilados y también permiten aprovechar otras características de Azure Monitor, como las alertas y las visualizaciones.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Puede transmitir las métricas y los registros de recursos a [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs). Transmita la telemetría de diagnóstico a los centros de eventos para proporcionar la siguiente funcionalidad:

- **Transmisión de registros a registros de terceros y sistemas de telemetría**

  Transmita todas sus métricas y todos sus registros de recursos a un centro de eventos único para canalizar datos de registro en una herramienta SIEM o de análisis de registros de terceros.
- **Creación de una plataforma personalizada de registro y telemetría**

  La naturaleza altamente escalable de publicación y suscripción de los centros de eventos otorga la flexibilidad necesaria para ingerir métricas y registros de recursos en una plataforma de telemetría personalizada. Consulte [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) (Diseño y cambio de tamaño de una plataforma de telemetría a escala global en Azure Event Hubs) para más información.
- **Visualización del estado del servicio mediante la transmisión de datos a Power BI**

  Use Event Hubs, Stream Analytics y Power BI para transformar los datos de diagnóstico en información sobre los servicios de Azure prácticamente en tiempo real. Consulte [Stream Analytics y Power BI: panel de análisis en tiempo real de flujo de datos](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para detalles sobre esta solución.

### <a name="azure-storage"></a>Azure Storage

Transmita las métricas y los registros de recursos a [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Utilice el almacenamiento de Azure para archivar gran cantidad de información de telemetría de diagnóstico por una fracción del costo de las dos opciones anteriores de streaming.

## <a name="use-extended-events"></a>Uso de eventos extendidos 

Además, puede usar [eventos extendidos](/sql/relational-databases/extended-events/extended-events) en SQL Server para llevar a cabo operaciones avanzadas de supervisión y solución de problemas. La arquitectura de eventos extendidos permite a los usuarios recopilar tantos datos como sea necesario para solucionar o identificar un problema de rendimiento. Para más información sobre los eventos extendidos en Azure SQL Database, consulte [Eventos extendidos en Azure SQL Database](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las recomendaciones de rendimiento inteligentes para bases de datos únicas y agrupadas, consulte [Recomendaciones de rendimiento del asesor de bases de datos](database-advisor-implement-performance-recommendations.md).
- Para más información sobre cómo supervisar de forma automática el rendimiento de la base de datos con diagnósticos automatizados y análisis de causa principal de problemas de rendimiento, consulte [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
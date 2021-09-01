---
title: Registros de Azure Monitor
description: Obtenga información sobre los conceptos básicos de los registros de Azure Monitor, que se usan para el análisis avanzado de los datos de supervisión.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 616d1f5cf4fc2806d56c34335e66418452c7684b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735794"
---
# <a name="azure-monitor-logs-overview"></a>Introducción a los registros de Azure Monitor
Los registros de Azure Monitor son una característica de Azure Monitor que recopila y organiza los datos de registro y rendimiento de los [recursos supervisados](../monitor-reference.md). Se pueden consolidar los datos de varios orígenes en una sola área de trabajo. Estos orígenes pueden ser:

- [Registros de plataforma](../essentials/platform-logs-overview.md) de los servicios de Azure.
- Datos de registro y rendimiento de los [agentes de máquina virtual](../agents/agents-overview.md).
- Datos de uso y rendimiento de las [aplicaciones](../app/app-insights-overview.md). 

A continuación, puede analizar los datos mediante un lenguaje de consulta sofisticado que sea capaz de analizar rápidamente millones de registros. 

Puede realizar una consulta simple que solo recupere un conjunto específico de registros o realizar un análisis de datos sofisticado para identificar patrones críticos en los datos de supervisión. Trabaje con las consultas de registro y sus resultados de forma interactiva mediante Log Analytics, úselas en reglas de alertas para recibir notificaciones proactivas de los problemas o visualice los resultados en un libro o panel.

> [!NOTE]
> Los registros de Azure Monitor son la mitad de la plataforma de datos que admite Azure Monitor. La otra mitad son las [métricas de Azure Monitor](../essentials/data-platform-metrics.md), que almacenan datos numéricos en una base de datos de serie temporal. Los datos numéricos son más ligeros que los datos de los registros de Azure Monitor. Las métricas de Azure Monitor son capaces de admitir escenarios casi en tiempo real, por lo que son útiles para la creación de alertas y la detección rápida de problemas. 
>
> Las métricas de Azure Monitor solo pueden almacenar datos numéricos en una estructura determinada, mientras que los registros de Azure Monitor pueden almacenar una variedad de tipos de datos que tienen sus propias estructuras. También se pueden realizar análisis complejos sobre los datos de los registros de Azure Monitor mediante consultas de registro, que no se pueden usar para analizar los datos de las métricas de Azure Monitor.

## <a name="what-can-you-do-with-azure-monitor-logs"></a>¿Qué puede hacer con los registros de Azure Monitor?
En la tabla siguiente, se enumeran algunas de las formas en las que se pueden usar los registros de Azure Monitor:

|  | Descripción |
|:---|:---|
| **Análisis** | Use [Log Analytics](./log-analytics-tutorial.md) en Azure Portal para escribir [consultas de registros](./log-query-overview.md) y analizar los datos de registro de forma interactiva con un motor de análisis eficaz. |
| **Alerta** | Configurar un [regla de alerta de registro](../alerts/alerts-log.md) que envíe una notificación o realice [una acción automatizada](../alerts/action-groups.md) cuando los resultados de la consulta coincidan con un resultado concreto. |
| **Visualizar** | Ancle los resultados representados como tablas o gráficos a un [panel de Azure](../../azure-portal/azure-portal-dashboards.md).<br>Cree un [libro](../visualize/workbooks-overview.md) para combinar con varios conjuntos de datos en un informe interactivo. <br>Exporte los resultados de una consulta a [Power BI](../visualize/powerbi.md) para usar diferentes visualizaciones y compartirlos con usuarios fuera de Azure.<br>Exporte los resultados de una consulta a [Grafana](../visualize/grafana-plugin.md) para utilizar sus paneles y combinar con otros orígenes de datos.|
| **Obtener información** | Compatibilidad con [Insights](../monitor-reference.md#insights-and-core-solutions), que proporciona una experiencia de supervisión personalizada para determinadas aplicaciones y servicios.  |
| **Recuperar** | Acceda a los resultados de la consulta de registros desde una línea de comandos mediante la [CLI de Azure](/cli/azure/monitor/log-analytics).<br>Acceda a los resultados de la consulta de registros desde una línea de comandos mediante [cmdlets de PowerShell](/powershell/module/az.operationalinsights).<br>Acceda a los resultados de la consulta de registros desde una aplicación personalizada con la [API REST](https://dev.loganalytics.io/). |
| **Exportarar** | Configure la [exportación automatizada de datos de registro](./logs-data-export.md) a una cuenta de Azure Storage o a Azure Event Hubs.<br>Cree un flujo de trabajo para recuperar datos de registro y copiarlos en una ubicación externa mediante [Azure Logic Apps](./logicapp-flow-connector.md). |

![Diagrama en el que se muestra información general sobre los registros de Azure Monitor.](media/data-platform-logs/logs-overview.png)

## <a name="data-collection"></a>datos, recopilación
Una vez que cree un área de trabajo de Log Analytics, debe configurar orígenes para que envíen sus datos. No se recopilan datos automáticamente. 

Esta configuración será diferente en función del origen de datos. Por ejemplo:

- [Cree una configuración de diagnóstico](../essentials/diagnostic-settings.md) para enviar los registros de recursos desde los recursos de Azure al área de trabajo. 
- [Habilite VM Insights](../vm/vminsights-enable-overview.md) para recopilar datos de máquinas virtuales. 
- Configure [orígenes de datos en el área de trabajo](../agents/data-sources.md) para recopilar más eventos y datos de rendimiento.

Para obtener una lista completa de los orígenes de datos que puede configurar para enviar datos a los registros de Azure Monitor, consulte [¿Qué supervisa Azure Monitor?](../monitor-reference.md)

## <a name="log-analytics-and-workspaces"></a>Log Analytics y áreas de trabajo
Log Analytics es una herramienta que se encuentra en Azure Portal. Se puede utilizar para editar y ejecutar consultas de registro y analizar de forma interactiva los resultados. A continuación, puede usar esas consultas para admitir otras características de Azure Monitor, como los libros y las alertas de consultas de registro. Acceda a Log Analytics desde la opción **Registros** del menú de Azure Monitor o desde la mayoría de los demás servicios en Azure Portal.

Consulte [Introducción a Log Analytics en Azure Monitor](./log-analytics-overview.md) para obtener una descripción de Log Analytics. Consulte [Tutorial de Log Analytics](./log-analytics-tutorial.md) para recorrer el uso de las características de Log Analytics para crear una consulta de registro simple y analizar los resultados.

Azure Monitor almacena los datos que recopila en una o varias [áreas de trabajo de Log Analytics](./design-logs-deployment.md). Un área de trabajo define:

- La ubicación geográfica de los datos.
- Los derechos de acceso que definen qué usuarios pueden acceder a los datos.
- Las opciones de configuración, como el plan de tarifa y la retención de datos.  

Debe crear al menos un área de trabajo para usar los registros de Azure Monitor. Una sola área de trabajo puede ser suficiente para todos los datos de supervisión, o bien puede optar por crear varias áreas de trabajo en función de sus requisitos. Por ejemplo, podría tener un área de trabajo para los datos de producción y otra para las pruebas. 

Para crear una nueva área de trabajo, consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](./quick-create-workspace.md). Para conocer las consideraciones sobre la creación de varias áreas de trabajo, consulte [Diseño de la implementación de registros de Azure Monitor](design-logs-deployment.md).

## <a name="log-queries"></a>Consultas de registros
Los datos se recuperan de un área de trabajo de Log Analytics mediante una consulta de registros, que es una solicitud de solo lectura para procesar datos y devolver resultados. Las consultas de registros se escriben en el [lenguaje de consulta Kusto (KQL)](/azure/data-explorer/kusto/query/). KQL es el mismo lenguaje de consulta que utiliza Azure Data Explorer. 

Puede escribir consultas de registro en Log Analytics para analizar de forma interactiva los resultados, usarlas en reglas de alertas para notificar de forma proactiva los problemas o incluir los resultados en libros o paneles. La información detallada incluye las consultas precompiladas para admitir sus vistas y libros.

Para obtener una lista de dónde se utilizan las consultas de registro y referencias a tutoriales y otra documentación para ayudarle a comenzar, consulte [Consultas de registro en Azure Monitor](./log-query-overview.md).

![Captura de pantalla en la que se muestran consultas de Log Analytics.](media/data-platform-logs/log-analytics.png)

## <a name="data-structure"></a>Estructura de los datos
Las consultas de registro recuperan sus datos de un área de trabajo Log Analytics. Cada área de trabajo contiene varias tablas que están organizadas en columnas independientes con varias filas de datos. Cada tabla se define mediante un conjunto único de columnas. Las filas de datos proporcionadas por el origen de datos comparten esas columnas. 

[![Diagrama que muestra la estructura de los registros de Azure Monitor.](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)

Los datos de registro de Application Insights también se almacenan en los registros de Azure Monitor, pero de manera distinta según la configuración de la aplicación: 

- Si se trata de una aplicación basada en áreas de trabajo, los datos se almacenan en un área de trabajo de Log Analytics en un conjunto estándar de tablas. Los tipos de datos incluyen solicitudes, excepciones y vistas de páginas de la aplicación. Varias aplicaciones pueden usar la misma área de trabajo. 

- Si se trata de una aplicación clásica, los datos no se almacenan en un área de trabajo de Log Analytics. Usa el mismo lenguaje de consulta y las consultas se crean y ejecutan con la misma herramienta de Log Analytics en Azure Portal. Los elementos de datos de las aplicaciones clásicas se almacenan separados entre sí. La estructura general es igual que la de las aplicaciones basadas en áreas de trabajo, aunque los nombres de tablas y columnas son distintos. 

Para obtener una comparación detallada del esquema de las aplicaciones clásicas y las basadas en áreas de trabajo, consulte [Cambios en los recursos basados en áreas de trabajo](../app/apm-tables.md).

> [!NOTE]
> La experiencia clásica de Application Insights incluye compatibilidad con versiones anteriores para las consultas de recursos, los libros y las alertas basadas en registros. Para consultar o ver según la [nueva estructura o esquema de tablas basada en áreas de trabajo](../app/apm-tables.md), primero debe ir al área de trabajo de Log Analytics. Durante la versión preliminar, al seleccionar **Registros** en los paneles de Application Insights, se proporciona acceso a la experiencia de consulta clásica de Application Insights. Para más información, consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](./scope.md).

[![Diagrama que muestra la estructura de los registros de Azure Monitor para Application Insights.](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="relationship-to-azure-data-explorer"></a>Relación con Azure Data Explorer
Los registros de Azure Monitor se basa en Azure Data Explorer. Un área de trabajo de Log Analytics es el equivalente de una base de datos en Azure Data Explorer. Las tablas se estructuran igual y ambas usan KQL. 

La experiencia de usar Log Analytics para trabajar con las consultas de Azure Monitor en Azure Portal es similar a la experiencia de usar la interfaz de usuario web de Azure Data Explorer. Incluso puede [incluir datos de un área de trabajo de Log Analytics en una consulta de Azure Data Explorer](/azure/data-explorer/query-monitor-data). 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las [consultas de registros](./log-query-overview.md) para recuperar y analizar datos provenientes de un área de trabajo de Log Analytics.
- Obtenga más información acerca de las [métricas en Azure Monitor](../essentials/data-platform-metrics.md).
- Obtenga información sobre los [Orígenes de datos de supervisión para Azure Monitor](../agents/data-sources.md) disponibles para diferentes recursos de Azure.

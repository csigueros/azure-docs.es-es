---
title: 'Supervisión de máquinas virtuales con Azure Monitor: análisis de datos de supervisión'
description: Obtenga información sobre las distintas características Azure Monitor que puede usar para analizar el estado y el rendimiento de las máquinas virtuales.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: cca95effdbbda099dd3e082e950b92f26e96f80f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459476"
---
# <a name="monitor-virtual-machines-with-azure-monitor-analyze-monitoring-data"></a>Supervisión de máquinas virtuales con Azure Monitor: análisis de datos de supervisión
Este artículo forma parte del escenario [Supervisión de máquinas virtuales y sus cargas de trabajo en Azure Monitor](monitor-virtual-machine.md). Describe cómo analizar los datos de supervisión de las máquinas virtuales después de completar su configuración.

Después de habilitar VM Insights en las máquinas virtuales, los datos estarán disponibles para su análisis. En este artículo se describen las distintas características Azure Monitor que puede usar para analizar el estado y el rendimiento de las máquinas virtuales. Varias de estas características proporcionan una experiencia diferente en función de si está analizando una sola máquina o varias. Cada experiencia se describe aquí con los comportamientos únicos de cada característica en función de la experiencia que se esté utilizando.

> [!NOTE]
> En este artículo se incluyen instrucciones sobre el análisis de los datos recopilados por Azure Monitor y VM Insights. Para los datos que configure para supervisar las cargas de trabajo que se ejecutan en máquinas virtuales, consulte [Supervisión de cargas de trabajo](monitor-virtual-machine-workloads.md).

## <a name="single-machine-experience"></a>Experiencia de una sola máquina
Acceda a la experiencia de análisis de una sola máquina desde la sección **Supervisión** del menú de Azure Portal para cada máquina virtual de Azure y servidor habilitado para Azure Arc. Estas opciones limitan los datos que ve a esa máquina o al menos establecen un filtro inicial para ella. De esta manera, puede centrarse en una máquina determinada, ver su rendimiento actual y sus tendencias a lo largo del tiempo, y ayudar a identificar los problemas que pueda estar experimentando.

:::image type="content" source="media/monitor-virtual-machines/vm-menu.png" alt-text="Captura de pantalla que muestra el análisis de una máquina virtual en Azure Portal." lightbox="media/monitor-virtual-machines/vm-menu.png":::

- **Página de información general**: seleccione la pestaña **Supervisión** para mostrar las [métricas de plataforma](../essentials/data-platform-metrics.md) para el host de máquina virtual. Puede obtener una vista rápida de la tendencia en distintos períodos de tiempo para métricas importantes, como la CPU, la red y el disco. Sin embargo, como se trata de métricas de host, no se incluyen los contadores del sistema operativo invitado, como la memoria. Seleccione un gráfico para trabajar con estos datos en el [explorador de métricas](../essentials/metrics-getting-started.md), donde puede realizar diferentes agregaciones y agregar más contadores para el análisis.
- **Registro de actividad**: consulte las entradas del [registro de actividad](../essentials/activity-log.md#view-the-activity-log) filtradas para la máquina virtual actual. Use este registro para ver la actividad reciente de la máquina, como los cambios de configuración y cuándo se detuvo e inició. 
- **Insights**: abra [VM Insights](../vm/vminsights-overview.md) con el mapa de la máquina virtual actual seleccionado. En el mapa se muestran los procesos en ejecución en la máquina, las dependencias de otras máquinas y los procesos externos. Para los detalles sobre cómo usar la vista de mapa para una sola máquina, consulte [Uso de la característica de mapa de VM Insights para comprender los componentes de la aplicación](vminsights-maps.md#view-a-map-from-a-vm).

    Seleccione la pestaña **Rendimiento** para ver las tendencias de los contadores de rendimiento críticos durante distintos períodos de tiempo. Al abrir VM Insights desde el menú de la máquina virtual, también hay una tabla con métricas detalladas para cada disco. Para los detalles sobre cómo usar la vista de mapa para una sola máquina, consulte [Representación gráfica del rendimiento con VM Insights](vminsights-performance.md#view-performance-directly-from-an-azure-vm). 

- **Alertas**: vea las [alertas](../alerts/alerts-overview.md) para la máquina virtual actual. Estas alertas solo usan la máquina como recurso de destino, por lo que podría haber otras alertas asociadas a ella. Es posible que tenga que usar la opción **Alertas** del menú Azure Monitor para ver las alertas de todos los recursos. Para los detalles, vea [Supervisión de máquinas virtuales con Azure Monitor: alertas](monitor-virtual-machine-alerts.md).
- **Métricas**: abra el explorador de métricas con el ámbito establecido en la máquina. Esta opción es la misma que la selección de uno de los gráficos de rendimiento en la página **Información general**, salvo que la métrica aún no se ha agregado.
- **Configuración de diagnóstico**: habilite y configure la [extensión de diagnóstico](../agents/diagnostics-extension-overview.md) para la máquina virtual actual. Esta opción es diferente de la opción **Configuración de diagnóstico** para otros recursos de Azure. Habilite solo la extensión de diagnóstico si necesita enviar datos a Azure Event Hubs o Azure Storage.
- **Recomendaciones de Advisor**: consulte las recomendaciones de [Azure Advisor](../../advisor/index.yml) para la máquina virtual actual.
- **Registros**: abra [Log Analytics](../logs/log-analytics-overview.md) con el [ámbito](../logs/scope.md) establecido en la máquina virtual actual. Puede seleccionar entre una variedad de consultas existentes para profundizar en los datos de registro y rendimiento solo para esta máquina. 
- **Connection Monitor**: abra [Connection Monitor de Network Watcher](../../network-watcher/connection-monitor-overview.md) para supervisar las conexiones entre la máquina virtual actual y otras máquinas virtuales. 
- **Libros**: abra la galería de libros con los libros de VM Insights para máquinas únicas. Para una lista de los libros de VM Insights diseñados para máquinas individuales, consulte [Libros de VM Insights](vminsights-workbooks.md#vm-insights-workbooks).

## <a name="multiple-machine-experience"></a>Experiencia de varias máquinas
Acceda a la experiencia de análisis de varias máquinas desde el menú **Monitor** en Azure Portal para cada máquina virtual de Azure y servidor habilitado para Azure Arc. Estas opciones proporcionan acceso a todos los datos para que pueda seleccionar las máquinas virtuales que le interesa comparar.

:::image type="content" source="media/monitor-virtual-machines/monitor-menu.png" alt-text="Captura de pantalla que muestra el análisis de varias máquinas virtuales en Azure Portal." lightbox="media/monitor-virtual-machines/monitor-menu.png":::

- **Registro de actividad**: consulte las entradas del [registro de actividad](../essentials/activity-log.md#view-the-activity-log) filtradas para todos los recursos. Cree un filtro para un **Tipo de recurso** de máquinas virtuales o conjuntos de escalado de máquinas virtuales para ver los eventos de todas las máquinas.
- **Alertas**: vea las [alertas](../alerts/alerts-overview.md) de todos los recursos, que incluyen alertas relacionadas con máquinas virtuales, pero que están asociadas al área de trabajo. Cree un filtro para un **Tipo de recurso** de máquinas virtuales o conjuntos de escalado de máquinas virtuales para ver las alertas de todas las máquinas. 
- **Métricas**: abra el [explorador de métricas](../essentials/metrics-getting-started.md) sin ningún ámbito seleccionado. Esta característica es especialmente útil cuando se quieren comparar tendencias en varias máquinas. Seleccione una suscripción o un grupo de recursos para agregar rápidamente un grupo de máquinas para analizarlas juntas.
- **Registros**: abra [Log Analytics](../logs/log-analytics-overview.md) con el [ámbito](../logs/scope.md) establecido en el área de trabajo. Puede seleccionar entre una variedad de consultas existentes para profundizar en los datos de registro y rendimiento para todas las máquinas. O bien, puede crear una consulta personalizada para realizar análisis adicionales.
- **Libros**: abra la galería de libros con los libros de VM Insights para varias máquinas. Para una lista de los libros de VM Insights diseñados para varias máquinas, consulte [Libros de VM Insights](vminsights-workbooks.md#vm-insights-workbooks). 
- **Virtual Machines**: abra [VM Insights](../vm/vminsights-overview.md) con la pestaña **Introducción** abierta. Esta acción muestra todas las máquinas de la suscripción de Azure e identifica las que se supervisan. Use esta vista para incorporar máquinas individuales que aún no se están supervisando.

    Seleccione la pestaña **Rendimiento** para comparar las tendencias de los contadores de rendimiento críticos de varias máquinas durante distintos períodos de tiempo. Seleccione todas las máquinas de una suscripción o un grupo de recursos para incluir en la vista. Para los detalles sobre cómo usar la vista de mapa para una sola máquina, consulte [Representación gráfica del rendimiento con VM Insights](vminsights-performance.md#view-performance-directly-from-an-azure-vm).

    Seleccione la pestaña **Mapa** para ver los procesos en ejecución en las máquinas, las dependencias entre las máquinas y los procesos externos. Seleccione todas las máquinas de una suscripción o grupo de recursos o inspeccione los datos de una sola máquina. Para los detalles sobre cómo usar la vista de mapa para varias máquinas, consulte [Uso de la característica de mapa de VM Insights para comprender los componentes de la aplicación](vminsights-maps.md#view-a-map-from-azure-monitor). 
 
## <a name="compare-metrics-and-logs"></a>Comparación de métricas y registros
Para muchas características de Azure Monitor, no es necesario comprender los distintos tipos de datos que usa y dónde se almacenan. Puede usar VM Insights, por ejemplo, sin comprender qué datos se usan para rellenar la vista de rendimiento, la vista de mapa y los libros. Solo tiene que centrarse en la lógica que está analizando. A medida que profundiza, deberá comprender la diferencia entre [Métricas](../essentials/data-platform-metrics.md) y [Registros](../logs/data-platform-logs.md). Las diferentes características de Azure Monitor usan distintos tipos de datos. El tipo de alerta que se usa para un escenario determinado depende de que los datos estén disponibles en una ubicación en particular.

Este nivel de detalle puede resultar confuso si es nuevo en Azure Monitor. La siguiente información le ayuda a comprender las diferencias entre los tipos de datos:

- Los datos no numéricos, como los eventos, se almacenan en registros. Las métricas solo pueden incluir datos numéricos muestreados a intervalos regulares.
- Los datos numéricos se pueden almacenar en métricas y registros para que se puedan analizar de maneras diferentes y admitir distintos tipos de alertas.
- VM Insights envía los datos de rendimiento del sistema operativo invitado a los registros mediante el agente de Log Analytics.
- El agente de Azure Monitor envía los datos de rendimiento del sistema operativo invitado a las métricas.

> [!NOTE]
> El agente de Azure Monitor envía datos a las métricas y los registros. En este escenario, solo se usa para métricas porque el agente de Log Analytics envía datos a los registros según sea necesario actualmente para VM Insights. Cuando VM Insights usa el agente de Azure Monitor, este escenario se actualizará para quitar el agente de Log Analytics.

## <a name="analyze-data-with-vm-insights"></a>Análisis de datos con VM Insights
VM Insights incluye varios gráficos de rendimiento que ayudan a obtener rápidamente el estado del funcionamiento de las máquinas supervisadas, su rendimiento tendencial a lo largo del tiempo y las dependencias entre máquinas y procesos. También ofrece una vista consolidada de los distintos aspectos de cualquier máquina supervisada, como sus propiedades y eventos recopilados en el área de trabajo de Log Analytics.

En la pestaña **Introducción** se muestran todas las máquinas de la suscripción de Azure e identifica las que se supervisan. Use esta vista para identificar rápidamente qué máquinas no se supervisan y para incorporar máquinas individuales que aún no se están supervisando.

:::image type="content" source="media/monitor-virtual-machines/vminsights-get-started.png" alt-text="Captura de pantalla que muestra la introducción de VM Insights." lightbox="media/monitor-virtual-machines/vminsights-get-started.png":::

La vista **Rendimiento** incluye varios gráficos con varios indicadores clave de rendimiento (KPI) para ayudarle a determinar cuál es el rendimiento de las máquinas. En los gráficos se muestra el uso de recursos durante un período de tiempo. Puede usarlos para identificar cuellos de botella, ver anomalías o cambiar a una perspectiva en la que se enumeren las máquinas a fin de ver el uso de recursos en función de la métrica seleccionada. Para los detalles sobre cómo usar la vista de rendimiento, consulte [Representación gráfica del rendimiento con VM Insights](vminsights-performance.md).

:::image type="content" source="media/monitor-virtual-machines/vminsights-performance.png" alt-text="Captura de pantalla que muestra el rendimiento de VM Insights." lightbox="media/monitor-virtual-machines/vminsights-performance.png":::

Use la vista **Mapa** para ver los procesos en ejecución en las máquinas y sus dependencias de otras máquinas y procesos externos. Puede cambiar el período de tiempo de la vista para determinar si estas dependencias han cambiado desde otro período de tiempo. Para los detalles sobre cómo usar la vista de mapa, consulte [Uso de la característica de mapa de VM Insights para comprender los componentes de la aplicación](vminsights-maps.md).

:::image type="content" source="media/monitor-virtual-machines/vminsights-map.png" alt-text="Captura de pantalla que muestra el mapa de VM Insights." lightbox="media/monitor-virtual-machines/vminsights-map.png":::

## <a name="analyze-metric-data-with-metrics-explorer"></a>Análisis de datos de métricas con el Explorador de métricas
Con el explorador de métricas, puede trazar gráficos, correlacionar visualmente tendencias e investigar picos y caídas en valores de métricas. Para los detalles sobre el uso de esta herramienta, consulte [Introducción al Explorador de métricas de Azure](../essentials/metrics-getting-started.md). 

Las máquinas virtuales usan tres espacios de nombres.

| Espacio de nombres | Descripción | Requisito |
|:---|:---|:---|
| Máquina virtual anfitriona | Métricas de la anfitriona recopiladas automáticamente para todas las máquinas virtuales de Azure. La lista detallada de métricas en se encuentra en [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines). | Se recopilan automáticamente sin necesidad de configuración. |
| Invitado (clásico) | Conjunto limitado de datos de rendimiento del sistema operativo invitado y de la aplicación. Está disponible en el explorador de métricas, pero no en otras características de Azure Monitor, como las alertas de métricas.  | [Extensión de diagnósticos](../agents/diagnostics-extension-overview.md) instalada. Los datos se leen desde Azure Storage.  |
| Máquina virtual invitada | Datos de rendimiento del sistema operativo invitado y de la aplicación disponibles para todas las características de Azure Monitor mediante métricas. | [Agente de Azure Monitor](../agents/azure-monitor-agent-overview.md) instalado con una [Regla de recopilación de datos](../agents/data-collection-rule-overview.md). |

## <a name="analyze-log-data-with-log-analytics"></a>Análisis de datos de registro con Log Analytics
Con Log Analytics puede realizar análisis personalizados de sus datos de registro. Use Log Analytics cuando quiera profundizar en los datos que se usan para crear las vistas en VM Insights. Puede que quiera analizar las diferentes lógicas y agregaciones de los datos, correlacionar los datos de seguridad recopilados por Azure Security Center y Azure Sentinel con los datos de estado y disponibilidad, o trabajar con los datos recopilados para las [cargas de trabajo](monitor-virtual-machine-workloads.md).

No es necesario que sepa escribir una consulta de registro para usar Log Analytics. Hay varias consultas compiladas previamente que puede seleccionar y ejecutar sin modificaciones o usar como inicio de una consulta personalizada. Seleccione **Consultas** en la parte superior de la pantalla de Log Analytics y vea las consultas con un **Tipo de recurso** de **Máquinas virtuales** o **Conjuntos de escalas de máquina virtual**. Para información sobre cómo usar estas consultas, consulte [Uso de consultas en Log Analytics de Azure Monitor](../logs/queries.md). Para ver un tutorial sobre cómo usar Log Analytics para ejecutar consultas y trabajar con sus resultados, consulte [Tutorial de Log Analytics](../logs/log-analytics-tutorial.md).

:::image type="content" source="media/monitor-virtual-machines/vm-queries.png" alt-text="Captura de pantalla que muestra las consultas de máquina virtual" lightbox="media/monitor-virtual-machines/vm-queries.png":::.

Al iniciar Log Analytics desde VM Insights mediante el panel de propiedades de la vista **Rendimiento** o **Mapa**, se enumeran las tablas que tienen datos para el equipo seleccionado. Seleccione una tabla para abrir Log Analytics con una consulta simple que devuelva todos los registros de esa tabla para el equipo seleccionado. Trabaje con estos resultados o modifique la consulta para un análisis más complejo. El [ámbito](../log/../logs/scope.md) establecido en el área de trabajo significa que tiene datos de acceso para todos los equipos que usan esa área de trabajo. 

:::image type="content" source="media/monitor-virtual-machines/table-query.png" alt-text="Captura de pantalla que muestra una consulta de tabla." lightbox="media/monitor-virtual-machines/table-query.png":::

## <a name="visualize-data-with-workbooks"></a>Visualización de datos con libros
Los [Libros](../visualize/workbooks-overview.MD) proporcionan informes interactivos en Azure Portal y combinan diferentes tipos de datos en una sola vista. Los libros combinan texto, [consultas de registros](/azure/data-explorer/kusto/query/), métricas y parámetros en informes interactivos avanzados. Otros miembros del equipo con acceso a los mismos recursos de Azure pueden editar los libros.

Los libros son útiles en escenarios como los siguientes:

* Explorar la utilización de la máquina virtual cuando no se conocen de antemano las métricas de interés, como el uso de CPU, el espacio en disco, la memoria y las dependencias de red. A diferencia de otras herramientas de análisis de uso, los libros le permiten combinar varios tipos de visualizaciones y análisis, lo que los hace idóneos para este tipo de exploración de forma libre.
* Explicar a su equipo cómo funciona una máquina virtual aprovisionada recientemente, mostrando las métricas para contadores clave y otros eventos de registro.
* Compartir los resultados de un experimento de cambio de tamaño de la máquina virtual con otros miembros del equipo. Puede explicar los objetivos del experimento con texto. A continuación, puede mostrar todas las métricas de uso y consultas de análisis empleadas para evaluar el experimento, junto con indicadores claros sobre si cada métrica está por encima o por debajo del objetivo.
* Notificar el impacto de una interrupción del servicio en la utilización de la máquina virtual, con la combinación de datos, explicación del texto y análisis de los pasos siguientes para evitar más interrupciones en el futuro.

VM Insights incluye los siguientes libros. Puede usar estos libros o utilizarlos como punto de partida para crear libros personalizados a fin de abordar requisitos concretos.

### <a name="single-virtual-machine"></a>una sola máquina virtual

| Libro | Descripción |
|----------|-------------|
| Rendimiento | Proporciona una versión personalizable de la vista Rendimiento que usa todos los contadores de rendimiento de Log Analytics que se han habilitado. | 
| Conexiones | Ofrece una visión detallada de las conexiones entrantes y salientes de las máquinas virtuales. | 

### <a name="multiple-virtual-machines"></a>Múltiples máquinas virtuales

| Libro | Descripción |
|----------|-------------|
| Rendimiento | Proporciona una versión personalizable de la vista Lista de N principales y gráficos en un único libro que usa todos los contadores de rendimiento de Log Analytics que se han habilitado.|
| Contadores de rendimiento | Proporciona una vista Gráfico de N principales entre un amplio conjunto de contadores de rendimiento. |
| Conexiones | Ofrece una visión detallada de las conexiones entrantes y salientes de las máquinas supervisadas. |
| Puertos activos | Proporciona una lista de los procesos que se han enlazado a los puertos en las máquinas supervisadas y su actividad en el periodo de tiempo seleccionado. |
| Open Ports (Abrir puertos) | Proporciona el número de puertos abiertos en las máquinas supervisadas, así como los detalles de estos puertos. |
| Conexiones con errores | Muestra el número de conexiones con errores en las máquinas supervisadas, la tendencia de errores y si el porcentaje de errores aumenta con el tiempo. |
| Seguridad y auditoría | Proporciona un análisis del tráfico TCP/IP que informa sobre las conexiones generales, las conexiones malintencionadas, y en las que residen los puntos de conexión IP de forma global. Para habilitar todas las características, deberá habilitar la Detección de seguridad. |
| Tráfico TCP | Proporciona un informe de clasificación de las máquinas supervisadas y del tráfico enviado, recibido y el total, en una cuadrícula que se muestra como una línea de tendencia. |
| Comparación de tráfico | Compara las tendencias en el tráfico de red para una sola máquina o para un grupo de ellas. |
| Agente de Log Analytics | Analiza el estado de los agentes, incluido el número de agentes que se conectan a un área de trabajo que son incorrectos y el efecto del agente en el rendimiento de la máquina. Este libro no está disponible en VM Insights como los demás libros. En el menú de Azure Monitor, vaya a **Libros** y seleccione **Plantillas públicas**. |

Para instrucciones sobre cómo crear sus propios libros personalizados, consulte [Creación de informes interactivos de VM Insights con libros](vminsights-workbooks.md).

:::image type="content" source="media/monitor-virtual-machines/workbook-example.png" alt-text="Captura de pantalla que muestra los libros de máquina virtual" lightbox="media/monitor-virtual-machines/workbook-example.png":::.

## <a name="next-steps"></a>Pasos siguientes

* [Supervisión de máquinas virtuales con Azure Monitor: alertas](monitor-virtual-machine-alerts.md)
* [Supervisión de máquinas virtuales con Azure Monitor: cargas de trabajo](monitor-virtual-machine-workloads.md)

---
title: Introducción a Azure Monitor | Microsoft Docs
description: Introducción a los servicios y funcionalidades de Microsoft que contribuyen a una estrategia de supervisión completa de los servicios y las aplicaciones de Azure.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 639386e1a64ff40375fac5baafc10b6a3a1cb84d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227062"
---
# <a name="azure-monitor-overview"></a>Introducción a Azure Monitor

Azure Monitor ayuda a maximizar la disponibilidad y el rendimiento de las aplicaciones y los servicios. Ofrece una solución completa para recopilar, analizar y actuar en la telemetría desde los entornos local y en la nube. Esta información le ayudará a conocer el rendimiento de las aplicaciones y a identificar de manera proactiva los problemas que les afectan y los recursos de los que dependen.

Entre los ejemplos de lo que puede hacer con Azure Monitor se incluyen:

- Detección y diagnóstico de problemas en aplicaciones y dependencias con [Application Insights](app/app-insights-overview.md).
- Correlación de problemas de infraestructura con [VM Insigths](vm/vminsights-overview.md) y [Container Insights](containers/container-insights-overview.md).
- Profundización en sus datos de supervisión con [Log Analytics](logs/log-query-overview.md) para la solución de problemas y diagnósticos profundos.
- Soporte técnico de operaciones a escala con [alertas inteligentes](alerts/alerts-smartgroups-overview.md) y [acciones automatizadas](alerts/alerts-action-rules.md).
- Creación de visualizaciones con [paneles](visualize/tutorial-logs-dashboards.md) y [libros](visualize/workbooks-overview.md) de Azure.
- Recopile datos de los [recursos supervisados](./monitor-reference.md) mediante [métricas en Azure Monitor](./essentials/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Información general

El siguiente diagrama proporciona una visión general de Azure Monitor. En el centro del diagrama están los almacenes de datos de las métricas y los registros, que son los dos tipos fundamentales de datos que se utilizan en Azure Monitor. En la parte izquierda están los [orígenes de datos de supervisión](agents/data-sources.md) que rellenan estos [almacenes de datos](data-platform.md). En la derecha puede ver las diferentes funciones que realiza Azure Monitor con los datos recopilados. Esto incluye acciones como el análisis, la alerta y la transmisión a sistemas externos.

![Introducción a Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Supervisión de la plataforma de datos

Todos los datos recopilados por Azure Monitor pueden clasificarse como uno de los dos tipos fundamentales: [métricas y registros](data-platform.md). Las [métricas](essentials/data-platform-metrics.md) son valores numéricos que describen algún aspecto de un sistema en un momento dado. Las métricas son ligeras y capaces de admitir escenarios de tiempo casi real. Los [registros](logs/data-platform-logs.md) contienen distintos tipos de datos organizados en grupos de registros, donde cada tipo tiene diferentes conjuntos de propiedades. Los datos de telemetría, como los eventos y los seguimientos, se almacenan como registros junto con los datos de rendimiento para poder analizarlos de forma combinada.

En muchos recursos de Azure, los datos recopilados por Azure Monitor aparecen directamente en la página de información general de Azure Portal. Eche un vistazo a cualquier máquina virtual, por ejemplo, y verá varios gráficos en los que aparecen métricas de rendimiento. Haga clic en cualquiera de los gráficos para abrir los datos en el [explorador de métricas](essentials/metrics-charts.md) de Azure Portal, lo que le permitirá crear gráficos con los valores de diversas métricas a lo largo del tiempo.  Puede ver los gráficos de forma interactiva o anclarlos a un panel para verlos con otras visualizaciones.

![El diagrama muestra los datos de métricas que fluyen hacia el Explorador de métricas para usarlos en las visualizaciones.](media/overview/metrics.png)

Los datos de registro recopilados por Azure Monitor se pueden analizar con [consultas](logs/log-query-overview.md) que recuperan, consolidan y analizan rápidamente los datos recopilados.  Puede crear y probar consultas con [Log Analytics](./logs/log-query-overview.md) en Azure Portal. Puede analizar los datos directamente con distintas herramientas o guardar las consultas para usarlas con [visualizaciones](best-practices-analysis.md) o [reglas de alertas](alerts/alerts-overview.md).

Azure Monitor utiliza una versión del [lenguaje de consulta de Kusto](/azure/kusto/query/) adecuado para realizar búsquedas de registros simples, pero también dispone de funciones avanzadas, como agregaciones, combinaciones y análisis inteligentes. Puede aprender rápidamente el lenguaje de consulta con [diversas lecciones](logs/get-started-queries.md).  Se proporciona orientación concreta a los usuarios que ya están familiarizados con [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) y [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet).

![El diagrama muestra los datos de registros que fluyen hacia Log Analytics para su análisis.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>¿Qué datos recopila Azure Monitor?

Azure Monitor puede recopilar datos de [diversos orígenes](monitor-reference.md), como la aplicación y cualquier sistema operativo o servicio en los que se base, o incluso la propia plataforma. Azure Monitor recopila datos de cada uno de los siguientes niveles:

- **Datos de supervisión de aplicaciones:** datos sobre el rendimiento y la funcionalidad del código que ha escrito, independientemente de la plataforma.
- **Datos de supervisión del sistema operativo invitado:** datos sobre el sistema operativo en el que se ejecuta la aplicación. La aplicación se puede ejecutar en Azure, en otra nube o en el entorno local. 
- **Datos de supervisión de recursos de Azure:** datos acerca del funcionamiento de un recurso de Azure.
- **Datos de supervisión de la suscripción de Azure:** datos sobre el funcionamiento y la administración de una suscripción de Azure, así como sobre el estado y el funcionamiento del propio Azure. 
- **Datos de supervisión del inquilino de Azure:** datos sobre el funcionamiento de los servicios de Azure en el nivel del inquilino, como Azure Active Directory.

En cuanto crea una suscripción a Azure y empieza a agregar recursos, como máquinas virtuales y aplicaciones web, Azure Monitor comienza a recopilar datos.  Los [registros de actividad](essentials/platform-logs-overview.md) registran la creación y modificación de recursos. Las [métricas](essentials/data-platform-metrics.md) indican cómo está funcionando un recurso y los recursos que consume. 

[Habilite el diagnóstico](essentials/platform-logs-overview.md) para ampliar los datos que va a recopilar con el funcionamiento interno de los recursos.  [Agregue un agente](agents/agents-overview.md) a recursos de proceso para recopilar la telemetría de los sistemas operativos invitados. 

Habilite la supervisión de la aplicación con [Application Insights](app/app-insights-overview.md) para recopilar información detallada, como vistas de página, solicitudes de aplicación y excepciones. Compruebe de forma más exhaustiva la disponibilidad de la aplicación configurando una [prueba de disponibilidad](app/monitor-web-app-availability.md) para simular el tráfico de usuarios.

### <a name="custom-sources"></a>Orígenes personalizados

Azure Monitor puede recopilar datos de registro de cualquier cliente de REST mediante [Data Collector API](logs/data-collector-api.md). Esto permite crear escenarios de supervisión personalizados y ampliar la supervisión a los recursos que no exponen datos de telemetría en otros orígenes.

## <a name="insights"></a>Información detallada
Los datos de supervisión solo resultan útiles si aportan una mayor visibilidad sobre el funcionamiento del entorno informático. [Insights](monitor-reference.md#insights-and-core-solutions) proporciona una experiencia de supervisión personalizada para determinados servicios de Azure. Estos requieren una configuración mínima y aumentan la visibilidad del funcionamiento de los recursos críticos.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) supervisa la disponibilidad, el rendimiento y el uso de las aplicaciones web, tanto si están hospedadas en la nube como en un entorno local. Esta solución utiliza la eficaz plataforma de análisis de datos de Azure Monitor para proporcionar información exhaustiva sobre las operaciones de la aplicación y permite diagnosticar errores sin esperar a que un usuario los notifique. Application Insights incorpora puntos de conexión con una serie de herramientas de desarrollo y se integra con Visual Studio para admitir los procesos de DevOps.

![Detalles de la aplicación](media/overview/app-insights.png)

### <a name="container-insights"></a>Container Insights
[Container Insights](containers/container-insights-overview.md) supervisa el rendimiento de las cargas de trabajo de contenedor implementadas en clústeres de Kubernetes administrados y hospedados en Azure Kubernetes Service (AKS). Proporciona información sobre el rendimiento recopilando métricas de los controladores, los nodos y los contenedores disponibles en Kubernetes mediante Metrics API. También se recopilan registros del contenedor.  Una vez habilitada la supervisión de clústeres de Kubernetes, estas métricas y registros se recopilan automáticamente mediante una versión en contenedor del agente de Log Analytics para Linux.

![Estado de los contenedores](media/overview/container-insights.png)

### <a name="vm-insights"></a>VM Insights
[VM insights](vm/vminsights-overview.md) supervisa sus instancias de Azure Virtual Machines (VM) a escala. Analiza el rendimiento y el estado de las máquinas virtuales Windows y Linux, e identifica los distintos procesos y dependencias interconectadas en procesos externos. La solución permite supervisar el rendimiento y las dependencias de las aplicaciones en máquinas virtuales hospedadas en el entorno local o en otro proveedor en la nube.  


![VM Insights](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Respuesta a situaciones críticas
Además de permitirle analizar de forma interactiva los datos de supervisión, una solución de supervisión eficaz debe ser capaz de responder proactivamente a condiciones críticas que se den en los datos que recopila. Esto podría hacerse enviando un mensaje o correo a un administrador responsable de investigar un problema. O también podría hacerse iniciando un proceso automatizado que intente corregir una condición de error.


### <a name="alerts"></a>Alertas
Las [alertas de Azure Monitor](alerts/alerts-overview.md) informan de forma proactiva de los estados críticos e intentan aplicar acciones correctivas. Las reglas de alertas basadas en métricas proporcionan alertas casi en tiempo real con valores numéricos. Las reglas basadas en los registros permiten una lógica compleja con datos de varios orígenes.

Las reglas de alertas de Azure Monitor utilizan [grupos de acciones](alerts/action-groups.md), que contienen diferentes conjuntos de destinatarios y acciones que pueden compartirse entre varias reglas. En función de los requisitos, los grupos de acciones pueden realizar diferentes acciones, como utilizar webhooks para que las alertas inicien acciones externas o se integren con las herramientas de administración de servicios de TI.

![Captura de pantalla que muestra las alertas en Azure Monitor junto con la gravedad, el numero de alertas totales y otra información.](media/overview/alerts.png)

### <a name="autoscale"></a>Escalado automático
Gracias al escalado automático, puede ejecutar la cantidad correcta de recursos para administrar la carga de la aplicación. Cree reglas que usen las métricas recopiladas por Azure Monitor para determinar cuándo se deben agregar automáticamente recursos al aumentar la carga. Elimine los recursos inactivos para ahorrar dinero. Tiene que especificar un número mínimo y máximo de instancias y la lógica para decidir cuándo deben aumentar o disminuir los recursos.

![El diagrama muestra el escalado automático, con varios servidores en una línea con la etiqueta Tiempo de procesador > 80 % y dos servidores marcados como Capacidad mínima, tres servidores como Capacidad actual y cinco como Capacidad máxima.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualización de los datos de supervisión
Las [visualizaciones](best-practices-analysis.md), como los gráficos y las tablas, son herramientas eficaces para resumir los datos de supervisión y presentarlos a distintos destinatarios. Azure Monitor cuenta con sus propias características para visualizar los datos de supervisión y utiliza otros servicios de Azure para publicarlos ante diferentes destinatarios.

### <a name="dashboards"></a>Paneles
Los [paneles de Azure](../azure-portal/azure-portal-dashboards.md) permiten combinar distintos tipos de datos en un único panel en [Azure Portal](https://portal.azure.com). Si lo desea, también compartir el panel con otros usuarios de Azure. La salida de cualquier gráfico de métricas o consulta de registro se puede agregar a un panel de Azure. Por ejemplo, puede crear un panel que contenga diferentes iconos que muestren un gráfico de métricas, una tabla de registros de actividad, un gráfico de uso de Application Insights y la salida de una consulta de registro.

![La captura de pantalla muestra un panel de Azure, que incluye iconos de aplicación y de seguridad, junto con otra información personalizable.](media/overview/dashboard.png)

### <a name="workbooks"></a>Workbooks
Los [libros](visualize/workbooks-overview.md) proporcionan un lienzo flexible para el análisis de datos y la creación de informes visuales completos en Azure Portal. Permiten acceder a varios orígenes de datos desde Azure y combinarlos en experiencias interactivas unificadas. Use los libros proporcionados en Insights o cree los suyos propios a partir de plantillas predefinidas.


![Ejemplo de Workbooks](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) es un servicio de análisis empresarial que proporciona visualizaciones interactivas de varios orígenes de datos. Es un medio eficaz de poner los datos a disposición de otras personas de la organización y externas. Puede configurar Power BI para que [los datos de registro se importen automáticamente desde Azure Monitor](./logs/log-powerbi.md) y utilizar estas otras visualizaciones.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integración y exportación de datos
A menudo, necesitará integrar Azure Monitor con otros sistemas y crear soluciones personalizadas que utilicen los datos de supervisión. Otros servicios de Azure funcionan con Azure Monitor para proporcionar esta integración.

### <a name="event-hub"></a>Centro de eventos
[Azure Event Hubs](../event-hubs/index.yml) es una plataforma de streaming y un servicio de ingesta de eventos. Puede transformar y almacenar los datos con cualquier proveedor de análisis en tiempo real o adaptador de almacenamiento o procesamiento por lotes. Utilice Event Hubs para [transmitir datos de Azure Monitor](essentials/stream-monitoring-data-event-hubs.md) a herramientas de supervisión y administración de eventos e información de seguridad de asociados.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) es un servicio que le permite automatizar tareas y procesos de negocio mediante flujos de trabajo que se integran con diferentes sistemas y servicios. Hay disponibles actividades que leen y escriben métricas y registros en Azure Monitor. Esto permite crear flujos de trabajo que se integren con otros sistemas.


### <a name="api"></a>API
Existen varias API para leer y escribir métricas y registros en Azure Monitor, que además proporcionan acceso a las alertas generadas. También puede configurar y recuperar alertas. De este modo, dispone de unas posibilidades prácticamente ilimitadas para crear soluciones personalizadas que se integren con Azure Monitor.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre:

* Las [métricas y registros](./data-platform.md#metrics) de los datos que recopila Azure Monitor.
* Los [orígenes de datos](agents/data-sources.md) de la forma en que los distintos componentes de la aplicación envían la telemetría.
* Las [consultas de registros](logs/log-query-overview.md) para analizar los datos recopilados.
* [Procedimientos recomendados](/azure/architecture/best-practices/monitoring) para supervisar los servicios y las aplicaciones en la nube.
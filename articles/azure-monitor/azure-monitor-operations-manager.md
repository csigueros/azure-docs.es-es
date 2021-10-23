---
title: Azure Monitor para clientes actuales de Operations Manager
description: Guía para que los usuarios actuales de Operations Manager transfieran la supervisión de ciertas cargas de trabajo a Azure Monitor como parte de una transición a la nube.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: e6c09cfb749542f268c6a6bf749328698b98688e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178469"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor para clientes actuales de Operations Manager
Este artículo es una guía para los clientes que usan actualmente [System Center Operations Manager](/system-center/scom/welcome) y que están planeando una transición a [Azure Monitor](overview.md) a medida que migran aplicaciones empresariales y otros recursos a Azure. Se da por supuesto que el objetivo final es una transición completa a la nube, que reemplazará todas las funcionalidades posibles de Operations Manager con Azure Monitor, sin poner en peligro los requisitos empresariales y de TI. 

Las recomendaciones específicas que se realicen en este artículo cambiarán a medida que Azure Monitor y Operations Manager incorporen características. Aunque la estrategia básica seguirá siendo la misma.

> [!IMPORTANT]
> La implementación de varias de las características de Azure Monitor descritas aquí conlleva un costo, por lo que debe evaluar su valor antes de realizar la implementación en todo el entorno.

## <a name="prerequisites"></a>Requisitos previos
En este artículo se da por sentado que ya usa [Operations Manager](/system-center/scom) y, al menos, tiene conocimientos básicos de [Azure Monitor](overview.md). Para ver una comparación completa entre ambas opciones, consulte [Guía sobre la supervisión en la nube: Introducción a las plataformas de supervisión](/azure/cloud-adoption-framework/manage/monitor/platform-overview). En este artículo se detallan las diferencias de características específicas entre ambas opciones para ayudarle a comprender algunas de las recomendaciones de este texto. 


## <a name="general-strategy"></a>Estrategia general
No hay ninguna herramienta de migración que convierta los recursos de Operations Manager en recursos de Azure Monitor, ya que las plataformas son totalmente distintas. En su lugar, la migración constituirá una [implementación de Azure Monitor estándar](best-practices.md) mientras sigue usando Operations Manager. A medida que personalice Azure Monitor para cumplir con sus requisitos de diferentes aplicaciones y componentes, y a medida que obtenga más características, podrá empezar a retirar diferentes módulos de administración y agentes en Operations Manager.

La estrategia general recomendada en este artículo es la misma que en la [Guía sobre la supervisión en la nube](/azure/cloud-adoption-framework/manage/monitor/), que recomienda una [estrategia de supervisión de nube híbrida](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) que permite realizar una transición gradual a la nube. Aunque algunas características pueden superponerse, esta estrategia le permitirá mantener los procesos empresariales existentes a medida que se familiarice con la plataforma nueva. Solo dejará atrás la funcionalidad de Operations Manager cuando pueda reemplazarla por Azure Monitor. El uso de varias herramientas de supervisión aumenta la complejidad, aunque le permite sacar partido de la capacidad de Azure Monitor para supervisar las cargas de trabajo en la nube de próxima generación, a la vez que conserva la capacidad de Operations Manager para supervisar el software del servidor y los componentes de infraestructura que pueden encontrarse en el entorno local o en otras nubes. 


## <a name="components-to-monitor"></a>Componentes que se supervisarán
Categorizar los diferentes tipos de cargas de trabajo que necesita supervisar ayuda a definir una estrategia de supervisión distinta para cada una. [Guía sobre la supervisión en la nube: Formulación de una estrategia de supervisión](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) proporciona un desglose detallado de las diferentes capas de su entorno que necesitan supervisión a medida que pasa de aplicaciones empresariales heredadas a aplicaciones modernas en la nube.

Antes de la nube, se usaba Operations Manager para supervisar todas las capas. Cuando comience la transición con la infraestructura como servicio (IaaS), seguirá usando Operations Manager para las máquinas virtuales, pero empezará a usar Azure Monitor para los recursos en la nube. A medida que su transición avance a aplicaciones modernas que usan la plataforma como servicio (PaaS), podrá centrarse más en Azure Monitor y empezar a retirar la funcionalidad de Operations Manager.


![Modelos en la nube](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Estas capas se pueden simplificar en las siguientes categorías, que se describen con más detalle en el resto de este artículo. Aunque es posible que no todas las cargas de trabajo de supervisión del entorno encajen en una de estas categorías, cada una de ellas debe estar suficientemente parecida a una categoría determinada para que se apliquen las recomendaciones generales.

**Aplicaciones empresariales.** Aplicaciones que ofrecen una funcionalidad específica para su empresa. Pueden ser internas o externas y a menudo se desarrollan internamente mediante código personalizado. Las aplicaciones heredadas normalmente se hospedarán en máquinas virtuales o equipos físicos que ejecutan Windows o Linux, mientras que las aplicaciones más recientes se basarán en servicios de aplicaciones de Azure, como Azure Web Apps y Azure Functions.

**Servicios de Azure.** Recursos de Azure que son compatibles con las aplicaciones empresariales que se han migrado a la nube. Esto incluye servicios como Azure Storage, Azure SQL y Azure IoT. Esto también incluye máquinas virtuales de Azure, ya que se supervisan como otros servicios de Azure, pero las aplicaciones y el software que se ejecutan en el sistema operativo invitado de esas máquinas virtuales requieren más supervisión más allá del host.

**Software del servidor.** Software que se ejecuta en máquinas virtuales y físicas que son compatibles con aplicaciones empresariales o aplicaciones empaquetadas que proporcionan funcionalidad general a su empresa. Entre los ejemplos se incluyen Internet Information Server (IIS), SQL Server, Exchange y SharePoint. Esto también incluye el sistema operativo Windows o Linux en sus máquinas virtuales y físicas.

**Infraestructura local.** Componentes específicos del entorno local que requieren supervisión. Esto incluye recursos como servidores físicos, almacenamiento y componentes de red. Estos son los componentes que se virtualizan cuando se transiciona a la nube.

## <a name="sample-walkthrough"></a>Tutorial de ejemplo
El siguiente es un tutorial hipotético de una migración de Operations Manager a Azure Monitor. No está diseñado para representar toda la complejidad de una migración real, aunque proporciona como mínimo los pasos básicos y su secuencia. En las secciones a continuación se describe cada uno de estos pasos con más detalle.

El entorno antes de cambiar los componentes a Azure se basa en máquinas virtuales y físicas con ubicaciones en el entorno local o con un proveedor de servicios administrados. Este usa Operations Manager para supervisar las aplicaciones empresariales, el software de servidor y otros componentes de infraestructura en el entorno, como servidores físicos y redes. Usted usa los módulos de administración estándar para el software de servidor, como IIS, SQL Server y el software de varios proveedores, y ajusta dichos módulos según sus requisitos específicos. Crea módulos de administración personalizados para las aplicaciones empresariales y otros componentes que no se pueden supervisar con los módulos de administración actuales y configura Operations Manager para admitir los procesos empresariales.

La migración a Azure comienza con IaaS al cambiar las máquinas virtuales que admiten aplicaciones empresariales a Azure. Los requisitos de supervisión de estas aplicaciones y el software de servidor de los que dependen no cambian y puede seguir usando Operations Manager en estos servidores con los módulos de administración actuales. 

Azure Monitor está habilitado para los servicios de Azure en cuanto crea una suscripción a Azure. Azure Monitor recopila automáticamente las métricas de la plataforma y el registro de actividad y usted configura los registros de recursos que se van a recopilar para poder analizar de manera interactiva toda la telemetría disponible mediante consultas de registro. VM Insights se habilita en las máquinas virtuales para analizar los datos de supervisión en todo el entorno y para detectar las relaciones entre las máquinas y los procesos. Puede ampliar el uso de Azure Monitor a las máquinas virtuales y físicas en el entorno local si habilita en ellas servidores habilitados para Azure Arc. 

Usted habilita Application Insights para cada una de sus aplicaciones empresariales. Identifica los distintos componentes de cada aplicación, comienza a recopilar datos de uso y rendimiento e identifica los errores que se producen en el código. Usted crea pruebas de disponibilidad para probar de manera proactiva las aplicaciones externas y avisarle de los problemas de rendimiento o disponibilidad. Aunque Application Insights ofrece características eficaces que no se encuentran en Operations Manager, sigue confiando en los módulos de administración personalizados que desarrolló para las aplicaciones empresariales, ya que incluyen escenarios de supervisión que aún no incluye Azure Monitor. 

A medida que se familiariza con Azure Monitor, comienza a crear reglas de alertas que pueden reemplazar algunas de las funcionalidades del módulo de administración y comienza a desarrollar los procesos empresariales para que usen la nueva plataforma de supervisión. Esto le permite empezar a quitar los equipos y los módulos de administración del grupo de administración de Operations Manager. Sigue usando los módulos de administración para el software de servidor crítico y la infraestructura local, aunque sigue atento a nuevas características en Azure Monitor que le permitirán retirar funcionalidades adicionales.

## <a name="monitor-azure-services"></a>Supervisión de servicios de Azure
En realidad, los servicios de Azure requieren que Azure Monitor recopile telemetría y esta opción se habilita en el momento en que se crea una suscripción a Azure. El [registro de actividad](essentials/activity-log.md) se recopila automáticamente para la suscripción y las [métricas de plataforma](essentials/data-platform-metrics.md) se recopilan automáticamente desde todos los recursos de Azure que se crean. Puede empezar a usar el [explorador de métricas](essentials/metrics-getting-started.md) de inmediato, que es similar a las vistas de rendimiento en la consola de Operations, aunque ofrece un análisis interactivo y [agregaciones avanzadas](essentials/metrics-charts.md) de datos. [Cree una alerta de métrica](alerts/alerts-metric.md) para recibir una notificación cuando un valor supere un umbral o [agregue un gráfico a un panel de Azure](essentials/metrics-charts.md#pinning-to-dashboards) para visibilidad.

[![Explorador de métricas](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Cree una configuración de diagnóstico](essentials/diagnostic-settings.md) para que cada recurso de Azure envíe métricas y [registros de recurso](essentials/resource-logs.md), que proporcionan detalles sobre el funcionamiento interno de cada recurso a un área de trabajo de Log Analytics. Esto le proporciona toda la telemetría disponible para los recursos y le permite usar [Log Analytics](logs/log-analytics-overview.md) para analizar de manera interactiva los datos de rendimiento y registro con un lenguaje de consulta avanzado que no tiene ningún equivalente en Operations Manager. También puede crear [alertas de consulta de registro](alerts/alerts-log-query.md), que pueden usar una lógica compleja para determinar las condiciones de una alerta y correlacionar los datos de varios recursos.

[![Análisis de registros](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Las [conclusiones](monitor-reference.md) en Azure Monitor son similares a los módulos de administración, ya que proporcionan una supervisión única para un servicio de Azure determinado. Las conclusiones están disponibles actualmente para varios servicios, como las redes, el almacenamiento y los contenedores, y continuamente se agregan otros.

[![Ejemplo de conclusiones](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Las conclusiones se basa en [libros](visualize/workbooks-overview.md) de Azure Monitor, que combinan las métricas y las consultas de registro en informes interactivos enriquecidos. Cree sus propios libros para combinar datos de varios servicios, de manera similar a crea vistas e informes personalizados en la consola de Operations.

### <a name="azure-management-pack"></a>Módulo de administración de Azure
El [módulo de administración de Azure](https://www.microsoft.com/download/details.aspx?id=50013) permite que Operations Manager detecte recursos de Azure y supervise su mantenimiento según un conjunto determinado de escenarios de supervisión. Este módulo de administración requiere la configuración adicional de cada recurso en Azure, pero puede resultar útil para ofrecerle visibilidad de los recursos de Azure en la consola de Operations hasta que desarrolle los procesos empresariales para centrarse en Azure Monitor.

[![Módulo de administración de Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Puede optar por usar el módulo de administración de Azure si quiere obtener visibilidad para determinados recursos de Azure en la consola de Operations y quiere integrar algunas alertas básicas con los procesos existentes. En realidad, usa datos que ha recopilado Azure Monitor. No obstante, debe pensar en Azure Monitor para la supervisión completa a largo plazo de sus recursos de Azure. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Supervisión del software de servidor y la infraestructura local
Al trasladar máquinas a la nube, los requisitos de supervisión del software no cambian. Ya no necesita supervisar sus componentes físicos porque están virtualizados, pero el sistema operativo invitado y sus cargas de trabajo tienen los mismos requisitos, independientemente del entorno.

[VM Insights](vm/vminsights-overview.md) es la característica principal de Azure Monitor para supervisar máquinas virtuales, su sistema operativo invitado y las cargas de trabajo. De manera similar a Operations Manager, VM Insights usa un agente para recopilar datos del sistema operativo invitado de las máquinas virtuales. Se trata de los mismos datos de rendimiento y eventos que suelen usar los módulos de administración para el análisis y las alertas. No obstante, no hay ninguna regla preexistente para identificar y enviar alertas sobre los problemas de las aplicaciones empresariales y el software de servidor que se ejecuta en esas máquinas. Debe crear sus propias reglas de alerta para recibir notificaciones proactivamente de los problemas detectados.

[![Rendimiento de VM Insights](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor tampoco mide el mantenimiento de las distintas aplicaciones y servicios que se ejecutan en una máquina virtual. Las alertas de métricas se pueden resolver automáticamente cuando un valor cae por debajo de un umbral, aunque actualmente Azure Monitor no tiene la capacidad de definir criterios de mantenimiento para las aplicaciones y servicios que se ejecutan en la máquina, ni puede proporcionar un resumen de mantenimiento para agrupar el mantenimiento de los componentes relacionados.

> [!NOTE]
> Una nueva [característica de estado de invitado para VM Insights](vm/vminsights-health-overview.md) se encuentra actualmente en versión preliminar pública y envía alertas según el estado de mantenimiento de un conjunto de métricas de rendimiento. Inicialmente, estará limitada a un conjunto específico de contadores de rendimiento relacionados con el sistema operativo invitado y no con aplicaciones u otras cargas de trabajo que se ejecutan en la máquina virtual.
> 
> [![Estado de invitado de VM Insights](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

La supervisión del software en las máquinas de un entorno híbrido normalmente usará una combinación de VM Insights y Operations Manager, según los requisitos de cada máquina y de la experiencia que tenga en el desarrollo de procesos operativos relacionados con Azure Monitor. Ambas plataformas usan el agente de administración de Microsoft (denominado agente de Log Analytics en Azure Monitor), de modo que ambas pueden supervisar simultáneamente una misma máquina.

> [!NOTE]
> En el futuro, VM Insights pasará al [agente de Azure Monitor](agents/azure-monitor-agent-overview.md), que se encuentra actualmente en versión preliminar pública. Será compatible con Microsoft Monitoring Agent, por lo que ambas plataformas podrán supervisar la misma máquina virtual.

Siga usando Operations Manager para las funcionalidades que Azure Monitor aún no puede proporcionar. Esto incluye los módulos de administración para software de servidor crítico, como IIS, SQL Server o Exchange. También puede tener módulos de administración personalizados desarrollados para una infraestructura local con la que no se puede comunicar mediante Azure Monitor. También siga usando Operations Manager si está integrado estrechamente en sus procesos operativos hasta que pueda realizar la transición para modernizar las operaciones de servicio en las que Azure Monitor y otros servicios de Azure pueden aumentar sus opciones o reemplazarlas. 

Use Azure Monitor para VM para mejorar la supervisión actual, incluso si no reemplazará inmediatamente a Operations Manager. Entre los ejemplos de características exclusivas de Azure Monitor se incluyen los siguientes:

- Detección y supervisión de las relaciones entre las máquinas virtuales y sus dependencias externas.
- Visualización de datos de rendimiento agregados de varias máquinas virtuales en gráficos y libros interactivos.
- Uso de [consultas de registro](logs/log-query-overview.md) para analizar de manera interactiva la telemetría de sus máquinas virtuales con datos de otros recursos de Azure.
- Creación de [reglas de alertas de registro](alerts/alerts-log-query.md) basadas en una lógica compleja en varias máquinas virtuales.

[![Asignación de VM Insights](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Además de las máquinas virtuales de Azure, VM Insights puede supervisar máquinas en el entorno local y en otras nubes mediante [servidores habilitados para Azure Arc](../azure-arc/servers/overview.md). Los servidores habilitados para Azure Arc permiten administrar las máquinas Windows y Linux hospedadas fuera de Azure, en la red corporativa o en otro proveedor de nube, de manera coherente con cómo se administran las máquinas virtuales nativas de Azure.



## <a name="monitor-business-applications"></a>Supervisión de aplicaciones empresariales
Normalmente, se necesitan módulos de administración personalizados para supervisar las aplicaciones empresariales con Operations Manager, de modo que se aprovechan los agentes instalados en cada máquina virtual. Application Insights en Azure Monitor supervisa las aplicaciones basadas en web, independientemente de si se encuentran en Azure, en otras nubes o en el entorno local, por lo que se puede usar para todas las aplicaciones tanto si se han migrado a Azure como si no.

Si la supervisión de una aplicación empresarial está limitada a la funcionalidad que proporciona la [plantilla de rendimiento de aplicaciones .NET]() en Operations Manager, probablemente pueda migrar a Application Insights sin pérdidas de funcionalidad. De hecho, Application Insights incluirá un número significativo de características adicionales, entre las que se incluyen las siguientes:

- Detección automática y supervisión de los componentes de la aplicación.
- Recopilación de datos detallados sobre el uso y el rendimiento de la aplicación, como el tiempo de respuesta, las tasas de error y las tasas de solicitud.
- Recopilación de datos del explorador, como vistas de página y rendimiento de la carga.
- Detección de excepciones y exploración en profundidad del seguimiento de la pila y las solicitudes relacionadas.
- Realización de análisis avanzados con características como [seguimiento distribuido](app/distributed-tracing.md) y [detección inteligente](app/proactive-diagnostics.md).
- Use el [explorador de métricas](essentials/metrics-getting-started.md) para analizar de manera interactiva los datos de rendimiento.
- Use [consultas de registro](logs/log-query-overview.md) para analizar de manera interactiva la telemetría recopilada junto con los datos recopilados para los servicios de Azure y VM Insights.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Hay algunos escenarios en los que es posible que necesite continuar el uso de Operations Manager junto con Application Insights hasta que obtenga la funcionalidad necesaria. Entre los ejemplos en los que es posible que necesite continuar con Operations Manager, se incluyen los siguientes:

-  Las [pruebas de disponibilidad](app/monitor-web-app-availability.md), que le permiten supervisar y enviar alertas sobre la disponibilidad y la capacidad de respuesta de las aplicaciones, requieren de solicitudes entrantes de las direcciones IP de los agentes de pruebas web. Si su directiva no permite este tipo de acceso, es posible que deba continuar el uso de los [monitores de disponibilidad de aplicaciones web](/system-center/scom/web-application-availability-monitoring-template) en Operations Manager.
- En Operations Manager puede establecer cualquier intervalo de sondeo para las pruebas de disponibilidad; muchos clientes realizan comprobaciones cada 60-120 segundos. Application Insights tiene un intervalo de sondeo mínimo de 5 minutos, que puede resultar demasiado largo para algunos clientes.
- Una cantidad significativa de supervisión en Operations Manager se realiza mediante la recopilación de eventos generados por las aplicaciones y la ejecución de scripts en el agente local. No se trata de opciones estándar en Application Insights, por lo que puede requerir un trabajo personalizado para cumplir sus requisitos empresariales. Esto podría incluir reglas de alertas personalizadas con datos almacenados de eventos en un área de trabajo Log Analytics y scripts iniciados en un invitado de la máquina virtual con [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).
- Según el idioma en el que la aplicación esté escrita, es posible que esté limitado en la [instrumentación que se puede usar con Application Insights](app/platforms.md).

Según la estrategia básica de las otras secciones de esta guía, siga usando Operations Manager para las aplicaciones empresariales, pero aproveche las características adicionales que proporciona Application Insights. A medida que pueda reemplazar funcionalidades críticas con Azure Monitor, podrá empezar a retirar los módulos de administración personalizados.


## <a name="next-steps"></a>Pasos siguientes

- Consulte la [Guía sobre la supervisión en la nube](/azure/cloud-adoption-framework/manage/monitor/) para obtener una comparación detallada de Azure Monitor y System Center Operations Manager, así como más detalles sobre el diseño y la implementación de un entorno de supervisión híbrido.
- Obtenga más información sobre la [supervisión de recursos de Azure en Azure Monitor](essentials/monitor-azure-resource.md).
- Obtenga más información sobre la [supervisión de máquinas virtuales de Azure en Azure Monitor](vm/monitor-vm-azure.md).
- Obtenga más información sobre [VM Insights](vm/vminsights-overview.md).
- Obtenga más información sobre [Application Insights](app/app-insights-overview.md).

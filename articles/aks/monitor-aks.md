---
title: Supervisión de Azure Kubernetes Service (AKS) con Azure Monitor
description: Se describe cómo usar Azure Monitor para supervisar el mantenimiento y rendimiento de clústeres de AKS y sus cargas de trabajo.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2021
ms.openlocfilehash: 68f0d94482d65f261b40095ac715ad65f76fe7af
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124772208"
---
# <a name="monitoring-azure-kubernetes-service-aks-with-azure-monitor"></a>Supervisión de Azure Kubernetes Service (AKS) con Azure Monitor
En este escenario se describe cómo usar Azure Monitor para supervisar el mantenimiento y rendimiento de Azure Kubernetes Service (AKS). Incluye la recopilación de datos de telemetría críticos para la supervisión, el análisis y la visualización de los datos recopilados para identificar tendencias, y cómo configurar alertas para recibir notificaciones proactivas de problemas críticos.

En la [Guía sobre la supervisión en la nube](/azure/cloud-adoption-framework/manage/monitor/) se definen los [objetivos de supervisión principales](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements) en los que se debe centrar con relación a sus recursos de Azure. Este escenario se centra en la supervisión de mantenimiento y estado usando Azure Monitor.

## <a name="scope-of-the-scenario"></a>Ámbito del escenario
Este escenario está pensado para los clientes que usan Azure Monitor con el fin de supervisar AKS. No incluye lo siguiente, aunque este contenido se puede agregar en actualizaciones posteriores del escenario:

- Supervisión de clústeres de Kubernetes fuera de Azure, salvo para hacer referencia a contenido existente de Kubernetes habilitado para Azure Arc 
- Supervisión de AKS con herramientas distintas de Azure Monitor, salvo para rellenar espacios en Azure Monitor y Container Insights

> [!NOTE]
> Azure Monitor se ha diseñado para supervisar la disponibilidad y el rendimiento de recursos en la nube. Aunque los datos operativos almacenados en Azure Monitor pueden ser útiles para investigar incidentes de seguridad, se han diseñado otros servicios de Azure para supervisar la seguridad. La supervisión de la seguridad de AKS se lleva a cabo mediante [Azure Sentinel](../sentinel/overview.md) y [Azure Security Center](../security-center/security-center-introduction.md). Consulte [Supervisión de máquinas virtuales con Azure Monitor: supervisión de la seguridad](../azure-monitor/vm/monitor-virtual-machine-security.md) para obtener una descripción de las herramientas de supervisión de la seguridad de Azure y su relación con Azure Monitor.
>
> Para obtener información sobre cómo usar los servicios de seguridad con el fin de supervisar AKS, consulte [Azure Defender para Kubernetes: ventajas y características](../security-center/defender-for-kubernetes-introduction.md) y [Conexión de los registros de diagnóstico de Azure Kubernetes Service (AKS) a Azure Sentinel](../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks).
## <a name="container-insights"></a>Container Insights
AKS genera [métricas de plataforma y registros de recursos](monitor-aks-reference.md), como cualquier otro recurso de Azure, que puede usar para supervisar su mantenimiento y rendimiento básicos. Habilite [Container Insights](../azure-monitor/containers/container-insights-overview.md) para ampliar esta supervisión. Container Insights es una característica de Azure Monitor que supervisa el mantenimiento y rendimiento de los clústeres administrados de Kubernetes que están hospedados en AKS, además de otras configuraciones de clúster. Container Insights proporciona vistas interactivas y libros que analizan los datos recopilados para diversos escenarios de supervisión. 

[Prometheus](https://prometheus.io/) y [Grafana](https://www.prometheus.io/docs/visualization/grafana/) son herramientas de código abierto que gozan de gran popularidad para supervisar Kubernetes y cuentan con el respaldo de la CNCF. AKS expone numerosas métricas con el formato de Prometheus, lo que hace que esta herramienta sea una opción muy popular para la supervisión. [Container Insights](../azure-monitor/containers/container-insights-overview.md) ofrece integración nativa con AKS, recopilación de métricas y registros críticos, alertas sobre problemas identificados y visualización con libros. También recopila ciertas métricas de Prometheus, además de gran cantidad de información nativa de Azure Monitor. Container Insights complementa y completa la supervisión E2E de AKS, incluida la recopilación de registros que Prometheus no proporciona como herramienta independiente. Muchos clientes usan juntos la integración de Prometheus y Azure Monitor para la supervisión E2E.

Para obtener más información sobre cómo usar Container Insights, consulte [Introducción a Container Insights](../azure-monitor/containers/container-insights-overview.md). En los [niveles de supervisión de AKS con Container Insights](#monitor-layers-of-aks-with-container-insights) que figuran a continuación se presentan varias características de Container Insights y los escenarios de supervisión que admiten.

:::image type="content" source="media/monitor-aks/container-insights.png" alt-text="Container Insights" lightbox="media/monitor-aks/container-insights.png":::


## <a name="configure-monitoring"></a>Configuración de la supervisión
En las siguientes secciones se describen los pasos necesarios para configurar la supervisión completa de su clúster de AKS mediante Azure Monitor.
### <a name="create-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics
Necesita al menos un área de trabajo de Log Analytics para admitir Container Insights y recopilar y analizar otros datos de telemetría sobre el clúster de AKS. El área de trabajo no tiene ningún coste, pero sí que incurrirá en costes de ingesta y retención al recopilar datos. Consulte [Administración del uso y los costos con los registros de Azure Monitor](../azure-monitor/logs/manage-cost-storage.md) para más información.

Si está empezando a trabajar con Azure Monitor, comience con una sola área de trabajo y considere la posibilidad de crear más a medida que evolucionen los requisitos. Muchos entornos usan una sola área de trabajo para todos los recursos de Azure que supervisan. Puede incluso compartir un área de trabajo que usen [Azure Security Center y Azure Sentinel](../azure-monitor/vm/monitor-virtual-machine-security.md), aunque muchos clientes optan por separar los datos de telemetría de disponibilidad y rendimiento de los datos de seguridad. 

Para obtener información sobre la lógica que debe tener en cuenta al diseñar una configuración de área de trabajo, consulte [Diseño de la implementación de registros de Azure Monitor](../azure-monitor/logs/design-logs-deployment.md).

### <a name="enable-container-insights"></a>Habilitación de la información de contenedor
Al habilitar Container Insights para el clúster de AKS, implementa una versión en contenedor del [agente de Log Analytics](../agents/../azure-monitor/agents/log-analytics-agent.md) que envía datos a Azure Monitor. Hay varios métodos para habilitarlo en función de si trabaja con un clúster de AKS nuevo o ya existente. Consulte [Habilitación de Container Insights](../azure-monitor/containers/container-insights-onboard.md) para conocer los requisitos previos y las opciones de configuración.


### <a name="configure-collection-from-prometheus"></a>Configuración de la recopilación desde Prometheus
Con Container Insights puede recopilar determinadas métricas de Prometheus en su área de trabajo de Log Analytics sin necesidad de un servidor de esta herramienta. Puede analizar estos datos usando características de Azure Monitor junto con otros datos recopilados por Container Insights. Para obtener información sobre esta configuración, consulte [Configuración de la extracción de métricas de Prometheus con Container Insights](../azure-monitor/containers/container-insights-prometheus-integration.md).


### <a name="collect-resource-logs"></a>Recopilación de registros de recursos
Los registros de componentes del plano de control de AKS se implementan en Azure como [registros de recursos](../azure-monitor/essentials/resource-logs.md). Container Insights no usa actualmente estos registros, por lo que debe crear sus propias consultas de registro para verlos y analizarlos. En [Cómo consultar registros de Container Insights](../azure-monitor/containers/container-insights-log-query.md#resource-logs) puede obtener información sobre la estructura de estos registros y cómo escribir consultas relacionadas con estos.

Debe crear una configuración de diagnóstico para recopilar registros de recursos. Cree varias configuraciones de diagnóstico para enviar distintos conjuntos de registros a diferentes ubicaciones. Consulte [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md) para obtener información sobre cómo crear configuraciones de diagnóstico para su clúster de AKS. 

El envío de registros de recursos a un área de trabajo tiene un coste, por lo que recopile solo las categorías de registro que tenga intención de usar. Envíe registros a una cuenta de almacenamiento de Azure para reducir costes si necesita conservar la información, pero no que esté fácilmente disponible para analizarla.  Consulte [Registros de recursos](monitor-aks-reference.md#resource-logs) para obtener una descripción de las categorías disponibles para AKS y [Administración del uso y los costes con los registros de Azure Monitor](../azure-monitor/logs/manage-cost-storage.md) para obtener información sobre el coste de la ingesta y retención de datos de registro. Empiece recopilando un número mínimo de categorías y, después, modifique la configuración de diagnóstico para recopilar más categorías a medida que aumenten sus necesidades y conozca los costes asociados.

Si no sabe seguro qué registros de recursos habilitar inicialmente, use las recomendaciones de la siguiente tabla, basadas en los requisitos más comunes de clientes. Habilite las demás categorías si descubre más adelante que necesita esta información.

| Category | ¿Habilitar? | Destination |
|:---|:---|:---|
| cluster-autoscaler      | Habilitar si el escalado automático está habilitado. | Área de trabajo de Log Analytics |
| guard                   | Habilitar si Azure Active Directory está habilitado. | Área de trabajo de Log Analytics |
| kube-apiserver          | Habilitar | Área de trabajo de Log Analytics |
| kube-audit              | Habilitar | Azure Storage. Reduce al mínimo los costes, pero conserva los registros de auditoría si lo requiere un auditor. |
| kube-audit-admin        | Habilitar | Área de trabajo de Log Analytics |
| kube-controller-manager | Habilitar | Área de trabajo de Log Analytics |
| kube-scheduler          | Disable | |
| AllMetrics              | Habilitar | Área de trabajo de Log Analytics |





## <a name="access-azure-monitor-features"></a>Acceso a las características de Azure Monitor

Puede acceder a las características de Azure Monitor para todos los clústeres de AKS de su suscripción desde el menú **Supervisión** de Azure Portal o, para un solo clúster de AKS, desde la sección **Supervisión** del menú **Servicios de Kubernetes**. En la siguiente captura de pantalla se muestra el menú **Supervisión** del clúster.

:::image type="content" source="media/monitor-aks/monitoring-menu.png" alt-text="Menú &quot;Supervisión&quot; de AKS" lightbox="media/monitor-aks/monitoring-menu.png":::

| Opción de menú | Descripción |
|:---|:---|
| Información detallada | Abre Container Insights para el clúster actual. Seleccione **Contenedores** en el menú **Supervisión** con el fin de abrir Container Insights para todos los clústeres.  |
| Alertas | Muestras alertas relacionadas con la máquina virtual actual. |
| Métricas | Abre el Explorador de métricas con el ámbito establecido en el clúster actual. |
| Configuración de diagnóstico | Crea la configuración de diagnóstico para que el clúster recopile registros de recursos. |
| Advisor | Recomendaciones de Azure Advisor para el clúster actual. |
| Registros | Abre Log Analytics con el ámbito establecido en el clúster actual para analizar datos de registros y acceder a consultas precompiladas. |
| Workbooks | Abre la galería de libros para el servicio de Kubernetes. |




## <a name="monitor-layers-of-aks-with-container-insights"></a>Supervisión de capas de AKS con Container Insights
Debido a la gran varianza de implementaciones de Kubernetes, cada cliente tendrá requisitos únicos para la supervisión de AKS. El enfoque que adopte debe basarse en factores como la escala, la topología, los roles organizativos y el número de inquilinos de los clústeres. En esta sección se presenta una estrategia común basada en un enfoque ascendente, desde la infraestructura hasta las aplicaciones. Cada capa tiene distintos requisitos de supervisión. Estas capas se ilustran en el siguiente diagrama y se analizan con más detalle en las siguientes secciones.

:::image type="content" source="media/monitor-aks/layers.png" alt-text="Capas de AKS"  border="false":::

### <a name="level-1---cluster-level-components"></a>Nivel 1: componentes de nivel de clúster
El nivel de clúster incluye los siguientes componentes:

| Componente | Requisitos de supervisión |
|:---|:---|
| Nodo |  Conozca el estado de preparación y el rendimiento de la CPU, la memoria y el disco para cada nodo y supervise de forma proactiva sus tendencias de uso antes de implementar cargas de trabajo. |


Use las vistas y los informes existentes en Container Insights para supervisar los componentes de nivel de clúster. En la vista **Clúster** se proporciona una vista rápida del rendimiento de los nodos de su clúster, incluido su uso de CPU y memoria. Use la vista **Nodos** para consultar el mantenimiento de cada nodo, además del mantenimiento y rendimiento de los pods que se ejecutan en cada uno de ellos. Consulte [Supervisión del rendimiento del clúster de Kubernetes con Container Insights](../azure-monitor/containers/container-insights-analyze.md) para obtener más información sobre cómo usar esta vista y analizar el mantenimiento y rendimiento de los nodos.

:::image type="content" source="media/monitor-aks/container-insights-cluster-view.png" alt-text="Vista de &quot;Clúster&quot; de Container Insights" lightbox="media/monitor-aks/container-insights-cluster-view.png":::

Use los libros de **Nodo** en Container Insights para analizar la capacidad de disco y la E/S además del uso de GPU. Puede consultar una descripción de los libros en [Libros de Nodo](../azure-monitor/containers/container-insights-reports.md#node-workbooks).

:::image type="content" source="media/monitor-aks/container-insights-node-workbooks.png" alt-text="Libros de &quot;Nodo&quot; de Container Insights" lightbox="media/monitor-aks/container-insights-node-workbooks.png":::


En escenarios de solución de problemas, es posible que tenga que acceder a los nodos de AKS directamente a efectos de mantenimiento o recopilación inmediata de registros. Aunque, por motivos de seguridad, los nodos de AKS no están expuestos a Internet, puede usar `kubectl debug` para conectarse a ellos mediante SSH. Para obtener información sobre este proceso, consulte [Conexión con SSH a los nodos de clúster de Azure Kubernetes Service (AKS) para mantenimiento o solución de problemas](ssh.md).



### <a name="level-2---managed-aks-components"></a>Nivel 2: componentes administrados de AKS
El nivel de AKS administrado incluye los siguientes componentes:

| Componente | Supervisión |
|:---|:---|
| Servidor de API | Supervise el estado del servidor de API, identificando cualquier aumento de la carga de solicitudes y cuellos de botella si el servicio está fuera de servicio. |
| Kubelet | La supervisión de Kubelet contribuye a solucionar problemas relacionados con la administración de pods, pods que no se inician, nodos que no están listos o pods que se van a eliminar.  |

Azure Monitor y Container Insights aún no proporcionan una supervisión completa del servidor de API. Aunque puede usar el Explorador de métricas para ver el contador **Solicitudes en proceso**, debe hacer referencia a las métricas en Prometheus para obtener una vista completa del rendimiento del servidor de API (incluidos valores como la latencia de solicitudes y el tiempo de procesamiento de la cola de trabajo). En [Grafana Labs](https://grafana.com/grafana/dashboards/12006) hay disponible un panel de Grafana donde se proporcionan vistas de las métricas críticas para el servidor de API. Use este panel en el servidor de Grafana ya existente o configure un nuevo servidor en Azure consultando [Supervisión de los servicios de Azure en Grafana](../azure-monitor/visualize/grafana-plugin.md).

:::image type="content" source="media/monitor-aks/grafana-api-server.png" alt-text="Servidor de API de Grafana" lightbox="media/monitor-aks/grafana-api-server.png":::

Use el libro **Kubelet** para consultar el mantenimiento y rendimiento de cada kubelet. Consulte [Libros de supervisión de recursos](../azure-monitor/containers/container-insights-reports.md#resource-monitoring-workbooks) para obtener información sobre estos libros. En escenarios de solución de problemas, puede acceder a los registros de kubelets usando el proceso que se describe en [Obtención de registros de kubelet desde nodos de clúster de Azure Kubernetes Service (AKS)](kubelet-logs.md).

:::image type="content" source="media/monitor-aks/container-insights-kubelet-workbook.png" alt-text="Libro de kubelet de Container Insights" lightbox="media/monitor-aks/container-insights-kubelet-workbook.png":::

### <a name="resource-logs"></a>Registros del recurso
Use [consultas de registro con registros de recursos](../azure-monitor/containers/container-insights-log-query.md#resource-logs) para analizar registros del plano de control generados por componentes de AKS. 

### <a name="level-3---kubernetes-objects-and-workloads"></a>Nivel 3: objetos y cargas de trabajo de Kubernetes
Los objetos y las cargas de trabajo de Kubernetes incluyen los siguientes componentes:

| Componente | Requisitos de supervisión |
|:---|:---|
| Implementaciones | Compare el estado real de la implementación con el deseado y supervise el estado y el uso de recursos de los pods que se ejecutan en ellos.  | 
| Pods | Supervise el estado y el uso de recursos, incluidas la CPU y la memoria, de los pods que se ejecutan en el clúster de AKS. |
| Contenedores | Supervise el uso de recursos, incluidas la CPU y la memoria, de los contenedores que se ejecutan en el clúster de AKS. |


Use las vistas y los informes existentes en Container Insights para supervisar los contenedores y pods. Use las vistas **Nodos** y **Controladoras** para consultar el mantenimiento y el rendimiento de los pods que se ejecutan en ellos y explorar en profundidad los de sus contenedores. Puede consultar el mantenimiento y rendimiento de contenedores directamente desde la vista **Contenedores**. Consulte [Supervisión del rendimiento del clúster de Kubernetes con Container Insights](../azure-monitor/containers/container-insights-analyze.md) para obtener más información sobre cómo usar esta vista y analizar el mantenimiento y rendimiento de los contenedores.

:::image type="content" source="media/monitor-aks/container-insights-containers-view.png" alt-text="Vista &quot;Contenedores&quot; de Container Insights" lightbox="media/monitor-aks/container-insights-containers-view.png":::

Use el libro **Implementación** de Container Insights con el fin de consultar métricas recopiladas con relación a implementaciones. Para obtener más información, consulte [Implementación y métricas de HPA con Container Insights](../azure-monitor/containers/container-insights-deployment-hpa-metrics.md).

> [!NOTE]
> La vista "Implementaciones" de Container Insights se encuentra actualmente en versión preliminar pública.

:::image type="content" source="media/monitor-aks/container-insights-deployments-workbook.png" alt-text="Libro &quot;Implementaciones&quot; de Container Insights" lightbox="media/monitor-aks/container-insights-deployments-workbook.png":::

#### <a name="live-data"></a>Datos en directo
En escenarios de solución de problemas, Container Insights proporciona acceso a registros de contenedores de AKS (stdout/stderror), eventos y métricas de pod en directo. Para obtener más información sobre esta característica, consulte [Cómo ver los registros de Kubernetes, eventos y métricas de pods en tiempo real](../azure-monitor/containers/container-insights-livedata-overview.md).

:::image type="content" source="media/monitor-aks/container-insights-live-data.png" alt-text="Datos en directo de Container Insights" lightbox="media/monitor-aks/container-insights-live-data.png":::

### <a name="level-4--applications"></a>Nivel 4: aplicaciones
El nivel de aplicación incluye las cargas de trabajo de aplicación que se ejecutan en el clúster de AKS.

| Componente | Requisitos de supervisión |
|:---|:---|
| APLICACIONES | Supervise las implementaciones de aplicaciones de microservicios para identificar errores de aplicación y problemas de latencia. Incluye información como las tasas de solicitudes, los tiempos de respuesta y las excepciones. |

Application Insights proporciona una supervisión completa de las aplicaciones que se ejecutan en AKS y otros entornos. Si tiene una aplicación de Java, puede proporcionar supervisión sin instrumentar el código siguiendo las instrucciones incluidas en [Supervisión de aplicaciones sin instrumentación para Kubernetes: Application Insights de Azure Monitor](../azure-monitor/app/kubernetes-codeless.md). Sin embargo, para realizar una supervisión completa, debe configurar una supervisión basada en código en función de la aplicación.

- [Aplicaciones ASP.NET](../azure-monitor/app/asp-net.md)
- [Aplicación ASP.NET Core](../azure-monitor/app/asp-net-core.md)
- [Aplicaciones de consola .NET](../azure-monitor/app/console.md)
- [Java](../azure-monitor/app/java-in-process-agent.md)
- [Node.js](../azure-monitor/app/nodejs.md)
- [Python](../azure-monitor/app/opencensus-python.md)
- [Otras plataformas](../azure-monitor/app/platforms.md)

Consulte [¿Qué es Application Insights?](../azure-monitor/app/app-insights-overview.md). 

### <a name="level-5--external-components"></a>Nivel 5: componentes externos
Los componentes externos de AKS incluyen lo siguiente:

| Componente | Requisitos de supervisión |
|:---|:---|
| Service Mesh, Ingress y Egress | Métricas basadas en el componente. |
| Colas de trabajo y base de datos | Métricas basadas en el componente. |

Supervise componentes externos, como Service Mesh, Ingress o Egress, con Prometheus y Grafana u otras herramientas de propiedad. Supervise bases de datos y otros recursos de Azure usando otras características de Azure Monitor.

## <a name="analyze-metric-data-with-metrics-explorer"></a>Análisis de datos de métricas con el Explorador de métricas
Use el Explorador de métricas cuando desee realizar análisis personalizados de datos de métricas recopilados con relación a sus contenedores. Con el Explorador de métricas puede trazar gráficos, correlacionar visualmente tendencias e investigar picos y caídas en valores de métricas. Cree una alerta de métrica para recibir notificaciones proactivas cuando el valor de una métrica cruce un umbral y ancle gráficos a paneles para que los usen distintos miembros de la organización.

Consulte [Introducción al Explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para obtener más información sobre esta característica. En [Supervisión de las métricas de referencia de datos de AKS](monitor-aks-reference.md#metrics) puede consultar una lista de las métricas de la plataforma que se recopilan para AKS. Cuando Container Insights está habilitado para un clúster, hay disponibles [valores de métrica adicionales](../azure-monitor/containers/container-insights-update-metrics.md).

:::image type="content" source="media/monitor-aks/metrics-explorer.png" alt-text="Explorador de métricas" lightbox="media/monitor-aks/metrics-explorer.png":::



## <a name="analyze-log-data-with-log-analytics"></a>Análisis de datos de registro con Log Analytics
Use Log Analytics cuando quiera analizar registros de recursos o profundizar en los datos que se usan para crear las vistas en Container Insights. Con Log Analytics puede realizar análisis personalizados de sus datos de registro. 

En [Cómo consultar registros de Container Insights](../azure-monitor/containers/container-insights-log-query.md) puede obtener información sobre cómo usar consultas de registro para analizar los datos que recopila Container Insights. Consulte [Uso de consultas en Azure Monitor Log Analytics](../azure-monitor/logs/queries.md) para obtener información sobre cómo usar estas consultas y [Tutorial de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) para acceder a un completo tutorial sobre cómo usar Log Analytics para ejecutar consultas y trabajar con sus resultados.

Para obtener una lista de las tablas recopiladas para AKS que se pueden analizar en el Explorador de métricas, consulte [Supervisión de registros de referencias de datos de AKS](monitor-aks-reference.md#azure-monitor-logs-tables).

:::image type="content" source="media/monitor-aks/log-analytics-queries.png" alt-text="Consultas de Log Analytics para Kubernetes" lightbox="media/monitor-aks/log-analytics-queries.png":::

Además de los datos de Container Insights, puede usar consultas de registro para analizar registros de recursos de AKS. Para obtener una lista de las categorías de registro disponibles, consulte [Registros de recursos de referencia de datos de AKS](monitor-aks-reference.md#resource-logs). Antes de que se recopilen los datos, debe crear una configuración de diagnóstico para recopilar cada categoría., tal y como se describe en [Configuración de la supervisión](#configure-monitoring). 




## <a name="alerts"></a>Alertas
Las [alertas de Azure Monitor](../azure-monitor/alerts/alerts-overview.md) notifican al usuario proactivamente sobre datos y patrones interesantes en los datos de supervisión. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Aunque no hay reglas de alertas preconfiguradas para clústeres de AKS, puede crear las suyas propias en función de los datos que recopile Container Insights.

> [!IMPORTANT]
> La mayoría de las reglas de alertas tienen un coste que depende del tipo de regla, del número de dimensiones que incluyen y de la frecuencia con que se ejecutan. Consulte **Reglas de alertas** en [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) antes de crear reglas de este tipo.


### <a name="choosing-the-alert-type"></a>Elección del tipo de alerta
Los tipos más comunes de reglas de alertas de Azure Monitor son las [alertas de métricas](../azure-monitor/alerts/alerts-metric.md) y las [alertas de consultas de registro](../azure-monitor/alerts/alerts-log-query.md). El tipo de regla de alertas que cree para un escenario determinado dependerá de dónde se encuentran los datos relacionados con la alerta. Sin embargo, es posible que tenga casos en los que los datos de un escenario de alertas determinado estén disponibles en métricas y registros, y necesite determinar qué tipo de regla usar. 

Normalmente, la mejor estrategia es usar alertas de métricas en lugar de alertas de registros siempre que sea posible, ya que tienen más capacidad de respuesta y ofrecen más información sobre el estado. Puede crear una alerta de métrica con respecto a cualquier valor que pueda analizar en el Explorador de métricas. Si la lógica de la regla de alertas requiere datos en registros, o bien si requiere una lógica más compleja, puede usar una regla de alertas de consulta de registro.

Por ejemplo, si desea que se envíe una alerta cuando una carga de trabajo de aplicación consuma demasiada CPU, puede crear una alerta de métrica mediante esta métrica. Si necesita una alerta cuando se encuentre un mensaje determinado en un registro del plano de control, necesitará una alerta de registro.
### <a name="metric-alert-rules"></a>Reglas de alertas de métricas
Las reglas de alertas de métricas usan los mismos valores de métrica que el Explorador de métricas. De hecho, puede crear una regla de alertas directamente desde el Explorador de métricas con los datos que esté analizando. Puede usar cualquiera de los valores de [métricas de referencia de datos de AKS](monitor-aks-reference.md#metrics) para las reglas de alertas de métrica.

Container Insights incluye una característica en versión preliminar pública que crea un conjunto recomendado de reglas de alertas de métrica para el clúster de AKS. Esta característica crea valores de métrica (también en versión preliminar) usados por las reglas de alertas, que también puede usar en el Explorador de métricas. Consulte [Alertas de métricas recomendadas (versión preliminar) de Container Insights](../azure-monitor/containers/container-insights-metric-alerts.md) para obtener más información sobre esta característica y sobre cómo crear alertas de métricas para AKS.


### <a name="log-alerts-rules"></a>Reglas de alertas de registro
Use reglas de alertas de registro para generar una alerta a partir de los resultados de una consulta de registro. Puede tratarse de datos recopilados por Container Insights o procedentes de registros de recursos de AKS. Consulte [Cómo crear alertas de registro desde Container Insights](../azure-monitor/containers/container-insights-log-alerts.md) para obtener más información sobre las reglas de alertas de registro para AKS y un conjunto de consultas de ejemplo diseñadas para reglas de alertas. También puede consultar [Cómo consultar registros de Container Insights](../azure-monitor/containers/container-insights-log-query.md) para obtener más información sobre las consultas de registro que se podrían modificar para reglas de alertas.

### <a name="virtual-machine-alerts"></a>Alertas de máquina virtual
AKS se basa en un conjunto de escalado de máquinas virtuales que debe ser correcto para ejecutar cargas de trabajo de AKS. Puede alertar sobre métricas críticas, como la CPU, la memoria y el almacenamiento de las máquinas virtuales, usando las instrucciones que se incluyen en [Supervisión de máquinas virtuales con Azure Monitor: alertas](../azure-monitor/vm/monitor-virtual-machine-alerts.md).

### <a name="prometheus-alerts"></a>Alertas de Prometheus
Para aquellas condiciones en las que Azure Monitor no tenga los datos necesarios para una condición de alerta, o en las que es posible que las alertas no tengan suficiente capacidad de respuesta, debe configurar alertas en Prometheus. Un ejemplo son las alertas para el servidor de API. Azure Monitor no recopila información crítica sobre el servidor de API, ni siquiera si está disponible o tiene un cuello de botella. Puede crear una alerta de consulta de registro con los datos de la categoría de registro de recursos kube-apiserver. Sin embargo, en este caso pueden pasar hasta varios minutos antes de recibir una alerta que puede no ser suficiente para sus requisitos. 


## <a name="next-steps"></a>Pasos siguientes

- Consulte [Supervisión de la referencia de datos de AKS](monitor-aks-reference.md) para obtener una referencia de las métricas, los registros y otros valores importantes que crea AKS.
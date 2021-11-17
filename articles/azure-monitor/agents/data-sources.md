---
title: Orígenes de datos en Azure Monitor | Microsoft Docs
description: Describe los datos disponibles para supervisar el estado y rendimiento de los recursos de Azure y las aplicaciones que se ejecutan en ellos.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: cac077bf962e8d021fb554acb5576d9d5665ebe6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284747"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Orígenes de datos de supervisión para Azure Monitor
Azure Monitor se basa en una [plataforma de datos de supervisión común](../data-platform.md) que incluye [registros](../logs/data-platform-logs.md) y [métricas](../essentials/data-platform-metrics.md). La recopilación de datos en esta plataforma permite que los datos de múltiples recursos se analicen juntos mediante un conjunto común de herramientas en Azure Monitor. Los datos de supervisión también pueden enviarse a otras ubicaciones para admitir determinados escenarios, y algunos recursos pueden realizar operaciones de escritura en otras ubicaciones para poder recopilarse en registros o métricas.

En este artículo se describen los diferentes orígenes de datos de datos de supervisión recopilados por Azure Monitor además de los datos de supervisión creados por los recursos de Azure. Se proporcionan vínculos a información detallada sobre la configuración necesaria para recopilar estos datos en diferentes ubicaciones.

## <a name="application-tiers"></a>Niveles de aplicación

Los orígenes de datos de supervisión de las aplicaciones de Azure se pueden organizar en niveles. Los niveles más altos son su propia aplicación y los niveles más bajos son componentes de la plataforma de Azure. El método de acceso a los datos de cada nivel es variable. Los niveles de aplicación se resumen en la tabla siguiente y los orígenes de datos de supervisión en cada nivel se presentan en las siguientes secciones. Consulte [Supervisión de ubicaciones de datos en Azure](../monitor-reference.md) para obtener una descripción de cada ubicación de datos y cómo puede acceder a sus datos.


![Supervisión de niveles](../media/overview/overview.png)


### <a name="azure"></a>Azure
La siguiente tabla describe brevemente los niveles de aplicación específicos de Azure. Siga el vínculo para obtener más información sobre cada una de las secciones a continuación.

| Nivel | Descripción | Método de recopilación |
|:---|:---|:---|
| [Inquilino de Azure](#azure-tenant) | datos sobre el funcionamiento de los servicios de Azure en el nivel del inquilino, como Azure Active Directory. | Vea los datos de AAD en el portal o configure la recopilación en o Azure Monitor mediante una configuración de diagnóstico de inquilino. |
| [Suscripción de Azure](#azure-subscription) | Datos relacionados con el estado y la administración de los servicios entre recursos en su suscripción de Azure, como Resource Manager y Service Health. | Ver en el portal o configurar la colección para Azure Monitor mediante perfil de registro. |
| [Recursos de Azure](#azure-resources) |  Datos sobre el funcionamiento y el rendimiento de cada recurso de Azure. | Métricas recopiladas automáticamente. Ver en el explorador de métricas.<br>Configure las opciones de diagnóstico para recopilar registros en Azure Monitor.<br>Hay perspectivas y soluciones de supervisión disponibles para realizar una supervisión más detallada de tipos de recursos específicos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, otra nube o local 
La siguiente tabla describe brevemente los niveles de aplicación posibles en Azure, otra nube o local. Siga el vínculo para obtener más información sobre cada una de las secciones a continuación.

| Nivel | Descripción | Método de recopilación |
|:---|:---|:---|
| [Sistema operativo (invitado)](#operating-system-guest) | Datos sobre el sistema operativo en los recursos de proceso. | Instale el agente de Log Analytics para recopilar orígenes de datos de cliente en Azure Monitor y Dependency Agent para recopilar las dependencias que admitan VM Insights.<br>Para Azure Virtual Machines, instale la extensión de Azure Diagnostics para recopilar registros y métricas en Azure Monitor. |
| [Código de aplicación](#application-code) | Datos sobre el rendimiento y la funcionalidad de la aplicación y el código reales, incluidos los seguimientos de rendimiento, registros de aplicaciones y telemetría de usuario. | Instrumentar el código para recopilar datos en Application Insights. |
| [Orígenes personalizados](#custom-sources) | Datos de servicios externos u otros componentes o dispositivos. | Recopilar datos de registro o métricas en Azure Monitor desde cualquier cliente REST. |

## <a name="azure-tenant"></a>Inquilino de Azure
Los datos de telemetría relacionados con el inquilino de Azure se recopilan desde los servicios de todos los inquilinos, como Azure Active Directory.

![Recopilación del inquilino de Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Registros de auditoría de Azure Active Directory
Los [informes de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md), contienen el historial de actividad de inicio de sesión y la traza de auditoría de los cambios realizados en un inquilino determinado. 

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Configure registros de Azure AD para que se recopilen en Azure Monitor para analizarlos con otros datos de supervisión. | [Integración de registros de Azure AD con registros de Azure Monitor (versión preliminar)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exporte registros de Azure AD en Azure Storage para realizar el procedimiento de archivado. | [Tutorial: Archivado de registros de Azure AD en una cuenta de Azure Storage (versión preliminar)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centro de eventos | Transmita los registros de Azure AD a otras ubicaciones mediante Event Hubs. | [Tutorial: Streaming de los registros de Azure Active Directory a Azure Event Hubs (versión preliminar)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Suscripción de Azure
Telemetría relacionada con el estado y el funcionamiento de su suscripción de Azure.

![Suscripción de Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Registro de actividad de Azure 
El [registro de actividad de Azure](../essentials/platform-logs-overview.md) incluye registros de Service Health, además de registros sobre los cambios de configuración aplicados a los recursos en su suscripción de Azure. El registro de actividad está disponible para todos los recursos de Azure y representa su vista _externa_.

| Destination | Descripción | Referencia |
|:---|:---|
| Registro de actividades | El registro de actividad se recopila en su propio almacén de datos, que puede ver en el menú de Azure Monitor o utilizar para crear alertas de registro de actividad. | [Consulta del registro de actividad de Azure en Azure Portal](../essentials/activity-log.md#view-the-activity-log) |
| Registros de Azure Monitor | Configure los registros de Azure Monitor para que recopilen el registro de actividad para analizarlo con otros datos de supervisión. | [Recopilación y análisis de los registros de actividad de Azure en un área de trabajo de Log Analytics en Azure Monitor](../essentials/activity-log.md) |
| Azure Storage | Exporte el registro de actividad en Azure Storage para realizar el procedimiento de archivado. | [Archivo del registro de actividad](../essentials/resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Transmita el registros de actividad a otras ubicaciones mediante Event Hubs. | [Transmisión del registro de actividad al centro de eventos](../essentials/resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) proporciona información sobre el estado de los servicios de Azure de la suscripción de los que dependen la aplicación y los recursos.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registro de actividades<br>Registros de Azure Monitor | Los registros de Service Health se almacenan en el registro de actividad de Azure, por lo que puede verlos en Azure Portal o realizar otras actividades que permita el registro de actividad. | [Visualización de notificaciones de mantenimiento del servicio mediante Azure Portal](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Recursos de Azure
Los registros de métricas y recursos proporcionan información sobre el funcionamiento _interno_ de los recursos de Azure. Están disponibles para la mayoría de servicios de Azure, y las perspectivas y soluciones de administración proporcionan datos adicionales para determinados servicios.

![Colección de recursos de Azure](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Métricas de la plataforma 
La mayoría de los servicios de Azure generarán [métricas de plataforma](../essentials/data-platform-metrics.md) que reflejan su rendimiento y funcionamiento directamente en la base de datos de métricas. Las [métricas específicas varían en función del tipo de recurso](../essentials/metrics-supported.md). 

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Métricas de Azure Monitor | Las métricas de la plataforma se escribirán en la base de datos de métricas de Azure Monitor sin ninguna configuración. Acceda a las métricas de la plataforma del Explorador de métricas.  | [Introducción al Explorador de métricas de Azure](../essentials/metrics-getting-started.md)<br>[Métricas compatibles con Azure Monitor](../essentials/metrics-supported.md) |
| Registros de Azure Monitor | Copie las métricas de la plataforma en los registros para las tendencias y otros análisis con Log Analytics. | [Diagnósticos de Azure Diagnostics directos a Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Transmita métricas a otras ubicaciones mediante Event Hubs. |[Flujo de datos de supervisión de Azure a un centro de eventos para que lo consuma una herramienta externa](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Registros del recurso
Los [registros de recursos](../essentials/platform-logs-overview.md) proporcionan información detallada sobre el funcionamiento _interno_ de un recurso de Azure.  Los registros de recursos se crean automáticamente, pero debe crear una configuración de diagnóstico para especificar un destino para que se recopilen de cada recurso.

Los requisitos de configuración y el contenido de los registros de recursos varían según el tipo de recurso, y no todos los servicios los crean. Consulte [Servicios, esquemas y categorías admitidos en los registros de recursos de Azure](../essentials/resource-logs-schema.md) para más información sobre los servicios y obtener vínculos a los procedimientos de configuración detallados. Si el servicio no aparece en este artículo, significa que se servicio no crea actualmente registros de recursos.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Envíe registros de recursos a registros de Azure Monitor para el análisis con otros datos de registro recopilados. | [Recopilación de registros de recursos de Azure en el área de trabajo de Log Analytics en Azure Monitor](../essentials/resource-logs.md#send-to-azure-storage) |
| Storage | Envíe registros de recursos a Azure Storage para archivarlos. | [Archivado de registros de recursos de Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Transmita registros de recursos a otras ubicaciones mediante Event Hubs. |[Transmisión de registros de recursos de Azure a un centro de eventos](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Sistema operativo (invitado)
Los recursos de proceso en Azure, en otras nubes y en el entorno local tienen un sistema operativo invitado para supervisar. Con la instalación de uno o más agentes, puede recopilar datos de telemetría del invitado en Azure Monitor para analizarlos con las mismas herramientas de supervisión que los propios servicios de Azure.

![Colección de recursos de proceso de Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensión de diagnóstico de Azure
La habilitación de la extensión de Azure Diagnostics para Azure Virtual Machines le permite recopilar registros y métricas del sistema operativo invitado de recursos de proceso, incluidos los roles web y de trabajo de Azure Cloud Services (clásico), Virtual Machines, conjuntos de escalado de máquinas virtuales y Service Fabric.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Storage | La extensión Azure Diagnostics almacena los datos en una cuenta de Azure Storage. | [Instalación y configuración de la extensión Azure Diagnostics (WAD) para Windows](./diagnostics-extension-windows-install.md)<br>[Uso de la extensión Diagnostics de Linux para supervisar métricas y registros](../../virtual-machines/extensions/diagnostics-linux.md) |
| Métricas de Azure Monitor | Cuando configure la extensión de Diagnostics para recopilar los contadores de rendimiento, se escribirán en la base de datos de métricas de Azure Monitor. | [Envío de métricas de SO invitado al almacén de métricas de Azure Monitor con una plantilla de Resource Manager para una máquina virtual Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Configure la extensión de Diagnostics para transmitir los datos a otras ubicaciones mediante Event Hubs.  | [Transmisión de datos de Azure Diagnostics a Event Hubs](./diagnostics-extension-stream-event-hubs.md)<br>[Uso de la extensión Diagnostics de Linux para supervisar métricas y registros](../../virtual-machines/extensions/diagnostics-linux.md) |
| Registros de Application Insights | Recopile registros y contadores de rendimiento a partir del recurso de proceso que admite su aplicación para el análisis con otros datos de aplicación. | [Envío de datos de diagnóstico de Cloud Services, Virtual Machines o Service Fabric a Application Insights](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agente de Log Analytics 
Instale el agente de Log Analytics para la supervisión y administración completas de las máquinas virtuales de Windows y Linux. La máquina virtual se puede ejecutar en Azure, en otra nube o en el entorno local.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | El agente de Log Analytics se conecta a Azure Monitor directamente o a través de System Center Operations Manager y le permite recopilar datos desde orígenes de datos que configura o desde soluciones de supervisión que proporcionan perspectivas adicionales en aplicaciones que se ejecutan en la máquina virtual. | [Orígenes de datos de agente en Azure Monitor](../agents/agent-data-sources.md)<br>[Conexión de Operations Manager con Azure Monitor](./om-agents.md) |
| Almacenamiento de máquinas virtuales | VM Insights usa el agente de Log Analytics para almacenar la información de estado de mantenimiento en una ubicación personalizada. Consulte la siguiente sección para más información.  |


### <a name="vm-insights"></a>VM Insights 
[VM Insights](../vm/vminsights-overview.md) ofrece una experiencia de supervisión personalizada para máquinas virtuales que proporciona características más allá de la funcionalidad de Azure Monitor principal. Requiere Dependency Agent en las máquinas virtuales de Windows y Linux que se integra con el agente de Log Analytics para recopilar datos detectados acerca de los procesos que se ejecutan en la máquina virtual y en las dependencias de procesos externos.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Almacena los datos sobre los procesos y dependencias en el agente. | [Uso de la característica de asignación de VM Insights (versión preliminar) para comprender los componentes de la aplicación](../vm/vminsights-maps.md) |



## <a name="application-code"></a>Código de aplicación
Se realiza la supervisión detallada de la aplicación en Azure Monitor con [Application Insights](/azure/application-insights/), que recopila datos de aplicaciones que se ejecutan en una variedad de plataformas. La aplicación se puede ejecutar en Azure, en otra nube o en el entorno local.

![Recopilación de datos de aplicación](media/data-sources/applications.png)


### <a name="application-data"></a>Datos de aplicación
Cuando se habilita Application Insights para una aplicación mediante la instalación de un paquete de instrumentación, recopila métricas y registros relacionados con el rendimiento y el funcionamiento de la aplicación. Application Insights almacena los datos que recopila en la misma plataforma de datos de Azure Monitor que utilizan otros orígenes de datos. Incluye completas herramientas para analizar estos datos, pero también puede analizarlos con datos de otros orígenes mediante herramientas como el Explorador de métricas y Log Analytics.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Datos operativos sobre la aplicación, como vistas de página, las solicitudes de aplicación, excepciones y seguimientos. | [Análisis de datos de registro en Azure Monitor](../logs/log-query-overview.md) |
|                    | Información de dependencia entre los componentes de aplicación para admitir la asignación de aplicaciones y la correlación de telemetría. | [Correlación de telemetría en Application Insights](../app/correlation.md) <br> [Mapa de aplicación](../app/app-map.md) |
|            | Resultados de las pruebas de disponibilidad que permiten probar la disponibilidad y capacidad de respuesta de la aplicación desde diferentes ubicaciones de la red Internet pública. | [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](../app/monitor-web-app-availability.md) |
| Métricas de Azure Monitor | Application Insights recopila las métricas que describen el rendimiento y el funcionamiento de la aplicación, además de las métricas personalizadas que define en la aplicación en la base de datos de métricas de Azure Monitor. | [Métricas agregadas previamente y basadas en registros en Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API de Application Insights para eventos y métricas personalizados](../app/api-custom-events-metrics.md) |
| Azure Storage | Envíe datos de aplicación a Azure Storage para el procedimiento de archivado. | [Exportación de datos de telemetría desde Application Insights](../app/export-telemetry.md) |
|            | Los detalles de las pruebas de disponibilidad se almacenan en Azure Storage. Utilice Application Insights en Azure Portal para descargar los análisis locales. Los resultados de las pruebas de disponibilidad se almacenan en los registros de Azure Monitor. | [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](../app/monitor-web-app-availability.md) |
|            | Los datos de seguimiento del generador de perfiles se almacenan en Azure Storage. Utilice Application Insights en Azure Portal para descargar los análisis locales.  | [Generación de perfiles de aplicaciones de producción en Azure con Application Insights](../app/profiler-overview.md) 
|            | Los datos de instantáneas de depuración capturados para un subconjunto de excepciones se almacenan en Azure Storage. Utilice Application Insights en Azure Portal para descargar los análisis locales.  | [Funcionamiento de las instantáneas](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluciones de supervisión y perspectivas
Las [soluciones de supervisión](../insights/solutions.md) y [perspectivas](../monitor-reference.md) recopilan datos para proporcionar conclusiones sobre el funcionamiento de un servicio o aplicación determinados. Pueden abordar recursos en diferentes niveles de aplicación e incluso varios niveles.

### <a name="monitoring-solutions"></a>Soluciones de supervisión

| Destination | Descripción | Referencia
|:---|:---|:---|
| Registros de Azure Monitor | Las soluciones de supervisión recopilan datos en registros de Azure Monitor, donde se pueden analizar mediante un lenguaje de consulta o [vistas](../visualize/view-designer.md) que se suelen incluir en la solución. | [Detalles de la recopilación de datos para las soluciones de supervisión en Azure](../monitor-reference.md) |


### <a name="container-insights"></a>Container Insights
[Container Insights](../containers/container-insights-overview.md) proporciona una experiencia de supervisión personalizada para [Azure Kubernetes Service (AKS)](../../aks/index.yml). Recopila los datos adicionales sobre estos recursos que se describen en la tabla siguiente.

| Destination | Descripción | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Almacena datos de supervisión para AKS, como inventario, registros y eventos. Los datos de métrica también se almacenan en los registros para poder aprovechar su funcionalidad de análisis en el portal. | [Descripción del rendimiento del clúster de AKS con Container Insights](../containers/container-insights-analyze.md) |
| Métricas de Azure Monitor | Los datos de métricas se almacenan en la base de datos de métricas para favorecer la visualización y las alertas. | [Visualización de métricas del contenedor en el Explorador de métricas](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Proporciona acceso directo a los registros de contenedor de Azure Kubernetes Service (AKS) (stdout/stderror), eventos y métricas de pod en el portal. | [Visualización de los registros de Kubernetes, los eventos y las métricas de pods en tiempo real](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>VM Insights
[VM Insights](../vm/vminsights-overview.md) proporciona una experiencia personalizada para la supervisión de máquinas virtuales. Se incluye una descripción de los datos recopilados por VM Insights en la sección [Sistema operativo (invitado)](#operating-system-guest) anterior.

## <a name="custom-sources"></a>Orígenes personalizados
Además de los niveles estándar de una aplicación, puede que necesite supervisar otros recursos que tengan datos de telemetría y que no puedan recopilarse con los otros orígenes de datos. Para estos recursos, escriba estos datos en las métricas o los registros mediante la API de Azure Monitor.

![Colección personalizada](media/data-sources/custom.png)

| Destination | Método | Descripción | Referencia |
|:---|:---|:---|:---|
| Registros de Azure Monitor | API de recopilador de datos | Recopile datos de registro desde cualquier cliente REST y almacénelos en el área de trabajo de Log Analytics. | [Envío de datos de registro a Azure Monitor con HTTP Data Collector API (versión preliminar pública)](../logs/data-collector-api.md) |
| Métricas de Azure Monitor | API de métricas personalizadas | Recopile datos de métricas desde cualquier cliente REST y almacénelos en la base de datos de métricas de Azure Monitor. | [Envío de métricas personalizadas de un recurso de Azure al almacén de métricas de Azure Monitor mediante la API REST](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Otros servicios
Otros servicios de Azure escriben datos en la plataforma de datos de Azure Monitor. Esto le permite analizar los datos reunidos por estos servicios con los datos recopilados por Azure Monitor y aprovechar las mismas herramientas de visualización y análisis.

| Servicio | Destination | Descripción | Referencia |
|:---|:---|:---|:---|
| [Microsoft Defender for Cloud](../../security-center/index.yml) | Registros de Azure Monitor | Microsoft Defender for Cloud almacena los datos de seguridad que recopila en el área de trabajo de Log Analytics, que permite que estos se analicen con otros datos de registro recopilados por Azure Monitor.  | [Recopilación de datos en Microsoft Defender for Cloud](../../security-center/security-center-enable-data-collection.md) |
| [Microsoft Sentinel](../../sentinel/index.yml) | Registros de Azure Monitor | Microsoft Sentinel almacena los datos que recopila a partir de diferentes orígenes de datos en el área de trabajo de Log Analytics, que permite que estos se analicen con otros datos de registro recopilados por Azure Monitor.  | [Conexión con orígenes de datos](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [tipos de datos de supervisión recopilados por Azure Monitor](../data-platform.md) y cómo ver y analizar estos datos.
- Enumere las [distintas ubicaciones en las que los recursos de Azure almacenan datos](../monitor-reference.md) y cómo acceder a ellos.

---
title: 'Supervisión de máquinas virtuales con Azure Monitor: configuración de la supervisión'
description: Obtenga información sobre cómo configurar máquinas virtuales para la supervisión en Azure Monitor. Supervise las máquinas virtuales y sus cargas de trabajo con un escenario de Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: ce9fd7bd2b123877f73978caaabafca8c767c2be
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674337"
---
# <a name="monitor-virtual-machines-with-azure-monitor-configure-monitoring"></a>Supervisión de máquinas virtuales con Azure Monitor: configuración de la supervisión
Este artículo forma parte del escenario [Supervisión de máquinas virtuales y sus cargas de trabajo en Azure Monitor](monitor-virtual-machine.md). Se describe cómo configurar la supervisión de las máquinas virtuales híbridas y de Azure en Azure Monitor.

En este artículo, se analizan las características más comunes de Azure Monitor para supervisar el host de la máquina virtual y su sistema operativo invitado. Según el entorno y los requisitos empresariales específicos, es posible que no quiera implementar todas las características habilitadas por esta configuración. En cada sección, se describen las características habilitadas por esa configuración y si pueden conllevar un costo adicional. Esta información le ayudará a evaluar si va a realizar cada paso de la configuración. Para obtener información detallada sobre los precios, consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

En la [información general del escenario](monitor-virtual-machine.md), se proporciona una descripción general de cada característica habilitada por esta configuración. En ese artículo, también se incluyen vínculos al contenido que proporcionan una descripción detallada de cada característica para ayudarle a evaluar aún más sus requisitos.

> [!NOTE]
> Las características habilitadas por la configuración admiten la supervisión de las cargas de trabajo que se ejecutan en la máquina virtual. Sin embargo, en función de las cargas de trabajo particulares, normalmente necesitará una configuración adicional. Para más información sobre esta configuración adicional, consulte [Supervisión de máquinas virtuales con Azure Monitor: cargas de trabajo](monitor-virtual-machine-workloads.md).

## <a name="configuration-overview"></a>Información general sobre la configuración
En la tabla siguiente, se muestran los pasos que se deben llevar a cabo para esta configuración. Cada uno de ellos vincula a la sección con la descripción detallada de ese paso de configuración.

| Paso | Descripción |
|:---|:---|
| [Sin configuración](#no-configuration) | Las métricas del registro de actividad y de la plataforma de los hosts de máquina virtual de Azure se recopilan automáticamente sin configuración. |
| [Creación y preparación de un área de trabajo de Log Analytics](#create-and-prepare-a-log-analytics-workspace) | Cree un área de trabajo de Log Analytics y configúrela para VM Insights. En función de sus requisitos específicos, puede configurar varias áreas de trabajo. |
| [Envío de un registro de actividad a un área de trabajo de Log Analytics](#send-an-activity-log-to-a-log-analytics-workspace) | Envíe el registro de actividad al área de trabajo para analizarlo con otros datos de registro. |
| [Preparación de máquinas híbridas](#prepare-hybrid-machines) | Las máquinas híbridas necesitan que estén instalados los agentes del servidor habilitado por Azure Arc para que se puedan administrar como máquinas virtuales de Azure o deben tener sus agentes instalados manualmente. |
| [Habilitación de VM Insights en las máquinas](#enable-vm-insights-on-machines) | Incorpore máquinas a VM Insights, que implementa los agentes necesarios y comienza a recopilar datos del sistema operativo invitado. |
| [Envío de los datos de rendimiento del invitado a las métricas](#send-guest-performance-data-to-metrics) |Instale el agente de Azure Monitor para enviar los datos de rendimiento a las métricas de Azure Monitor. |

## <a name="no-configuration"></a>Sin configuración
Azure Monitor proporciona un nivel básico de supervisión para máquinas virtuales de Azure sin costo y sin configuración. Las métricas de la plataforma para máquinas virtuales de Azure incluyen métricas importantes, como el uso de CPU, red y disco. Se pueden ver en la [página de información general](monitor-virtual-machine-analyze.md#single-machine-experience) de la máquina en Azure Portal. También se recopila automáticamente el registro de actividad e incluye la actividad reciente de la máquina, como cualquier cambio de configuración y cuándo se detuvo e inició.

## <a name="create-and-prepare-a-log-analytics-workspace"></a>Creación y preparación de un área de trabajo de Log Analytics
Necesita al menos un área de trabajo de Log Analytics para admitir VM Insights y recopilar los datos de telemetría del agente de Log Analytics. No hay ningún costo para el área de trabajo, pero se incurre en costos de ingesta y retención al recopilar datos. Para más información, consulte [Administrar el uso y los costos con los registros de Azure Monitor](../logs/manage-cost-storage.md).

Muchos entornos usan un área de trabajo única para todas las máquinas virtuales y otros recursos de Azure que supervisan. Incluso puede compartir un área de trabajo usada por [Azure Security Center y Azure Sentinel](monitor-virtual-machine-security.md), aunque muchos clientes deciden separar los datos de disponibilidad y telemetría del rendimiento de los datos de seguridad. Si está empezando a trabajar con Azure Monitor, comience con una sola área de trabajo y considere la posibilidad de crear más áreas de trabajo a medida que evolucionen los requisitos.

Para obtener detalles completos sobre la lógica que debe tener en cuenta para diseñar una configuración de área de trabajo, consulte [Diseño de la implementación de registros de Azure Monitor](../logs/design-logs-deployment.md).

### <a name="multihoming-agents"></a>Agentes de hospedaje múltiple
El hospedaje múltiple hace referencia a una máquina virtual que se conecta a varias áreas de trabajo. Normalmente, hay pocas razones para el hospedaje múltiple de los agentes de Azure Monitor por sí solos. Hacer que un agente envíe datos a varias áreas de trabajo probablemente crea datos duplicados en cada área de trabajo, lo que aumenta el costo general. Puede combinar datos de varias áreas de trabajo mediante [consultas entre áreas de trabajo](../logs/cross-workspace-query.md) y [libros](../visualizations/../visualize/workbooks-overview.md).

Sin embargo, se produce una razón por la que podría considerar el hospedaje múltiple cuando tiene un entorno con Azure Security Center o Azure Sentinel almacenado en un área de trabajo independiente de Azure Monitor. Una máquina supervisada por cada servicio debe enviar datos a cada área de trabajo. El agente de Windows admite este escenario porque puede enviar datos hasta a cuatro áreas de trabajo. El agente de Linux solo puede enviar datos a una única área de trabajo. Si desea que Azure Monitor y Azure Security Center o Azure Sentinel supervisen un conjunto común de máquinas Linux, los servicios deben compartir la misma área de trabajo.

Otra razón por la que podría realizar el hospedaje múltiple de los agentes se produce si usa un [modelo de supervisión híbrido](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring). En este modelo, se usan Azure Monitor y Operations Manager para supervisar las mismas máquinas. El agente de Log Analytics y el agente de administración de Microsoft para Operations Manager son el mismo agente. A veces se hace referencia a él con nombres diferentes.

### <a name="workspace-permissions"></a>Permisos de área de trabajo
El modo de acceso del área de trabajo define qué usuarios pueden acceder a diferentes conjuntos de datos. Para más información sobre cómo definir el modo de acceso y configurar los permisos, consulte [Administración del acceso a los datos de registro y las áreas de trabajo en Azure Monitor](../logs/manage-access.md). Si acaba de empezar a trabajar con Azure Monitor, considere la posibilidad de aceptar los valores predeterminados al crear el área de trabajo y configurar los permisos más adelante.

### <a name="prepare-the-workspace-for-vm-insights"></a>Preparación del área de trabajo para VM Insights
Prepare cada área de trabajo para VM Insights antes de habilitar la supervisión de las máquinas virtuales. En este paso, se instalan las soluciones necesarias que admiten la recopilación de datos del agente de Log Analytics. Esta configuración solo se completa una vez para cada área de trabajo. Para más información sobre esta configuración mediante Azure Portal, además de otros métodos, consulte [Información general sobre la habilitación de VM Insights](vminsights-enable-overview.md).

## <a name="send-an-activity-log-to-a-log-analytics-workspace"></a>Envío de un registro de actividad a un área de trabajo de Log Analytics
Puede ver las métricas de la plataforma y el registro de actividad recopilados para cada máquina virtual anfitriona en Azure Portal. Envíe estos datos a la misma área de trabajo de Log Analytics que VM Insights para analizarlos con los demás datos de supervisión recopilados sobre la máquina virtual. Es posible que ya haya realizado esta tarea al configurar la supervisión de otros recursos de Azure porque hay un único registro de actividad para todos los recursos de una suscripción de Azure.

No hay ningún costo por la ingesta o retención de datos del registro de actividad. Para más información sobre cómo crear una configuración de diagnóstico para enviar el registro de actividad al área de trabajo de Log Analytics, consulte [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../essentials/diagnostic-settings.md).

### <a name="network-requirements"></a>Requisitos de red
El agente de Log Analytics para Linux y Windows lleva a cabo la comunicación de salida con el servicio Azure Monitor mediante el puerto TCP 443. El agente de dependencias usa el agente de Log Analytics para todas las comunicaciones, por lo que no requiere ningún otro puerto. Para más información sobre cómo configurar el firewall y el servidor proxy, consulte [Requisitos de red](../agents/log-analytics-agent.md#network-requirements).

:::image type="content" source="media/monitor-virtual-machines/network-diagram.png" alt-text="Diagrama que muestra la red." lightbox="media/monitor-virtual-machines/network-diagram.png":::

### <a name="gateway"></a>Puerta de enlace
Con la puerta de enlace de Log Analytics, puede canalizar las comunicaciones desde las máquinas locales a través de una única puerta de enlace. Sin embargo, no puede usar los agentes del servidor habilitado para Azure Arc con la puerta de enlace de Log Analytics. Si la directiva de seguridad requiere una puerta de enlace, deberá instalar manualmente los agentes para las máquinas locales. Para más información sobre cómo configurar y usar la puerta de enlace de Log Analytics, consulte [Conexión de equipos sin acceso a Internet mediante la puerta de enlace de Log Analytics en Azure Monitor](../agents/gateway.md).

### <a name="azure-private-link"></a>Azure Private Link
Mediante Azure Private Link, puede crear un punto de conexión privado para el área de trabajo de Log Analytics. Una vez configurado, las conexiones al área de trabajo se deben realizar mediante este punto de conexión privado. Private Link funciona mediante invalidaciones de DNS, por lo que no hay ningún requisito de configuración en los agentes individuales. Para más información sobre Private Link, consulte [Uso de Azure Private Link para conectar redes a Azure Monitor de forma segura](../logs/private-link-security.md).

## <a name="prepare-hybrid-machines"></a>Preparación de máquinas híbridas
Una máquina híbrida es cualquier máquina que no se ejecute en Azure. Es una máquina virtual que se ejecuta o está hospedada en otra nube, o una máquina virtual o física que se ejecuta localmente en el centro de datos. Use [servidores habilitados para Azure Arc](../../azure-arc/servers/overview.md) en las máquinas híbridas para que pueda administrarlas de forma similar a las máquinas virtuales de Azure. Puede usar VM Insights en Azure Monitor para usar el mismo proceso para habilitar la supervisión de servidores habilitados para Azure Arc del mismo modo que para las máquinas virtuales de Azure. Para obtener una guía completa sobre cómo preparar las máquinas híbridas para Azure, consulte [Planeación e implementación de servidores habilitados para Azure Arc](../../azure-arc/servers/plan-at-scale-deployment.md). Esta tarea incluye habilitar las máquinas individuales y usar [Azure Policy](../../governance/policy/overview.md) para habilitar todo el entorno híbrido a gran escala.

No hay ningún costo más para los servidores habilitados para Azure Arc, pero puede haber algún costo para las distintas opciones que habilite. Para más información, consulte [Precios de Azure Arc](https://azure.microsoft.com/pricing/details/azure-arc/). Hay un costo por los datos recopilados en el área de trabajo después de que las máquinas híbridas estén habilitadas para VM Insights.

### <a name="machines-that-cant-use-azure-arcenabled-servers"></a>Máquinas que no pueden usar servidores habilitados para Azure Arc
Si tiene máquinas híbridas que cumplan los siguientes criterios, no podrán usar servidores habilitados para Azure Arc: 

- El sistema operativo de la máquina no es compatible con los agentes del servidor habilitado para Azure Arc. Para más información, consulte [Sistemas operativos admitidos](../../azure-arc/servers/agent-overview.md#prerequisites).
- La directiva de seguridad no permite que las máquinas se conecten directamente a Azure. El agente de Log Analytics puede usar la [puerta de enlace de Log Analytics](../agents/gateway.md) independientemente de si los servidores habilitados para Azure Arc están instalados o no. Los agentes del servidor habilitado para Azure Arc se deben conectar directamente a Azure.

Puede supervisar estas máquinas con Azure Monitor, pero debe instalar manualmente sus agentes. Para instalar manualmente el agente de Log Analytics y el agente de dependencias en esas máquinas híbridas, consulte [Habilitación de VM Insights para una máquina virtual híbrida](vminsights-enable-hybrid.md).

> [!NOTE]
> El punto de conexión privado para los servidores habilitados para Azure Arc está actualmente en versión preliminar pública. El punto de conexión permite que las máquinas híbridas se conecten de forma segura a Azure mediante una dirección IP privada de la red virtual.

## <a name="enable-vm-insights-on-machines"></a>Habilitación de VM Insights en las máquinas
Después de habilitar VM Insights en una máquina, este instala el agente de Log Analytics y el agente de dependencias, se conecta a un área de trabajo y comienza a recopilar datos de rendimiento. Puede empezar a usar vistas y libros de rendimiento para analizar las tendencias de diversas métricas del sistema operativo invitado, habilitar la característica de mapa de VM Insights para analizar los procesos en ejecución y las dependencias entre las máquinas y recopilar los datos necesarios para crear una variedad de reglas de alertas.

Puede habilitar VM Insights en las máquinas individuales mediante los mismos métodos que para las máquinas virtuales de Azure y los servidores habilitados para Azure Arc. Estos métodos incluyen la incorporación de máquinas individuales con Azure Portal o plantillas de Azure Resource Manager, o la habilitación de máquinas a gran escala mediante Azure Policy. No hay ningún costo directo para VM Insights, pero hay un costo por la ingesta y retención de los datos recopilados en el área de trabajo de Log Analytics.

Para conocer las distintas opciones para habilitar VM Insights para las máquinas, consulte [Información general sobre la habilitación de VM Insights](vminsights-enable-overview.md). Para crear una directiva que habilite automáticamente VM Insights en las máquinas nuevas a medida que se crean, consulte [Habilitación de VM Insights mediante Azure Policy](vminsights-enable-policy.md).


## <a name="send-guest-performance-data-to-metrics"></a>Envío de los datos de rendimiento del invitado a las métricas
El [agente de Azure Monitor](../agents/azure-monitor-agent-overview.md) reemplaza al agente de Log Analytics cuando es totalmente compatible con Azure Monitor, Azure Security Center y Azure Sentinel. Hasta ese momento, se puede instalar con el agente de Log Analytics para enviar datos de rendimiento desde el sistema operativo invitado de las máquinas a las métricas de Azure Monitor. Esta configuración le permite evaluar estos datos con el explorador de métricas y usar alertas de métricas.

El agente de Azure Monitor requiere al menos una regla de recopilación de datos (DCR) que defina qué datos debe recopilar y dónde debe enviarlos. Cualquier máquina del mismo grupo de recursos puede usar una única DCR.

Cree una única DCR para cada grupo de recursos con máquinas que se van a supervisar mediante el siguiente origen de datos: 

- **Tipo de origen de datos**: contadores de rendimiento
- **Destino**: métricas de Azure Monitor

Tenga cuidado de no enviar datos a los registros porque serían redundantes con los datos ya recopilados por el agente de Log Analytics.

Puede instalar un agente de Azure Monitor en las máquinas individuales mediante los mismos métodos que para las máquinas virtuales de Azure y los servidores habilitados para Azure Arc. Estos métodos incluyen la incorporación de máquinas individuales con Azure Portal o plantillas de Resource Manager, o la habilitación de máquinas a gran escala mediante Azure Policy. En el caso de las máquinas híbridas que no puedan usar servidores habilitados para Azure Arc, instale el agente manualmente.

Para crear una DCR e implementar el agente de Azure Monitor en uno o varios agentes mediante Azure Portal, consulte [Configuración de la recopilación de datos para el agente de Azure Monitor (versión preliminar)](../agents/data-collection-rule-azure-monitor-agent.md). Se describen otros métodos de instalación en [Instalación del agente de Azure Monitor (versión preliminar)](../agents/azure-monitor-agent-install.md). Para crear una directiva que implemente automáticamente el agente y la DCR en las máquinas nuevas a medida que se crean, consulte [Implementación de Azure Monitor a gran escala mediante Azure Policy](../deploy-scale.md#azure-monitor-agent).

## <a name="next-steps"></a>Pasos siguientes

* [Supervisión de máquinas virtuales con Azure Monitor: análisis de los datos de supervisión](monitor-virtual-machine-analyze.md)
* [Supervisión de máquinas virtuales con Azure Monitor: alertas](monitor-virtual-machine-alerts.md)
* [Supervisión de máquinas virtuales con Azure Monitor: cargas de trabajo](monitor-virtual-machine-workloads.md)
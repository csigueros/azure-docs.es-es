---
title: Supervisión de máquinas virtuales con Azure Monitor
description: Obtenga información sobre cómo usar Azure Monitor para supervisar el mantenimiento y el rendimiento de máquinas virtuales y sus cargas de trabajo.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/02/2021
ms.openlocfilehash: 44efb85ae2101ee1f35bd82b739e87103ad228bd
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681355"
---
# <a name="monitor-virtual-machines-with-azure-monitor"></a>Supervisión de máquinas virtuales con Azure Monitor
En este escenario se describe cómo usar Azure Monitor para supervisar el estado y el rendimiento de máquinas virtuales y sus cargas de trabajo. Incluye la recopilación de datos de telemetría críticos para la supervisión, el análisis y la visualización de los datos recopilados para identificar tendencias y cómo configurar alertas para recibir notificaciones proactivas de problemas críticos.

En este artículo, se presenta el escenario y se proporcionan conceptos generales para supervisar máquinas virtuales en Azure Monitor. Si quiere ir directamente a un área específica, consulte uno de los otros artículos que forman parte de este escenario descrito en la tabla siguiente.

| Artículo | Descripción |
|:---|:---|
| [Habilitar supervisión](monitor-virtual-machine-configure.md) | Configure Azure Monitor para supervisar máquinas virtuales, lo que incluye la habilitación de VM Insights y de cada máquina virtual para la supervisión.  |
| [Analizar](monitor-virtual-machine-analyze.md) | Analice los datos de supervisión que ha recopilado Azure Monitor de máquinas virtuales y sus aplicaciones y sistemas operativos invitados para identificar tendencias e información crítica. |
| [Alertas](monitor-virtual-machine-alerts.md)   | Cree alertas para identificar de forma proactiva los problemas críticos en los datos de supervisión. |
| [Supervisión de la seguridad](monitor-virtual-machine-security.md) | Descubra los servicios de Azure para supervisar la seguridad de las máquinas virtuales. |
| [Supervisión de cargas de trabajo](monitor-virtual-machine-workloads.md) | Supervise las aplicaciones y otras cargas de trabajo que se ejecutan en las máquinas virtuales. |

> [!IMPORTANT]
> Este escenario no incluye características que no están disponibles con carácter general. Las características de la versión preliminar pública, como el [estado de los invitados de la máquina virtual](vminsights-health-overview.md), pueden modificar significativamente las recomendaciones realizadas aquí. El escenario se actualizará a medida que las características en versión preliminar pasen a la disponibilidad general.

## <a name="types-of-machines"></a>Tipos de máquinas
En este escenario, se incluye la supervisión de los siguientes tipos de máquinas mediante Azure Monitor. Muchos de los procesos descritos aquí son los mismos, independientemente del tipo de máquina. Las consideraciones para los distintos tipos de máquinas se identifican claramente cuando corresponde. Entre los tipos de medidas, se incluyen los siguientes: 

- Máquinas virtuales de Azure.
- Conjuntos de escalado de máquinas virtuales.
- Máquinas híbridas, que son máquinas virtuales que se ejecutan en otras nubes, con un proveedor de servicios administrados o de forma local. También se incluyen máquinas físicas que se ejecutan en el entorno local.

## <a name="layers-of-monitoring"></a>Capas de supervisión
Básicamente, hay cuatro capas en una máquina virtual que requieren supervisión. Cada capa tiene un conjunto distinto de requisitos de telemetría y supervisión. 

| Nivel | Descripción |
|:---|:---|
| Máquina virtual anfitriona | La máquina virtual del host en Azure. Azure Monitor no tiene acceso al host en otras nubes, pero debe basarse en la información recopilada del sistema operativo invitado. El host puede ser útil para realizar el seguimiento de la actividad, como los cambios de configuración, pero normalmente no se usa para alertas significativas. |
| Sistema operativo invitado | El sistema operativo que se ejecuta en la máquina virtual, que es una versión de Windows o Linux. Hay una cantidad significativa de datos de supervisión disponibles del sistema operativo invitado, como eventos y datos de rendimiento. VM Insights en Azure Monitor proporciona una cantidad significativa de lógica para supervisar el estado y el rendimiento del sistema operativo invitado. |
| Cargas de trabajo | Cargas de trabajo que se ejecutan en el sistema operativo invitado que admiten las aplicaciones empresariales. Azure Monitor proporciona supervisión predefinida para algunas cargas de trabajo. Normalmente, debe configurar la recopilación de datos y las alertas de otras cargas de trabajo mediante el uso de los datos de supervisión que generan. |
| Application | La aplicación empresarial que depende de las máquinas virtuales se puede supervisar mediante [Application Insights](../app/app-insights-overview.md). 

:::image type="content" source="media/monitor-virtual-machines/monitoring-layers.png" alt-text="Diagrama en el que se muestran las capas de supervisión." lightbox="media/monitor-virtual-machines/monitoring-layers.png":::

## <a name="vm-insights"></a>VM Insights
Este escenario se centra en [VM Insights](../vm/vminsights-overview.md), que es la característica principal de Azure Monitor para supervisar máquinas virtuales. VM insights ofrece las siguientes características:

- Incorporación simplificada de agentes para habilitar la supervisión del sistema operativo invitado y las cargas de trabajo de una máquina virtual. 
- Libros y gráficos de rendimiento de tendencias predefinidos que permiten analizar las métricas de rendimiento básicas del sistema operativo invitado de la máquina virtual.
- Mapa de dependencias que muestra los procesos que se ejecutan en cada máquina virtual y los componentes interconectados con otras máquinas y orígenes externos.

## <a name="agents"></a>Agentes
Cualquier herramienta de supervisión, como Azure Monitor, requiere que un agente esté instalado en una máquina para recopilar datos de su sistema operativo invitado. Azure Monitor tiene actualmente varios agentes que recopilan datos diferentes, envían datos a diferentes ubicaciones y admiten características diferentes. VM Insights administra la implementación y configuración de los agentes que la mayoría de los clientes usarán. En la tabla siguiente, se describen distintos agentes por si necesita los escenarios concretos que admiten. Consulte [Introducción a los agentes de Azure Monitor](../agents/agents-overview.md) para ver una comparativa detallada de los diferentes agentes.

> [!NOTE]
> El agente de Azure Monitor reemplazará completamente al agente de Log Analytics, la extensión de diagnóstico y el agente de Telegraf una vez que obtenga la funcionalidad necesaria. Estos otros agentes siguen siendo necesarios para características como VM Insights, Azure Security Center y Azure Sentinel.

- [Agente de Azure Monitor](../agents/agents-overview.md#azure-monitor-agent): admite máquinas virtuales en Azure, otros entornos de nube y el entorno local. Envía datos a las métricas y registros de Azure Monitor. Cuando admita completamente VM Insights, Azure Security Center y Azure Sentinel, reemplazará por completo al agente de Log Analytics y la extensión de diagnóstico.
- [Agente de Log Analytics](../agents/agents-overview.md#log-analytics-agent): admite máquinas virtuales en Azure, otros entornos de nube y el entorno local. Envía datos a los registros de Azure Monitor. Admite soluciones de supervisión y VM Insights. Este es el mismo agente que se usa para System Center Operations Manager.
- [Dependency Agent](../agents/agents-overview.md#dependency-agent): recopila datos sobre los procesos que se ejecutan en la máquina virtual y sus dependencias. Se basa en el agente de Log Analytics para transmitir datos a Azure y admite VM Insights, Service Map y soluciones de Wire Data 2.0.
- [Extensión de diagnóstico de Azure](../agents/agents-overview.md#azure-diagnostics-extension): disponible solo para máquinas virtuales Azure Monitor. Puede enviar datos a Azure Event Hubs y Azure Storage.

## <a name="next-steps"></a>Pasos siguientes

[Análisis de los datos de supervisión recopilados para las máquinas virtuales](monitor-virtual-machine-analyze.md)

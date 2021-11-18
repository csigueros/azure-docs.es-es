---
title: Supervisión de los recursos de Azure con Azure Monitor | Microsoft Docs
description: Describe cómo recopilar y analizar los datos de supervisión de los recursos de Azure mediante Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/15/2021
ms.openlocfilehash: f93cd688ef22d7765edbd50e742262e9febec14c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297866"
---
# <a name="tutorial-monitor-azure-resources-with-azure-monitor"></a>Tutorial: Supervisión de los recursos de Azure con Azure Monitor
Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. Esa supervisión es posible con Azure Monitor, un servicio de supervisión de la pila completa en Azure que proporciona un conjunto completo de características para supervisar tanto los recursos de Azure como los recursos que tenga en el entorno local y en otras nubes.

En este tutorial, aprenderá:

> [!div class="checklist"]
> * Qué es Azure Monitor y cómo se integra en el portal para otros servicios de Azure.
> * Tipos de datos que recopila Azure Monitor para los recursos de Azure.
> * Herramientas de Azure Monitor que se usan para recopilar y analizar datos.

> [!NOTE]
> En este tutorial, se explican los conceptos relacionados con Azure Monitor y se hace un recorrido por diferentes elementos de menú. Para pasar a usar directamente las características de Azure Monitor, empiece con el [Tutorial: Análisis de las métricas de un recurso de Azure](../essentials/tutorial-metrics.md).


## <a name="monitoring-data"></a>Supervisión de datos

### <a name="azure-monitor-data-collection"></a>Recopilación de datos con Azure Monitor
En cuanto se crea un recurso de Azure, se habilita Azure Monitor y empieza a recopilar métricas y registros de actividad. Con algunas opciones de configuración, puede recopilar datos de supervisión adicionales y habilitar características adicionales. La plataforma de datos de Azure Monitor se compone de métricas y registros. Cada uno recopila diferentes tipos de datos y habilita diferentes características de Azure Monitor.

- Las [métricas de Azure Monitor](../essentials/data-platform-metrics.md) almacenan datos numéricos de los recursos supervisados en una base de datos de serie temporal. La base de datos de métricas se crea automáticamente para cada suscripción de Azure. Use el [Explorador de métricas](../essentials/tutorial-metrics.md) para analizar los datos de los registros de Azure Monitor.
- Los [registros de Azure Monitor](../logs/data-platform-logs.md) recopilan registros y datos de rendimiento donde pueden recuperarse y analizarse de diferentes formas mediante consultas de registro. Debe crear un área de trabajo de Log Analytics para recopilar los datos de registro. Use [Log Analytics](../logs/log-analytics-tutorial.md) para analizar los datos de los registros de Azure Monitor.

### <a name="monitoring-data-from-azure-resources"></a>Supervisión de datos de los recursos de Azure
Aunque los recursos de diferentes servicios de Azure tienen requisitos de supervisión diferentes, generan datos de supervisión con los mismos formatos con el fin de que pueda usar las mismas herramientas de Azure Monitor para analizar todos los recursos de Azure.

Los recursos de Azure generan los siguientes datos de supervisión:

- [Registro de actividad](./platform-logs-overview.md): eventos a nivel de suscripción que realizan un seguimiento de las operaciones de cada recurso de Azure; por ejemplo, la creación de un nuevo recurso o el inicio de una máquina virtual. Los eventos de registro de actividad se generan y recopilan automáticamente para verlos en Azure Portal. Puede crear una configuración de diagnóstico para enviar el registro de actividad a los registros de Azure Monitor.
- [Métricas de plataforma](../essentials/data-platform-metrics.md): valores numéricos que se recopilan automáticamente a intervalos regulares y describen algún aspecto de un recurso en un momento determinado. Las métricas de plataforma se generan y recopilan automáticamente en las métricas de Azure Monitor.
- [Registros de recurso](./platform-logs-overview.md): proporcionan información de las operaciones realizadas por un recurso de Azure; por ejemplo, la obtención de un secreto de un almacén de claves o el envío de una solicitud a una base de datos. Los registros de recurso se generan automáticamente, pero debe crear una configuración de diagnóstico para enviarlos a los registros de Azure Monitor.
- [Registros y métricas de invitado de máquina virtual](): datos de rendimiento y registro del sistema operativo invitado de máquinas virtuales de Azure. Debe instalar un agente en la máquina virtual para recopilar estos datos y enviarlos a las métricas y los registros de Azure Monitor.


## <a name="menu-options"></a>Opciones del menú
Aunque se puede acceder a las características de Azure Monitor desde el menú **Supervisión** de Azure Portal, también se puede acceder a ellas directamente desde el menú de diferentes servicios de Azure. Si bien la experiencia puede ser ligeramente diferente en los distintos servicios de Azure, todos comparten un conjunto común de opciones de supervisión en Azure Portal. Este conjunto incluye las opciones **Información general** y **Registro de actividad**, junto con varias opciones en la sección **Supervisión** del menú. 

:::image type="content" source="media/monitor-azure-resource/menu-01.png" lightbox="media/monitor-azure-resource/menu-01.png" alt-text="Menú Supervisión 1":::

:::image type="content" source="media/monitor-azure-resource/menu-02.png" lightbox="media/monitor-azure-resource/menu-02.png" alt-text="Menú Supervisión 2":::


## <a name="overview-page"></a>Página de información general
La página **Información general** incluye detalles sobre el recurso y, a menudo, su estado actual. Por ejemplo, una máquina virtual muestra su estado de ejecución actual. Muchos servicios de Azure tienen una pestaña **Supervisión** que incluye gráficos para una serie de métricas clave. Es una forma rápida de ver el funcionamiento del recurso. Además, puede hacer clic en cualquiera de los gráficos para abrirlo en el Explorador de métricas y ver un análisis más detallado. 

Consulte el [Tutorial: Análisis de las métricas de un recurso de Azure](../essentials/tutorial-metrics.md), donde se explica el uso del Explorador de métricas.

![Página de información general](media/monitor-azure-resource/overview-page.png)
### <a name="activity-log"></a>Registro de actividades 
El elemento de menú **Registro de actividad** permite ver las entradas del [registro de actividad](../essentials/activity-log.md) del recurso actual. 

:::image type="content" source="media/monitor-azure-resource/activity-log.png" lightbox="media/monitor-azure-resource/activity-log.png" alt-text="Registro de actividad":::

## <a name="alerts"></a>Alertas
La página **Alertas** muestra las alertas que se han desencadenado recientemente para el recurso. Las alertas lo avisan de forma proactiva cuando se encuentra algo importante en los datos de supervisión y pueden usar datos de las métricas o de los registros.

Consulte el [Tutorial: Creación de una alerta de métrica para un recurso de Azure](../alerts/tutorial-metric-alert.md) o el [Tutorial: Creación de una alerta de consulta de registro para un recurso de Azure](../alerts/tutorial-log-alert.md) para saber cómo crear reglas de alertas y cómo ver las alertas.

:::image type="content" source="media/monitor-azure-resource/alerts-view.png" lightbox="media/monitor-azure-resource/alerts-view.png" alt-text="Vista Alertas":::

## <a name="metrics"></a>Métricas
El elemento de menú **Métricas** abre el [Explorador de métricas](./metrics-getting-started.md), que permite trabajar con métricas individuales o combinar varias para identificar correlaciones y tendencias. Este es el mismo Explorador de métricas que se abre al hacer clic en alguno de los gráficos de la página **Información general**.

Consulte el [Tutorial: Análisis de las métricas de un recurso de Azure](../essentials/tutorial-metrics.md), donde se explica el uso del Explorador de métricas.

:::image type="content" source="media/monitor-azure-resource/metrics.png" lightbox="media/monitor-azure-resource/metrics.png" alt-text="Explorador de métricas":::


## <a name="diagnostic-settings"></a>Configuración de diagnóstico
La página **Configuración de diagnóstico** permite crear una [configuración de diagnóstico](../essentials/diagnostic-settings.md) para recopilar los registros de un recurso. Puede enviarlos a varios sitios, pero lo más habitual es enviarlos a un área de trabajo de Log Analytics para poder analizarlos con esta característica.

Consulte el [Tutorial: Recopilación y análisis de registros de recurso desde un recurso de Azure](../essentials/tutorial-resource-logs.md) para saber cómo crear una configuración de diagnóstico.

:::image type="content" source="media/monitor-azure-resource/diagnostic-settings.png" lightbox="media/monitor-azure-resource/diagnostic-settings.png" alt-text="Configuración de diagnóstico":::



## <a name="insights"></a>Información detallada 
El elemento de menú **Insights** muestra la información del recurso si el servicio de Azure tiene uno. [Insights](../monitor-reference.md) proporciona una experiencia de supervisión personalizada basada en la plataforma de datos de Azure Monitor y las características estándar. 


Consulte [Insights y visualizaciones mantenidas](../monitor-reference.md#insights-and-curated-visualizations) para obtener una lista de la información disponible y vínculos a la documentación correspondiente.

:::image type="content" source="media/monitor-azure-resource/insights.png" lightbox="media/monitor-azure-resource/insights.png" alt-text="Captura de pantalla de Insights":::

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un conocimiento básico de Azure Monitor, empiece a analizar algunas métricas de un recurso de Azure.

> [!div class="nextstepaction"]
> [Analizar métricas de un recurso de Azure](../essentials/tutorial-metrics.md)

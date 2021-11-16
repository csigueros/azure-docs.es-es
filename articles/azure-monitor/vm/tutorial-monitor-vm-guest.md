---
title: 'Tutorial: Recopilación de registros y métricas de invitado de una máquina virtual de Azure'
description: Cree una regla de recopilación de datos para recopilar registros y métricas de invitado de la máquina virtual de Azure.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/08/2021
ms.openlocfilehash: 5bd3b557bf7bc9ada4e75979e593c7fb0c338dd4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349938"
---
# <a name="tutorial-collect-guest-logs-and-metrics-from-azure-virtual-machine"></a>Tutorial: Recopilación de registros y métricas de invitado de una máquina virtual de Azure
Al [habilitar la supervisión con información de máquina virtual](tutorial-monitor-vm-enable.md), recopila datos de rendimiento mediante el agente de Log Analytics. Para recopilar registros del sistema operativo invitado y enviar datos de rendimiento a métricas de Azure Monitor, instale el [agente de Azure Monitor](../agents/azure-monitor-agent-overview.md) y cree una [regla de recopilación de datos](../agents/data-collection-rule-overview.md) (DCR) que defina los datos que se van a recopilar y dónde enviarlos. 

> [!NOTE]
> Antes del agente de Azure Monitor, las métricas de invitado de las máquinas virtuales de Azure se recopilaban con la [extensión de diagnóstico de Azure](../agents/diagnostics-extension-overview.md) para Windows (WAD) y Linux (LAD). Estos agentes siguen estando disponibles y se pueden configurar con el elemento de menú **Configuración de diagnóstico** de la máquina virtual, pero están en proceso de reemplazarse por el agente de Azure Monitor.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear una regla de recopilación de datos que envíe datos de rendimiento de invitado a métricas de Azure Monitor y registrar eventos en registros de Azure Monitor. 
> * Ver las métricas de invitado en el Explorador de métricas.
> * Ver los registros de invitado en Log Analytics.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente: 

- Una máquina virtual de Azure para supervisar.


## <a name="create-data-collection-rule"></a>Crear una regla de recopilación de datos.
Las [reglas de recopilación de datos](../agents/data-collection-rule-overview.md) en Azure Monitor definen los datos que se van a recopilar y dónde se deben enviar. Al definir la regla de recopilación de datos mediante Azure Portal, debe especificar las máquinas virtuales a las que se debe aplicar. El agente de Azure Monitor se instalará en las máquinas virtuales que todavía no lo tengan.

> [!NOTE]
> Actualmente debe instalar el agente de Azure Monitor desde el menú **Monitor** de Azure Portal. Esta función aún no está disponible en el menú de la máquina virtual. 

En el menú **Monitor** de Azure Portal, seleccione **Data Collection Rules** (Reglas de recopilación de datos) y luego **Crear** para crear una regla de recopilación de datos.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-create.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-create.png" alt-text="Creación de una regla de recopilación de datos":::

En la pestaña **Aspectos básicos**, proporcione un **Nombre de regla**, que es el nombre de la regla que se muestra en Azure Portal. Seleccione una **Suscripción**, un **Grupo de recursos** y una **Región** donde se almacenarán la regla de recopilación de datos y sus asociaciones. No es necesario que sean iguales que los recursos que se supervisan. El **Tipo de plataforma** define las opciones que están disponibles a medida que define el resto de la regla de recopilación de datos. Seleccione *Windows* o *Linux* si solo se asociarán esos recursos a una de ellas o *Personalizado* si se asociará a ambos tipos.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-basics.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-basics.png" alt-text="Aspectos básicos de la regla de recopilación de datos":::

## <a name="select-resources"></a>Selección de recursos
En la pestaña **Recursos**, identifique una o varias máquinas virtuales a las que se aplicará la regla de recopilación de datos. El agente de Azure Monitor se instalará en cualquiera que todavía no lo tenga. Haga clic en **Agregar recursos** y seleccione las máquinas virtuales, el grupo de recursos o la suscripción donde se encuentra la máquina virtual. La regla de recopilación de datos se aplicará a todas las máquinas virtuales del ámbito seleccionado.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-resources.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-resources.png" alt-text="Recursos de la regla de recopilación de datos":::

## <a name="select-data-sources"></a>Selección de orígenes de datos
Una única regla de recopilación de datos puede tener varios orígenes de datos. En este tutorial, usaremos la misma regla para recopilar métricas y registros de invitado. Se enviarán métricas tanto a Métricas de Azure Monitor como a Registros de Azure Monitor para que se puedan analizar con el Explorador de métricas y con Log Analytics.

En la pestaña **Recopilar y entregar**, haga clic en **Agregar origen de datos**. En **Tipo de origen de datos**, seleccione **Contadores de rendimiento**. Deje el valor **Básico** y seleccione los contadores que quiere recopilar. La opción **Personalizado** permite seleccionar valores de métricas individuales. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" alt-text="Origen de datos de métrica de la regla de recopilación de datos":::

Seleccione la pestaña **Destino**. **Métricas de Azure Monitor** ya debería aparecer. Haga clic en **Agregar destino** para agregar otro. Seleccione **Azure Monitor Logs** (Registros de Azure Monitor) para el **Tipo de destino**. Seleccione el área de trabajo de Log Analytics para la **Cuenta o espacio de nombres**. Haga clic en **Agregar origen de datos** para guardar el origen de datos.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" alt-text="Destino de la regla de recopilación de datos":::

Haga clic de nuevo en **Agregar origen de datos** para agregar registros a la regla de recopilación de datos. En **Tipo de origen de datos**, seleccione **Registros de eventos de Windows** o **Linux syslog** (Syslog de Linux). Seleccione los tipos de datos de registro que quiere recopilar. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" alt-text="Origen de datos de registros de Windows de la regla de recopilación de datos":::

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" alt-text="Origen de datos de registros de Linux de la regla de recopilación de datos":::

Seleccione la pestaña **Destino**. La opción **Registros de Azure Monitor** ya debería estar seleccionada para **Tipo de destino**. Seleccione el área de trabajo de Log Analytics para la **Cuenta o espacio de nombres**. Si aún no tiene un área de trabajo, puede seleccionar el área de trabajo predeterminada para su suscripción, que se creará automáticamente. Haga clic en **Agregar origen de datos** para guardar el origen de datos.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" alt-text="Destino de los registros de la regla de recopilación de datos":::

Haga clic en **Revisar y crear** para crear la regla de recopilación de datos e instalar el agente de Azure Monitor en las máquinas virtuales seleccionadas.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-save.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-save.png" alt-text="Guardar regla de recolección de datos":::

## <a name="viewing-logs"></a>Visualización de los registros
Los datos se recuperan de un área de trabajo de Log Analytics mediante una consulta de registro escrita en el lenguaje de consulta de Kusto (KQL). Aunque hay disponible un conjunto de consultas creadas previamente para las máquinas virtuales, usaremos una consulta sencilla a fin de ver los eventos que se recopilan. 

Seleccione **Registros** en el menú de la máquina virtual. Log Analytics se abre con una ventana de consulta vacía con el ámbito establecido en esa máquina. Las consultas incluirán solo los registros recopilados de esa máquina. 

> [!NOTE]
> Es posible que se abra la ventana **Consultas** al abrir Log Analytics. Esto incluye las consultas creadas previamente que se pueden usar. Por ahora, cierre esta ventana, ya que vamos a crear manualmente una consulta sencilla.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics.png" lightbox="media/tutorial-monitor-vm/log-analytics.png" alt-text="Log Analytics":::


En la ventana de consulta vacía, escriba `Event` o `Syslog` en función de si la máquina ejecuta Windows o Linux y, después, haga clic en **Ejecutar**. Se muestran los eventos recopilados en el **intervalo de tiempo**.

> [!NOTE]
> Si la consulta no devuelve ningún dato, es posible que deba esperar unos minutos hasta que se creen eventos en la máquina virtual para su recopilación. También es posible que tenga que modificar el origen de datos en la regla de recopilación de datos para incluir categorías de eventos adicionales.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics-query.png" lightbox="media/tutorial-monitor-vm/log-analytics-query.png" alt-text="Log Analytics con resultados de consulta":::

Para obtener información sobre el uso de Log Analytics para analizar datos de registro, vea [Tutorial de Log Analytics](../logs/log-analytics-tutorial.md). Para obtener información sobre cómo crear reglas de alertas a partir de datos de registro, vea [Tutorial: Creación de una alerta de consulta de registros para un recurso de Azure](../alerts/tutorial-log-alert.md).

## <a name="view-guest-metrics"></a>Visualización de métricas de invitado
Puede ver las métricas de la máquina virtual de host con el Explorador de métricas sin una regla de recopilación de datos, al igual que [cualquier otro recurso de Azure](../essentials/tutorial-metrics.md). Pero con la regla de recopilación de datos, puede usar el Explorador de métricas para ver las métricas de invitado además de las métricas de host.

Seleccione **Métricas** en el menú de la máquina virtual. Se abre el Explorador de métricas con el ámbito establecido en la máquina virtual. Haga clic en **Espacio de nombres de métrica** y seleccione **Invitado de la máquina virtual**. 

> [!NOTE]
> Si no ve **Invitado de máquina virtual**, es posible que solo tenga que esperar unos minutos más para que el agente se implemente y los datos empiecen a recopilarse.


:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer.png" lightbox="media/tutorial-monitor-vm/metrics-explorer.png" alt-text="Explorador de métricas":::

Se muestran las métricas de invitado disponibles. Seleccione una **Métrica** para agregarla al gráfico.

:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" lightbox="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" alt-text="Explorador de métricas con métricas de invitado":::

Puede obtener información completa sobre la visualización y el análisis de datos de métricas con el Explorador de métricas en [Tutorial: Análisis de métricas para un recurso de Azure](../essentials/tutorial-metrics.md), y sobre la creación de alertas de métricas en [Tutorial: Creación de una alerta de métricas para un recurso de Azure](../alerts/tutorial-metric-alert.md). 



## <a name="next-steps"></a>Pasos siguientes
Ahora que va a recopilar métricas de invitado para la máquina virtual, puede crear alertas de métricas basadas en métricas de invitado, como la memoria disponible y el espacio en disco lógico.

> [!div class="nextstepaction"]
> [Creación de una alerta de métrica en Azure Monitor](../alerts/tutorial-metric-alert.md)



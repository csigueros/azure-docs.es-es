---
title: Recopilación de orígenes de datos del registro de eventos de Windows con el agente de Log Analytics en Azure Monitor
description: Se describe cómo configurar la recopilación de registros de eventos de Windows mediante Azure Monitor y se incluyen detalles de los registros que crean.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: b87354ca6b7a079f60f8d64e2deae53b65cef167
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543860"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Recopilación de orígenes de datos del registro de eventos de Windows con el agente de Log Analytics
Muchas aplicaciones escriben en el registro de eventos de Windows, por lo que constituye uno de los [orígenes de datos](../agents/agent-data-sources.md) más comunes para los agentes de Log Analytics en máquinas virtuales Windows.  Puede recopilar eventos de registros estándar, como el sistema y la aplicación, además de especificar cualquier registro personalizado creado por las aplicaciones que debe supervisar.

> [!IMPORTANT]
> En este artículo se trata la recopilación de eventos de Windows con el [agente de Log Analytics](./log-analytics-agent.md), que es uno de los agentes usados por Azure Monitor. Otros agentes recopilan otros datos y se configuran de forma diferente. Consulte [Información general sobre los agentes de Azure Monitor](../agents/agents-overview.md) para obtener una lista de los agentes disponibles y los datos que pueden recopilar.

![Eventos de Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configuración de registros de eventos de Windows
Configure los registros de eventos en el [menú de configuración de agentes](../agents/agent-data-sources.md#configuring-data-sources) para el área de trabajo de Log Analytics.

Azure Monitor solo recopila eventos de los registros de eventos de Windows que se especifican en la configuración.  Para agregar un registro nuevo, escriba el nombre y haga clic en **+** .  Para cada registro, solo se recopilan los eventos con los niveles de gravedad seleccionados.  Compruebe las gravedades del registro determinado que desea recopilar.  No puede proporcionar criterios adicionales para filtrar eventos.

A medida que escribe el nombre de un registro de eventos, Azure Monitor da sugerencias de nombres comunes de este tipo de registros. Aun cuando el registro que desea agregar no aparezca en la lista, puede incluirlo escribiendo su nombre completo. Encontrará el nombre completo del registro en el Visor de eventos. En el Visor de eventos, abra la página *Propiedades* del registro y copie la cadena del campo *Nombre completo*.

[![Configurar eventos de Windows](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!IMPORTANT]
> No puede configurar la recopilación de eventos de seguridad en el área de trabajo. Para recopilar eventos de seguridad, es preciso usar [Azure Security Center](../../security-center/security-center-enable-data-collection.md) o [Azure Sentinel](../../sentinel/connect-windows-security-events.md).


> [!NOTE]
> Los eventos críticos del registro de eventos de Windows tendrán una gravedad de "error" en los registros de Azure Monitor.

## <a name="data-collection"></a>datos, recopilación
Azure Monitor recopila cada evento que coincide con una gravedad seleccionada de un registro de eventos supervisado al crear el evento.  El agente registra su lugar en cada registro de eventos del que recopila entradas.  Si el agente queda sin conexión durante un período, recopila los eventos desde donde quedó, aunque esos eventos se hayan creado mientras el agente estaba sin conexión.  Sin embargo, existe la posibilidad de que estos eventos no se recopilen si el registro de eventos hace que los eventos no recopilados se sobrescriban mientras el agente está sin conexión.

>[!NOTE]
>Azure Monitor no recopila eventos de auditoría creados por SQL Server del origen *MSSQLSERVER* con un identificador de evento 18453 que contenga las palabras clave *Classic* o *Audit Success* y la palabra clave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Propiedades de los registros de eventos de Windows
Los registros de eventos de Windows tienen un tipo **Event** y las propiedades que aparecen en la tabla siguiente:

| Propiedad | Descripción |
|:--- |:--- |
| Computer |Nombre del equipo desde el que se recopiló el evento. |
| EventCategory |Categoría del evento. |
| EventData |Todos los datos con formato sin procesar. |
| EventId |El número del evento. |
| EventLevel |La gravedad del evento en formato numérico. |
| EventLevelName |La gravedad del evento en formato de texto. |
| EventLog |El nombre del registro de eventos desde el que se recopiló el evento. |
| ParameterXml |Los valores de parámetro de evento en formato XML. |
| ManagementGroupName |Nombre del grupo de administración de agentes de System Center Operations Manager.  En el caso de los otros agentes, este valor es `AOI-<workspace ID>`. |
| RenderedDescription |La descripción del evento con valores de parámetro. |
| Source |Origen del evento. |
| SourceSystem |El tipo de agente desde el que se recopiló el evento. <br> OpsManager: agente de Windows, ya sea una conexión directa o administrado por Operations Manager <br> Linux: todos los agentes de Linux.  <br> AzureStorage: Diagnósticos de Azure |
| TimeGenerated |La fecha y la hora de creación del evento en Windows. |
| UserName |El nombre de usuario de la cuenta que registró el evento. |

## <a name="log-queries-with-windows-events"></a>Consultas de registros con eventos de Windows
La tabla siguiente proporciona distintos ejemplos de consultas de registros que recuperan registros de eventos de Windows.

| Consultar | Descripción |
|:---|:---|
| Evento |Todos los eventos de Windows. |
| Event &#124; where EventLevelName == "error" |Todos los eventos de Windows con gravedad de error. |
| Event &#124; summarize count() by Source |Contador de eventos de Windows por origen. |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |Contador de eventos de error de Windows por origen. |


## <a name="next-steps"></a>Pasos siguientes
* Configure Log Analytics para recopilar otros [orígenes de datos](../agents/agent-data-sources.md) para su análisis.
* Obtenga información acerca de las [consultas de registros](../logs/log-query-overview.md) para analizar los datos recopilados de soluciones y orígenes de datos.  
* Configure la [recopilación de contadores de rendimiento](data-sources-performance-counters.md) desde los agentes de Windows.

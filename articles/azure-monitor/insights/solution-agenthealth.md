---
title: Solución Agent Health en Azure Monitor | Microsoft Docs
description: Aprenda a usar esta solución para supervisar el estado de los agentes que informan directamente a Log Analytics o System Center Operations Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 33fd14f71e0ef441fc2b33765f7275fc33448e0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663447"
---
# <a name="agent-health-solution-in-azure-monitor"></a>Solución Agent Health en Azure Monitor
La solución Agent Health de Azure le ayuda a comprender qué agentes de supervisión no responden y envían datos operativos. Aquí se incluyen todos los agentes que informan directamente al área de trabajo de Log Analytics en Azure Monitor o a un grupo de administración de System Center Operations Manager conectado a Azure Monitor.

También puede usar la solución Agent Health para:

* Realizar un seguimiento de cuántos agentes están implementados y dónde se distribuyen geográficamente.
* Realizar otras consultas para mantener el conocimiento de la distribución de los agentes implementados en Azure, en otros entornos de nube o en el entorno local.    

## <a name="prerequisites"></a>Requisitos previos
Antes de implementar esta solución, confirme que tiene [agentes de Windows](../agents/agent-windows.md) compatibles que informan al área de trabajo de Log Analytics o a un [grupo de administración de Operations Manager](../agents/om-agents.md) integrado con el área de trabajo.

## <a name="management-packs"></a>Módulos de administración
Si el grupo de administración de Operations Manager está conectado a un área de trabajo de Log Analytics, se instalarán los siguientes módulos de administración en Operations Manager. Estos módulos de administración también se instalan en equipos Windows directamente conectados después de agregar esta solución.

* Intelligence Pack HealthAssessment Direct Channel de Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Intelligence Pack HealthAssessment Server Channel de Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentViaServer)  

No hay nada para configurar o administrar en estos módulos de administración. Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](../agents/om-agents.md).

## <a name="configuration"></a>Configuración
Agregue la solución Agent Health al área de trabajo de Log Analytics mediante el proceso descrito en [Incorporación de soluciones](solutions.md). No es necesario realizar ninguna otra configuración.

## <a name="supported-agents"></a>Agentes admitidos
En la tabla siguiente se describen los orígenes conectados compatibles con esta solución.

| Origen conectado | Compatible | Descripción |
| --- | --- | --- |
| Agentes de Windows | Sí | Se recopilan eventos de latido de agentes directos de Windows.|
| Grupo de administración de System Center Operations Manager | Sí | Se recopilan eventos de latido de agentes que informan al grupo de administración cada 60 segundos y después se reenvían a Azure Monitor. No se requiere ninguna conexión directa entre los agentes de Operations Manager y Azure Monitor. Los datos de eventos de latido se reenvían del grupo de administración al área de trabajo de Log Analytics.|

## <a name="using-the-solution"></a>Uso de la solución
Al agregar la solución al área de trabajo de Log Analytics, se agrega el icono **Agent Health** al panel. Este icono muestra el número total de agentes y el número de agentes que no responden en las últimas 24 horas.

![Captura de pantalla que muestra el icono de Agent Health en el panel.](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Seleccione el icono de **Agent Health** para abrir el panel **Agent Health**.  El panel incluye las columnas de la tabla siguiente. Cada columna muestra los 10 principales eventos por recuento que coinciden con los criterios de esa columna para el intervalo de tiempo especificado. Puede ejecutar una búsqueda de registros que proporcione toda la lista; para ello, seleccione **Ver todo** junto a cada columna o elija el encabezado de columna.

| Columna | Descripción |
|--------|-------------|
| Agent count over time (Número de agentes a lo largo del tiempo) | Una tendencia del número de agentes durante un período de siete días para agentes de Linux y Windows.|
| Count of unresponsive agents (Número de agentes que no responden) | Una lista de agentes que no han enviado ningún latido en las últimas 24 horas.|
| Distribution by OS Type (Distribución por tipo de sistema operativo) | Una partición de cuántos agentes de Windows y Linux tiene en su entorno.|
| Distribution by Agent Version (Distribución por versión del agente) | Una partición de las versiones de agente instaladas en su entorno y el número de cada una de ellas.|
| Distribution by Agent Category (Distribución por categoría del agente) | Una partición de las categorías de agentes que envían eventos de latido: agentes directos, agentes de Operations Manager o el servidor de administración de Operations Manager.|
| Distribution by Management Group (Distribución por grupo de administración) | Una partición de los grupos de administración de Operations Manager en su entorno.|
| Geo-location of Agents (Geolocalización de los agentes) | Una partición de los países o regiones donde tiene agentes y el número total de agentes instalados en cada país o región.|
| Count of Gateways Installed (Número de puertas de enlace instaladas) | Número de servidores que tienen instalada la puerta de enlace de Log Analytics y una lista de estos servidores.|

![Captura de pantalla que muestra un ejemplo del panel de la solución Agent Health.](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Registros de Azure Monitor
La solución crea un tipo de registro en el área de trabajo de Log Analytics: latido. Los registros de latido tienen las propiedades de la tabla siguiente:  

| Propiedad | Descripción |
| --- | --- |
| `Type` | `Heartbeat`|
| `Category` | `Direct Agent`, `SCOM Agent`o `SCOM Management Server`|
| `Computer` | Nombre del equipo|
| `OSType` | Sistema operativo Windows o Linux|
| `OSMajorVersion` | Versión principal del sistema operativo|
| `OSMinorVersion` | Versión secundaria del sistema operativo|
| `Version` | Versión del agente de Log Analytics o del agente de Operations Manager|
| `SCAgentChannel` | `Direct` o `SCManagementServer`|
| `IsGatewayInstalled` | `true` si está instalada la puerta de enlace de Log Analytics; de lo contrario `false`|
| `ComputerIP` | Dirección IP pública de una máquina virtual de Azure, si hay una disponible; dirección SNAT de Azure (no la dirección IP privada) de una máquina virtual que usa una dirección IP privada |
| `ComputerPrivateIPs` | Lista de direcciones IP privadas del equipo |
| `RemoteIPCountry` | Ubicación geográfica donde está implementado el equipo|
| `ManagementGroupName` | Nombre del grupo de administración de Operations Manager|
| `SourceComputerId` | Identificador único del equipo|
| `RemoteIPLongitude` | Longitud de la ubicación geográfica del equipo|
| `RemoteIPLatitude` | Latitud de la ubicación geográfica del equipo|

Cada agente que informa a un servidor de administración de Operations Manager enviará dos latidos. El valor de la propiedad `SCAgentChannel` incluirá `Direct` y `SCManagementServer`, en función de los orígenes de datos y las soluciones de supervisión que haya habilitado en la suscripción. 

Si recuerda, los datos de las soluciones se envían:

* Directamente desde un servidor de administración de Operations Manager a Azure Monitor.
* Directamente desde el agente a Azure Monitor, debido al volumen de datos recopilados en el agente.

En los eventos de latido que tengan el valor `SCManagementServer`, el valor `ComputerIP` es la dirección IP del servidor de administración porque carga realmente los datos. En los latidos donde `SCAgentChannel` esté establecido en `Direct`, es la dirección IP pública del agente.  

## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo
En la tabla siguiente se proporcionan búsquedas de registros de ejemplo de los registros que recopila la solución.

| Consultar | Descripción |
|:---|:---|
| Heartbeat &#124; distinct Computer |Número total de agentes |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Número de agentes que no responden en las últimas 24 horas |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Número de agentes que no responden en los últimos 15 minutos |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Equipos en línea en las últimas 24 horas |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total de agentes sin conexión en los últimos 30 minutos (durante las últimas 24 horas) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Tendencia del número de agentes a lo largo del tiempo por tipo de sistema operativo|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution by OS Type (Distribución por tipo de sistema operativo) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution by Agent Version (Distribución por versión del agente) |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution by Agent Category (Distribución por categoría del agente) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution by Management Group (Distribución por grupo de administración) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geo-location of Agents (Geolocalización de los agentes) |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de puertas de enlace de Log Analytics instaladas |

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre la [generación de alertas desde consultas de registro en Azure Monitor](../alerts/alerts-overview.md). 


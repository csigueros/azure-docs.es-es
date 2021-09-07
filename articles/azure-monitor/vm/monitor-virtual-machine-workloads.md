---
title: 'Supervisión de máquinas virtuales con Azure Monitor: cargas de trabajo'
description: Obtenga información sobre cómo supervisar las cargas de trabajo de invitado de máquinas virtuales en Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: e411f7fd6f8d8a21b2016cbe16d0c7a947313331
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674870"
---
# <a name="monitor-virtual-machines-with-azure-monitor-workloads"></a>Supervisión de máquinas virtuales con Azure Monitor: cargas de trabajo
Este artículo forma parte del escenario [Supervisión de máquinas virtuales y sus cargas de trabajo en Azure Monitor](monitor-virtual-machine.md). Describe cómo supervisar las cargas de trabajo que se ejecutan en los sistemas operativos invitados de las máquinas virtuales. En este artículo se incluyen detalles sobre el análisis y las alertas sobre diferentes orígenes de datos en las máquinas virtuales.

## <a name="configure-additional-data-collection"></a>Configuración de la recopilación de datos adicionales
La información de máquinas virtuales recopila solo los datos de rendimiento del sistema operativo invitado de las máquinas habilitadas. Puede habilitar la recopilación de datos de rendimiento, eventos y otros datos de supervisión adicionales del agente mediante la configuración del área de trabajo de Log Analytics. Solo se configura una vez porque cualquier agente que se conecta al área de trabajo descarga automáticamente la configuración y comienza inmediatamente a recopilar los datos definidos.

Para una lista de orígenes de datos disponibles e información sobre su configuración, consulte [Orígenes de datos de agente en Azure Monitor](../agents/agent-data-sources.md).

> [!NOTE]
> No se puede configurar de forma selectiva la recopilación de datos para diferentes máquinas. Todas las máquinas conectadas al área de trabajo usan la configuración de esa área de trabajo.

> [!IMPORTANT]
> Tenga cuidado de recopilar solo los datos que necesite. Hay costes asociados a los datos recopilados en el área de trabajo. Los datos que recopile solo deben admitir escenarios concretos de análisis y alertas.

## <a name="convert-management-pack-logic"></a>Conversión de la lógica del módulo de administración
Un número significativo de clientes que implementan Azure Monitor supervisan actualmente sus cargas de trabajo de máquina virtual mediante módulos de administración en System Center Operations Manager. No hay ninguna herramienta de migración que convierta los recursos de Operations Manager en recursos de Azure Monitor porque las plataformas son totalmente distintas. En su lugar, la migración constituye una implementación estándar de Azure Monitor mientras se sigue usando Operations Manager. A medida que personalice Azure Monitor para cumplir con sus requisitos de diferentes aplicaciones y componentes, y a medida que obtenga más características, podrá empezar a retirar diferentes módulos de administración y agentes en Operations Manager.

En lugar de intentar replicar toda la funcionalidad de un módulo de administración, analice la supervisión crítica proporcionada por el módulo de administración. Decida si puede replicar esos requisitos de supervisión mediante los métodos descritos en las secciones anteriores. En muchos casos, puede configurar reglas de recopilación de datos y alertas en Azure Monitor que repliquen suficiente funcionalidad para poder retirar un módulo de administración determinado. Los módulos de administración pueden incluir a menudo cientos e incluso miles de reglas y monitores.

En la mayoría de los escenarios, Operations Manager combina la recopilación de datos y las condiciones de alerta en la misma regla o monitor. En Azure Monitor, debe configurar la recopilación de datos y una regla de alertas para cualquier escenario de alerta.

Una estrategia es centrarse en los monitores y las reglas que desencadenaron alertas en su entorno. Consulte los [informes existentes disponibles en Operations Manager](/system-center/scom/manage-reports-installed-during-setup), como **Alertas** y **Most Common Alerts** (Alertas más comunes), que pueden ayudarle a identificar las alertas a lo largo del tiempo. También puede ejecutar la siguiente consulta en Operations Database para evaluar las alertas recientes más comunes.

```sql
select AlertName, COUNT(AlertName) as 'Total Alerts' from
Alert.vAlertResolutionState ars
inner join Alert.vAlertDetail adt on ars.AlertGuid = adt.AlertGuid
inner join Alert.vAlert alt on ars.AlertGuid = alt.AlertGuid
group by AlertName
order by 'Total Alerts' DESC
```

Evalúe la salida para identificar alertas específicas para la migración. Omita las alertas que se han ajustado o que se sabe que son problemáticas. Revise los módulos de administración para identificar las alertas críticas de interés que nunca se han activado.

## <a name="windows-or-syslog-event"></a>Evento de Windows o Syslog
En este escenario de supervisión común, el sistema operativo y las aplicaciones escriben en los eventos de Windows o Syslog. Cree una alerta en cuanto se encuentre un único evento. O bien, puede esperar a que se den una serie de eventos coincidentes dentro de un período de tiempo determinado.

Para recopilar estos eventos, configure un área de trabajo de Log Analytics para recopilar [eventos de Windows](../agents/data-sources-windows-events.md) o [eventos de Syslog](../agents/data-sources-windows-events.md). La ingesta y retención de estos datos en el área de trabajo tiene un coste.

Los eventos de Windows se almacenan en la tabla [Evento](/azure/azure-monitor/reference/tables/event) y los eventos de Syslog se almacenan en la tabla [Syslog](/azure/azure-monitor/reference/tables/syslog) del área de trabajo de Log Analytics.

### <a name="sample-log-queries"></a>Consultas de registros de ejemplo

- **Contar el número de eventos por registro de eventos y tipo de evento del equipo.**

    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

- **Contar el número de eventos por registro de eventos e id. de evento del equipo.**
    
    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

### <a name="sample-alert-rules"></a>Reglas de alertas de ejemplo
En el ejemplo siguiente se crea una alerta al crear un evento específico de Windows. Usa una regla de alertas de unidades métricas para crear una alerta independiente para cada equipo.

- **Crear una regla de alertas para un evento específico de Windows.**

   En este ejemplo se muestra un evento en el registro de aplicaciones. Especifique un umbral de 0 e infracciones consecutivas superior a 0.

    ```kusto
    Event 
    | where EventLog == "Application"
    | where EventID == 123 
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Crear una regla de alerta en eventos de Syslog con una gravedad determinada.**

   En el ejemplo siguiente se muestran los eventos de autorización de errores. Especifique un umbral de 0 e infracciones consecutivas superior a 0.

    ```kusto
    Syslog
    | where Facility == "auth"
    | where SeverityLevel == "err"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="custom-performance-counters"></a>Contadores de rendimiento personalizados
Es posible que necesite contadores de rendimiento creados por aplicaciones o el sistema operativo invitado que la información de máquinas virtuales no recopila. Configure el área de trabajo de Log Analytics para que recopile los [datos de rendimiento](../agents/data-sources-windows-events.md). La ingesta y retención de estos datos en el área de trabajo tiene un coste. Tenga cuidado de no recopilar datos de rendimiento que la información de máquinas virtuales ya está recopilando.

Los datos de rendimiento configurados por el área de trabajo se almacenan en la tabla [Rend](/azure/azure-monitor/reference/tables/perf). Esta tabla tiene una estructura diferente a la tabla [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) que la información de máquinas virtuales usa.

### <a name="sample-log-queries"></a>Consultas de registros de ejemplo

Para ejemplos de consultas de registro que usan contadores de rendimiento personalizados, vea [Consultas de registro con registros de rendimiento](../agents/data-sources-performance-counters.md#log-queries-with-performance-records).

### <a name="sample-alerts"></a>Alertas de ejemplo

- **Crear una alerta sobre el valor máximo de un contador.**
    
    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = max(CounterValue) by Computer
    ```

- **Crear una alerta sobre el valor promedio de un contador.**

    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = avg(CounterValue) by Computer
    ```

## <a name="text-logs"></a>Registros de texto
Algunas aplicaciones escriben eventos en un registro de texto almacenado en la máquina virtual. Defina un [registro personalizado](../agents/data-sources-custom-logs.md) en el área de trabajo de Log Analytics para recopilar estos eventos. Defina la ubicación del registro de texto y su configuración detallada. La ingesta y retención de estos datos en el área de trabajo tiene un coste.

Los eventos del registro de texto se almacenan en una tabla con un nombre similar a **MiTabla_CL**. El nombre y la estructura del registro se definen al configurarlo.

### <a name="sample-log-queries"></a>Consultas de registros de ejemplo
Los nombres de columna que se usan aquí son solo por ejemplo. Los nombres de columna para un registro determinado se definen al definirlo. Lo más probable es que los nombres de columna del registro sean diferentes.

- **Contar el número de eventos por código.**
    
    ```kusto
    MyApp_CL
    | summarize count() by code
    ```

### <a name="sample-alert-rule"></a>Regla de alertas de ejemplo

- **Crear una regla de alerta en cualquier evento de error.**
    
    ```kusto
    MyApp_CL
    | where status == "Error"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```
## <a name="iis-logs"></a>Registros IIS
IIS que se ejecuta en máquinas Windows escribe registros en un archivo de texto. Configure un área de trabajo de Log Analytics para recopilar los [registros del clúster](../agents/data-sources-iis-logs.md). La ingesta y retención de estos datos en el área de trabajo tiene un coste.

Los registros del registro de IIS se almacenan en la tabla [W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) del área de trabajo de Log Analytics.

### <a name="sample-log-queries"></a>Consultas de registros de ejemplo

- **Contar entradas de registro de IIS por dirección URL para el host www.contoso.com.**
    
    ```kusto
    W3CIISLog 
    | where csHost=="www.contoso.com" 
    | summarize count() by csUriStem
    ```

- **Revisar el total de bytes recibidos por cada máquina IIS.**

    ```kusto
    W3CIISLog 
    | summarize sum(csBytes) by Computer
    ```

### <a name="sample-alert-rule"></a>Regla de alertas de ejemplo

- **Crear una regla de alerta en cualquier registro con un estado de retorno de 500.**
    
    ```kusto
    W3CIISLog 
    | where scStatus==500
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="service-or-daemon"></a>Servicio o demonio
Para supervisar el estado de un servicio Windows o demonio de Linux, habilite la solución [Seguimiento de cambios e inventario](../../automation/change-tracking/overview.md) en [Azure Automation](../../automation/automation-intro.md). Azure Monitor no tiene capacidad para supervisar el estado de un servicio o demonio. Hay algunos métodos posibles que se pueden usar, como buscar eventos en el registro de eventos de Windows, pero este método no es confiable. También puede buscar el proceso asociado al servicio que se ejecuta en la máquina en la tabla [VMProcess](/azure/azure-monitor/reference/tables/vmprocess). Esta tabla solo se actualiza cada hora, lo que normalmente no es suficiente para las alertas.

> [!NOTE]
> La solución Change Tracking and Analysis es diferente de la característica [Change Analysis](vminsights-change-analysis.md) en la información de máquinas virtuales. Esta característica está en versión preliminar pública y aún no se incluye en este escenario.

Para las diferentes opciones para habilitar la solución Change Tracking en las máquinas virtuales, consulte [Habilitar Seguimiento de cambios e inventario](../../automation/change-tracking/overview.md#enable-change-tracking-and-inventory). Esta solución incluye métodos para configurar máquinas virtuales a escala. Tendrá que [crear una cuenta de Azure Automation](../../automation/automation-quickstart-create-account.md) para admitir la solución.

Al habilitar el Seguimiento de cambios e inventario, se crean dos tablas nuevas en el área de trabajo de Log Analytics. Use estas tablas para las reglas de alertas de consulta de registro.

| Tabla | Descripción |
|:---|:---|
| [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationdata) | Cambios en los datos de configuración de invitado |
| [ConfigurationData](/azure/azure-monitor/reference/tables/configurationdata) | Último estado notificado para los datos de configuración de invitado |


### <a name="sample-log-queries"></a>Consultas de registros de ejemplo

- **Enumerar todos los servicios y demonios que se han iniciado recientemente.**
    
    ```kusto
    ConfigurationChange
    | where ConfigChangeType == "Daemons" or ConfigChangeType == "WindowsServices"
    | where SvcState == "Running"
    | sort by Computer, SvcName
    ```

### <a name="alert-rule-samples"></a>Ejemplos de reglas de alerta

- **Crear una regla de alertas basada en cuándo se detiene un servicio específico.**

    
    ```kusto
    ConfigurationData
    | where SvcName == "W3SVC" 
    | where SvcState == "Stopped"
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

- **Crear una regla de alertas basada en cuándo se detiene uno de un conjunto de servicios.**
    
    ```kusto
    let services = dynamic(["omskd","cshost","schedule","wuauserv","heathservice","efs","wsusservice","SrmSvc","CertSvc","wmsvc","vpxd","winmgmt","netman","smsexec","w3svc","sms_site_vss_writer","ccmexe","spooler","eventsystem","netlogon","kdc","ntds","lsmserv","gpsvc","dns","dfsr","dfs","dhcp","DNSCache","dmserver","messenger","w32time","plugplay","rpcss","lanmanserver","lmhosts","eventlog","lanmanworkstation","wnirm","mpssvc","dhcpserver","VSS","ClusSvc","MSExchangeTransport","MSExchangeIS"]);
    ConfigurationData
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | where SvcName in (services)
    | where SvcState == "Stopped"
    | project TimeGenerated, Computer, SvcName, SvcDisplayName, SvcState
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

## <a name="port-monitoring"></a>Supervisión de puertos
La supervisión de puertos comprueba que una máquina está escuchando en un puerto determinado. Aquí se describen dos estrategias posibles para la supervisión de puertos.

### <a name="dependency-agent-tables"></a>Tablas de agentes de dependencias
Use [VMConnection](/azure/azure-monitor/reference/tables/vmconnection) y [VMBoundPort](/azure/azure-monitor/reference/tables/vmboundport) para analizar las conexiones y los puertos de la máquina. La tabla VMBoundPort se actualiza cada minuto con cada proceso que se ejecuta en el equipo y el puerto en el que escucha. Puede crear una alerta de consulta de registro similar a la alerta de latido que falta para buscar procesos que se han detenido o para alertar cuando la máquina no escucha en un puerto determinado.

### <a name="sample-log-queries"></a>Consultas de registros de ejemplo

- **Revisar el recuento de puertos abiertos en las máquinas virtuales, lo que resulta útil para evaluar qué máquinas virtuales tienen vulnerabilidades de configuración y seguridad.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize by Computer, Machine, Port, Protocol
    | summarize OpenPorts=count() by Computer, Machine
    | order by OpenPorts desc
    ```

- **Enumerar los puertos vinculados en las máquinas virtuales, lo que resulta útil para evaluar qué máquinas virtuales tienen vulnerabilidades de configuración y seguridad.**

    ```kusto
    VMBoundPort
    | distinct Computer, Port, ProcessName
    ```


- **Analizar la actividad de red por puerto para determinar cómo se configura la aplicación o el servicio.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
    | project-away TimeGenerated
    | order by Machine, Computer, Port, Ip, ProcessName
    ```

- **Revisar las tendencias de bytes enviados y recibidos para las máquinas virtuales.**

    ```kusto
    VMConnection
    | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer
    | order by Computer desc
    | render timechart
    ```

- **Usar errores de conexión a lo largo del tiempo para determinar si la tasa de errores es estable o está cambiando.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | extend bythehour = datetime_part("hour", TimeGenerated)
    | project bythehour, LinksFailed
    | summarize failCount = count() by bythehour
    | sort by bythehour asc
    | render timechart
    ```

- **Vincular tendencias de estado para analizar el comportamiento y el estado de conexión de una máquina.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h)
    | render timechart
    ```

### <a name="connection-manager"></a>Administrador de conexiones
La característica [Connection Monitor](../../network-watcher/connection-monitor-overview.md) de [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) se usa para probar las conexiones a un puerto en una máquina virtual. Una prueba comprueba que la máquina está escuchando en el puerto y que es accesible en la red.
Connection Manager requiere la extensión Network Watcher en la máquina cliente que inicia la prueba. No es necesario instalarla en la máquina que se está probando. Para los detalles, consulte [Tutorial: Supervisión de la comunicación de red mediante Azure Portal](../../network-watcher/connection-monitor.md).

Hay un coste adicional por Connection Manager. Para los detalles, vea [Precios de Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/).

## <a name="run-a-process-on-a-local-machine"></a>Ejecución de un proceso en una máquina local
La supervisión de algunas cargas de trabajo requiere un proceso local. Un ejemplo es un script de PowerShell que se ejecuta en la máquina local para conectarse a una aplicación y recopilar o procesar datos. Puede usar [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md), que forma parte de [Azure Automation](../../automation/automation-intro.md), para ejecutar un script de PowerShell local. No hay ningún cargo directo por Hybrid Runbook Worker, pero hay un coste por cada runbook que use.

El runbook puede acceder a cualquier recurso de la máquina local para recopilar los datos necesarios. No puede enviar datos directamente a Azure Monitor ni crear una alerta. Para crear una alerta, haga que el runbook escriba una entrada en un registro personalizado y, a continuación, configure ese registro para que Azure Monitor lo recopile. Cree una regla de alerta de consulta de registro que se inicie en esa entrada de registro.

## <a name="synthetic-transactions"></a>Transacciones sintéticas
Una transacción sintética se conecta a una aplicación o servicio que se ejecuta en una máquina para simular una conexión de usuario o tráfico de usuario real. Si la aplicación está disponible, puede dar por supuesto que la máquina se está ejecutando correctamente. [Application Insights](../app/app-insights-overview.md) de Azure Monitor proporciona esta funcionalidad. Solo funciona para aplicaciones a las que se puede acceder desde Internet. En el caso de las aplicaciones internas, debe abrir un firewall para permitir el acceso desde las direcciones URL específicas de Microsoft que realizan la prueba. O bien, puede usar una solución de supervisión alternativa, como System Center Operations Manager.

|Método | Descripción |
|:---|:---|
| [Prueba de dirección URL](../app/monitor-web-app-availability.md) | Garantiza que HTTP está disponible y devuelve una página web. |
| [Prueba de varios pasos](../app/availability-multistep.md) | Simula una sesión de usuario. |

## <a name="sql-server"></a>SQL Server

Use [SQL insights](../insights/sql-insights-overview.md) para supervisar la instancia de SQL Server que se ejecuta en las máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes

* [Obtención de información sobre el análisis de datos en registros de Azure Monitor mediante consultas de registro](../logs/get-started-queries.md)
* [Obtención de información sobre las alertas que usan métricas y registros en Azure Monitor](../alerts/alerts-overview.md)
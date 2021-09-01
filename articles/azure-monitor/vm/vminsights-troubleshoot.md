---
title: Solución de problemas de VM insights
description: Solucione problemas de instalación de VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: a7bcce4d18f6bcfe299a31dae3eb036bef12da9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729282"
---
# <a name="troubleshoot-vm-insights"></a>Solución de problemas de VM insights
En este artículo se proporciona información para la solución de los problemas que surgen al habilitar o usar VM insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>No se puede habilitar VM insights en un equipo
Al incorporar una máquina virtual de Azure desde Azure Portal, se producen los pasos siguientes:

- Se crea un área de trabajo de Log Analytics predeterminada si se ha seleccionado la opción.
- El agente de Log Analytics se instala en máquinas virtuales de Azure con una extensión de máquina virtual si el agente ya está instalado.
- Se instala Dependency Agent en las máquinas virtuales de Azure con una extensión, si se determina que es necesario.
  
Durante el proceso de incorporación, se comprueba cada uno de estos pasos y se muestra un estado de notificación en el portal. La configuración del área de trabajo y la instalación del agente normalmente tarda de 5 a 10 minutos. Los datos tardarán entre 5 y 10 minutos en estar disponibles para su visualización en el portal.

Si recibe un mensaje que indica que la máquina virtual debe incorporarse después de haber realizado el proceso de incorporación, espere hasta 30 minutos para que se complete el proceso. Si el problema persiste, consulte las siguientes secciones para ver las posibles causas.

### <a name="is-the-virtual-machine-running"></a>¿La máquina virtual se está ejecutando?
 Si la máquina virtual se ha desactivado durante un tiempo, está desactivada actualmente o solo se ha activado recientemente, no podrá mostrar los datos durante un tiempo hasta que estos lleguen.

### <a name="is-the-operating-system-supported"></a>¿El sistema operativo es compatible?
Si el sistema operativo no se encuentra en la lista de [sistemas operativos compatibles](vminsights-enable-overview.md#supported-operating-systems), la extensión no se instalará y verá este mensaje en espera de que lleguen los datos.

### <a name="did-the-extension-install-properly"></a>¿La extensión se instaló correctamente?
Si sigue viendo un mensaje que indica que la máquina virtual debe incorporarse, puede que una de las extensiones no se instalara correctamente (o las dos). Compruebe la página **Extensiones** de la máquina virtual en Azure Portal para comprobar que aparecen las siguientes extensiones.

| Sistema operativo | Agentes | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentLinux |

Si no ve las dos extensiones del sistema operativo en la lista de extensiones instaladas, deben instalarse. Si aparecen las extensiones pero su estado no aparece como *Aprovisionamiento realizado correctamente*, hay que quitar la extensión y volver a instalarla.

### <a name="do-you-have-connectivity-issues"></a>¿Tiene problemas de conectividad?
En máquinas Windows, puede usar la herramienta *TestCloudConnectivity* para identificar el problema de conectividad. Esta herramienta se instala de manera predeterminada con el agente en la carpeta *%SystemRoot%\Archivos de programa\Microsoft Monitoring Agent\Agent*. Ejecute la herramienta en un símbolo del sistema con privilegios elevados. Devolverá resultados y resaltará dónde se produce un error en la prueba. 

![Herramienta TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Más solución de problemas de agentes

Consulte los siguientes artículos para solucionar problemas relacionados con el agente de Log Analytics:

- [Procedimientos para solucionar problemas relacionados con el agente de Log Analytics para Windows](../agents/agent-windows-troubleshoot.md)
- [Cómo solucionar problemas relacionados con el agente de Log Analytics para Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>La vista de rendimiento no tiene datos
Si los agentes parecen estar instalados correctamente pero no ve ningún dato en la vista de rendimiento, consulte las siguientes secciones para ver las posibles causas.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>¿El área de trabajo Log Analytics ha alcanzado su límite de datos?
Compruebe las [reservas de capacidad y los precios de la ingesta de datos](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-agent-connected-to-azure-monitor-logs"></a>¿El agente de máquina virtual está conectado a los registros de Azure Monitor?

Abra Log Analytics desde **Registros** en el menú Azure Monitor de Azure Portal. Realice la consulta siguiente para el equipo:

```kuso
Heartbeat
| where Computer == "my-computer"
| sort by TimeGenerated desc 
```

Si no ve ningún dato o si el equipo no ha enviado un latido recientemente, es posible que tenga problemas con el agente. Consulte la sección anterior para obtener información sobre la solución de problemas del agente.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>La máquina virtual no aparece en la vista de mapa

### <a name="is-the-dependency-agent-installed"></a>¿Está instalado el agente Dependency Agent?
 Utilice la información de las secciones anteriores para determinar si el agente Dependency Agent está instalado y funciona correctamente.

### <a name="are-you-on-the-log-analytics-free-tier"></a>¿Está en el nivel Gratis de Log Analytics?
El [nivel Gratis de log Analytics](https://azure.microsoft.com/pricing/details/monitor/) es un plan de precios heredado que permite hasta cinco máquinas Service Map únicas. Ninguna máquina posterior aparecerá en Service Map, incluso si los cinco anteriores ya no envían datos.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>¿La máquina virtual envía datos de registro y de rendimiento a los registros de Azure Monitor?
Use la consulta de registro de la sección [La vista de rendimiento no tiene datos](#performance-view-has-no-data) para determinar si se están recopilando datos para la máquina virtual. Si no se recopilan datos, use la herramienta TestCloudConnectivity descrita anteriormente para determinar si tiene problemas de conectividad.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>La máquina virtual aparece en la vista de mapa pero le faltan datos
Si la máquina virtual está en la vista de mapa, el agente Dependency Agent se instala y se ejecuta, pero el controlador de kernel no se cargó. Compruebe el archivo de registro en las siguientes ubicaciones:

| Sistema operativo | Log | 
|:---|:---|
| Windows | C:\Archivos de Programa\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

Las últimas líneas del archivo deben indicar por qué no se cargó el kernel. Por ejemplo, es posible que el kernel no sea compatible con Linux si actualizó el kernel.
## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la incorporación de agentes de VM Insights, consulte [Información general sobre la habilitación de VM Insights](vminsights-enable-overview.md).

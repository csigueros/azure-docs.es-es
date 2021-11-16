---
title: Solución de problemas de Change Tracking e Inventario de Azure Automation
description: En este artículo se trata cómo solucionar problemas con la característica Change Tracking e Inventario de Azure Automation.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: 21699d306742c2a732155ac8df78608f5c3dd7ae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346029"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Solución de problemas de Change Tracking e Inventario

En este artículo se describe cómo diagnosticar y solucionar problemas de Change Tracking e Inventario de Azure Automation. Para obtener información general de Change Tracking e Inventario, consulte [Información general de Change Tracking e Inventario](../change-tracking/overview.md).

## <a name="general-errors"></a>Errores generales

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Escenario: La máquina ya está registrada en otra cuenta

### <a name="issue"></a>Incidencia

Aparece el siguiente mensaje de error:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

La máquina ya se ha implementado en otra área de trabajo para Change Tracking.

### <a name="resolution"></a>Solución

1. Asegúrese de que las notificaciones de la máquina se envían al área de trabajo correcta. Para obtener instrucciones sobre cómo comprobar esto, consulte [Comprobación de la conectividad del agente a Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Asegúrese también de que esta área de trabajo esté vinculada a su cuenta de Azure Automation. Para ello, vaya a la cuenta de Automation y seleccione **Área de trabajo vinculada** en **Recursos relacionados**.

1. Asegúrese de que la máquina se muestre en el área de trabajo de Log Analytics vinculada a la cuenta de Automation. Ejecute la siguiente consulta en el área de trabajo de Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Si no ve la máquina en los resultados de la consulta, significa que no se ha registrado recientemente. Probablemente haya un problema de configuración local. Debe volver a instalar el agente de Log Analytics.

   Si el equipo aparece en los resultados de la consulta, compruebe en la propiedad Solutions que aparezca **changeTracking**. Esto comprueba que está registrado con Seguimiento de cambios e inventario. Si no es así, compruebe si hay problemas de configuración de ámbito. La configuración de ámbito determina qué máquinas están configuradas para Seguimiento de cambios e inventario. Para configurar la configuración de ámbito para el equipo de destino, consulte [Habilitación de Seguimiento de cambios e inventario desde una cuenta de Automation](../change-tracking/enable-from-automation-account.md).

   En el área de trabajo, ejecute esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Si recibe el resultado ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota```, significa que la cuota definida en el área de trabajo se ha alcanzado, lo que ha impedido que se guarden los datos. En el área de trabajo, vaya a **Uso y costos estimados**. Seleccione un nuevo **Plan de tarifa** que le permita usar más datos o haga clic en **Límite diario** y quite el límite.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Uso y costos estimados." lightbox="./media/change-tracking/change-tracking-usage.png":::

Si el problema no se resuelve, siga los pasos descritos en [Implementación de Hybrid Runbook Worker en Windows](../automation-windows-hrw-install.md) para volver a instalar Hybrid Worker para Windows. Para Linux, siga los pasos que aparecen en [Implementación de Hybrid Runbook Worker en Linux](../automation-linux-hrw-install.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Escenario: no se muestran los registros de Change Tracking e Inventario para máquinas Windows

#### <a name="issue"></a>Incidencia

No aparecen los resultados de Change Tracking e Inventario para las máquinas Windows donde se ha habilitado la característica.

#### <a name="cause"></a>Causa

Este error puede tener las causas siguientes:

* El agente de Azure Log Analytics para Windows no se está ejecutando.
* Está bloqueada la comunicación a la cuenta de Automation.
* No se han descargado los módulos de administración de Change Tracking e Inventario.
* La VM que se quiere habilitar puede provenir de una máquina clonada que no se haya preparado mediante la preparación del sistema (sysprep) con el agente de Log Analytics para Windows instalado.

#### <a name="resolution"></a>Resolución

En la máquina del agente de Log Analytics, vaya a **C:\Archivos de programa\Microsoft Monitoring Agent\Agent\Tools** y ejecute los siguientes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Si sigue necesitando ayuda, puede recopilar la información de diagnóstico y ponerse en contacto con el equipo de soporte técnico.

> [!NOTE]
> El agente de Log Analytics habilita el seguimiento de errores de forma predeterminada. Para habilitar los mensajes de error detallados como en el ejemplo anterior, use el parámetro `VER`. Para más información sobre los seguimientos, use `INF` cuando invoque `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>El agente de Log Analytics para Windows no se está ejecutando

Compruebe que el agente de Log Analytics para Windows (**HealthService.exe**) está en ejecución en la máquina.

##### <a name="communication-to-automation-account-blocked"></a>Bloqueada la comunicación con la cuenta de Automation

Consulte el Visor de eventos en el equipo y busque cualquier evento que contenga la palabra `changetracking`.

Para conocer las direcciones y puertos que deben estar permitidos para que Change Tracking e Inventario funcione, consulte la sección sobre [planeamiento de la red](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Módulos de administración no descargados

Compruebe que los siguientes módulos de administración de Change Tracking e Inventario están instalados en el entorno local:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Máquina virtual a partir de una máquina clonada que no se ha preparado con sysprep

Si utiliza una imagen clonada, primero prepare con sysprep la imagen y, a continuación, instale el agente de Log Analytics para Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Escenario: No hay resultados de Change Tracking e Inventario en las máquinas Linux

#### <a name="issue"></a>Incidencia

No ve los resultados de Change Tracking e Inventario de las máquinas Linux que tienen habilitada la característica. 

#### <a name="cause"></a>Causa
Estas son algunas causas posibles específicas de este problema:
* El agente de Log Analytics para Linux no está en ejecución.
* El agente de Log Analytics para Linux no está configurado correctamente.
* Hay conflictos de supervisión de la integridad de los archivos (FIM).

#### <a name="resolution"></a>Resolución 

##### <a name="log-analytics-agent-for-linux-not-running"></a>El agente de Log Analytics para Linux no está en ejecución

Compruebe que el demonio del agente de Log Analytics para Linux (**omsagent**) está en ejecución en la máquina. Ejecute la siguiente consulta en el área de trabajo de Log Analytics que está vinculada a la cuenta de Automation.

```loganalytics
Copy
Heartbeat
| summarize by Computer, Solutions
```

Si no ve la máquina en los resultados de la consulta, no se ha registrado recientemente. Probablemente haya un problema de configuración local y debe volver a instalar el agente. Para obtener información sobre la instalación y configuración, consulte [Recopilación de datos de registro con el agente de Log Analytics](../../azure-monitor/agents/log-analytics-agent.md).

Si el equipo aparece en los resultados de la consulta, compruebe la configuración de ámbito. Consulte [Soluciones de supervisión como destino en Azure Monitor](../../azure-monitor/insights/solution-targeting.md).

Para más información sobre cómo solucionar este problema, consulte [Problema: no se ve ningún dato de Linux](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>El agente de Log Analytics para Linux no está configurado correctamente

Es posible que el agente de Log Analytics para Linux no esté correctamente configurado para el registro y la recopilación de la salida de la línea de comandos mediante la herramienta Recopilador de registros de OMS. Consulte [Información general de Change Tracking e Inventario](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>Conflictos de FIM

La característica FIM de Defender for Cloud podría estar validando incorrectamente la integridad de los archivos de Linux. Compruebe que FIM está operativo y configurado correctamente para la supervisión de archivos de Linux. Consulte [Información general de Change Tracking e Inventario](../change-tracking/overview.md).

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece aquí o no puede resolverlo, intente obtener ayuda adicional mediante uno de los siguientes canales:

* Obtenga respuestas de expertos de Azure en los [foros de Azure](https://azure.microsoft.com/support/forums/).
* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. El Soporte técnico de Azure pone en contacto a la comunidad de Azure con respuestas, soporte técnico y expertos.
* Registrar un incidente de soporte técnico de Azure. Vaya al [sitio de Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte técnico**.

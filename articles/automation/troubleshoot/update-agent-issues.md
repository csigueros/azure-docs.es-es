---
title: Solución de problemas del agente de actualización de Windows en Azure Automation
description: En este artículo se describe cómo solucionar y resolver problemas con el agente de Windows Update en Update Management.
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: a9dd04181f8118bef21dd64fc92c65cce248c1c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738612"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Solución de problemas del agente de actualización de Windows

Puede haber muchas razones por las que el equipo no se muestra como Listo (correcto) durante una implementación de Update Management. Puede comprobar el estado de un agente de Hybrid Runbook Worker en Windows para determinar el problema subyacente. A continuación se enumeran los tres estados de disponibilidad para una máquina:

* Párese: Hybrid Runbook Worker está implementado y se vio por última vez hace menos de una hora.
* Escenario desconectado: Hybrid Runbook Worker está implementado y se vio por última vez hace más de una hora.
* No configurado: Hybrid Runbook Worker no se encuentra o no ha finalizado su implementación.

> [!NOTE]
> Puede haber un ligero retraso entre lo que Azure Portal muestra y el estado actual de una máquina.

En este artículo se explica cómo ejecutar el solucionador de problemas para máquinas de Azure desde Azure Portal y para máquinas que no son de Azure en el [escenario sin conexión](#troubleshoot-offline).

> [!NOTE]
> El script del solucionador de problemas incluye ahora comprobaciones de Windows Server Update Services (WSUS) y de las claves de descarga automática e instalación.

## <a name="start-the-troubleshooter"></a>Iniciar el solucionador de problemas

Para las máquinas de Azure, al hacer clic en el vínculo **Solucionar problemas** en la columna **Preparación de actualizaciones del agente** del portal, se abre la página de solución de problemas del Agente de actualización. Para las máquinas que no son de Azure, el vínculo le lleva a este artículo. Consulte [Solución de problemas sin conexión](#troubleshoot-offline) para solucionar los problemas de una máquina que no es de Azure.

![Captura de pantalla de la lista de máquinas virtuales de Update Management](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para comprobar el estado de Hybrid Runbook Worker, la máquina virtual debe estar en ejecución. Si la máquina virtual no se está ejecutando, aparecerá el botón **Start the VM** (Iniciar la máquina virtual).

En la página Solucionar problemas del Agente de actualización, haga clic en **Ejecutar comprobaciones** para iniciar el solucionador de problemas. El solucionador de problemas usa [Ejecutar comando](../../virtual-machines/windows/run-command.md) para ejecutar un script en la máquina y comprobar las dependencias. Una vez completado el proceso del solucionador de problemas, devuelve el resultado de las comprobaciones.

![Página de solución de problemas del Agente de actualización](../media/update-agent-issues/troubleshoot-page.png)

Los resultados se muestran en la página cuando haya terminado el proceso. Las secciones de las comprobaciones muestran lo que está incluido en cada una de ellas.

![Captura de pantalla de Comprobaciones de la solución de problemas del Agente de actualización](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Comprobaciones de requisitos previos

### <a name="operating-system"></a>Sistema operativo

La comprobación del sistema operativo constata si Hybrid Runbook Worker ejecuta alguno de los sistemas operativos incluidos en la tabla siguiente.

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2012 y posteriores |Se requiere .NET Framework 4.6 o posterior. ([Descargar .NET Framework](/dotnet/framework/install/guide-for-developers)).<br/> Se requiere Windows PowerShell 5.1.  ([Descargar Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).        |

### <a name="net-462"></a>.NET 4.6.2

La comprobación de .NET Framework constata si el sistema tiene instalado [.NET Framework 4.6.2](https://dotnet.microsoft.com/download/dotnet-framework/net462) o superior.

### <a name="wmf-51"></a>WMF 5.1

La comprobación de WMF constata si el sistema tiene la versión requerida de Windows Management Framework (WMF), [Windows Management Fotograma 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Esta comprobación determina si usa TLS 1.2 para cifrar las comunicaciones. TLS 1.0 ya no es compatible con la plataforma. Se recomienda usar TLS 1.2 para comunicarse con Update Management.

## <a name="connectivity-checks"></a>Comprobaciones de conectividad

### <a name="registration-endpoint"></a>Punto de conexión del registro

Esta comprobación determina si el agente puede comunicarse correctamente con el servicio del agente.

Las configuraciones de proxy y firewall deben permitir que el agente de Hybrid Runbook Worker se comunique con el punto de conexión de registro. Para ver una lista de direcciones y puertos que deben abrirse, consulte [Planeamiento de red](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Punto de conexión de las operaciones

Esta comprobación determina si el agente puede comunicarse correctamente con el servicio de datos en tiempo de ejecución del trabajo.

Las configuraciones de proxy y firewall deben permitir que el agente de Hybrid Runbook Worker se comunique con el servicio de datos en tiempo de ejecución del trabajo. Para ver una lista de direcciones y puertos que deben abrirse, consulte [Planeamiento de red](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Comprobaciones del estado de servicio de las máquinas virtuales

### <a name="monitoring-agent-service-status"></a>Supervisión del estado de servicio del agente

Esta comprobación determina si el agente de Log Analytics para Windows (`healthservice`) se ejecuta en la máquina. Para obtener más información sobre cómo solucionar problemas del servicio, consulte [El agente de Log Analytics para Windows no está en ejecución](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar el agente de Log Analytics para Windows, consulte [Instalación del agente de Log Analytics para Windows](../../azure-monitor/agents/agent-windows.md).

### <a name="monitoring-agent-service-events"></a>Supervisión de eventos de servicio del agente

Esta comprobación determina si se ha producido algún evento 4502 en el registro de Azure Operations Manager de la maquina durante las últimas 24 horas.

Para más información sobre este evento, consulte el [evento 4502 del registro de Operations Manager](hybrid-runbook-worker.md#event-4502) para este evento.

## <a name="access-permissions-checks"></a>Comprobaciones de permisos de acceso

> [!NOTE]
> El solucionador de problemas no enruta actualmente el tráfico a través de un servidor proxy si se ha configurado uno.

### <a name="crypto-folder-access"></a>Acceso a la carpeta Crypto

La comprobación del acceso a la carpeta Crypto determina si la cuenta del sistema local tiene acceso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Solución de problemas sin conexión

Puede usar el solucionador de problemas en una instancia sin conexión de Hybrid Runbook Worker si ejecuta el script de forma local. Obtenga el siguiente script de GitHub: [UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1). Para ejecutar el script, debe tener WMF 4.0 o superior instalado. Para descargar la versión más reciente de PowerShell, consulte [Instalación de varias versiones de PowerShell](/powershell/scripting/install/installing-powershell).

El resultado de este script tendrá un aspecto similar al del siguiente ejemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de Hybrid Runbook Worker](hybrid-runbook-worker.md).

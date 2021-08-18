---
title: Solución de problemas del agente de actualización de Linux en Azure Automation
description: En este artículo se describe cómo solucionar y resolver problemas con el agente de Windows Update de Linux en Update Management.
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9fdfa1541e928491aecc039d51338f7ef1f1ef3d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181416"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Solución de problemas del agente de actualización de Linux

Puede haber muchas razones por las que el equipo no se muestra como Listo (correcto) en Update Management. Puede comprobar el estado de un agente de Hybrid Runbook Worker en Linux para determinar el problema subyacente. A continuación se enumeran los tres estados de disponibilidad para una máquina:

* Párese: Hybrid Runbook Worker está implementado y se vio por última vez hace menos de una hora.
* Escenario desconectado: Hybrid Runbook Worker está implementado y se vio por última vez hace más de una hora.
* No configurado: Hybrid Runbook Worker no se encuentra o no ha finalizado su implementación.

> [!NOTE]
> Puede haber un ligero retraso entre lo que Azure Portal muestra y el estado actual de una máquina.

En este artículo se explica cómo ejecutar el solucionador de problemas para máquinas de Azure desde Azure Portal y para máquinas que no son de Azure en el [escenario sin conexión](#troubleshoot-offline).

> [!NOTE]
> El script del solucionador de problemas no enruta actualmente el tráfico a través de un servidor proxy si se ha configurado uno.

## <a name="start-the-troubleshooter"></a>Iniciar el solucionador de problemas

Para las máquinas de Azure, seleccione el vínculo **Solucionar problemas** en la columna **Preparación de actualizaciones del agente** del portal para abrir la página de solución de problemas del Agente de actualización. Para las máquinas que no son de Azure, el vínculo le lleva a este artículo. Para solucionar problemas de una máquina que no es de Azure, consulte las instrucciones de la sección "Solucionar problemas sin conexión".

![Página de lista de VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Las comprobaciones requieren que la máquina esté en ejecución. Si la máquina virtual no se está ejecutando, aparecerá el botón **Iniciar la máquina virtual**.

En la página de solución de problemas del Agente de actualización, haga clic en **Ejecutar comprobaciones** para iniciar el solucionador de problemas. El solucionador de problemas usa [Ejecutar comando](../../virtual-machines/linux/run-command.md) para ejecutar un script en la máquina a fin de verificar las dependencias que el agente tiene. Una vez completado el proceso del solucionador de problemas, devuelve el resultado de las comprobaciones.

![Página de solución de problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Una vez finalizadas las comprobaciones, los resultados se muestran en la ventana. En las secciones de comprobación se proporciona información acerca de lo que se busca en cada comprobación.

![Página de comprobaciones del agente de actualización](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Comprobaciones de requisitos previos

### <a name="operating-system"></a>Sistema operativo

La comprobación del sistema operativo comprueba si Hybrid Runbook Worker está ejecutando alguno de los siguientes sistemas operativos.

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) y 7 (x64)      | Los agentes de Linux deben tener acceso a un repositorio de actualización. La aplicación de revisiones basada en la clasificación requiere "yum" para devolver los datos de seguridad que CentOS no tiene de forma nativa.         |
|Red Hat Enterprise (x86/x64) 6 y 7 (x64)     | Los agentes de Linux deben tener acceso a un repositorio de actualización.        |
|SUSE Linux Enterprise Server 11 (x86/x64) y 12 (x64)     | Los agentes de Linux deben tener acceso a un repositorio de actualización.        |
|Ubuntu 14.04 LTS, 16.04 LTS y 18.04 LTS (x86/x64)      |Los agentes de Linux deben tener acceso a un repositorio de actualización.         |

## <a name="monitoring-agent-service-health-checks"></a>Supervisión de comprobaciones del estado de servicio del agente

### <a name="log-analytics-agent"></a>Agente de Log Analytics

Esta comprobación garantiza que el agente de Log Analytics para Linux está instalado. Para obtener instrucciones sobre cómo instalarlo, consulte [Install the agent for Linux](../../azure-monitor/vm/monitor-virtual-machine.md#agents) (Instalación del agente para Linux).

### <a name="log-analytics-agent-status"></a>Estado del agente de Log Analytics

Esta comprobación garantiza que el agente de Log Analytics para Linux está ejecutándose. Si el agente no se está ejecutando, puede ejecutar el comando siguiente para intentar reiniciarlo. Para más información sobre cómo solucionar problemas del agente, consulte [Linux: Solución de incidencias de Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Hospedaje múltiple

Esta comprobación determina si el agente está informando a varias áreas de trabajo. Update Management no es compatible con el hospedaje múltiple.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Esta comprobación garantiza que el agente de Log Analytics para Linux tiene el paquete de Hybrid Runbook Worker. Este paquete es necesario para que funcione Update Management. Para más información, consulte [El agente de Log Analytics para Linux no está en ejecución](hybrid-runbook-worker.md#oms-agent-not-running).

Update Management descarga paquetes de Hybrid Runbook Worker desde el punto de conexión de las operaciones. Por lo tanto, si la instancia de Hybrid Runbook Worker no se está ejecutando y se produce un error en la comprobación del [punto de conexión de las operaciones](#operations-endpoint), no podrá realizarse la actualización.

### <a name="hybrid-runbook-worker-status"></a>Estado de Hybrid Runbook Worker

Esta comprobación garantiza que se está ejecutando Hybrid Runbook Worker en el equipo. Los procesos del ejemplo siguiente deben estar presentes si la instancia de Hybrid Runbook Worker se está ejecutando correctamente.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Comprobaciones de conectividad

### <a name="general-internet-connectivity"></a>Conectividad a Internet general

Esta comprobación asegura que la máquina tenga acceso a internet.

### <a name="registration-endpoint"></a>Punto de conexión del registro

Esta comprobación determina si Hybrid Runbook Worker puede comunicarse correctamente con Azure Automation en el área de trabajo de Log Analytics.

Las configuraciones de proxy y firewall deben permitir que el agente de Hybrid Runbook Worker se comunique con el punto de conexión de registro. Para ver una lista de direcciones y puertos que deben abrirse, consulte [Planeamiento de red](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Punto de conexión de las operaciones

Esta comprobación determina si el agente de Log Analytics puede comunicarse correctamente con el servicio de datos en tiempo de ejecución del trabajo.

Las configuraciones de proxy y firewall deben permitir que el agente de Hybrid Runbook Worker se comunique con el servicio de datos en tiempo de ejecución del trabajo. Para ver una lista de direcciones y puertos que deben abrirse, consulte [Planeamiento de red](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Punto de conexión de Log Analytics 1

Esta comprobación verifica que la máquina tenga acceso a los puntos de conexión necesarios para el agente de Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Punto de conexión de Log Analytics 2

Esta comprobación verifica que la máquina tenga acceso a los puntos de conexión necesarios para el agente de Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Punto de conexión de Log Analytics 3

Esta comprobación verifica que la máquina tenga acceso a los puntos de conexión necesarios para el agente de Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Solución de problemas sin conexión

Puede utilizar el solucionador de problemas sin conexión en un Hybrid Runbook Worker mediante la ejecución local del script. El script de Python, [UM_Linux_Troubleshooter_Offline.py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py), se puede encontrar en GitHub. En el ejemplo siguiente se muestra un ejemplo del resultado de este script:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de Hybrid Runbook Worker](hybrid-runbook-worker.md).
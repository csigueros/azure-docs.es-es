---
title: Información general sobre la habilitación de VM Insights
description: Aprenda a implementar y configurar VM Insights. Más información sobre los requisitos del sistema.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: fa656b974a89924a2ce029a7c5e38b4d9f68f6b7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440095"
---
# <a name="enable-vm-insights-overview"></a>Información general sobre la habilitación de VM Insights

En este artículo se proporciona información general sobre las opciones disponibles para habilitar VM Insights con el fin de supervisar el estado y el rendimiento de lo siguiente:

- Azure Virtual Machines 
- Conjuntos de escalado de máquinas virtuales de Azure
- Máquinas virtuales híbridas conectadas con Azure Arc
- Máquinas virtuales locales
- Máquinas virtuales hospedadas en otro entorno de nube  

Para configurar VM Insights:

* Habilite una única máquina virtual de Azure, un conjunto de escalado de máquinas virtuales de Azure o una máquina de Azure Arc; para ello, seleccione **Insights** directamente del menú de Azure Portal.
* Habilite varias máquinas virtuales de Azure, conjuntos de escalado de máquinas virtuales de Azure o máquinas de Azure Arc mediante Azure Policy. Este método asegura que se instalan y configuran correctamente las dependencias necesarias en las máquinas virtuales y conjuntos de escalado nuevos y existentes. Se informa de las máquinas virtuales y conjuntos de escalado no compatibles para que pueda decidir si habilitarlas y cómo corregir las incompatibilidades.
* Habilite varias máquinas virtuales de Azure, máquinas virtuales de Azure Arc, conjuntos de escalado de máquinas virtuales de Azure o máquinas de Azure Arc mediante una suscripción o un grupo de recursos especificado con PowerShell.
* Habilite VM Insights para supervisar las VM o los equipos físicos hospedados en la red corporativa o en otro entorno de nube.

## <a name="supported-machines"></a>Equipos compatibles
VM Insights es compatible con las máquinas siguientes:

- Máquina virtual de Azure
- Conjunto de escalado de máquinas virtuales de Azure
- Máquina virtual híbrida conectada con Azure Arc

> [!IMPORTANT]
> Si el dispositivo Ethernet de la máquina virtual tiene más de nueve caracteres, VM Insights no lo reconocerá y los datos no se enviarán a la tabla InsightsMetrics. El agente recopilará los datos de [otros orígenes](../agents/agent-data-sources.md).

## <a name="supported-azure-arc-machines"></a>Máquinas de Azure Arc admitidas
VM Insights está disponible para los servidores habilitados para Azure Arc en las regiones donde el servicio de extensión de Arc está disponible. Debe ejecutar la versión 0 9 o superior del agente de Arc.

| Origen conectado | Compatible | Descripción |
|:--|:--|:--|
| Agentes de Windows | Sí | Junto con el [agente de Log Analytics para Windows](../agents/log-analytics-agent.md), los agentes de Windows requieren Dependency Agent. Para más información, consulte el artículo sobre los [sistemas operativos compatibles](../agents/agents-overview.md#supported-operating-systems). |
| Agentes de Linux | Sí | Junto con el [agente de Log Analytics para Linux](../agents/log-analytics-agent.md), los agentes de Linux requieren Dependency Agent. Para más información, consulte el artículo sobre los [sistemas operativos compatibles](#supported-operating-systems). |
| Grupo de administración de System Center Operations Manager | No | |

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

VM Insights admite cualquier sistema operativo que sea compatible con el agente de Log Analytics y Dependency Agent. Consulte [Información general sobre los agentes de Azure Monitor](../agents/agents-overview.md#supported-operating-systems).

> [!IMPORTANT]
> La característica de estado de invitado de VM Insights tiene una compatibilidad con sistemas operativos más limitada mientras se encuentra en versión preliminar pública. Consulte [Habilitación del estado de invitado de VM Insights (versión preliminar)](../vm/vminsights-health-enable.md) para obtener una lista detallada.

### <a name="linux-considerations"></a>Consideraciones sobre Linux
Consulte la siguiente lista de consideraciones sobre la compatibilidad de Linux de Dependency Agent que admite VM Insights:

- Se admiten solo versiones de kernel SMP Linux y predeterminados.
- Las versiones de kernel no estándar, como Physical Address Extension (PAE) y Xen, no son compatibles con ninguna distribución de Linux. Por ejemplo, un sistema con la cadena de versión *2.6.16.21-0.8-xen* no es compatible.
- No se admiten los kernel personalizados, incluidas las recompilaciones de kernels estándar.
- Para distribuciones de Debian que no sean la versión 9.4, no se admite la característica de asignación y la característica de rendimiento solo está disponible en el menú de Azure Monitor. No está disponible directamente desde el panel izquierdo de la máquina virtual de Azure.
- Se admite el kernel de CentOSPlus.

El kernel de Linux debe revisarse para las vulnerabilidades de Spectre y Meltdown. Consulte al proveedor de distribución de Linux para más información. Ejecute el comando siguiente para comprobar si se mitigaron las vulnerabilidades de Spectre/Meltdown:

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

La salida de este comando tendrá un aspecto similar al siguiente y especificará si un equipo es vulnerable a cualquier problema. Si faltan estos archivos, no se aplica la revisión a la máquina.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics
VM Insights requiere un área de trabajo Log Analytics. Consulte [Configuración del área de trabajo de Log Analytics para VM Insights](vminsights-configure-workspace.md) para conocer los detalles y requisitos de esta área de trabajo.
## <a name="agents"></a>Agentes
VM Insights requiere la instalación de los dos agentes siguientes en cada máquina virtual o conjunto de escalado de máquinas virtuales que se supervisarán. Para incorporar el recurso, Instale estos agentes y conéctelos al área de trabajo.  Consulte los [requisitos de red](../agents/log-analytics-agent.md#network-requirements) para estos agentes.

- [Agente de Log Analytics](../agents/log-analytics-agent.md). Recopila eventos y datos de rendimiento de la máquina virtual o del conjunto de escalado de máquinas virtuales y los entrega al área de trabajo de Log Analytics. Los métodos de implementación para el agente de Log Analytics en los recursos de Azure usan la extensión de VM para [Windows](../../virtual-machines/extensions/oms-windows.md) y [Linux](../../virtual-machines/extensions/oms-linux.md).
- Dependency Agent. Recopila datos detectados acerca de los procesos que se ejecutan en las dependencias de las máquinas virtuales y los procesos externos, que usa la [característica de asignación de VM Insights](../vm/vminsights-maps.md). Dependency Agent depende del agente de Log Analytics para entregar los datos a Azure Monitor. Los métodos de implementación de Dependency Agent en los recursos de Azure usan la extensión VM para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) y [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> El agente de Log Analytics es el mismo agente que se usa para System Center Operations Manager. VM Insights puede supervisar agentes que también están supervisados por Operations Manager si están conectados directamente e instala Dependency Agent en estos. VM Insights no puede supervisar los agentes conectados a Azure Monitor a través de una [conexión de grupo de administración](../tform/../agents/om-agents.md).

A continuación se muestran varios métodos para implementar estos agentes. 

| Método | Descripción |
|:---|:---|
| [Azure Portal](../vm/vminsights-enable-portal.md) | Instale ambos agentes en una sola máquina virtual, en un conjunto de escalado de máquinas virtuales o en máquinas virtuales híbridas conectadas a Azure Arc. |
| [Plantillas de Resource Manager](../vm/vminsights-enable-resource-manager.md) | Instale ambos agentes con cualquiera de los métodos admitidos para implementar una plantilla de Resource Manager, como la CLI y PowerShell. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Asigne la iniciativa de Azure Policy para instalar automáticamente los agentes cuando se crea una máquina virtual o un conjunto de escalado de máquinas virtuales. |
| [Instalación manual](../vm/vminsights-enable-hybrid.md) | Instale los agentes en el sistema operativo invitado en equipos hospedados fuera de Azure, incluidos los de su centro de datos u otros entornos en la nube. |


## <a name="network-requirements"></a>Requisitos de red

- Consulte los [requisitos de red](../agents/log-analytics-agent.md#network-requirements) para el agente de Log Analytics.
- Dependency Agent requiere una conexión de la máquina virtual a la dirección 169.254.169.254. Este es el punto de conexión de servicio de metadatos de Azure. Asegúrese de que la configuración del firewall permite las conexiones a este punto de conexión.


## <a name="management-packs"></a>Módulos de administración
Cuando un área de trabajo de Log Analytics está configurada para VM Insights, se reenvían dos módulos de administración a todos los equipos Windows conectados a esa área de trabajo. Los módulos de administración se denominan *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* y *Microsoft.IntelligencePacks.VMInsights* y se escriben en *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs*. 

El origen de datos que utiliza el módulo de administración *ApplicationDependencyMonitor* es * *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. El origen de datos que utiliza el módulo de administración *VMInsights* es *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y uso

Microsoft recopila automáticamente datos de uso y rendimiento a través del servicio Azure Monitor. Microsoft usa estos datos para mejorar la calidad, la seguridad y la integridad del servicio. 

Para proporcionar funcionalidades de solución de problemas precisas y eficientes, la característica de asignación incluye datos sobre la configuración del software. Los datos proporcionan información como el sistema operativo y versión, la dirección IP, el nombre DNS y el nombre de la estación de trabajo. Microsoft no recopila nombres, direcciones ni otra información de contacto.

Para más información sobre el uso y la recopilación de datos, vea la [Declaración de privacidad de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Pasos siguientes

Para aprender a usar la característica de supervisión de rendimiento, consulte [Visualización del rendimiento de VM Insights](../vm/vminsights-performance.md). Para visualizar las dependencias de las aplicaciones detectadas, consulte [Visualización de la característica de asignación de VM Insights](../vm/vminsights-maps.md).

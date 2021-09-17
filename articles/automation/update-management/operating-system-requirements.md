---
title: Clientes compatibles con Update Management en Azure Automation
description: En este artículo se describen los sistemas operativos Windows y Linux compatibles con Update Management en Azure Automation.
services: automation
ms.subservice: update-management
ms.date: 07/14/2021
ms.topic: conceptual
ms.openlocfilehash: d45ab7ba80a33658d1a3feaf024cb23ea65d5fab
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771804"
---
# <a name="operating-systems-supported-by-update-management"></a>Sistemas operativos compatibles con Update Management

En este artículo se detallan los sistemas operativos Windows y Linux compatibles y los requisitos del sistema para máquinas o servidores administrados por Update Management.

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

En la tabla siguiente se muestra una lista de sistemas operativos compatibles para evaluaciones de actualizaciones y parches. La aplicación de revisiones requiere un sistema Hybrid Runbook Worker, que se instala automáticamente al habilitar la máquina virtual o el servidor para la administración mediante Update Management. Para obtener información sobre los requisitos del sistema para Hybrid Runbook Worker, vea [Implementación de Hybrid Runbook Worker en Windows](../automation-windows-hrw-install.md#prerequisites) e [Implementación de Hybrid Runbook Worker en Linux](../automation-linux-hrw-install.md#prerequisites).

Se supone que todos los sistemas operativos son x64. No se admite x86 para ningún sistema operativo.

> [!NOTE]
> La evaluación de la actualización de máquinas Linux solo se admite en determinadas regiones tal como se muestra en la [tabla de asignaciones](../how-to/region-mappings.md#supported-mappings) del área de trabajo de Log Analytics y la cuenta de Automation.

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2019 (Datacenter o Standard incluido Server Core)<br><br>Windows Server 2016 (Datacenter o Standard sin incluir Server Core)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM and SP1 Standard)| Update Management admite evaluaciones y parches para este sistema operativo. [Hybrid Runbook Worker](../automation-windows-hrw-install.md) es compatible con Windows Server 2008 R2. |
|CentOS 6, 7 y 8       | Los agentes de Linux requieren acceso a un repositorio de actualización. La aplicación de revisiones basada en la clasificación requiere `yum` para devolver los datos de seguridad que CentOS no tiene en sus versiones RTM. Para más información sobre la aplicación de revisiones basadas en clasificaciones en CentOS, consulte [Actualización de clasificaciones en Linux](view-update-assessments.md#linux).          |
|Oracle Linux 6.x, 7.x, 8x | Los agentes de Linux requieren acceso a un repositorio de actualización.        |
|Red Hat Enterprise 6, 7 y 8      | Los agentes de Linux requieren acceso a un repositorio de actualización.        |
|SUSE Linux Enterprise Server 12, 15, 15.1 y 15.2      | Los agentes de Linux requieren acceso a un repositorio de actualización.     |
|Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS y 20.04 LTS       |Los agentes de Linux requieren acceso a un repositorio de actualización.         |

> [!NOTE]
> Update Management no admite la automatización de la administración de actualizaciones de forma segura en todas las instancias de un conjunto de escalado de máquinas virtuales de Azure. La [actualización automática de imágenes de sistema operativo](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) es el método recomendado para administrar las actualizaciones de este tipo de imágenes en el conjunto de escalado.

## <a name="unsupported-operating-systems"></a>Sistemas operativos no admitidos

En la tabla siguiente se enumeran los sistemas operativos no admitidos por Update Management:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | No se admiten sistemas operativos cliente (por ejemplo, Windows 7 y Windows 10).<br> En el caso de Windows Virtual Desktop(WVD) de Azure, el método recomendado<br> para administrar las actualizaciones es usar [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) para la administración de revisiones de máquinas cliente de Windows 10. |
|Windows Server 2016 Nano Server     | No compatible.       |
|Nodos de Azure Kubernetes Service | No compatible. Use el proceso de aplicación de revisiones que se describe en [Aplicación de actualizaciones de kernel y seguridad en los nodos de Linux en Azure Kubernetes Service (AKS)](../../aks/node-updates-kured.md)|

## <a name="system-requirements"></a>Requisitos del sistema

La información siguiente describe los requisitos específicos del sistema operativo. Para obtener más instrucciones, vea [Planeamiento de red](plan-deployment.md#ports). Para comprender los requisitos para TLS 1.2, consulte el artículo sobre [TLS 1.2 para Azure Automation](../automation-managing-data.md#tls-12-for-azure-automation).

### <a name="windows"></a>Windows

Requisitos de software:

- Se requiere .NET Framework 4.6 o posterior. ([Descargue .NET Framework](/dotnet/framework/install/guide-for-developers)).
- Se requiere Windows PowerShell 5.1. ([Descargue Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
- La característica Update Management depende del rol Hybrid Runbook Worker del sistema, y debe confirmar sus [requisitos del sistema](../automation-windows-hrw-install.md#prerequisites).

Los agentes de Windows Update deben estar configurados para comunicarse con un servidor de Windows Server Update Services (WSUS) o requieren acceso a Microsoft Update. Para las máquinas híbridas, se recomienda instalar el agente de Log Analytics para Windows; para ello, primero debe conectar la máquina a los [servidores habilitados para Azure Arc](../../azure-arc/servers/overview.md) y, después, use Azure Policy para asignar la definición de la directiva integrada [Implementar el agente de Log Analytics en máquinas de Azure Arc con Windows](../../governance/policy/samples/built-in-policies.md#monitoring). Como alternativa, si planea supervisar las máquinas con VM Insights, en su lugar, use la iniciativa [Habilitar información de máquinas virtuales](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Puede usar Update Management con Microsoft Endpoint Configuration Manager. Para más información sobre escenarios de integración, consulte este artículo sobre la [integración de Update Management con Endpoint Configuration Manager de Windows](mecmintegration.md). El [agente de Log Analytics para Windows](../../azure-monitor/agents/agent-windows.md) es necesario para los servidores Windows administrados por sitios en su entorno de Configuration Manager.

De forma predeterminada, las máquinas virtuales Windows implementadas desde Azure Marketplace se establecen para recibir actualizaciones automáticas del servicio de Windows Update. Este comportamiento no cambia cuando se agregan VM Windows al área de trabajo. Si no administra activamente las actualizaciones mediante Update Management, se aplica el comportamiento predeterminado (las actualizaciones se aplican automáticamente).

> [!NOTE]
> Puede modificar la directiva de grupo para que los reinicios de la máquina solo los pueda realizar el usuario, no el sistema. Es posible que las máquinas administradas se bloqueen si Update Management no tiene derechos para reiniciar la máquina sin la intervención manual del usuario. Para obtener más información, consulte el tema sobre la [configuración de la directiva de grupo para actualizaciones automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

### <a name="linux"></a>Linux

Requisitos de software:

- La máquina requiere acceso a un repositorio de actualización, privado o público.
- Se requiere TLS 1.1 o TLS 1.2 para interactuar con Update Management.
- La característica Update Management depende del rol Hybrid Runbook Worker del sistema, y debe confirmar sus [requisitos del sistema](../automation-linux-hrw-install.md#prerequisites). Como Update Management usa runbooks de Automation para iniciar la evaluación y actualización de las máquinas, revise la [versión de Python necesaria](../automation-linux-hrw-install.md#supported-runbook-types) para la distribución de Linux compatible.

> [!NOTE]
> La evaluación de la actualización de máquinas Linux solo se admite en determinadas regiones. Consulte la [tabla de asignaciones](../how-to/region-mappings.md#supported-mappings) del área de trabajo de Log Analytics y la cuenta de Automation.

Para las máquinas híbridas, se recomienda instalar el agente de Log Analytics para Linux; para ello, primero debe conectar la máquina a los [servidores habilitados para Azure Arc](../../azure-arc/servers/overview.md) y, después, use Azure Policy para asignar la definición de la directiva integrada [Implementar el agente de Log Analytics en máquinas de Azure Arc con Linux](../../governance/policy/samples/built-in-policies.md#monitoring). Como alternativa, si planea supervisar las máquinas con Azure Monitor para VM, en su lugar, use la iniciativa [Habilitar Azure Monitor para VM](../../governance/policy/samples/built-in-initiatives.md#monitoring).

## <a name="next-steps"></a>Pasos siguientes

Antes de habilitar y usar Update Management, vea [Planeamiento de la implementación de Update Management](plan-deployment.md).
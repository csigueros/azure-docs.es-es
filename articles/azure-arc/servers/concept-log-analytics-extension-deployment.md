---
title: Implementación del agente de Log Analytics en servidores habilitados para Arc
description: En este artículo se revisan los distintos métodos para implementar el agente de Log Analytics en máquinas basadas en Windows y Linux que están registradas con servidores habilitados para Azure Arc en el centro de datos local u otro entorno de nube.
ms.date: 10/22/2021
ms.topic: conceptual
ms.openlocfilehash: 150b0c032108cd6d0aad84b6bcadf1b7101c7ef1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269873"
---
# <a name="understand-deployment-options-for-the-log-analytics-agent-on-azure-arc-enabled-servers"></a>Información sobre las opciones de implementación del agente de Log Analytics en servidores habilitados para Azure Arc

Azure Monitor admite varios métodos para instalar el agente de Log Analytics y conectar con el servicio la máquina o el servidor registrados con servidores habilitados para Azure Arc. Los servidores habilitados para Azure Arc admiten el marco de extensión de máquina virtual de Azure, que proporciona tareas de configuración y automatización posteriores a la implementación, lo que le permite simplificar la administración de máquinas híbridas tal como puede hacerlo con las VM de Azure.

El agente de Log Analytics es necesario si desea:

* Supervisar el sistema operativo, las cargas de trabajo que se ejecutan en la máquina o el servidor mediante [VM Insights](../../azure-monitor/vm/vminsights-overview.md). Analizar más en profundidad y crear alertas con otras características de [Azure Monitor](../../azure-monitor/overview.md).
* Supervisar la seguridad en Azure mediante el uso de [Azure Security Center](../../security-center/security-center-introduction.md) o [Azure Sentinel](../../sentinel/overview.md).
* Administrar las actualizaciones del sistema operativo con [Update Management en Azure Automation](../../automation/update-management/overview.md).
* Recopilar el inventario y llevar un seguimiento de los cambios mediante [Seguimiento de cambios e inventario en Azure Automation](../../automation/change-tracking/overview.md).
* Ejecutar runbooks de Automation directamente en la máquina y en los recursos del inventario mediante una instancia de [Hybrid Runbook Worker en Azure Automation](../../automation/automation-hybrid-runbook-worker.md).

En este artículo se revisan los métodos de implementación de la extensión de máquina virtual del agente de Log Analytics en varias máquinas virtuales o servidores físicos de producción del entorno, a fin de ayudarlo a determinar cuál funciona mejor para la organización. Si está interesado en el agente de Azure Monitor nuevo y quiere ver una comparación detallada, revise el artículo [Información general sobre los agentes de Azure Monitor](../../azure-monitor//agents/agents-overview.md).  

## <a name="installation-options"></a>Opción de instalación

Puede usar métodos distintos para instalar la extensión de máquina virtual mediante un método o una combinación. En esta sección se describe cada uno de ellos para que los tenga en cuenta.

### <a name="using-arc-enabled-servers"></a>Mediante servidores habilitados para Arc

Este método permite administrar la instalación, administración y eliminación de extensiones de VM en [Azure Portal](manage-vm-extensions-portal.md), mediante [PowerShell](manage-vm-extensions-powershell.md), la [CLI de Azure](manage-vm-extensions-cli.md) o con una [plantilla de Azure Resource Manager (ARM)](manage-vm-extensions-template.md).

#### <a name="advantages"></a>Ventajas

* Puede ser útil para realizar pruebas.
* Resulta útil si tiene que administrar pocas máquinas.

#### <a name="disadvantages"></a>Inconvenientes

* La automatización es limitada cuando se usa una plantilla de Azure Resource Manager; de lo contrario, es lenta.
* Solo se puede centrar en un servidor único habilitado para Arc y no en varias instancias.
* Solo permite especificar un área de trabajo única a la que informar. Requiere el uso de PowerShell o de la CLI de Azure para configurar la extensión de máquina virtual del agente de Windows de Log Analytics para informar hasta a cuatro áreas de trabajo.
* No permite implementar Dependency Agent desde el portal. Solo puede usar PowerShell, la CLI de Azure o la plantilla de ARM.

### <a name="using-azure-policy"></a>Uso de Azure Policy

Puede usar Azure Policy para implementar la extensión de máquina virtual del agente de Log Analytics a escala en las máquinas de su entorno y conservar el cumplimiento de la configuración. Esto se logra con la definición de directiva **Configurar extensión de Log Analytics en servidores Linux habilitados para Azure Arc** / **Configurar extensión de Log Analytics en servidores Windows habilitados para Azure Arc**, o bien la iniciativa de directiva **Habilitar Azure Monitor para VM**.

Azure Policy incluye varias definiciones creadas previamente relacionadas con Azure Monitor. Para una lista completa de la directivas integradas de la categoría **Supervisión**, consulte [Definiciones integradas de Azure Policy para Azure Monitor](../../azure-monitor/policy-reference.md).

#### <a name="advantages"></a>Ventajas

* Si se quita la extensión de VM, la reinstala después de evaluar la directiva.
* Identifica e instala la extensión de VM cuando un servidor habilitado para Azure Arc nuevo se registra con Azure.
* Solo permite especificar un área de trabajo única a la que informar. Requiere el uso de PowerShell o de la CLI de Azure para configurar la extensión de máquina virtual del agente de Windows de Log Analytics para informar hasta a cuatro áreas de trabajo.

#### <a name="disadvantages"></a>Inconvenientes

* La directiva **Configurar extensión de Log Analytics en servidores** *sistema operativo* **habilitados para Azure Arc** solo instala la extensión de VM de Log Analytics y configura el agente para que informe a un área de trabajo de Log Analytics especificada. Si le interesa VM Insights para supervisar el rendimiento del sistema operativo y asignar procesos en ejecución y dependencias de otros recursos, debe aplicar la iniciativa de directiva **Habilitar Azure Monitor para VM**. Instala y configura las extensiones de VM de Log Analytics y de VM de Dependency Agent, que son obligatorias.
* El ciclo de evaluación de cumplimiento estándar es una vez cada 24 horas. Un examen de evaluación de una suscripción o un grupo de recursos se puede iniciar con la CLI de Azure, Azure PowerShell, una llamada a la API de REST o mediante la acción de GitHub del examen de cumplimiento de Azure Policy. Para más información, consulte [Desencadenadores de evaluación](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

### <a name="using-azure-automation"></a>Uso de Azure Automation

El entorno operativo de automatización de procesos de Azure Automation y su compatibilidad con runbooks de PowerShell y Python pueden permitirle automatizar la implementación de la extensión de VM del agente de Log Analytics a escala en las máquinas del entorno.

#### <a name="advantages"></a>Ventajas

* Puede usar un método con script para automatizar su implementación y configuración mediante lenguajes de script que conoce.
* Se ejecuta según una programación que usted mismo define y controla.
* Autentíquense de forma segura en servidores habilitados para Arc desde la cuenta de Automation con una identidad administrada.

#### <a name="disadvantages"></a>Inconvenientes

* Quita una cuenta de Azure Automation.
* Necesita experiencia en la creación y administración de runbooks en Azure Automation.
* Necesita la creación de un runbook basado en PowerShell o Python en función del sistema operativo de destino.

## <a name="next-steps"></a>Pasos siguientes

* A fin de administrar las actualizaciones del sistema operativo con Update Management en Azure Automation, revise [Habilitación desde una cuenta de Automation](../../automation/update-management/enable-from-automation-account.md) y siga los pasos para habilitar las máquinas que informan al área de trabajo.

* A fin de administrar los cambios con Seguimiento de cambios e inventario en Azure Automation, revise [Habilitación desde una cuenta de Automation](../../automation/change-tracking/enable-from-automation-account.md) y siga los pasos para habilitar las máquinas que informan al área de trabajo.

* Puede usar la característica Hybrid Runbook Worker de Azure Automation para ejecutar runbooks directamente en máquinas o servidores registrados con servidores habilitados para Arc. Consulte el artículo [Implementación de una extensión de VM de Hybrid Runbook Worker](../../automation/extension-based-hybrid-runbook-worker-install.md).

* Para empezar a recopilar eventos relacionados con la seguridad con Azure Sentinel, consulte[Incorporación a Azure Sentinel](scenario-onboard-azure-sentinel.md), o bien, si desea realizar la recopilación con Azure Security Center, consulte[Incorporación a Azure Security Center](../../security-center/quickstart-onboard-machines.md).

* Consulte los artículos [Supervisión del rendimiento](../../azure-monitor/vm/vminsights-performance.md) y [Asignación de dependencias](../../azure-monitor/vm/vminsights-maps.md) para ver el rendimiento de la máquina y ver componentes de aplicación detectados.
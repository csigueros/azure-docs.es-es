---
title: Instalación del agente de Azure Monitor
description: Opciones para instalar el agente de Azure Monitor (AMA) en máquinas virtuales de Azure y servidores habilitados para Azure Arc.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 3c1b3ac13f79037b3357f3ecba0d3d668f88d0b5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850895"
---
# <a name="install-the-azure-monitor-agent"></a>Instalación del agente de Azure Monitor
En este artículo se proporcionan las distintas opciones disponibles actualmente para instalar el [agente de Azure Monitor](azure-monitor-agent-overview.md) en máquinas virtuales de Azure y en los servidores habilitados para Azure Arc, así como las opciones para crear [asociaciones con reglas de recopilación de datos](data-collection-rule-azure-monitor-agent.md) que definen qué datos debe recopilar el agente.

## <a name="prerequisites"></a>Prerrequisitos
Antes de instalar el agente de Azure Monitor, debe cumplir con los siguientes requisitos previos.

- La [identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) debe estar habilitada en las máquinas virtuales de Azure. Esto no es necesario en el caso de los servidores habilitados para Azure Arc. La identidad del sistema se habilitará automáticamente si el agente se instala como parte del proceso de [creación y asignación de una regla de recopilación de datos mediante Azure Portal](#install-with-azure-portal).
- La [etiqueta de servicio AzureResourceManager](../../virtual-network/service-tags-overview.md) debe estar habilitada en la red virtual de la máquina virtual.
- La máquina virtual debe tener acceso a los siguientes puntos de conexión HTTPS:
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> El agente de Azure Monitor no admite actualmente vínculos privados.

## <a name="virtual-machine-extension-details"></a>Detalles de la extensión de máquina virtual
El agente de Azure Monitor se implementa como [extensión de VM de Azure](../../virtual-machines/extensions/overview.md) con los detalles de la tabla siguiente. Se puede instalar con cualquiera de los métodos para instalar las extensiones de máquina virtual, incluidos los descritos en este artículo.

| Propiedad | Windows | Linux |
|:---|:---|:---|
| Publicador | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Tipo      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>Versiones de extensión
Se recomienda encarecidamente actualizar a las versiones posteriores a la disponibilidad general que se enumeran a continuación en lugar de usar versiones preliminares o intermedias.

| Fecha de la versión | Notas de la versión | Windows | Linux |
|:---|:---|:---|:---|:---|
| Junio de 2021 | Se ha anunciado la disponibilidad general. <ul><li>Todas las características excepto el destino de métricas ya tienen disponibilidad general</li><li>Calidad, seguridad y cumplimiento de producción</li><li>Disponibilidad en todas las regiones públicas</li><li>Mejoras de rendimiento y escalado para EPS de versiones superiores</li></ul> [Más información](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| Julio de 2021 | <ul><li>Compatibilidad con proxies directos</li><li>Compatibilidad con la puerta de enlace de Log Analytics</li></ul> [Más información](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| Agosto de 2021 | Se ha corregido el problema que permitía las métricas de Azure Monitor como único destino | 1.1.2.0 | 1.10.9.0<sup>1</sup> |
| Septiembre de 2021 | <ul><li>Se ha corregido un problema que provocaba pérdida de datos al reiniciar el agente</li><li>Se ha solucionado la regresión introducida en la versión 1.1.3.1<sup>2</sup> para los servidores Windows Arc</li></ul> | 1.1.3.2 | 1.12.2.0 <sup>2</sup> |  

<sup>1</sup> No use la versión 1.10.7.0 <sup>2</sup> de AMA Linux. Existe una regresión conocida donde no funciona en servidores habilitados para Arc.


## <a name="install-with-azure-portal"></a>Instalación con Azure Portal
Para instalar el agente de Azure Monitor mediante Azure Portal, siga el proceso para [crear una regla de recopilación de datos](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) en Azure Portal. Esto le permite asociar la regla de recopilación de datos con una o varias máquinas virtuales de Azure o servidores habilitados para Azure Arc. El agente se instalará en cualquiera de estas máquinas virtuales que no lo tengan aún.


## <a name="install-with-resource-manager-template"></a>Instalación con una plantilla de Resource Manager
Puede usar las plantillas de Resource Manager para instalar el agente de Azure Monitor en máquinas virtuales de Azure y en servidores habilitados para Azure Arc, así como para crear una asociación con las reglas de recopilación de datos. Debe crear todas las reglas de recopilación de datos antes de crear la asociación.

Obtenga plantillas de ejemplo para instalar el agente y crear la asociación de los siguientes vínculos: 

- [Plantilla para instalar el agente de Azure Monitor (Azure y Azure Arc)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Plantilla para crear una asociación con una regla de recopilación de datos](./resource-manager-data-collection-rules.md)

Instale las plantillas con [cualquier método de implementación para las plantillas de Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md), como los siguientes comandos.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Instalación con PowerShell
Puede instalar el agente de Azure Monitor en máquinas virtuales de Azure y en servidores habilitados para Azure Arc mediante el comando de PowerShell para agregar una extensión de máquina virtual. 

### <a name="azure-virtual-machines"></a>Azure Virtual Machines
Use los siguientes comandos de PowerShell para instalar el agente de Azure Monitor en máquinas virtuales de Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>Servidores habilitados para Azure Arc
Use los siguientes comandos de PowerShell para instalar el agente de Azure Monitor en los servidores habilitados para Azure Arc.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="install-with-azure-cli"></a>Instalación con la CLI de Azure
Puede instalar el agente de Azure Monitor en máquinas virtuales de Azure y en servidores habilitados para Azure Arc mediante el comando de la CLI de Azure para agregar una extensión de máquina virtual. 

### <a name="azure-virtual-machines"></a>Azure Virtual Machines
Use los siguientes comandos de la CLI para instalar el agente de Azure Monitor en máquinas virtuales de Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Servidores habilitados para Azure Arc
Use los siguientes comandos de la CLI para instalar el agente de Azure Monitor en los servidores habilitados para Azure Arc.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="install-with-azure-policy"></a>Instalación con Azure Policy
Use las siguientes directivas e iniciativas de directiva para instalar automáticamente el agente y asociarlo a una regla de recopilación de datos, cada vez que cree una máquina virtual.

### <a name="built-in-policy-initiatives"></a>Iniciativas de directiva integradas
[Vea los requisitos previos para la instalación del agente](azure-monitor-agent-install.md#prerequisites). 

Las iniciativas de directiva para máquinas virtuales Windows y Linux se componen de directivas individuales que:

- Instalan la extensión del agente de Azure Monitor en la máquina virtual.
- Crean e implementan la asociación para vincular la máquina virtual a una regla de recopilación de datos.

![Captura de pantalla parcial de la página Definiciones de Azure Policy que muestra dos iniciativas de directiva integradas para configurar el agente de Azure Monitor.](media/azure-monitor-agent-install/built-in-ama-dcr-initiatives.png)  

### <a name="built-in-policies"></a>Directivas integradas 
Puede optar por usar las directivas individuales de sus respectivas iniciativas de directiva, en función de sus necesidades. Por ejemplo, si solo desea instalar automáticamente el agente, use la primera directiva de la iniciativa como se muestra en el siguiente ejemplo.  

![Captura de pantalla parcial de la página Definiciones de Azure Policy que muestra las directivas que se incluyen en la iniciativa para configurar el agente de Azure Monitor.](media/azure-monitor-agent-install/built-in-ama-dcr-policy.png)  

### <a name="remediation"></a>Corrección
Las iniciativas o directivas se aplicarán a cada máquina virtual a medida que estas se creen. Una [tarea de corrección](../../governance/policy/how-to/remediate-resources.md) implementa las definiciones de directivas de la iniciativa en los *recursos existentes*, de modo que puede configurar el agente de Azure Monitor para los recursos que ya se han creado. 

Al crear la asignación mediante Azure Portal, tiene la opción de crear una tarea de corrección al mismo tiempo. Consulte [Corrección de los recursos no compatibles con Azure Policy](../../governance/policy/how-to/remediate-resources.md) para más información sobre la corrección.

![Captura de pantalla que muestra la corrección de iniciativa para el Azure Monitor agente.](media/azure-monitor-agent-install/built-in-ama-dcr-remediation.png)

## <a name="diagnostic-settings"></a>Configuración de diagnóstico
La [configuración de diagnóstico](../essentials/diagnostic-settings.md) recopila registros y métricas de recursos de Azure y los enruta a varias ubicaciones. Una ubicación típica es un área de trabajo de Log Analytics, que le permite analizar los datos con [consultas de registro](../logs/log-query-overview.md) y [alertas de registro](../alerts/alerts-log.md). Use Azure Policy para crear automáticamente una configuración de diagnóstico cada vez que cree un recurso.


## <a name="next-steps"></a>Pasos siguientes

- [Cree una regla de recopilación de datos](data-collection-rule-azure-monitor-agent.md) para recopilar datos del agente y enviarlos a Azure Monitor.

---
title: Información general del agente de Azure Monitor
description: Información general sobre el agente de Azure Monitor, que recopila datos de supervisión del sistema operativo invitado de máquinas virtuales.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.custom: references_regions
ms.openlocfilehash: b037f82b0d56f09000c5fb9e2814ff80aa6f1a82
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272086"
---
# <a name="azure-monitor-agent-overview"></a>Información general del agente de Azure Monitor
El agente de Azure Monitor (AMA) recopila datos de supervisión del sistema operativo invitado de máquinas virtuales de Azure y los entrega a Azure Monitor. En este artículo se proporciona información general sobre el agente de Azure Monitor, incluido cómo instalarlo y cómo configurar la recopilación de datos.

## <a name="relationship-to-other-agents"></a>Relación con otros agentes
El agente de Azure Monitor reemplaza los siguientes agentes antiguos que Azure Monitor utiliza actualmente para recopilar datos de invitado de máquinas virtuales ([consulte las lagunas conocidas](/azure/azure-monitor/faq#is-the-new-azure-monitor-agent-at-parity-with-existing-agents)):

- [Agente de Log Analytics:](./log-analytics-agent.md) envía datos a un área de trabajo de Log Analytics y admite VM Insights y soluciones de supervisión.
- [Extensión de Diagnostics:](./diagnostics-extension-overview.md) envía datos a las métricas de Azure Monitor (solo en Windows), a Azure Event Hubs y a Azure Storage.
- [Agente de Telegraf:](../essentials/collect-custom-metrics-linux-telegraf.md) envía datos a las métricas de Azure Monitor (solo en Linux).

Además de consolidar esta funcionalidad en un solo agente, el agente de Azure Monitor proporciona las siguientes ventajas frente a los ya existentes:

- **Ámbito de supervisión:** configure de forma centralizada la recopilación de diferentes conjuntos de datos procedentes de diferentes conjuntos de VM.
- **Hospedaje múltiple en Linux:** envíe datos de VM Linux a varias áreas de trabajo.
- **Filtrado de eventos de Windows:** use consultas de XPATH para filtrar los eventos de Windows que se recopilan.
- **Administración de extensiones mejorada:** el agente de Azure Monitor utiliza un nuevo método de control de la extensibilidad que es más transparente y controlable que los módulos de administración y los complementos de Linux presentes en los agentes de Log Analytics actuales.

### <a name="current-limitations"></a>Limitaciones actuales
En comparación con los ya existentes, este nuevo agente aún no tiene paridad completa.
- **Comparación con agentes de Log Analytics (MMA/OMS):**
    - Actualmente no se admiten todas las soluciones de Log Analytics. Consulte [qué se admite](#supported-services-and-features).
    - No se admiten instancias de Azure Private Link.
    - No se admite la recopilación de registros personalizados ni registros de IIS.
- **Comparación con las extensiones de Azure Diagnostics (WAD/LAD):**
    - No se admiten Event Hubs ni cuentas de Storage como destinos.

### <a name="changes-in-data-collection"></a>Cambios en la recopilación de datos
Los métodos para definir la recopilación de datos de los agentes existentes son distintos entre sí. Cada uno de ellos tiene desafíos que se abordan con el agente de Azure Monitor.

- El agente de Log Analytics obtiene su configuración de un área de trabajo de Log Analytics. Esto es fácil de configurar de forma centralizada, pero es difícil establecer definiciones independientes para diferentes máquinas virtuales. Solo puede enviar datos a un área de trabajo de Log Analytics.
- La extensión de Diagnostics tiene una configuración para cada máquina virtual. Resulta fácil establecer definiciones independientes para diferentes máquinas virtuales, pero difícil de administrar de forma centralizada. Solo puede enviar datos a métricas de Azure Monitor, Azure Event Hubs o Azure Storage. En el caso de los agentes de Linux, se necesita el agente de Telegraf de código abierto para enviar datos a métricas de Azure Monitor.

El agente de Azure Monitor utiliza las [reglas de recopilación de datos](data-collection-rule-overview.md) para configurar los datos que se van a recopilar de cada agente. Las reglas de recopilación de datos permiten la capacidad de administración de la configuración de la recopilación a escala y, al mismo tiempo, habilitan configuraciones únicas con ámbito para subconjuntos de máquinas. Son independientes del área de trabajo y de la máquina virtual, por lo que se pueden definir una vez y reutilizar en múltiples máquinas y entornos. Consulte [Configuración de la recopilación de datos para el agente de Azure Monitor](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-the-azure-monitor-agent"></a>¿Debo cambiar al agente de Azure Monitor?
El agente de Azure Monitor reemplaza los [agentes antiguos de Azure Monitor](agents-overview.md). Para iniciar la transición de sus VM de los agentes actuales al nuevo, tenga presentes los siguientes factores:

- **Requisitos de entorno:** el agente de Azure Monitor admite actualmente [estos sistemas operativos](./agents-overview.md#supported-operating-systems). Lo más probable es que en este nuevo agente se proporcione compatibilidad con las versiones futuras del sistema operativo, con el entorno y requisitos de red. 
 
  Evalúe si el agente de Azure Monitor admite su entorno. Si no es así, tendrá que seguir usando el agente actual. Si el agente de Azure Monitor admite su entorno actual, considere la posibilidad de realizar la transición a este.
- **Requisitos de características actuales y nuevas:** el agente de Azure Monitor presenta varias funcionalidades nuevas, como el filtrado, el control ("scoping") y el hospedaje múltiple. Sin embargo, todavía no es equiparable a los agentes actuales en lo que respecta a otra funcionalidad, como la recopilación personalizada de registros y la integración con todas las soluciones. [(Consulte las soluciones en la versión preliminar).](/azure/azure-monitor/faq#which-log-analytics-solutions-are-supported-on-the-new-azure-monitor-agent) 
 
  La mayoría de las nuevas funcionalidades de Azure Monitor solo estarán disponibles con su agente. Con el tiempo, habrá más funcionalidad disponible solo en el nuevo agente. Considere si el agente de Azure Monitor tiene las características que necesita y si hay algunas de las que pueda prescindir temporalmente para obtener otras importantes en el nuevo agente. 
  
  Si el agente de Azure Monitor tiene todas las funcionalidades principales que necesita, considere la posibilidad de realizar la transición a este. Si hay características críticas que necesita, continúe con el agente actual hasta que el agente de Azure Monitor alcance la paridad.
- **Tolerancia al reprocesamiento:** si va a configurar un nuevo entorno con recursos como scripts de implementación y plantillas de incorporación, evalúe el esfuerzo que conlleva. Si la configuración va a implicar una cantidad considerable de trabajo, plantéese la posibilidad de configurar su nuevo entorno con el nuevo agente, puesto que ya tiene disponibilidad general. 
 
  Se publicará una fecha de desuso para los agentes de Log Analytics en agosto de 2021. Se prestará asistencia con relación a los agentes actuales durante varios años una vez que se inicie el desuso.

## <a name="supported-resource-types"></a>Tipos de recurso admitidos
Actualmente se admiten máquinas virtuales de Azure, conjuntos de escalado de máquinas virtuales y servidores compatibles con Azure Arc. En este momento no se admiten Azure Kubernetes Service ni otros tipos de recursos de proceso.

## <a name="supported-regions"></a>Regiones admitidas
El agente de Azure Monitor está disponible en todas las regiones públicas que admiten Log Analytics. En este momento no se admiten regiones ni nubes gubernamentales.
## <a name="supported-services-and-features"></a>Servicios y características admitidos
En la siguiente tabla se muestra la compatibilidad actual del agente de Azure Monitor con otros servicios de Azure.

| Servicio de Azure | Compatibilidad actual | Más información |
|:---|:---|:---|
| [Azure Security Center](../../security-center/security-center-introduction.md) | Versión preliminar privada | [Vínculo de registro](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | Versión preliminar privada | [Vínculo de registro](https://aka.ms/AMAgent) |

En la siguiente tabla se muestra la compatibilidad actual del agente de Azure Monitor con características de Azure Monitor.

| Característica de Azure Monitor | Compatibilidad actual | Más información |
|:---|:---|:---|
| [VM Insights](../vm/vminsights-overview.md) | Versión preliminar privada  | [Vínculo de registro](https://forms.office.com/r/jmyE821tTy) |
| [Estado de invitado de VM Insights](../vm/vminsights-health-overview.md) | Versión preliminar pública | Disponible solo en el nuevo agente |
| [SQL Insights](../insights/sql-insights-overview.md) | Versión preliminar pública | Disponible solo en el nuevo agente |

En la siguiente tabla se muestra la compatibilidad actual del agente de Azure Monitor con soluciones de Azure.

| Solución | Compatibilidad actual | Más información |
|:---|:---|:---|
| [Seguimiento de cambios](../../automation/change-tracking/overview.md) | Se admite como supervisión de la integridad de archivos en la versión preliminar privada de Azure Security Center.  | [Vínculo de registro](https://aka.ms/AMAgent) |
| [Administración de actualizaciones](../../automation/update-management/overview.md) | Use la versión 2 de Update Management (versión preliminar privada) que no requiere un agente. | [Vínculo de registro](https://www.yammer.com/azureadvisors/threads/1064001355087872) |

## <a name="coexistence-with-other-agents"></a>Coexistencia con otros agentes
El agente de Azure Monitor puede coexistir con los agentes existentes para que pueda seguir usando su funcionalidad existente durante la evaluación o la migración. Esta funcionalidad es especialmente importante debido a las limitaciones para admitir las soluciones existentes. Tenga cuidado al recopilar datos duplicados, ya que podría sesgar los resultados de la consulta y generar más cargos por la ingesta y retención de datos. 

Por ejemplo, VM Insights usa el agente de Log Analytics para enviar datos de rendimiento a un área de trabajo de Log Analytics. También puede haber configurado el área de trabajo para recopilar de los agentes eventos de Windows y de Syslog. Si instala el agente de Azure Monitor y crea una regla de recopilación de datos para estos mismos eventos y datos de rendimiento, se generarán datos duplicados.

Por lo tanto, asegúrese de que no recopila los mismos datos de ambos agentes. Si está seguro, compruebe que vayan a destinos independientes.

## <a name="costs"></a>Costos
El agente de Azure Monitor no cuesta nada, pero puede incurrir en cargos por los datos ingeridos. Para obtener información sobre la recopilación y retención de datos de Log Analytics y conocer las métricas de clientes, consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="data-sources-and-destinations"></a>Orígenes y destinos de los datos
En la siguiente tabla se enumeran los tipos de datos que se pueden recopilar actualmente con el agente de Azure Monitor usando reglas de recopilación y dónde se pueden enviar dichos datos. En [¿Qué supervisa Azure Monitor?](../monitor-reference.md) puede consultar una lista con información detallada, soluciones y otros servicios que usan el agente de Azure Monitor para recopilar otros tipos de datos.

El agente de Azure Monitor envía datos a métricas de Azure Monitor o a un área de trabajo de Log Analytics que admite registros de Azure Monitor.

| Origen de datos | Destinations | Descripción |
|:---|:---|:---|
| Rendimiento        | Métricas de Azure Monitor<sup>1</sup><br>Área de trabajo de Log Analytics | Valores numéricos que miden el rendimiento de diferentes aspectos del sistema operativo y las cargas de trabajo. |
| registros de eventos de Windows; | Área de trabajo de Log Analytics | Información enviada al sistema de registro de eventos de Windows |
| syslog             | Área de trabajo de Log Analytics | Información enviada al sistema de registro de eventos de Windows. |

<sup>1</sup> Actualmente hay una limitación en el agente Azure Monitor para Linux. No está permitido usar métricas de Azure Monitor como *único* destino. Sí que es posible usarlas junto con registros de Azure Monitor. Esta limitación se solucionará en la siguiente actualización de la extensión.

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos
En [Sistemas operativos admitidos](agents-overview.md#supported-operating-systems) puede consultar una lista de las versiones de sistemas operativos Windows y Linux que son compatibles actualmente con el agente de Azure Monitor.

## <a name="security"></a>Seguridad
El agente de Azure Monitor no requiere ninguna clave, pero en su lugar requiere una [identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Para poder implementar el agente, en cada máquina virtual debe tener habilitada una identidad administrada asignada por el sistema.

## <a name="networking"></a>Redes
El agente de Azure Monitor admite etiquetas de servicio de Azure. Se requieren las etiquetas AzureMonitor y AzureResourceManager. El agente de Azure Monitor no funciona todavía con ámbitos de Private Link de Azure Monitor. Si la máquina se conecta mediante un servidor proxy para comunicarse a través de Internet, consulte los siguientes requisitos para conocer la configuración de red necesaria.

### <a name="proxy-configuration"></a>Configuración de proxy

Las extensiones del agente de Azure Monitor para Windows y Linux pueden comunicarse a través de un servidor proxy o una puerta de enlace de Log Analytics a Azure Monitor mediante el protocolo HTTPS. Úselo para máquinas virtuales de Azure, conjuntos de escalado de máquinas virtuales de Azure y Azure Arc para servidores. Use los ajustes de extensiones a efectos de configuración como se describe en los siguientes pasos. Se admite autenticación tanto anónima como básica usando un nombre de usuario y una contraseña.

1. Use este diagrama de flujo para determinar primero los valores de los parámetros *setting* y *protectedSetting*.

   ![Diagrama de flujo para determinar los valores de los parámetros setting y protectedSetting al habilitar la extensión.](media/azure-monitor-agent-overview/proxy-flowchart.png)


1. Una vez determinados los valores de los parámetros *setting* y *protectedSetting*, proporcione estos parámetros adicionales al implementar el agente de Azure Monitor usando comandos de PowerShell. Los siguientes ejemplos corresponden a máquinas virtuales de Azure:

    | Parámetro | Value |
    |:---|:---|
    | SettingString | Objeto JSON del diagrama de flujo anterior convertido en cadena. Omítase si no es aplicable. Ejemplo: {"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}}. |
    | ProtectedSettingString | Objeto JSON del diagrama de flujo anterior convertido en cadena. Omítase si no es aplicable. Ejemplo: {"proxy":{"username": "[username]","password": "[password]"}}. |


# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

---

## <a name="next-steps"></a>Pasos siguientes

- [Instale el agente de Azure Monitor](azure-monitor-agent-install.md) en máquinas virtuales Windows y Linux.
- [Cree una regla de recopilación de datos](data-collection-rule-azure-monitor-agent.md) para recopilar datos del agente y enviarlos a Azure Monitor.

---
title: Introducción a los servidores habilitados para Azure Arc
description: Aprenda a usar los servidores habilitados para Azure Arc para administrar servidores hospedados fuera de Azure como un recurso de Azure.
ms.date: 09/30/2021
ms.topic: overview
ms.openlocfilehash: c064abb4258f36207e8bd4f02f7cb68d8ce1fce1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355393"
---
# <a name="what-is-azure-arc-enabled-servers"></a>¿Qué son los servidores habilitados para Azure Arc?

Los servidores habilitados para Azure Arc permiten administrar servidores físicos y máquinas virtuales Windows y Linux hospedados *fuera* de Azure, en la red corporativa o en otro proveedor de servicios en la nube. Esta experiencia de administración está diseñada para ser coherente con el modo de administración de las máquinas virtuales nativas de Azure. Cuando una máquina híbrida se conecta a Azure, se convierte en una máquina conectada y se trata como un recurso de Azure. Cada máquina conectada tiene un identificador de recurso que permite incluirla en un grupo de recursos. Ahora, puede aprovechar las ventajas de las construcciones estándar de Azure, como Azure Policy y la aplicación de etiquetas. Los proveedores de servicios que administran la infraestructura local de un cliente pueden administrar las máquinas híbridas en los diferentes entornos del cliente del mismo modo que lo hacen actualmente con los recursos nativos de Azure mediante [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md).

Para ofrecer esta experiencia con las máquinas híbridas, debe instalar el agente de Azure Connected Machine en cada máquina. Este agente no proporciona ninguna otra funcionalidad y no reemplaza al [agente de Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). El agente de Log Analytics para Windows y Linux es necesario cuando:

* Desee supervisar de forma proactiva el sistema operativo y las cargas de trabajo que se ejecutan en la máquina.
* Lo administre mediante soluciones o runbooks de Automation, como Update Management.
* Utilice otros servicios de Azure, como [Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-cloud-operations"></a>Operaciones en la nube admitidas 

Si conecta la máquina a servidores habilitados para Azure Arc, podrá realizar las funciones operativas que se describen en la tabla siguiente.

|Función operativa |Descripción | 
|--------------------|------------|
|**Control** ||
| Azure Policy |Asigne las [configuraciones de invitado de Azure Policy](../../governance/policy/concepts/guest-configuration.md) para auditar la configuración dentro de la máquina. Para saber cuánto cuesta utilizar las directivas de configuración de invitado de Azure Policy con servidores habilitados para Arc, consulte la [guía de precios](https://azure.microsoft.com/pricing/details/azure-policy/) de Azure Policy.|
|**Protección** ||
| Azure Security Center | Proteja servidores que no son de Azure con [Microsoft Defender para punto de conexión](/microsoft-365/security/defender-endpoint), incluido mediante [Azure Defender](../../security-center/defender-for-servers-introduction.md), para la detección de amenazas, para la administración de vulnerabilidades y para supervisar proactivamente las posibles amenazas de seguridad. Azure Security Center presenta las alertas y las sugerencias de corrección de las amenazas detectadas. |
| Azure Sentinel | Las máquinas conectadas a servidores habilitados para Arc se pueden [configurar con Azure Sentinel](scenario-onboard-azure-sentinel.md) para recopilar eventos relacionados con la seguridad y relacionarlos con otros orígenes de datos. |
|**Configuración** ||
| Azure Automation |Automatice tareas de administración frecuentes y lentas mediante PowerShell y [runbooks](../../automation/automation-runbook-execution.md) de Python.<br> Evalúe los cambios de configuración del software instalado, los servicios de Microsoft, los archivos y el Registro de Windows, y los demonios de Linux utilizando [Seguimiento de cambios e inventario](../../automation/change-tracking/overview.md).<br> Utilice [Update Management](../../automation/update-management/overview.md) para administrar las actualizaciones del sistema operativo de los servidores Windows y Linux. |
| Azure Automanage (versión preliminar) | Automatice la incorporación y configuración de un conjunto de servicios de Azure cuando utilice [Automanage para máquinas: servidores habilitados para Arc](../../automanage/automanage-arc.md). |
| Extensiones de máquina virtual | Proporciona tareas de automatización y configuración posteriores a la implementación [utilizando extensiones de máquina virtual de servidores habilitados para Arc](manage-vm-extensions.md) que son compatibles con máquinas Windows que no son de Azure o máquinas Linux. |
|**Supervisión**|
| Azure Monitor | Supervise el rendimiento del sistema operativo invitado de la máquina conectada y detecte los componentes de la aplicación para supervisar sus procesos y dependencias con otros recursos mediante [Insights para VM](../../azure-monitor/vm/vminsights-overview.md). Recopile otros datos de registro del sistema operativo o las cargas de trabajo que se ejecutan en la máquina, como los datos de rendimiento y los eventos, con el [agente de Log Analytics](../../azure-monitor/agents/agents-overview.md#log-analytics-agent). Estos datos se guardan en un [área de trabajo de Log Analytics](../../azure-monitor/logs/design-logs-deployment.md). |

> [!NOTE]
> Actualmente, no se puede habilitar Update Management de Azure Automation directamente desde un servidor habilitado para Azure Arc. Consulte [Habilitación de Update Management desde una cuenta de Automation](../../automation/update-management/enable-from-automation-account.md) para saber más sobre los requisitos y cómo habilitarlo para el servidor.

Los datos de registro que se recopilan y se almacenan en un área de trabajo de Log Analytics desde la máquina híbrida ahora contienen propiedades específicas de la máquina, como un identificador de recurso, para permitir el acceso a los registros en el [contexto del recurso](../../azure-monitor/logs/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

Para más información sobre el uso de los servidores habilitados para Azure Arc en la implementación de servicios de supervisión, seguridad y actualización de Azure en entornos híbridos y multinube, vea el vídeo siguiente.

> [!VIDEO https://www.youtube.com/embed/mJnmXBrU1ao]

## <a name="supported-regions"></a>Regiones admitidas

Para obtener una lista definitiva de las regiones que admiten los servidores habilitados para Azure Arc, consulte la página de [productos de Azure por región](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

En la mayoría de los casos, la ubicación que seleccione al crear el script de instalación debe ser la región de Azure más cercana geográficamente a la ubicación de la máquina. Los datos en reposo se almacenan en la ubicación geográfica de Azure que contiene la región que especifique, lo que también puede afectar a su elección de región si tiene requisitos de residencia de datos. Si la región de Azure a la que está conectada la máquina experimente una interrupción, la máquina conectada no se verá afectada, aunque es posible que las operaciones de administración que usan Azure no se puedan completar. Si se produce una interrupción regional y se dispone de varias ubicaciones que admiten un servicio con redundancia geográfica, es mejor conectar las máquinas de cada ubicación a otra región de Azure.

La siguiente información de metadatos sobre la máquina conectada se recopila y almacena en la región donde se configura el recurso de la máquina de Azure Arc:

- Nombre y versión del sistema operativo
- Nombre del equipo
- Nombre de dominio completo (FQDN) del equipo
- Versión del agente Connected Machine

Por ejemplo, si la máquina se registra en Azure Arc en la región Este de EE. UU., estos datos se almacenan en la región de EE. UU.

### <a name="supported-environments"></a>Entornos admitidos

Los servidores habilitados para Azure Arc admiten la administración de servidores físicos y máquinas virtuales hospedados *fuera* de Azure. Para obtener detalles concretos sobre qué entornos de nube híbrida que hospedan máquinas virtuales se admiten, vea [Requisitos previos del agente de Azure Connected Machine](agent-overview.md#supported-environments).

> [!NOTE]
> Los servidores habilitados para Azure Arc no están diseñados para la administración de máquinas virtuales que se ejecutan en Azure, ni la permiten.

### <a name="agent-status"></a>Estado del agente

El agente Connected Machine envía un mensaje de latido al servicio cada 5 minutos. Si el servicio deja de recibir estos mensajes de latido de una máquina, esa máquina se considera sin conexión y el estado cambiará automáticamente a **Desconectado** en el portal en un plazo de 15 a 30 minutos. Al recibir un mensaje de latido subsiguiente del agente de Connected Machine, su estado se cambiará automáticamente a **Conectado**.

## <a name="next-steps"></a>Pasos siguientes

* Antes de evaluar o habilitar los servidores habilitados para Azure Arc en varias máquinas híbridas, consulte la [introducción al agente de Connected Machine](agent-overview.md) para conocer los requisitos y detalles técnicos del agente y los métodos de implementación.

* Examine la [guía de planeamiento e implementación](plan-at-scale-deployment.md) para planear la implementación de servidores habilitados para Azure Arc a cualquier escala e implementar la administración y supervisión centralizadas.

---
title: Supervisión y protección de máquinas virtuales con servicios nativos de Azure
description: Obtenga información sobre cómo integrar e implementar herramienta nativas de Microsoft Azure para supervisar y administrar las cargas de trabajo de Azure VMware Solution.
ms.topic: how-to
ms.date: 08/15/2021
ms.openlocfilehash: 36bdb029f61a707f83ae42e66f399f376db23025
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323878"
---
# <a name="monitor-and-protect-vms-with-azure-native-services"></a>Supervisión y protección de máquinas virtuales con servicios nativos de Azure

Los servicios nativos de Microsoft Azure permiten supervisar, administrar y proteger las máquinas virtuales (VM) en un entorno híbrido (Azure, Azure VMware Solution y local). En este artículo, integrará los servicios nativos de Azure en la nube privada de Azure VMware Solution. También aprenderá a usar las herramientas para administrar las máquinas virtuales a lo largo de su ciclo de vida. 

Los servicios nativos de Azure que puede integrar con Azure VMware Solution incluyen:

- **Azure Arc** lleva la administración de Azure a cualquier infraestructura, incluidos Azure VMware Solution, entornos locales u otras plataformas en la nube. Los [servidores habilitados para Azure Arc](../azure-arc/servers/overview.md) permiten administrar servidores físicos y máquinas virtuales Windows y Linux hospedados *fuera* de Azure, en la red corporativa o en otro proveedor de nube. Puede adjuntar un clúster de Kubernetes hospedado en el entorno de Azure VMware Solution mediante [Kubernetes habilitado para Azure Arc](../azure-arc/kubernetes/overview.md). 

- **Azure Monitor** recopila y analiza los datos de telemetría de sus entornos locales y de nube, y actúa sobre dichos datos. No requiere ninguna implementación.  Puede supervisar el rendimiento del sistema operativo invitado para descubrir y asignar dependencias de aplicaciones para máquinas virtuales de Azure VMware Solution o locales. El área de trabajo de Log Analytics en Azure Monitor habilita la recopilación de registros y contadores de rendimiento mediante el agente de Log Analytics o extensiones. 

   Con Azure Monitor, puede recopilar datos de distintos [orígenes para supervisarlos y analizarlos](../azure-monitor/agents/data-sources.md) y diferentes tipos de [datos para analizarlos, visualizarlos y generar alertas sobre ellos](../azure-monitor/data-platform.md). También puede crear reglas de alertas para identificar problemas en su entorno, como, por ejemplo, uso elevado de recursos, revisiones que faltan, espacio en disco bajo y latido de las máquinas virtuales. Puede establecer una respuesta automatizada a eventos detectados enviando una alerta a las herramientas de Administración de servicios de TI (ITSM). La notificación de detección de alertas también se puede enviar por correo electrónico.

- **Azure Security Center** fortalece la seguridad de los centros de datos y proporciona protección contra amenazas avanzada en las cargas de trabajo híbridas tanto en la nube como a nivel local. Además, valora la vulnerabilidad de las máquinas virtuales de Azure VMware Solution, genera alertas cuando es necesario y las reenvía a Azure Monitor para que las resuelva. Por ejemplo, se evalúan las revisiones del sistema operativo que faltan, los errores de configuración de seguridad y [Endpoint Protection](../security-center/security-center-services.md). También puede definir directivas de seguridad en [Azure Security Center](azure-security-integration.md).

- **Azure Update Management** administra las actualizaciones del sistema operativo de las máquinas Windows y Linux en un entorno híbrido en Azure Automation. Supervisa el cumplimiento de la aplicación de revisiones y reenvía alertas de desviación de la aplicación de revisiones a Azure Monitor para su corrección. Azure Update Management debe conectarse al área de trabajo de Log Analytics para usar datos almacenados con el fin de evaluar el estado de las actualizaciones en las máquinas virtuales.

- El **áreas de trabajo de Log Analytics** almacena datos de registro. Cada área de trabajo tiene su propio repositorio de datos y configuración para almacenar datos. Puede supervisar las máquinas virtuales de Azure VMware Solution mediante el agente de Log Analytics. Las máquinas conectadas al área de trabajo de Log Analytics usan el [agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md) para recopilar datos sobre los cambios en el software instalado, los servicios de Microsoft, los archivos y el Registro de Windows y los demonios de Linux de los servidores supervisados. Cuando los datos están disponibles, el agente los envía a Azure Monitor para su procesamiento. Los registros de Azure Monitor aplican la lógica a los datos recibidos, los anotan y hacen que estén disponibles para el análisis. Use la [compatibilidad con las extensiones de máquina virtual ](../azure-arc/servers/manage-vm-extensions.md) de los servidores habilitados para Azure Arc para implementar agentes de Log Analytics en máquinas virtuales. 



## <a name="benefits"></a>Ventajas

- Los servicios nativos de Azure se pueden usar para administrar las máquinas virtuales en un entorno híbrido (Azure, Azure VMware Solution y local).
- Supervisión y visibilidad integradas de las máquinas virtuales de Azure, Azure VMware Solution y locales.
- Con Azure Update Management en Azure Automation, puede administrar las actualizaciones del sistema operativo de las máquinas Windows y Linux. 
- Azure Security Center proporciona Advanced Threat Protection, incluyendo lo siguiente:
    - Supervisión de la integridad de los archivos
    - Alertas de seguridad sin archivos
    - Evaluación de revisiones del sistema operativo
    - Evaluación de configuraciones de seguridad incorrectas
    - Evaluación de EndPoint Protection 
- Implemente con facilidad el agente de Log Analytics mediante la compatibilidad con la extensión de máquina virtual de servidores habilitados para Azure Arc para máquinas virtuales nuevas y existentes. 
- El área de trabajo de Log Analytics en Azure Monitor habilita la recopilación de registros y contadores de rendimiento mediante el agente de Log Analytics o extensiones. Recopile datos y registros en un único punto y presente dichos datos en diferentes servicios nativos de Azure. 
- Entre las ventajas agregadas de Azure Monitor se incluyen: 
    - Supervisión fluida 
    - Mejor visibilidad de la infraestructura 
    - Notificaciones instantáneas 
    - Resolución automática 
    - Rentabilidad 


## <a name="topology"></a>Topología

En el diagrama se muestra la arquitectura de supervisión integrada de las máquinas virtuales de Azure VMware Solution.

:::image type="content" source="media/concepts/integrated-azure-monitoring-architecture.png" alt-text="Diagrama que muestra la arquitectura de supervisión integrada de Azure." border="false":::

El agente de Log Analytics habilita la recopilación de datos de registro de Azure, Azure VMware Solution y las máquinas virtuales locales. Los datos de registro se envían a los registros de Azure Monitor y se almacenan en un área de trabajo de Log Analytics. Puede implementar el agente de Log Analytics mediante la [compatibilidad con la extensión de máquina virtual](../azure-arc/servers/manage-vm-extensions.md) de servidores habilitados para Arc para máquinas virtuales nuevas y existentes. 

Una vez que el área de trabajo de Log Analytics recopila los registros, puede configurarla con Azure Security Center para valorar el estado de vulnerabilidad de las máquinas virtuales de las máquinas virtuales de Azure VMware Solution y generar una alerta en caso de que exista alguna vulnerabilidad crítica.  Por ejemplo, se evalúan las revisiones del sistema operativo que faltan, los errores de configuración de seguridad y [Endpoint Protection](../security-center/security-center-services.md).

Puede configurar el área de trabajo de Log Analytics con Azure Sentinel para detección de alertas, visibilidad de amenazas, búsqueda y respuesta a amenazas. En el diagrama anterior, Azure Security Center se conecta a Azure Sentinel mediante el conector de Azure Security Center. Azure Security Center reenvía la vulnerabilidad del entorno a Azure Sentinel para crear un incidente y asignarlo junto con otras amenazas. También puede crear la consulta de reglas programadas para detectar la actividad no deseada y convertirla en incidentes.


## <a name="before-you-start"></a>Antes de comenzar

Si no está familiarizado con Azure o con ninguno de los servicios mencionados anteriormente, consulte los artículos siguientes:

- [Introducción a la autenticación de cuentas de Automation](../automation/automation-security-overview.md)
- [Diseño de la implementación de registros de Azure Monitor](../azure-monitor/logs/design-logs-deployment.md) y [Azure Monitor](../azure-monitor/overview.md)
- [Planeamiento](../security-center/security-center-planning-and-operations-guide.md) y [Plataformas compatibles](../security-center/security-center-os-coverage.md) para Azure Security Center
- [Información general sobre la habilitación de Azure Monitor para VM](../azure-monitor/vm/vminsights-enable-overview.md)
- [¿Qué son los servidores habilitados para Azure Arc?](../azure-arc/servers/overview.md) y [¿Qué es Kubernetes habilitado para Azure Arc?](../azure-arc/kubernetes/overview.md)
- [Introducción a Update Management](../automation/update-management/overview.md)



## <a name="enable-azure-update-management"></a>Habilitación de Azure Update Management

[Azure Update Management](../automation/update-management/overview.md) en Azure Automation administra las actualizaciones del sistema operativo de las máquinas Windows y Linux en un entorno híbrido. Supervisa el cumplimiento de la aplicación de revisiones y reenvía alertas de desviación de la aplicación de revisiones a Azure Monitor para su corrección. Azure Update Management debe conectarse al área de trabajo de Log Analytics para usar datos almacenados con el fin de evaluar el estado de las actualizaciones en las máquinas virtuales.

1. Antes de poder agregar el área de trabajo de Log Analytics a Azure Update Management, primero debe [crear una cuenta de Azure Automation](../automation/automation-create-standalone-account.md). 

   >[!TIP]
   >Puede [usar una plantilla de Azure Resource Manager (ARM) para crear una cuenta de Azure Automation](../automation/quickstart-create-automation-account-template.md). El uso de una plantilla de ARM requiere menos pasos que otros métodos de implementación.

1. [Creación de un área de trabajo de Log Analytics en Azure Portal](../azure-monitor/logs/quick-create-workspace.md) Si lo prefiere, también puede crear un área de trabajo a través de la [CLI](../azure-monitor/logs/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) o una [plantilla de Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md).

1. [Habilite Update Management desde una cuenta de Automation](../automation/update-management/enable-from-automation-account.md). En el proceso, vinculará el área de trabajo de Log Analytics con la cuenta de Automation. 
 
1. Una vez que haya habilitado Update Management, puede [implementar actualizaciones en máquinas virtuales y revisar los resultados](../automation/update-management/deploy-updates.md). 


## <a name="enable-azure-security-center"></a>Habilitación de Azure Security Center

Valorará la vulnerabilidad de las máquinas virtuales de Azure VMware Solution y generará alertas en caso necesario. Estas alertas de seguridad se pueden reenviar a Azure Monitor para su resolución. Para más información, vea [Características admitidas de VM](../security-center/security-center-services.md).

Azure Security Center ofrece muchas características, entre las que se incluyen:
- Supervisión de la integridad de los archivos
- Detección de ataques sin archivos
- Evaluación de revisiones del sistema operativo 
- Evaluación de configuraciones de seguridad incorrectas
- Evaluación de EndPoint Protection

>[!NOTE]
>Azure Security Center es una herramienta preconfigurada que no requiere implementación, pero deberá habilitarla en Azure Portal. 


1. [Agregue máquinas virtuales de Azure VMware Solution a Security Center](azure-security-integration.md#add-azure-vmware-solution-vms-to-security-center). 

2. [Habilite Azure Defender en Security Center](../security-center/enable-azure-defender.md). Security Center evalúa las máquinas virtuales en busca de posibles problemas de seguridad. También proporciona [recomendaciones de seguridad](../security-center/security-center-recommendations.md) en la pestaña Información general. 

3. [Defina directivas de seguridad](../security-center/tutorial-security-policy.md) en Azure Security Center. 

Para más información, consulte [Integración de Azure Security Center con Azure VMware Solution](azure-security-integration.md).



## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Incorporación de máquinas virtuales a servidores habilitados para Azure Arc

Extienda la administración de Azure a cualquier infraestructura, incluidos Azure VMware Solution, entornos locales u otras plataformas de nube.  Para información sobre la habilitación de servidores habilitados para Azure Arc para varias máquinas virtuales Windows o Linux, vea [Conexión de máquinas híbridas a Azure a gran escala](../azure-arc/servers/onboard-service-principal.md).



## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Incorporación de clústeres híbridos de Kubernetes con Kubernetes habilitado para Azure Arc


Asocie un clúster de Kubernetes hospedado en el entorno de Azure VMware Solution mediante Kubernetes habilitado para Azure Arc. Para más información, consulte [Creación de una entidad de servicio de incorporación habilitada para Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="deploy-the-log-analytics-agent"></a>Implementación del agente de Log Analytics

Supervise las máquinas virtuales de Azure VMware Solution mediante el agente de Log Analytics. Las máquinas conectadas al área de trabajo de Log Analytics usan el [agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md) para recopilar datos sobre los cambios en el software instalado, los servicios de Microsoft, los archivos y el Registro de Windows y los demonios de Linux de los servidores supervisados. Cuando los datos están disponibles, el agente los envía a Azure Monitor para su procesamiento. Los registros de Azure Monitor aplican la lógica a los datos recibidos, los anotan y hacen que estén disponibles para el análisis.

Implemente el agente de Log Analytics con [compatibilidad con la extensión de máquina virtual de servidores habilitados para Azure Arc](../azure-arc/servers/manage-vm-extensions.md).




## <a name="enable-azure-monitor"></a>Habilitación de Azure Monitor

Puede recopilar datos de distintos [orígenes para supervisarlos y analizarlos](../azure-monitor/agents/data-sources.md) y diferentes tipos de [datos para analizarlos, visualizarlos y generar alertas sobre ellos](../azure-monitor/data-platform.md). También puede crear reglas de alertas para identificar problemas en su entorno, como, por ejemplo, uso elevado de recursos, revisiones que faltan, espacio en disco bajo y latido de las máquinas virtuales. Puede establecer una respuesta automatizada a eventos detectados enviando una alerta a las herramientas de Administración de servicios de TI (ITSM). La notificación de detección de alertas también se puede enviar por correo electrónico.

Supervise el rendimiento del sistema operativo invitado para detectar y asignar dependencias de aplicaciones en máquinas virtuales de Azure VMware Solution o locales. El área de trabajo de Log Analytics en Azure Monitor habilita la recopilación de registros y contadores de rendimiento mediante el agente de Log Analytics o extensiones. 


1. [Diseñe la implementación de registros de Azure Monitor](../azure-monitor/logs/design-logs-deployment.md).

1. [Información general sobre la habilitación de Azure Monitor para VM](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Configure el área de trabajo de Log Analytics para Azure Monitor para VM](../azure-monitor/vm/vminsights-configure-workspace.md).

1. Cree reglas de alertas para identificar problemas en su entorno:

   - [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

   - [Creación, visualización y administración de alertas de registro mediante Azure Monitor](../azure-monitor/alerts/alerts-log.md).

   - [Reglas de acción](../azure-monitor/alerts/alerts-action-rules.md) para establecer acciones y notificaciones automatizadas.

   - [Conexión de Azure a las herramientas de ITSM mediante el Conector de Administración de servicios de TI](../azure-monitor/alerts/itsmc-overview.md).


## <a name="next-steps"></a>Pasos siguientes

Ahora que ya conoce los conceptos sobre red e interconectividad de Azure VMware Solution, puede que le interese aprender sobre la [integración de Azure Security Center con Azure VMware Solution](azure-security-integration.md).

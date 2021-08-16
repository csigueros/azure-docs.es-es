---
title: Integración e implementación de servicios nativos de Azure
description: Obtenga información sobre cómo integrar e implementar herramienta nativas de Microsoft Azure para supervisar y administrar las cargas de trabajo de Azure VMware Solution.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: e7af5617ee62f451f57daf806f70c7c433e40612
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758303"
---
# <a name="integrate-and-deploy-azure-native-services"></a>Integración e implementación de servicios nativos de Azure

Los servicios nativos de Microsoft Azure permiten supervisar, administrar y proteger las máquinas virtuales (VM) en un entorno híbrido (Azure, Azure VMware Solution y local). Para más información, vea [Características admitidas de VM](../security-center/security-center-services.md).

Los servicios nativos de Azure que puede integrar con Azure VMware Solution incluyen:

- **Área de trabajo de Log Analytics:** cada área de trabajo tiene su propio repositorio de datos y configuración para almacenar datos de registro. Los orígenes de datos y las soluciones se configuran para almacenar sus datos en un área de trabajo específica. Implemente con facilidad el agente de Log Analytics mediante la compatibilidad con la extensión de máquina virtual de servidores habilitados para Azure Arc para máquinas virtuales nuevas y existentes. 
- **Azure Security Center:** sistema unificado de administración de la seguridad de infraestructura que fortalece el nivel de seguridad de sus centros de datos y proporciona protección contra amenazas avanzada en las cargas de trabajo híbridas locales o en la nube. 
- **Azure Sentinel** es una solución de administración de eventos e información de seguridad (SIEM), nativa de la nube. Proporciona análisis de seguridad, detección de alertas y respuesta automática a amenazas en un entorno. Azure Sentinel se basa en un área de trabajo de Log Analytics.
- **Azure Arc:** amplía la administración de Azure a cualquier infraestructura, incluidos Azure VMware Solution, el entorno local u otras plataformas en la nube. 
- **Azure Update Management:** administra las actualizaciones del sistema operativo de las máquinas Windows y Linux en un entorno híbrido.
- **Azure Monitor** es una solución completa para recopilar, analizar y actuar en la telemetría desde los entornos local y en la nube. No requiere ninguna implementación. 

En este artículo, integrará los servicios nativos de Azure en la nube privada de Azure VMware Solution. También aprenderá a usar las herramientas para administrar las máquinas virtuales a lo largo de su ciclo de vida. 


## <a name="enable-azure-update-management"></a>Habilitación de Azure Update Management

[Azure Update Management](../automation/update-management/overview.md) en Azure Automation administra las actualizaciones del sistema operativo de las máquinas Windows y Linux en un entorno híbrido. Supervisa el cumplimiento de la aplicación de revisiones y reenvía alertas de desviación de la aplicación de revisiones a Azure Monitor para su corrección. Azure Update Management debe conectarse al área de trabajo de Log Analytics para usar datos almacenados con el fin de evaluar el estado de las actualizaciones en las máquinas virtuales.

1. [Cree una cuenta de Azure Automation](../automation/automation-create-standalone-account.md). 

   >[!TIP]
   >Puede [usar una plantilla de Azure Resource Manager (ARM) para crear una cuenta de Azure Automation](../automation/quickstart-create-automation-account-template.md). El uso de una plantilla de ARM requiere menos pasos que otros métodos de implementación.

1. [Habilite Update Management desde una cuenta de Automation](../automation/update-management/enable-from-automation-account.md).  Esto vincula el área de trabajo de Log Analytics a la cuenta de Automation. También habilita máquinas virtuales de Azure y que no son de Azure en Update Management.

   - Si tiene un área de trabajo, seleccione **Update Management**. A continuación, seleccione el área de trabajo de Log Analytics y la cuenta de Automation y, después, seleccione **Habilitar**. La instalación tarda hasta 15 minutos en completarse.

   - Si quiere crear un área de trabajo de Log Analytics, consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](../azure-monitor/logs/quick-create-workspace.md). También puede crear un área de trabajo a través de la [CLI](../azure-monitor/logs/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) o una [plantilla de Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md).
 
1. Una vez que haya habilitado Update Management, puede [implementar actualizaciones en máquinas virtuales y revisar los resultados](../automation/update-management/deploy-updates.md). 


## <a name="enable-azure-security-center"></a>Habilitación de Azure Security Center

Azure Security Center proporciona protección contra amenazas avanzada en máquinas virtuales (VM) locales y de Azure VMware Solution. Evalúa la vulnerabilidad de las máquinas virtuales de Azure VMware Solution y genera alertas según sea necesario. Estas alertas de seguridad se pueden reenviar a Azure Monitor para su resolución. 

Azure Security Center ofrece muchas características, entre las que se incluyen:
- Supervisión de la integridad de los archivos
- Detección de ataques sin archivos
- Evaluación de revisiones del sistema operativo 
- Evaluación de configuraciones de seguridad incorrectas
- Evaluación de EndPoint Protection

>[!NOTE]
>Azure Security Center es una herramienta preconfigurada que no requiere implementación, pero deberá habilitarla en Azure Portal. 

Para habilitar Azure Security Center, vea [Integración de Azure Security Center con Azure VMware Solution](azure-security-integration.md).

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Incorporación de máquinas virtuales a servidores habilitados para Azure Arc

Azure Arc amplía la administración de Azure a cualquier infraestructura, incluidos Azure VMware Solution y el entorno local.  Los [servidores habilitados para Azure Arc](../azure-arc/servers/overview.md) permiten administrar servidores físicos y máquinas virtuales Windows y Linux hospedados *fuera* de Azure, en la red corporativa o en otro proveedor de nube.

Para información sobre la habilitación de servidores habilitados para Azure Arc para varias máquinas virtuales Windows o Linux, vea [Conexión de máquinas híbridas a Azure a gran escala](../azure-arc/servers/onboard-service-principal.md).

## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Incorporación de clústeres híbridos de Kubernetes con Kubernetes habilitado para Azure Arc

[Kubernetes habilitado para Azure Arc](../azure-arc/kubernetes/overview.md) permite adjuntar un clúster de Kubernetes hospedado en el entorno de Azure VMware Solution. 

Para más información, consulte [Creación de una entidad de servicio de incorporación habilitada para Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).

## <a name="deploy-the-log-analytics-agent"></a>Implementación del agente de Log Analytics

Puede supervisar las máquinas virtuales de Azure VMware Solution mediante el agente de Log Analytics. Las máquinas conectadas al área de trabajo de Log Analytics usan el [agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md) para recopilar datos sobre los cambios en el software instalado, los servicios de Microsoft, los archivos y el Registro de Windows y los demonios de Linux de los servidores supervisados. Cuando los datos están disponibles, el agente los envía a Azure Monitor para su procesamiento. Los registros de Azure Monitor aplican la lógica a los datos recibidos, los anotan y hacen que estén disponibles para el análisis.

Implemente el agente de Log Analytics con [compatibilidad con la extensión de máquina virtual de servidores habilitados para Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

## <a name="enable-azure-monitor"></a>Habilitación de Azure Monitor

[Azure Monitor](../azure-monitor/overview.md) es una solución completa para recopilar, analizar y actuar en la telemetría desde los entornos local y en la nube. Algunas de las ventajas agregadas de Azure Monitor incluyen: 

   - Supervisión fluida 

   - Mejor visibilidad de la infraestructura 

   - Notificaciones instantáneas 

   - Resolución automática 

   - Rentabilidad 

Puede recopilar datos de diferentes orígenes para su supervisión y análisis. Para obtener más información, consulte [Orígenes de datos de supervisión para Azure Monitor](../azure-monitor/agents/data-sources.md).  También puede recopilar diferentes tipos de datos para su análisis, visualización y alertas. Para más información, consulte [Plataforma de datos de Azure Monitor](../azure-monitor/data-platform.md). 

Puede supervisar el rendimiento del sistema operativo invitado y descubrir y asignar dependencias de aplicaciones para máquinas virtuales de Azure VMware Solution o locales. También puede crear reglas de alertas para identificar problemas en su entorno, como, por ejemplo, uso elevado de recursos, revisiones que faltan, espacio en disco bajo y latido de las máquinas virtuales. Puede establecer una respuesta automatizada a eventos detectados enviando una alerta a las herramientas de Administración de servicios de TI (ITSM). La notificación de detección de alertas también se puede enviar por correo electrónico.


1. [Diseñe la implementación de registros de Azure Monitor](../azure-monitor/logs/design-logs-deployment.md).

1. [Información general sobre la habilitación de Azure Monitor para VM](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Configure el área de trabajo de Log Analytics para Azure Monitor para VM](../azure-monitor/vm/vminsights-configure-workspace.md).

1. Cree reglas de alertas para identificar problemas en su entorno:
   - [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](../azure-monitor/alerts/alerts-metric.md).
   - [Creación, visualización y administración de alertas de registro mediante Azure Monitor](../azure-monitor/alerts/alerts-log.md).
   - [Reglas de acción](../azure-monitor/alerts/alerts-action-rules.md) para establecer acciones y notificaciones automatizadas.
   - [Conexión de Azure a las herramientas de ITSM mediante el Conector de Administración de servicios de TI](../azure-monitor/alerts/itsmc-overview.md).
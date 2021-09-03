---
title: 'Supervisión de máquinas virtuales con Azure Monitor: seguridad'
description: Obtenga información sobre los servicios para supervisar la seguridad de las máquinas virtuales y cómo se relacionan con Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 8f32be602ac1ba8ea7bb5805ca8df120b0af917e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674286"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Supervisión de máquinas virtuales con Azure Monitor: supervisión de la seguridad
Este artículo forma parte del escenario [Supervisión de máquinas virtuales y sus cargas de trabajo en Azure Monitor](monitor-virtual-machine.md). Se describen los servicios de Azure para supervisar la seguridad de las máquinas virtuales y cómo se relacionan con Azure Monitor. Azure Monitor se ha diseñado para supervisar la disponibilidad y el rendimiento de las máquinas virtuales y otros recursos en la nube. Aunque los datos operativos almacenados en Azure Monitor pueden ser útiles para investigar incidentes de seguridad, se han diseñado otros servicios de Azure para supervisar la seguridad. 

> [!IMPORTANT]
> Los servicios de seguridad tienen su propio costo independiente al de Azure Monitor. Antes de configurar estos servicios, consulte su información de precios para determinar la inversión adecuada en su uso.

## <a name="azure-services-for-security-monitoring"></a>Servicios de Azure para la supervisión de seguridad
Azure Monitor se centra en datos operativos, como los registros de actividad, las métricas y los orígenes admitidos de Log Analytics, incluidos los eventos de Windows (excepto los de seguridad), los contadores de rendimiento, los registros y Syslog. La supervisión de la seguridad en Azure se realiza mediante Azure Security Center y Azure Sentinel. Cada uno de estos servicios tiene un costo adicional, por lo que debe determinar su valor para el entorno antes de implementarlos.

[Azure Security Center](../../security-center/security-center-introduction.md) recopila información sobre los recursos de Azure y los servidores híbridos. Aunque Security Center puede recopilar eventos de seguridad, se centra en la recopilación de datos de inventario, resultados de exámenes de evaluación y auditorías de directivas para resaltar las vulnerabilidades y recomendar acciones correctivas. Entre las características destacadas se incluyen un mapa de red interactivo, el acceso a máquinas virtuales Just-In-Time, la seguridad de red adaptable y controles de aplicación adaptables para bloquear archivos ejecutables sospechosos.

[Azure Defender para servidores](../../security-center/azure-defender.md) es la solución de evaluación de servidores proporcionada por Security Center. Defender para servidores puede enviar eventos de seguridad de Windows a Log Analytics. Security Center no se basa en eventos de seguridad de Windows para las alertas ni el análisis. El uso de esta característica permite el archivado centralizado de eventos con fines de investigación u otros fines.

[Azure Sentinel](../../sentinel/overview.md) es una solución de administración de eventos e información de seguridad (SIEM) de y respuesta automatizada de orquestación de seguridad (SOAR). Sentinel recopila datos de seguridad de una amplia gama de orígenes de Microsoft y de terceros para proporcionar alertas, visualización y automatización. Esta solución se centra en consolidar tantos registros de seguridad como sea posible, incluidos los eventos de seguridad de Windows. Azure Sentinel también puede recopilar registros de eventos de seguridad de Windows y normalmente comparte un área de trabajo de Log Analytics con Security Center. Los eventos de seguridad solo se pueden recopilar desde Azure Sentinel o Security Center cuando comparten la misma área de trabajo. A diferencia de Security Center, los eventos de seguridad son un componente clave de las alertas y el análisis en Azure Sentinel.

[Defender para punto de conexión](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) es una plataforma de seguridad empresarial para puntos de conexión diseñada para evitar, detectar, investigar y responder a amenazas avanzadas. Se ha diseñado con un enfoque principal en la protección de dispositivos de usuario para Windows. Defender para punto de conexión supervisa estaciones de trabajo, servidores, tabletas y teléfonos móviles con varios sistemas operativos en busca de problemas de seguridad y vulnerabilidades. Defender para punto de conexión está estrechamente alineado con Microsoft Endpoint Manager para recopilar datos y proporcionar evaluaciones de seguridad. La recopilación de datos se basa principalmente en los registros de seguimiento ETW y se almacena en un área de trabajo aislada.

## <a name="integration-with-azure-monitor"></a>Integración con Azure Monitor
En la tabla siguiente se enumeran los puntos de integración de Azure Monitor con los servicios de seguridad. Todos los servicios usan el mismo agente de Log Analytics, lo que reduce la complejidad porque no se implementan otros componentes en las máquinas virtuales. Security Center y Azure Sentinel almacenan sus datos en un área de trabajo de Log Analytics para que pueda usar consultas de registro a fin de poner en correlación los datos recopilados por los distintos servicios. O bien, puede crear un libro personalizado que combine datos de seguridad y datos de disponibilidad y rendimiento en una sola vista.

| Punto de integración       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender para punto de conexión |
|:---|:---|:---|:---|:---|
| Recopila eventos de seguridad     |   | X | X | X |
| Almacena los datos en el área de trabajo de Log Analytics | X | X | X |   | 
| Usa el agente de Log Analytics     | X | X | X | X | 


## <a name="workspace-design-considerations"></a>Consideraciones del diseño de áreas de trabajo
Como se ha descrito en [Supervisión de máquinas virtuales con Azure Monitor: configuración de la supervisión](monitor-virtual-machine-configure.md#create-and-prepare-a-log-analytics-workspace), Azure Monitor y los servicios de seguridad necesitan un área de trabajo de Log Analytics. En función de los requisitos concretos, puede optar por compartir un área de trabajo común, o bien separar los datos de disponibilidad y rendimiento de los datos de seguridad. Para obtener detalles completos sobre la lógica que debe tener en cuenta al diseñar una configuración de área de trabajo, vea [Diseño de la implementación de registros de Azure Monitor](../logs/design-logs-deployment.md).

## <a name="agent-deployment"></a>Implementación de agentes
Puede configurar Security Center para que implemente de forma automática el agente de Log Analytics en máquinas virtuales de Azure. Aunque esto podría parecer redundante con la implementación del mismo agente por parte de Azure Monitor, lo más probable es que quiera habilitar los dos, ya que cada uno de ellos realizará su propia configuración. Por ejemplo, si Security Center intenta aprovisionar una máquina que ya supervisa Azure Monitor, usará el agente que ya está instalado y agregará la configuración para el área de trabajo de Security Center.

## <a name="next-steps"></a>Pasos siguientes

* [Supervisión de máquinas virtuales con Azure Monitor: análisis de los datos de supervisión](monitor-virtual-machine-analyze.md)
* [Supervisión de máquinas virtuales con Azure Monitor: alertas](monitor-virtual-machine-alerts.md)

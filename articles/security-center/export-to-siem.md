---
title: Transmisión de alertas de Azure Security Center a sistemas de administración de eventos e información de seguridad (SIEM) y otras soluciones de supervisión
description: Obtenga información sobre cómo transmitir las alertas de seguridad a Azure Sentinel, SIEM de terceros, SOAR o soluciones de ITSM.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: ad4d401072bc96784b45c0f5fd43d08f3de8ade0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823824"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR

Azure Security Center puede transmitir las alertas de seguridad a las soluciones más populares de administración de eventos e información de seguridad (SIEM), respuesta automatizada de orquestación de seguridad (SOAR) y administración de servicios de TI (ITSM).

Existen herramientas nativas de Azure para garantizar que puede ver los datos de las alertas en todas las soluciones más populares que se usan hoy en día, entre las que se incluyen:

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar de IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Transmisión de alertas a Azure Sentinel 

Security Center se integra de forma nativa con Azure Sentinel, la solución SOAR y SIEM nativa de nube de Azure. 

[Obtenga más información sobre Azure Sentinel](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Conectores de Azure Sentinel para Security Center

Azure Sentinel incluye conectores integrados para Azure Security Center en los niveles de suscripción y de inquilino:

- [Transmisión de alertas a Azure Sentinel en el nivel de suscripción](../sentinel/connect-azure-security-center.md)
- [Conexión de todas las suscripciones del inquilino a Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Cuando se conecta Azure Defender a Azure Sentinel, el estado de las alertas de Azure Defender que se ingieren en Azure Sentinel se sincroniza entre los dos servicios. Por ejemplo, cuando se cierra una alerta en Azure Defender, también se mostrará como cerrada en Azure Sentinel. El cambio del estado de una alerta en Azure Defender "no"* afecta al estado de los **incidentes** de Azure Sentinel que contienen la alerta de Azure Sentinel sincronizada, solo al de la propia alerta sincronizada.

Al habilitar la característica en vista previa, la **sincronización de alertas bidireccional**, se sincronizará automáticamente el estado de las alertas de Azure Defender originales con incidentes de Azure Sentinel que contienen las copias de esas alertas de Azure Defender. Por ejemplo, cuando se cierra un incidente de Azure Sentinel que contiene una alerta de Azure Defender, Azure Defender cerrará automáticamente la alerta original correspondiente.

Obtenga más información en [Conexión de alertas de Azure Defender desde Azure Security Center](../sentinel/connect-azure-security-center.md).

> [!NOTE]
> La característica de sincronización de alertas bidireccional no está disponible en la nube de Azure Government. 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Configuración de la ingesta de todos los registros de auditoría en Azure Sentinel 

Otra alternativa para investigar las alertas de Security Center en Azure Sentinel es transmitir los registros de auditoría a Azure Sentinel:
    - [Conexión de eventos de seguridad de Windows](../sentinel/connect-windows-security-events.md)
    - [Recopilación de datos de orígenes basados en Linux mediante Syslog](../sentinel/connect-syslog.md)
    - [Conectar datos del registro de actividad de Azure](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> Azure Sentinel se factura en función del volumen de datos ingeridos para el análisis en Azure Sentinel y almacenados en el área de trabajo de Log Analytics de Azure Monitor. Azure Sentinel ofrece un modelo de precios flexible y predecible. [Obtenga más información en la página de precios de Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Transmisión de alertas con la Microsoft Graph Security API

Security Center tiene integración integrada con la Microsoft Graph Security API. No se requiere ninguna configuración y no hay costos adicionales. 

Puede usar esta API para transmitir alertas del **inquilino completo** (y datos de muchos otros productos de seguridad de Microsoft) a SIEM de terceros y otras plataformas populares:

- **Splunk Enterprise y Splunk Cloud** - [Uso del complemento de Microsoft Graph Security API para Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Conexión a la Microsoft Graph Security API en Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [Instrucciones para instalar y configurar la aplicación de Microsoft Graph Security API desde el almacén de ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [Módulo de compatibilidad de dispositivos de IBM para Azure Security Center a través de Microsoft Graph API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **InSpark**, etc: [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Obtenga más información sobre Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Transmisión de alertas con Azure Monitor 

Para transmitir alertas en **ArcSight**, **Splunk**, **SumoLogic**, servidores Syslog, **LogRhythm**, **plataforma de observabilidad de Logz.io Cloud** y otras soluciones de supervisión. Conecte Security Center con Azure monitor a través de Azure Event Hubs:

1. Habilite la [exportación continua](continuous-export.md) para transmitir alertas de Security Center a una instancia dedicada de Azure Event Hubs en el nivel de suscripción. 
    > [!TIP]
    > Para hacer esto en el nivel de grupo de administración mediante Azure Policy, consulte [Creación de configuraciones de automatización de exportación continua a gran escala](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies).

1. [Conecte la instancia de Azure Event Hubs a su solución preferida mediante los conectores integrados de Azure Monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. También puede transmitir los registros sin procesar a la instancia de Azure Event Hubs y conectarse a su solución preferida. Obtenga más información en [Datos de supervisión disponibles](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Para ver los esquemas de eventos de los tipos de datos exportados, visite el artículo sobre los [esquemas de eventos del centro de eventos](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Pasos siguientes

En esta página se explica cómo asegurarse de que los datos de alerta de Azure Security Center estén disponibles en la herramienta SIEM, SOAR o ITSM de su elección. Para obtener material relacionado, consulte:

- [¿Qué es Azure Sentinel?](../sentinel/overview.md)
- [Validación de alertas en Azure Security Center](security-center-alert-validation.md): compruebe que las alertas estén configuradas correctamente.
- [Exportación continua de datos de Security Center](continuous-export.md)
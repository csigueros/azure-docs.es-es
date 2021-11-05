---
title: Transmisión de alertas de Microsoft Defender for Cloud a sistemas de administración de eventos e información de seguridad (SIEM) y otras soluciones de supervisión
description: Información sobre cómo transmitir las alertas de seguridad a Microsoft Sentinel, SIEM de terceros, SOAR o soluciones de ITSM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7235fc2fe3fb06cf342408e9aa99bf6db2e13393
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075757"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud puede transmitir las alertas de seguridad a las soluciones más populares de administración de eventos e información de seguridad (SIEM), respuesta automatizada de orquestación de seguridad (SOAR) y administración de servicios de TI (ITSM).

Existen herramientas nativas de Azure para garantizar que puede ver los datos de las alertas en todas las soluciones más populares que se usan hoy en día, entre las que se incluyen:

- **Microsoft Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar de IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-microsoft-sentinel"></a>Transmisión de alertas a Microsoft Sentinel 

Defender for Cloud se integra de forma nativa con Microsoft Sentinel, la solución SIEM y SOAR nativa de nube de Azure. 

[Más información sobre Microsoft Sentinel](../sentinel/overview.md)

### <a name="microsoft-sentinels-connectors-for-defender-for-cloud"></a>Conectores de Microsoft Sentinel para Defender for Cloud

Microsoft Sentinel incluye conectores integrados para Defender for Cloud en los niveles de suscripción e inquilino:

- [Transmisión de alertas a Microsoft Sentinel en el nivel de suscripción](../sentinel/connect-azure-security-center.md)
- [Conexión de todas las suscripciones del inquilino a Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

Al conectar Defender for Cloud a Microsoft Sentinel, el estado de las alertas de Defender for Cloud que se ingieren en Microsoft Sentinel se sincroniza entre los dos servicios. Por ejemplo, cuando se cierra una alerta en Defender for Cloud, esa alerta también se mostrará como cerrada en Microsoft Sentinel. El cambio del estado de una alerta en Defender for Cloud "no"* afectará al estado de los **incidentes** de Microsoft Sentinel que contienen la alerta de Microsoft Sentinel sincronizada, sino solo al de la propia alerta sincronizada.

Al habilitar la característica en vista previa, la **sincronización de alertas bidireccional**, se sincronizará automáticamente el estado de las alertas de Defender for Cloud originales con incidentes de Microsoft Sentinel que contienen las copias de esas alertas de Defender for Cloud. Por ejemplo, cuando se cierra un incidente de Microsoft Sentinel que contiene una alerta de Defender for Cloud, Defender for Cloud cerrará automáticamente la alerta original correspondiente.

Encontrará más información en [Conexión de alertas de Defender for Cloud desde Microsoft Defender for Cloud](../sentinel/connect-azure-security-center.md).

> [!NOTE]
> La característica de sincronización de alertas bidireccional no está disponible en la nube de Azure Government. 

### <a name="configure-ingestion-of-all-audit-logs-into-microsoft-sentinel"></a>Configuración de la ingesta de todos los registros de auditoría en Microsoft Sentinel 

Otra alternativa para investigar las alertas de Defender for Cloud en Microsoft Sentinel es transmitir los registros de auditoría a Microsoft Sentinel:
    - [Conexión de eventos de seguridad de Windows](../sentinel/connect-windows-security-events.md)
    - [Recopilación de datos de orígenes basados en Linux mediante Syslog](../sentinel/connect-syslog.md)
    - [Conectar datos del registro de actividad de Azure](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> Microsoft Sentinel se factura en función del volumen de datos ingeridos para el análisis en Microsoft Sentinel y almacenados en el área de trabajo de Log Analytics de Azure Monitor. Microsoft Sentinel ofrece un modelo de precios flexible y predecible. [Más información en la página de precios de Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Transmisión de alertas con la Microsoft Graph Security API

Defender for Cloud está listo para integrarse con Microsoft Graph Security API. No se requiere ninguna configuración y no hay costos adicionales. 

Puede usar esta API para transmitir alertas del **inquilino completo** (y datos de muchos otros productos de seguridad de Microsoft) a SIEM de terceros y otras plataformas populares:

- **Splunk Enterprise y Splunk Cloud** - [Uso del complemento de Microsoft Graph Security API para Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Conexión a la Microsoft Graph Security API en Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [Instrucciones para instalar y configurar la aplicación de Microsoft Graph Security API desde el almacén de ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [Módulo de compatibilidad de dispositivos de IBM para Microsoft Defender for Cloud a través de Microsoft Graph API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **InSpark**, etc: [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Obtenga más información sobre Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Transmisión de alertas con Azure Monitor 

Para transmitir alertas en **ArcSight**, **Splunk**, **SumoLogic**, servidores Syslog, **LogRhythm**, **plataforma de observabilidad de Logz.io Cloud** y otras soluciones de supervisión. Conecte Defender for Cloud con Azure Monitor a través de Azure Event Hubs:

1. Habilite la [exportación continua](continuous-export.md) para transmitir alertas de Defender for Cloud a una instancia dedicada de Azure Event Hubs en el nivel de suscripción. 
    > [!TIP]
    > Para hacer esto en el nivel de grupo de administración mediante Azure Policy, consulte [Creación de configuraciones de automatización de exportación continua a gran escala](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies).

1. [Conecte la instancia de Azure Event Hubs a su solución preferida mediante los conectores integrados de Azure Monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. También puede transmitir los registros sin procesar a la instancia de Azure Event Hubs y conectarse a su solución preferida. Obtenga más información en [Datos de supervisión disponibles](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Para ver los esquemas de eventos de los tipos de datos exportados, visite el artículo sobre los [esquemas de eventos del centro de eventos](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Pasos siguientes

En esta página hemos descrito cómo garantizar que los datos de alerta de Microsoft Defender for Cloud están disponibles en la herramienta SIEM, SOAR o ITSM de su elección. Para obtener material relacionado, consulte:

- [¿Qué es Microsoft Sentinel?](../sentinel/overview.md)
- [Validación de alertas en Microsoft Defender for Cloud](alert-validation.md): compruebe que las alertas están configuradas correctamente.
- [Exportación continua de datos de Defender for Cloud](continuous-export.md)

---
title: Registro y auditoría de seguridad de Azure | Microsoft Docs
description: Conozca los registros disponibles en Azure y la información detallada de seguridad que puede obtener.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: f7481e22b9d03d7f49f4ee383bb52c4772631c16
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178919"
---
# <a name="azure-security-logging-and-auditing"></a>Registro y auditoría de seguridad de Azure

Azure proporciona una amplia gama de opciones de registro y auditoría de seguridad configurables para ayudarle a identificar vacíos en las directivas y mecanismos de seguridad. Este artículo describe la generación, recopilación y análisis de los registros de seguridad provenientes de los servicios hospedados en Azure.

> [!Note]
> Algunas de las recomendaciones de este artículo pueden provocar un aumento del uso de datos, de la red o de los recursos de proceso, lo que podría incrementar los costos de las licencias o las suscripciones.

## <a name="types-of-logs-in-azure"></a>Tipos de registros de Azure

Las aplicaciones de nube son complejas y tienen muchas partes móviles. Los datos de registro pueden proporcionar información detallada sobre las aplicaciones y ayudarle a:

- Solucionar problemas pasados o impedir posibles problemas
- Mejorar el rendimiento o el mantenimiento de la aplicación
- Automatizar acciones que, de otro modo, requerirían intervención manual

Los registros de Azure se clasifican en los tipos siguientes:
* Los **registros de control y administración** proporcionan información sobre las operaciones CREATE, UPDATE y DELETE de Azure Resource Manager. Para más información, consulte [Registros de actividad de Azure](../../azure-monitor/essentials/platform-logs-overview.md).

* Los **registros del plano de datos** proporcionan información sobre los eventos desencadenados como parte del uso de los recursos de Azure. Ejemplos de este tipo de registro son los registros de eventos del sistema de Windows, de seguridad y de aplicaciones en una máquina virtual, así como los [registros de diagnóstico](../../azure-monitor/essentials/platform-logs-overview.md) que se han configurado mediante Azure Monitor.

* Los **eventos procesados** proporcionan información sobre eventos y alertas analizados que se han procesado en su nombre. Ejemplos de este tipo son las [alertas de Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md), donde [Azure Security Center](../../security-center/security-center-introduction.md) ha procesado y analizado su suscripción y proporciona unas alertas de seguridad concisas.

En la tabla siguiente se enumeran los tipos más importante de registros disponibles en Azure.

| Categoría del registro | Tipo de registro | Uso | Integración |
| ------------ | -------- | ------ | ----------- |
|[Registros de actividad](../../azure-monitor/essentials/platform-logs-overview.md)|Eventos de plano de control de los recursos de Azure Resource Manager|    Proporciona información detallada sobre las operaciones que se realizaron en los recursos de la suscripción.|    API REST y [Azure Monitor](../../azure-monitor/essentials/platform-logs-overview.md)|
|[Registros de recursos de Azure](../../azure-monitor/essentials/platform-logs-overview.md)|Datos frecuentes acerca del funcionamiento de los recursos de Azure Resource Manager de la suscripción| Proporciona información detallada sobre las operaciones que el mismo recurso realiza.| Azure Monitor|
|[Informes de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Registros e informes | Informa sobre las actividades de inicio de sesión de usuario e información de actividades del sistema acerca de la administración de grupos y usuarios.|[Graph API](../../active-directory/develop/microsoft-graph-intro.md)|
|[Máquinas virtuales y servicios en la nube](../../azure-monitor/vm/monitor-virtual-machine.md)|Servicio de Registro de eventos de Windows y Syslog de Linux|   Captura los datos del sistema y los datos de registro en las máquinas virtuales, y transfiere estos datos a la cuenta de almacenamiento que elija.|   Windows con [WAD](../../azure-monitor/agents/diagnostics-extension-overview.md) (almacenamiento de Windows Azure Diagnostics) y Linux en Azure Monitor|
|[Análisis de Azure Storage](/rest/api/storageservices/fileservices/storage-analytics)|El registro de almacenamiento proporciona datos de métricas de una cuenta de almacenamiento|Proporciona información detallada sobre seguimiento de solicitudes, análisis de tendencias de uso y diagnóstico de problemas con la cuenta de almacenamiento.| API de REST o [biblioteca de cliente](/dotnet/api/overview/azure/storage)|
|[Registros de flujo de los grupos de seguridad de red (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Tiene formato JSON y muestra flujos entrantes y salientes por cada regla|Muestra información sobre el tráfico IP de entrada y salida a través de un grupo de seguridad de red.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insights](../../azure-monitor/app/app-insights-overview.md)|Registros, excepciones y diagnósticos personalizados|  Proporciona un servicio de supervisión de rendimiento de aplicaciones (APM) para desarrolladores web en varias plataformas.| API de REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Datos de proceso y alertas de seguridad](../../security-center/security-center-introduction.md)|   Alertas de Azure Security Center, alertas de registro de Azure Monitor|    Proporciona información y alertas de seguridad.|  API de REST, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integración de registros con sistemas locales de SIEM
En [Integración de alertas de Security Center](../../security-center/security-center-partner-integration.md) se describe cómo sincronizar las alertas de Security Center, los eventos de seguridad de máquina virtual recopilados por los registros de diagnóstico de Azure y los registros de auditoría de Azure, con los registros de Azure Monitor o una solución SIEM.

## <a name="next-steps"></a>Pasos siguientes

- [Auditoría y registro](management-monitoring-overview.md): proteja los datos mediante el mantenimiento de la visibilidad y la rápida respuesta a las alertas de seguridad puntuales

- [Registro de seguridad y recopilación de registros de auditoría en Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): aplique esta configuración para asegurarse de que las instancias de Azure están recopilando los registros de seguridad y auditoría correctos.

- [Configuración de auditoría para una colección de sitios](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): si es el administrador de una colección de sitios, recupere el historial de las acciones de un usuario individua y el historial de las acciones realizadas durante un intervalo de fechas concreto.

- [Busque en el registro de auditoría del Centro de seguridad de Microsoft 365](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance): utilice el Centro de seguridad de Microsoft 365 para buscar en el registro de auditoría unificado y consultar la actividad de usuario y de administrador en su organización.
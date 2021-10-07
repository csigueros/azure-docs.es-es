---
title: Resistencia mediante la supervisión y el análisis con Azure AD B2C | Microsoft Docs
description: Resistencia mediante la supervisión y el análisis con Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccb3876799c0a1473f06c1838536df17eab03398
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754388"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Resistencia mediante la supervisión y el análisis

La supervisión maximiza la disponibilidad y el rendimiento de las aplicaciones y los servicios. Ofrece una solución completa para recopilar, analizar y actuar en la telemetría desde la infraestructura y las aplicaciones. Las alertas le notifican de forma proactiva cuando se detectan problemas con el servicio o las aplicaciones. Le permiten identificar y solucionar los problemas antes de que los usuarios finales del servicio puedan verlos. [Log Analytics de Azure AD](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) le ayuda a analizar, buscar los registros de auditoría y los registros de inicio de sesión, y crear vistas personalizadas.

Vea este vídeo para aprender a configurar la supervisión y los informes en Azure AD B2C mediante Azure Monitor.

>[!Video https://www.youtube.com/embed/Mu9GQy-CbXI]

## <a name="monitor-and-get-notified-through-alerts"></a>Supervisión y recepción de notificaciones mediante alertas

Es fundamental supervisar el sistema y la infraestructura para garantizar el estado general de los servicios. Comienza con la definición de las métricas empresariales, como la llegada de nuevos usuarios, las tasas de autenticación del usuario final y la conversión. Configure estos indicadores para su supervisión. Si está planeando un próximo aumento debido al tráfico de promoción o de vacaciones, revise las estimaciones específicamente para el evento y el punto de referencia correspondiente para las métricas de negocio. Después del evento, vuelva al punto de referencia anterior.

Del mismo modo, para detectar errores o interrupciones de rendimiento, la configuración de una buena base de referencia y la definición de las alertas es una práctica indispensable para responder rápidamente a los problemas emergentes.

![La imagen muestra componentes de supervisión y análisis](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Implementación de supervisión y alertas

- **Supervisión**: Use [Azure Monitor](../../active-directory-b2c/azure-monitor.md) para supervisar continuamente el estado de los objetivos de nivel de servicio (SLO) clave y recibir una notificación cada vez que se produzca un cambio crítico. Comience por identificar la directiva de Azure AD B2C o una aplicación como un componente crítico de la empresa cuyo mantenimiento debe supervisarse para mantener los SLO. Identifique los indicadores clave que se alinean con los SLO.
Por ejemplo, realice un seguimiento de las métricas siguientes, ya que una caída repentina puede provocar una pérdida en el negocio.

  - **Total de solicitudes**: Número total de solicitudes enviadas a la directiva de Azure AD B2C.

  - **Tasa de éxito (%)** : solicitudes totales/número total de solicitudes.

  Obtenga acceso a los [indicadores clave](../../active-directory-b2c/view-audit-logs.md) en [Application Insights](../../active-directory-b2c/analytics-with-application-insights.md), donde se almacenan los registros basados en directivas, los [registros de auditoría](../../active-directory-b2c/analytics-with-application-insights.md) y los registros de inicio de sesión de Azure AD B2C.  

   - **Visualizaciones**: uso de los paneles de compilación de Log Analytics para supervisar visualmente los indicadores clave.

   - **Período actual**: creación de gráficos temporales para mostrar los cambios en el número total de solicitudes y la tasa de éxito (%) en el período actual, por ejemplo, la semana actual.

   - **Período anterior**: creación de gráficos temporales para mostrar los cambios en el número total de solicitudes y la tasa de éxito (%) en un período anterior a modo de referencia, por ejemplo, la semana pasada.

- **Alertas**: el uso de Log Analytics define las [alertas](../../azure-monitor/alerts/alerts-log.md) que se desencadenan cuando se producen cambios repentinos en los indicadores clave. Estos cambios pueden afectar negativamente a los SLO. Las alertas usan diversas formas de métodos de notificación, como correo electrónico, SMS y webhooks. Comience por definir un criterio que actúe como un umbral en el que se desencadenará la alerta. Por ejemplo:
  - Alerta contra una caída brusca en el número total de solicitudes: desencadenar una alerta cuando el número total de solicitudes disminuya repentinamente. Por ejemplo, cuando hay una caída del 25 % en el número total de solicitudes en comparación con el período anterior, se genera una alerta.  
  - Alerta contra una caída significativa en la tasa de éxito (%): desencadenar una alerta cuando la tasa de éxito de la directiva seleccionada disminuya de forma significativa.
  - Al recibir una alerta, solucione el problema mediante [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md) y la [extensión de VS Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) para Azure AD B2C. Después de resolver el problema e implementar una aplicación o directiva actualizada, continúa supervisando los indicadores clave hasta que vuelvan al intervalo normal.

- **Alertas de servicio**: use las [alertas de nivel de servicio de Azure AD B2C](../../service-health/service-health-overview.md) para recibir notificaciones de problemas de servicio, mantenimiento planeado, aviso de estado y aviso de seguridad.

- **Informes**: [mediante Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), compile informes que le ayuden a comprender la información de usuario, los desafíos técnicos y las oportunidades de crecimiento.
  - **Panel de estado**: cree [paneles personalizados con la característica de panel de Azure](../../azure-monitor/app/tutorial-app-dashboards.md), que permite agregar gráficos mediante consultas de Log Analytics. Por ejemplo, identifique el patrón de inicios de sesión correctos y erróneos, los motivos de error y la telemetría de los dispositivos utilizados para realizar las solicitudes.
  - **Recorridos abandonados de Azure AD B2C**: use el [libro](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) para realizar un seguimiento de la lista de recorridos abandonados de Azure AD B2C en los que el usuario inició el recorrido de inicio de sesión o registro, pero nunca lo terminó. Proporciona detalles sobre el id. de directiva y el desglose de los pasos que realiza el usuario antes de abandonar el recorrido.
  - **Libros de supervisión de Azure AD B2C**: Use los [libros de supervisión](https://github.com/azure-ad-b2c/siem), que incluyen el panel de Azure AD B2C, las operaciones de Multi-Factor Authentication (MFA), el informe de acceso condicional y los registros de búsqueda por correlationId, para obtener información más detallada sobre el estado del entorno de Azure AD B2C.
  
## <a name="next-steps"></a>Pasos siguientes

- [Recursos de resistencia para desarrolladores de Azure AD B2C](resilience-b2c.md)
  - [Experiencia del usuario final resistente](resilient-end-user-experience.md)
  - [Interfaces resistentes con procesos externos](resilient-external-processes.md)
  - [Resistencia a través de los procedimientos recomendados para desarrolladores](resilience-b2c-developer-best-practices.md)
- [Aumento de la resistencia en la infraestructura de autenticación](resilience-in-infrastructure.md)
- [Aumento de la resistencia de la autenticación y autorización en las aplicaciones](resilience-app-development-overview.md)

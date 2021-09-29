---
title: Exportación y uso de datos de Azure Active Directory Identity Protection
description: Aprenda a investigar el uso de datos a largo plazo en Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/30/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ea682d65bde9748a4cd55ed066eb8f31eb418a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784097"
---
# <a name="how-to-export-risk-data"></a>Procedimiento de exportación de datos de riesgo

Azure AD almacena informes y señales de seguridad durante un período de tiempo definido, el que puede no ser lo suficientemente prolongado para lo que se refiere a la información de riesgo.

| Informe/señal | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| --- | --- | --- | --- |
| Registros de auditoría | 7 días | 30 días | 30 días |
| Inicios de sesión | 7 días | 30 días | 30 días |
| Uso de MFA de Azure AD | 30 días | 30 días | 30 días |
| Inicios de sesión no seguros | 7 días | 30 días | 30 días |

Las organizaciones pueden optar por almacenar datos durante períodos más largos. Para ello, modifican la configuración de diagnóstico de Azure AD a fin de enviar datos de **RiskyUsers** y **UserRiskEvents** a un área de trabajo de Log Analytics, archivar datos en una cuenta de almacenamiento, transmitir datos a un centro de eventos o enviar datos a una solución de asociado. Puede encontrar estas opciones en **Azure Portal** > **Azure Active Directory**, **Configuración de diagnóstico** > **Editar configuración**. Si no tiene una configuración de diagnóstico, siga las instrucciones que aparecen en el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../../azure-monitor/essentials/diagnostic-settings.md) para crear una.

>[!NOTE]
>La configuración de diagnóstico para RiskyUsers y UserRiskEvents se encuentra actualmente en versión preliminar pública.

[ ![Pantalla de configuración de diagnóstico en Azure AD que muestra la configuración existente](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png) ](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png#lightbox)

## <a name="log-analytics"></a>Log Analytics

Log Analytics permite que las organizaciones consulten datos mediante consultas integradas o consultas de Kusto personalizadas creadas. Para más información, consulte [Introducción a las consultas de registro en Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

Una vez que se habilita, encontrará el acceso a Log Analytics en **Azure Portal** > **Azure AD** > **Log Analytics**. Las tablas que más les interesan a los administradores de Identity Protection son **AADRiskyUsers** y **AADUserRiskEvents**.

- AADRiskyUsers: proporciona datos como el informe **Usuarios de riesgo** en Identity Protection.
- AADUserRiskEvents: proporciona datos como el informe **Detecciones de riesgo** en Identity Protection.

[ ![Vista de Log Analytics que muestra una consulta en la tabla AADUserRiskEvents en la que se muestran los 5 eventos principales](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png) ](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png#lightbox)

En la imagen anterior, se ejecutó la consulta siguiente a fin de mostrar las cinco detecciones de riesgo desencadenadas más recientes. 

```kusto
AADUserRiskEvents
| take 5
```

Otra opción es consultar la tabla AADRiskyUsers para ver todos los usuarios de riesgo.

```kusto
AADRiskyUsers
```

> [!NOTE]
> Log Analytics solo tiene visibilidad de los datos a medida que se transmiten. No se muestran los eventos que se producen antes de habilitar el envío de los envíos desde Azure AD.

## <a name="storage-account"></a>Cuenta de almacenamiento

Si se enrutan los registros a una cuenta de Azure Storage, se pueden conservar durante más tiempo que el período de retención predeterminado. Para más información, consulte el artículo [Tutorial: Archivado de registros de Azure AD en una cuenta de Azure Storage](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="azure-event-hubs"></a>Azure Event Hubs

Azure Event Hubs puede examinar los datos entrantes provenientes de orígenes como Azure AD Identity Protection y proporcionar análisis y correlación en tiempo real. Para más información, consulte el artículo [Tutorial: Transmisión de registros de Azure Active Directory a un centro de eventos de Azure](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="other-options"></a>Otras opciones

Las organizaciones pueden optar por [conectar datos de Azure AD a Azure Sentinel](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) también para un procesamiento adicional.

Las organizaciones pueden usar [Microsoft Graph API para interactuar con los eventos de riesgo mediante programación](howto-identity-protection-graph-api.md).

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la supervisión de Azure Active Directory?](../reports-monitoring/overview-monitoring.md)
- [Instalación y uso de las vistas de Log Analytics para Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Conexión de datos desde Azure Active Directory (Azure AD) Identity Protection](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)
- [Azure Active Directory Identity Protection y el SDK de PowerShell de Microsoft Graph](howto-identity-protection-graph-api.md)
- [Tutorial: Transmisión de registros de Azure Active Directory a un centro de eventos de Azure](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
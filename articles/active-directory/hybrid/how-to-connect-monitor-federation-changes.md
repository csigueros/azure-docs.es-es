---
title: Supervisión de los cambios en la configuración de federación en Azure AD | Microsoft Docs
description: En este artículo se explica cómo supervisar los cambios en la configuración de federación con Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7ffc5980eed3268f299ee0073cfa810c17878e53
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229715"
---
# <a name="monitor-changes-to-federation-configuration-in-your-azure-ad"></a>Supervisión de cambios en la configuración de federación en Azure AD

Al federar un entorno local con Azure AD, establece una relación de confianza entre el proveedor de identidades local y Azure AD. 

Debido a esta confianza establecida, Azure AD respeta el token de seguridad emitido por el proveedor de identidades local después de la autenticación, para conceder acceso a los recursos protegidos por Azure AD. 

Por lo tanto, es fundamental que esta confianza (configuración de federación) se supervise estrechamente y se capture cualquier actividad inusual o sospechosa.

Para supervisar la relación de confianza, se recomienda configurar alertas para recibir notificaciones cuando se realicen cambios en la configuración de federación.


## <a name="set-up-alerts-to-monitor-the-trust-relationship"></a>Configuración de alertas para supervisar la relación de confianza

Siga estos pasos para configurar alertas para supervisar la relación de confianza:

1. [Configure los registros de auditoría de Azure AD](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) para que fluyan a un área de trabajo de Azure Log Analytics. 
2. [Cree una regla de alertas](../../azure-monitor/alerts/alerts-log.md) que se desencadena en función de la consulta de registro de Azure AD. 
3. [Agregue un grupo de acciones](../../azure-monitor/alerts/action-groups.md) a la regla de alertas para que reciba una notificación cuando se cumpla la condición de alerta.  

Una vez configurado el entorno, los datos fluyen de la siguiente manera: 

 1. Los registros de Azure AD se rellenan según la actividad del inquilino.  
 2. La información de registro fluye al área de trabajo de Azure Log Analytics.  
 3. Un trabajo en segundo plano de Azure Monitor ejecuta la consulta de registro en función de la configuración de la regla de alertas del paso de configuración (2) anterior.  
    ```
     AuditLogs 
     |  extend TargetResource = parse_json(TargetResources) 
     | where ActivityDisplayName contains "Set federation settings on domain" or ActivityDisplayName contains "Set domain authentication" 
     | project TimeGenerated, SourceSystem, TargetResource[0].displayName, AADTenantId, OperationName, InitiatedBy, Result, ActivityDisplayName, ActivityDateTime, Type 
     ```
     
 4. Si el resultado de la consulta coincide con la lógica de alerta (es decir, el número de resultados es mayor que o igual a 1), se inicia el grupo de acciones. Supongamos que se inició, por lo que el flujo continúa en el paso 5.  
 5. La notificación se envía al grupo de acciones seleccionado al configurar la alerta.

 > [!NOTE]
 >  Además de configurar alertas, se recomienda revisar periódicamente los dominios configurados en el inquilino de Azure AD y quitar los dominios obsoletos, desconocidos o sospechosos. 




## <a name="next-steps"></a>Pasos siguientes

- [Integración de registros de Azure AD con registros de Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Creación, visualización y administración de alertas de registro mediante Azure Monitor](../../azure-monitor/alerts/alerts-log.md)
- [Administración de la confianza de AD FS con Azure AD mediante Azure AD Connect](how-to-connect-azure-ad-trust.md)
- [Procedimientos recomendados para proteger los Servicios de federación de Active Directory](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)
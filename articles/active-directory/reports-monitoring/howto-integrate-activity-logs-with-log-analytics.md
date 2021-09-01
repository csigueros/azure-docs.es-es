---
title: Transmisión de registros de Azure Active Directory a registros de Azure Monitor | Microsoft Docs
description: Aprenda a integrar registros de Azure Active Directory con registros de Azure Monitor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/09/2021
ms.author: markvi
ms.reviewer: besiler
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44599666f6da3fddcd6c3df36daf25faac807e6e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429316"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integración de registros de Azure AD con registros de Azure Monitor

Siga los pasos de este artículo para integrar los registros de Azure Active Directory (Azure AD) con Azure Monitor.

Use la integración de los registros de actividad de Azure AD en los registros de Azure Monitor para realizar tareas como:

 * Comparar los registros de inicio de sesión de Azure AD con los registros de seguridad publicados por Azure Security Center.
  
 * Solucionar problemas de cuellos de botella de rendimiento en la página de inicio de sesión de la aplicación mediante la correlación de datos de rendimiento de la aplicación de Azure Application Insights.

 * Analizar tanto los usuarios que suponen un riesgo como los registros de detección de riesgo de Identity Protection para detectar amenazas en el entorno (versión preliminar pública)
 
 * Identificar los inicios de sesión de las aplicaciones que usan la Biblioteca de autenticación de Active Directory (ADAL) para la autenticación. [Está a punto llegar la fecha de finalización del soporte técnico de ADAL](../develop/msal-migration.md).

Este vídeo de una sesión de Microsoft Ignite muestra las ventajas del uso de registros de Azure Monitor para los registros de Azure AD en escenarios prácticos:

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

## <a name="supported-reports"></a>Informes admitidos

Puede enrutar los registros de actividad de auditoría y los registros de actividad de inicio de sesión en registros de Azure Monitor para su posterior análisis. 

* **Registros de auditoría**: el [informe de actividad de registros de auditoría](concept-audit-logs.md) le proporciona acceso al historial de todas las tareas llevadas a cabo en el inquilino.
* **Registros de inicio de sesión**: Con el [informe de actividad de inicios de sesión](concept-sign-ins.md), puede determinar quién ha realizado las tareas notificadas en el informe de registros de auditoría.
* **Registros de aprovisionamiento**: Con los [registros de aprovisionamiento](../app-provisioning/application-provisioning-log-analytics.md), puede supervisar qué usuarios se han creado, actualizado y eliminado en todas las aplicaciones de terceros. 
* **Registros de usuarios de riesgo (versión preliminar pública)** : con los [registros de usuarios de riesgo](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), se pueden supervisar los cambios en el nivel de riesgo de los usuarios y en la actividad de corrección. 
* **Registros de detecciones de riesgo (versión preliminar pública)** : con los [registros de detecciones de riesgo](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections) se pueden supervisar las detecciones de riesgo del usuario y analizar las tendencias en la actividad de riesgo detectada en la organización. 


## <a name="prerequisites"></a>Requisitos previos 

Para usar esta característica, necesita:

* Suscripción a Azure. Si no tiene ninguna suscripción de Azure, puede [registrarse para obtener una evaluación gratuita](https://azure.microsoft.com/free/).
* Un inquilino de Azure AD.
* Un usuario que sea *administrador global* o *administrador de seguridad* para el inquilino de Azure AD.
* Un área de trabajo de Log Analytics en la suscripción a Azure. Aprenda a [crear un área de trabajo de Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="licensing-requirements"></a>Requisitos de concesión de licencia

Necesita un inquilino de Azure AD Premium P1 o P2 para usar esta característica. Puede encontrar el tipo de licencia del inquilino en la página **[Información general](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)** en **Azure Active Directory**.

![Información del inquilino](./media/howto-integrate-activity-logs-with-log-analytics/tenant-information.png)
 
Si desea saber durante cuánto tiempo se almacenan los datos de actividad en un inquilino prémium, consulte [¿Durante cuánto tiempo Azure AD almacena los datos?](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)

## <a name="send-logs-to-azure-monitor"></a>Envío de registros a Azure Monitor

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

2. Seleccione **Azure Active Directory** > **Configuración de diagnóstico** -> **Agregar configuración de diagnóstico**. También puede seleccionar **Exportar configuraciones** desde la página **Registros de auditoría** o **Inicios de sesión** para ir a la página de configuración de diagnóstico.  
    
3. En el menú **Configuración de diagnóstico**, seleccione la casilla **Enviar a área de trabajo de Log Analytics** y, después, seleccione **Configurar**.

4. Seleccione el área de trabajo de Log Analytics a la que quiere enviar los registros o cree una nueva área de trabajo en el cuadro de diálogo proporcionado.  

5. Realice cualquiera de las siguientes acciones, o todas ellas:
    * Para enviar los registros de auditoría al área de trabajo de Log Analytics, seleccione la casilla **AuditLogs**. 
    * Para enviar los registros de inicio de sesión al área de trabajo de Log Analytics, seleccione la casilla **SignInLogs**.
    * Para enviar los registros de inicio de sesión de los usuarios no interactivos al área de trabajo de Log Analytics, active la casilla **NonInteractiveUserSignInLogs**.
    * Para enviar los registros de inicio de sesión de las entidades de servicio al área de trabajo de Log Analytics, active la casilla **ServicePrincipleSignInLogs**.
    * Para enviar los registros de inicio de sesión de entidades administradas al área de trabajo de Log Analytics, active la casilla **ManagedIdentitySignInLogs**.
    * Para enviar registros de aprovisionamiento al área de trabajo de Log Analytics, active la casilla **ProvisioningLogs**.
    * Para enviar Servicios de federación de Active Directory (AD FS) de inicio de sesión (ADFS) al área de trabajo de Log Analytics, seleccione **ADFSSignInLogs**.
    * Para enviar registros de usuarios de riesgo al área de trabajo de Log Analytics, active la casilla **RiskyUsers**. (versión preliminar pública)
    * Para enviar registros de detecciones de riesgo al área de trabajo de Log Analytics, active la casilla **UserRiskEvents**. (versión preliminar pública)

6. Seleccione **Guardar** para guardar la configuración.

    ![Configuración de diagnóstico](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Después de unos 15 minutos, compruebe que los eventos se transmiten al área de trabajo de Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

* [Análisis de registros de actividad de Azure AD con registros de Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalación y uso de las vistas de Log Analytics para Azure Active Directory](howto-install-use-log-analytics-views.md)

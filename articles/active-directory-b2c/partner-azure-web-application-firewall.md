---
title: Tutorial para configurar Azure Active Directory B2C con Azure Web Application Firewall
titleSuffix: Azure AD B2C
description: Tutorial para configurar Azure Active Directory B2C con Azure Web Application Firewall para proteger las aplicaciones de ataques malintencionados
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 03c66f9610ab8dc309098e1eee695ded477938bc
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768450"
---
# <a name="tutorial-configure-azure-web-application-firewall-with-azure-active-directory-b2c"></a>Tutorial: Configuración de Azure Web Application Firewall con Azure Active Directory B2C

En este tutorial de ejemplo, aprenderá a habilitar la solución[Azure Web Application Firewall (WAF)](https://azure.microsoft.com/services/web-application-firewall/#overview) para el inquilino de Azure Active Directory (AD) B2C con dominio personalizado. Azure WAF ofrece una protección centralizada de las aplicaciones web contra las vulnerabilidades de seguridad comunes.

>[!NOTE]
>Esta característica está en versión preliminar pública.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Una suscripción de Azure: Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

- [Un inquilino de Azure AD B2C](tutorial-create-tenant.md): el servidor de autorización, responsable de comprobar las credenciales del usuario en función de las directivas personalizadas definidas en el inquilino.  También se conoce como proveedor de identidades.

- [Azure Front Door (AFD)](https://docs.microsoft.com/azure/frontdoor/): el responsable de habilitar dominios personalizados para el inquilino de Azure AD B2C.  

- [Azure WAF](https://azure.microsoft.com/services/web-application-firewall/#overview): administra todo el tráfico que se envía al servidor de autorización.

## <a name="azure-ad-b2c-setup"></a>Configuración de Azure AD B2C

Para usar dominios personalizados en Azure AD B2C, es necesario usar la característica de dominio personalizado que proporciona AFD. Obtenga información sobre la [habilitación de dominios personalizados de Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow).  

Una vez que el dominio de Azure AD B2C personalizado se haya configurado correctamente mediante AFD, [pruebe el dominio personalizado](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-custom-policy#test-your-custom-domain) antes de continuar.  

## <a name="onboard-with-azure-waf"></a>Incorporación con Azure WAF

Para habilitar Azure WAF, configure una directiva de WAF y asóciela a AFD para la protección.

### <a name="create-a-waf-policy"></a>Creación de una directiva WAF

Cree una directiva básica de WAF con un conjunto de reglas predeterminado (DRS) administrado en [Azure Portal](https://portal.azure.com).

1. Vaya a [Azure Portal](https://portal.azure.com). Seleccione **Crear un recurso** y, a continuación, busque Azure WAF. Seleccione **Azure Web Application Firewall (WAF)**  > **Crear**.

2. Vaya a la página de **Crear directiva de WAF** y seleccione la pestaña **Aspectos básicos**. Escriba la siguiente información y acepte los valores predeterminados para el resto de la configuración.

| Value | Descripción |
|:--------|:-------|
| Directiva de | WAF global (Front Door)|
| SKU de Front Door | Seleccione entre la SKU Básica, Estándar o Premium. |
|Suscripción | Seleccione el nombre de la suscripción a Front Door. |
| Resource group | Seleccione el nombre del grupo de recursos de Front Door. |
| Nombre de la directiva | Escriba un nombre único para la directiva WAF. |
| Estado de directiva | Establézcalo como Habilitado. |
| Modo de directiva | Establézcalo como Detección. |

3. Seleccionar **Revisar y crear**.

4. Vaya a la pestaña **Asociación** de la página Creación de una directiva WAF, seleccione **Asociar un perfil de Front Door** e indique la configuración siguiente.

| Value | Descripción |
|:----|:------|
| Front Door | Seleccione el nombre Front Door asociado al dominio personalizado de Azure AD B2C. |
| Dominios | Seleccione los dominios personalizados de Azure AD B2C a los que desea asociar la directiva WAF.|

5. Seleccione **Agregar**.

6. Seleccione **Revisar y crear** y, luego, **Crear**.

### <a name="change-policy-mode-from-detection-to-prevention"></a>Cambio del modo de directiva de detección a prevención

Cuando se crea una directiva WAF, de forma predeterminada está en modo de detección. En el modo de detección, WAF no bloquea las solicitudes, sino que las solicitudes que coinciden con las reglas de WAF se registran en los registros de WAF. Para obtener más información sobre el registro de WAF, consulte [Supervisión y registro de Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor).

La consulta de ejemplo muestra todas las solicitudes bloqueadas por la directiva de WAF en las últimas 24 horas. Los detalles incluyen el nombre de la regla, los datos de solicitud, la acción realizada por la directiva y el modo de directiva.

![La imagen muestra las solicitudes bloqueadas.](./media/partner-azure-web-application-firewall/blocked-requests-query.png)

![La imagen muestra los detalles de las solicitudes bloqueadas.](./media/partner-azure-web-application-firewall/blocked-requests-details.png)

Se recomienda permitir que WAF capture solicitudes en modo de detección. Revise los registros de WAF para determinar si hay alguna regla en la directiva que esté causando resultados falsos positivos. A continuación, [excluya las reglas de WAF basadas en los registros de WAF](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-exclusion#define-exclusion-based-on-web-application-firewall-logs).

Para ver WAF en acción, use Cambiar al modo de prevención para cambiar del modo de detección al modo de prevención. Todas las solicitudes que coinciden con las reglas que se definen en el conjunto de reglas predeterminado (DRS) se bloquean y se registran en los registros de WAF.

![La imagen muestra el cambio al modo de prevención.](./media/partner-azure-web-application-firewall/switch-to-prevention-mode.png)

En caso de que quiera volver al modo de detección, puede hacerlo mediante la opción Cambiar al modo de detección.

![La imagen muestra el cambio al modo de detección.](./media/partner-azure-web-application-firewall/switch-to-detection-mode.png)

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y registro de Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor/)

- [WAF con listas de exclusión de servicios de Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-exclusion/)

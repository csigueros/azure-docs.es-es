---
title: Recomendaciones de seguridad de Microsoft Defender for Cloud para MFA
description: Aprenda a aplicar la autenticación multifactor para las suscripciones de Azure mediante Microsoft Defender for Cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ba741d97e3a9537b64957ce419b06208fd2a528
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092925"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Administración de la aplicación de la autenticación multifactor (MFA) en las suscripciones

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si solo usa contraseñas para autenticar a los usuarios, está dejando al descubierto un vector de ataque. Los usuarios suelen usar contraseñas no seguras o reutilizarlas para varios servicios. Con [MFA](https://www.microsoft.com/security/business/identity/mfa) habilitada, las cuentas son más seguras y los usuarios aún podrán autenticarse en casi cualquier aplicación con inicio de sesión único (SSO).

Hay varias maneras de habilitar MFA para los usuarios de Azure Active Directory (AD) en función de las licencias que posea una organización. En esta página se proporcionan los detalles de cada uno en el contexto de Microsoft Defender for Cloud.


## <a name="mfa-and-microsoft-defender-for-cloud"></a>MFA y Microsoft Defender for Cloud 

Defender for Cloud coloca un valor alto en MFA. El control de seguridad que más contribuye a la puntuación de seguridad es **Habilitar MFA**. 

Las recomendaciones del control Habilitar MFA garantizan que se cumplen los procedimientos recomendados para los usuarios de las suscripciones:

- MFA debe estar habilitada en las cuentas con permisos de propietario en la suscripción
- MFA debe estar habilitada en las cuentas con permisos de escritura en la suscripción

Hay tres maneras de habilitar MFA y cumplir las dos recomendaciones de Defender for Cloud: valores predeterminados de seguridad, asignación por usuario y directiva de acceso condicional (CA). Cada una de estas opciones se explica más adelante.

### <a name="free-option---security-defaults"></a>Opción gratuita: valores predeterminados de seguridad
Si usa la edición gratuita de Azure AD, use los [valores predeterminados de seguridad](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) para habilitar la autenticación multifactor en su inquilino.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA para clientes de Microsoft 365 Empresa, E3 o E5
Los clientes con Microsoft 365 pueden usar una **asignación por usuario**. En este caso, Azure AD MFA está o bien habilitado o bien deshabilitado para todos los usuarios, para todos los eventos de inicio de sesión. No se puede habilitar la autenticación multifactor para un subconjunto de usuarios, o en ciertos escenarios, y la administración se realiza a través del portal de Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA para clientes de Azure AD Premium
Para mejorar la experiencia del usuario, actualice a Azure AD Premium P1 o P2 para usar las opciones de la **directiva de acceso condicional (CA)** . Para configurar una directiva de CA, necesitará [permisos de inquilino de Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

La directiva de CA debe:
- Aplicar MFA
- Incluir el id. de la aplicación de administración de Microsoft Azure (797f4846-ba00-4fd7-ba43-dac1f8f63013) o todas las aplicaciones
- No excluir el id. de la aplicación de administración de Microsoft Azure

Los clientes de **Azure AD Premium P1** pueden usar el acceso condicional de Azure AD para solicitar a los usuarios la autenticación multifactor en determinados escenarios o eventos y adaptarse así a los requisitos empresariales. Otras licencias que incluyen esta funcionalidad son Enterprise Mobility + Security E3, Microsoft 365 F1 y Microsoft 365 E3.

**Azure AD Premium P2** proporciona las características de seguridad más seguras y una experiencia de usuario mejorada. Esta licencia agrega [acceso condicional basado en riesgos](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) a las características de Azure AD Premium P1. El acceso condicional basado en riesgos se adapta a los patrones de los usuarios y minimiza los mensajes de autenticación multifactor. Otras licencias que incluyen esta funcionalidad son Enterprise Mobility + Security E5 y Microsoft 365 E5.

Obtenga más información en la [documentación sobre el acceso condicional Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificación de cuentas sin Multi-Factor Authentication (MFA) habilitado

Puede ver la lista de cuentas de usuario sin MFA habilitada desde la página de detalles de recomendaciones de Defender for Cloud o mediante Azure Resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Visualización de las cuentas sin MFA habilitado en Azure Portal
En la página de detalles de la recomendación, seleccione una suscripción de la lista **Recursos con estado incorrecto**, o bien seleccione **Tomar medidas** y se mostrará la lista.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Visualización de las cuentas sin MFA habilitado mediante Azure Resource Graph
Para ver qué cuentas no tienen MFA habilitado, use la siguiente consulta de Azure Resource Graph. La consulta devuelve todas los recursos (cuentas) incorrectos de la recomendación "MFA debe estar habilitado en las cuentas con permisos de propietario en la suscripción". 

1. Abra **Azure Resource Graph Explorer**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Inicio de la página de recomendaciones de Azure Resource Graph Explorer**" :::

1. Escriba la siguiente consulta y seleccione **Ejecutar consulta**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. La propiedad `additionalData` revela la lista de identificadores de objeto de cuenta para las cuentas que no tienen MFA aplicado. 

    > [!NOTE]
    > Las cuentas se muestran como identificadores de objeto en lugar de nombres de cuenta para proteger la privacidad de los titulares.

> [!TIP]
> Como alternativa, puede usar el método [Assessments - Get](/rest/api/securitycenter/assessments/get) de la API de REST de Defender for Cloud.


## <a name="faq---mfa-in-defender-for-cloud"></a>Preguntas más frecuentes: MFA en Defender for Cloud

- [Ya estamos usando la directiva de CA para aplicar MFA. ¿Por qué todavía se obtienen las recomendaciones de Defender for Cloud?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [Estamos usando una herramienta de MFA de terceros para aplicar MFA. ¿Por qué todavía se obtienen las recomendaciones de Defender for Cloud?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [¿Por qué Defender for Cloud muestra las cuentas de usuario sin permisos en la suscripción como "que requieren MFA"?](#why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Estamos aplicando MFA con PIM. ¿Por qué las cuentas de PIM se muestran como no conformes?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [¿Puedo excluir o descartar algunas de las cuentas?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [¿Existen limitaciones para las protecciones de identidad y acceso de Defender for Cloud?](#are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>Ya usamos una directiva de acceso condicional para aplicar MFA. ¿Por qué todavía se obtienen las recomendaciones de Defender for Cloud?
Para investigar por qué se siguen generando recomendaciones, compruebe las siguientes opciones de configuración en la directiva de acceso condicional de MFA:

- Ha incluido las cuentas en la sección **Usuarios** de la directiva de acceso condicional de MFA (o uno de los grupos en la sección **Grupos**).
- El id. de la aplicación de administración de Azure (797f4846-ba00-4fd7-ba43-dac1f8f63013) o todas las aplicaciones se incluyen en la sección **Aplicaciones** de la directiva de CA de MFA.
- El id. de la aplicación de administración de Azure no se ha excluido de la sección **Aplicaciones** de la directiva de CA de MFA.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>Usamos una herramienta de MFA de terceros para aplicar MFA. ¿Por qué todavía se obtienen las recomendaciones de Defender for Cloud?
Las recomendaciones de MFA de Defender for Cloud no admiten herramientas de MFA de terceros (por ejemplo, DUO).

Si las recomendaciones son irrelevantes para su organización, considere la posibilidad de marcarlas como "mitigadas", como se describe en [Exención de recursos y recomendaciones de la puntuación de seguridad](exempt-resource.md). También puede [deshabilitar una recomendación](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>¿Por qué Defender for Cloud muestra las cuentas de usuario sin permisos en la suscripción como "que requieren MFA"?
Las recomendaciones de MFA de Defender for Cloud hacen referencia a los roles [RBAC de Azure](../role-based-access-control/role-definitions-list.md) y al rol de [administradores de suscripciones clásicas de Azure](../role-based-access-control/classic-administrators.md). Compruebe que ninguna de las cuentas tenga dichos roles.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Aplicamos MFA con PIM. ¿Por qué las cuentas de PIM se muestran como no conformes?
Las recomendaciones de MFA de Defender for Cloud actualmente no admiten cuentas de PIM. Puede agregar estas cuentas a una directiva de CA en la sección de usuarios o grupos.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>¿Puedo excluir o descartar algunas de las cuentas?
Actualmente no se admite la funcionalidad de excluir algunas cuentas que no usan MFA.  

### <a name="are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections"></a>¿Existen limitaciones para las protecciones de identidad y acceso de Defender for Cloud?
Existen limitaciones para las protecciones de identidad y acceso de Defender for Cloud:

- Las recomendaciones de identidad no están disponibles para las suscripciones con más de 600 cuentas. En tales casos, estas recomendaciones se enumerarán en "evaluaciones no disponibles".
- Las recomendaciones de identidad no están disponibles para los agentes de administración del asociado del Proveedor de soluciones en la nube (CSP).
- Las recomendaciones de identidad no identifican las cuentas que se administran con un sistema de Privileged Identity Management (PIM). Si usa una herramienta PIM, podría observar resultados inexactos en el control **Manage access and permissions** (Administrar el acceso y los permisos).


## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte el siguiente artículo:

- [Protección de la red en Microsoft Defender for Cloud](protect-network-resources.md)

---
title: Recomendaciones de seguridad de Azure Security Center para MFA
description: Obtenga información sobre cómo aplicar la autenticación multifactor en las suscripciones de Azure con Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 6ca7b13a7a0873300995a91a6469fc29050f3c95
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047045"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Administración de la aplicación de la autenticación multifactor (MFA) en las suscripciones

Si solo usa contraseñas para autenticar a los usuarios, está dejando al descubierto un vector de ataque. Los usuarios suelen usar contraseñas no seguras o reutilizarlas para varios servicios. Con [MFA](https://www.microsoft.com/security/business/identity/mfa) habilitada, las cuentas son más seguras y los usuarios aún podrán autenticarse en casi cualquier aplicación con inicio de sesión único (SSO).

Hay varias maneras de habilitar MFA para los usuarios de Azure Active Directory (AD) en función de las licencias que posea una organización. En esta página se proporcionan los detalles de cada una en el contexto de Azure Security Center.


## <a name="mfa-and-security-center"></a>MFA y Security Center 

Security Center valora mucho la autenticación multifactor. El control de seguridad que más contribuye a la puntuación de seguridad es **Habilitar MFA**. 

Las recomendaciones del control Habilitar MFA garantizan que se cumplen los procedimientos recomendados para los usuarios de las suscripciones:

- MFA debe estar habilitada en las cuentas con permisos de propietario en la suscripción
- MFA debe estar habilitada en las cuentas con permisos de escritura en la suscripción

Hay tres maneras de habilitar MFA para cumplir con las dos recomendaciones de Security Center: los valores predeterminados de seguridad, la asignación por usuario, y la directiva de acceso condicional (CA). Cada una de estas opciones se explica más adelante.

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

Puede ver la lista de cuentas de usuario sin MFA habilitado en la página de detalles de la recomendación de Security Center o mediante Azure Resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Visualización de las cuentas sin MFA habilitado en Azure Portal
En la página de detalles de la recomendación, seleccione una suscripción de la lista **Recursos con estado incorrecto**, o bien seleccione **Tomar medidas** y se mostrará la lista.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Visualización de las cuentas sin MFA habilitado mediante Azure Resource Graph
Para ver qué cuentas no tienen MFA habilitado, use la siguiente consulta de Azure Resource Graph. La consulta devuelve todas los recursos (cuentas) incorrectos de la recomendación "MFA debe estar habilitado en las cuentas con permisos de propietario en la suscripción". 

1. Abra **Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Inicio de la página de recomendaciones de Azure Resource Graph Explorer**" :::

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
> También puede usar el método [Assessments - Get](/rest/api/securitycenter/assessments/get) de la API REST de Security Center.


## <a name="faq---mfa-in-security-center"></a>Preguntas frecuentes sobre MFA en Security Center

- [Ya estamos usando la directiva de CA para hacer cumplir MFA. ¿Por qué seguimos recibiendo las recomendaciones de Security Center?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Vamos a usar una herramienta de MFA de terceros para aplicar MFA. ¿Por qué todavía se obtienen las recomendaciones de Security Center?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [¿Por qué se muestran en Security Center cuentas de usuario sin permisos en la suscripción que requieren MFA?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Estamos aplicando MFA con PIM. ¿Por qué las cuentas de PIM se muestran como no conformes?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [¿Puedo excluir o descartar algunas de las cuentas?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [¿Existen limitaciones para las protecciones de acceso e identidad de Security Center?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Ya usamos una directiva de acceso condicional para aplicar MFA. ¿Por qué seguimos recibiendo recomendaciones de Security Center?
Para investigar por qué se siguen generando recomendaciones, compruebe las siguientes opciones de configuración en la directiva de acceso condicional de MFA:

- Ha incluido las cuentas en la sección **Usuarios** de la directiva de acceso condicional de MFA (o uno de los grupos en la sección **Grupos**).
- El id. de la aplicación de administración de Azure (797f4846-ba00-4fd7-ba43-dac1f8f63013) o todas las aplicaciones se incluyen en la sección **Aplicaciones** de la directiva de CA de MFA.
- El id. de la aplicación de administración de Azure no se ha excluido de la sección **Aplicaciones** de la directiva de CA de MFA.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Usamos una herramienta de MFA de terceros para aplicar MFA. ¿Por qué seguimos recibiendo recomendaciones de Security Center?
Las recomendaciones de MFA de Security Center no admiten herramientas de MFA de terceros (por ejemplo, DUO).

Si las recomendaciones son irrelevantes para su organización, considere la posibilidad de marcarlas como "mitigadas", como se describe en [Exención de recursos y recomendaciones de la puntuación de seguridad](exempt-resource.md). También puede [deshabilitar una recomendación](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>¿Por qué se muestran en Security Center cuentas de usuario sin permisos en la suscripción que requieren MFA?
Las recomendaciones de MFA de Security Center hacen referencia a roles de [RBAC de Azure](../role-based-access-control/role-definitions-list.md) y al rol [Administradores de la suscripción clásica de Azure](../role-based-access-control/classic-administrators.md). Compruebe que ninguna de las cuentas tenga dichos roles.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Aplicamos MFA con PIM. ¿Por qué las cuentas de PIM se muestran como no conformes?
Las recomendaciones de MFA de Security Center no admiten actualmente cuentas de PIM. Puede agregar estas cuentas a una directiva de CA en la sección de usuarios o grupos.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>¿Puedo excluir o descartar algunas de las cuentas?
Actualmente no se admite la funcionalidad de excluir algunas cuentas que no usan MFA.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>¿Existen limitaciones para las protecciones de acceso e identidad de Security Center?
Existen algunas limitaciones para las protecciones de acceso e identidad de Security Center:

- Las recomendaciones de identidad no están disponibles para las suscripciones con más de 600 cuentas. En tales casos, estas recomendaciones se enumerarán en "evaluaciones no disponibles".
- Las recomendaciones de identidad no están disponibles para los agentes de administración del asociado del Proveedor de soluciones en la nube (CSP).
- Las recomendaciones de identidad no identifican las cuentas que se administran con un sistema de Privileged Identity Management (PIM). Si usa una herramienta PIM, podría observar resultados inexactos en el control **Manage access and permissions** (Administrar el acceso y los permisos).


## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte el siguiente artículo:

- [Protección de las redes en Azure Security Center](security-center-network-recommendations.md)
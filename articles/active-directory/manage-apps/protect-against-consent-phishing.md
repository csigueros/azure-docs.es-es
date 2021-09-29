---
title: Protección contra la suplantación de identidad de consentimiento | Administración de aplicaciones de Azure AD
description: Obtenga información sobre las formas de mitigar los ataques de suplantación de identidad de consentimiento basados en la aplicación mediante Azure AD.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: tilarso
ms.openlocfilehash: 6d2a6afe4555bc9324241616ae6c22494216a146
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791448"
---
# <a name="protecting-against-consent-phishing"></a>Protección contra la suplantación de identidad en el consentimiento

La productividad ya no se limita a las redes privadas y el trabajo ha cambiado drásticamente hacia los servicios en la nube. Aunque las aplicaciones en la nube permiten a los empleados ser productivos de forma remota, los atacantes también pueden usar ataques basados en la aplicación para obtener acceso a datos valiosos de la organización. Es posible que esté familiarizado con los ataques centrados en los usuarios, como la suplantación de identidad en el correo electrónico o el riesgo de credenciales. La ***suplantación de identidad en el consentimiento*** es otro vector de amenaza que se debe tener en cuenta.
En este artículo se explora qué es la suplantación de identidad en el consentimiento, qué hace Microsoft para protegerle y qué pasos pueden seguir las organizaciones para protegerse.

## <a name="what-is-consent-phishing"></a>¿Qué es la suplantación de identidad en el consentimiento?

Los ataques de suplantación de identidad en el consentimiento engañan a los usuarios para conceder permisos a aplicaciones en la nube malintencionadas. Estas aplicaciones malintencionadas pueden obtener acceso a los datos y servicios en la nube legítimos de los usuarios. A diferencia del riesgo de credenciales, los *actores de amenazas* que realizan la suplantación de identidad en el consentimiento se dirigirán directamente a usuarios que puedan conceder acceso a sus datos personales o de la organización. La pantalla de consentimiento muestra todos los permisos que recibe la aplicación. Dado que la aplicación la hospeda un proveedor legítimo (como la plataforma de identidad de Microsoft), los usuarios que no sospechan aceptan los términos o pulsan "*Aceptar*", lo que concede a una aplicación malintencionada los permisos solicitados para los datos del usuario o la organización.

:::image type="content" source="./media/protect-consent-phishing/permissions-requested.png" alt-text="Captura de pantalla que muestra la ventana de permisos solicitados que requieren consentimiento del usuario.":::

*Ejemplo de una aplicación de OAuth que solicita acceso a una amplia variedad de permisos.*

## <a name="mitigating-consent-phishing-attacks-using-azure-ad"></a>Mitigación de ataques de suplantación de identidad en el consentimiento mediante Azure AD

Los administradores, los usuarios o los investigadores de seguridad de Microsoft pueden marcar aplicaciones de OAuth que parecen comportarse de forma sospechosa. Microsoft revisará una aplicación marcada para determinar si la aplicación infringe los términos del servicio. Si se confirma una infracción, Azure AD deshabilitará la aplicación e impedirá su uso en todos los servicios Microsoft.

Cuando Azure AD deshabilita una aplicación de OAuth, suceden algunas cosas:
- La aplicación malintencionada y las entidades de servicio relacionadas se colocan en un estado completamente deshabilitado. Todas las nuevas solicitudes de tokens o solicitudes de tokens de actualización se deshabilitan, pero los tokens de acceso existentes siguen siendo válidos hasta su expiración.
- Se expone el estado deshabilitado a través de una propiedad expuesta denominada *disabledByMicrosoftStatus* en los tipos de recursos de [aplicación](/graph/api/resources/application?view=graph-rest-1.0&preserve-view=true) y [entidad de servicio](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true) relacionados de Microsoft Graph.
- Los administradores globales que pueden haber tenido un usuario en su organización que haya dado su consentimiento a una aplicación antes de la deshabilitación por parte de Microsoft deberían recibir un correo electrónico que refleje la acción realizada y los pasos recomendados para investigar y mejorar su posición de seguridad.

## <a name="recommended-response-and-remediation"></a>Respuesta y corrección recomendadas

Si su organización se ha visto afectada por una aplicación deshabilitada por Microsoft, se recomiendan estos pasos inmediatos para proteger el entorno:

1. Investigue la actividad de la aplicación deshabilitada, lo que incluye:
    - Permisos delegados o permisos de aplicación solicitados por la aplicación.
    - Registros de auditoría de Azure AD para la actividad de la aplicación y la actividad de inicio de sesión para los usuarios autorizados a usar la aplicación.
1. Revise e implemente las [instrucciones para defenderse de las concesiones de consentimiento ilegales](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) en productos en la nube de Microsoft, incluidos los permisos de auditoría y el consentimiento para la aplicación deshabilitada o cualquier otra aplicación sospechosa detectada durante la revisión.
1. Implemente los procedimientos recomendados para la protección frente a la suplantación de identidad en el consentimiento, que se describen a continuación.


## <a name="best-practices-for-hardening-against-consent-phishing-attacks"></a>Procedimientos recomendados para la protección frente a ataques de suplantación de identidad en el consentimiento

El objetivo de Microsoft es que los administradores tengan el control, para lo cual reciben la información y las funcionalidades adecuadas para controlar los permisos y el uso de las aplicaciones en de las organizaciones. Aunque los atacantes nunca descansan, hay pasos que las organizaciones pueden seguir para mejorar su posición de seguridad. Algunos procedimientos recomendados que se deben seguir son los siguientes:

* Formar a la organización sobre el funcionamiento de nuestro marco de permisos y consentimiento
    - Comprenda los datos y permisos que una aplicación solicita y aprenda cómo funcionan  [los permisos y el consentimiento](../develop/v2-permissions-and-consent.md) en nuestra plataforma.
    - Asegúrese de que los administradores saben cómo  [administrar y evaluar las solicitudes de consentimiento](./manage-consent-requests.md).
    - [Audite las aplicaciones y los permisos consentidos](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) de su organización rutinariamente para asegurarse de que las aplicaciones que se usan solo acceden a los datos que necesitan y respetan los principios de privilegios mínimos.
* Saber cómo detectar y bloquear tácticas comunes de suplantación de identidad en el consentimiento
    - Compruebe si hay errores de ortografía y gramática. Si un mensaje de correo electrónico o la pantalla de consentimiento de la aplicación tienen errores ortográficos y gramaticales, es probable que se trate de una aplicación sospechosa. En ese caso, puede notificarlo directamente en el [mensaje de consentimiento](../develop/application-consent-experience.md#building-blocks-of-the-consent-prompt) con el vínculo "*Notificarlo aquí*". Microsoft investigará si se trata de una aplicación malintencionada y, si se confirma, la deshabilitará.
    - No confíe en nombres de aplicación y direcciones URL de dominio como fuente de autenticidad. A los atacantes les gusta suplantar nombres y dominios de aplicaciones para dar la impresión de que proceden de compañías o servicios legítimos y lograr el consentimiento para una aplicación malintencionada. En su lugar, valide el origen de la dirección URL del dominio y use aplicaciones de [publicadores verificados](../develop/publisher-verification-overview.md) siempre que sea posible.
    - Bloquee los [correos electrónicos de suplantación de identidad en el consentimiento con Microsoft Defender para Office 365](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies#impersonation-settings-in-anti-phishing-policies-in-microsoft-defender-for-office-365) mediante la protección contra campañas de suplantación de identidad en las que un atacante suplanta a un usuario conocido de su organización.
    - Configure directivas de seguridad de aplicaciones en la nube de Microsoft como  [directivas de actividad](/cloud-app-security/user-activity-policies),  [directivas de detección de anomalías](/cloud-app-security/anomaly-detection-policy) y  [directivas de aplicación de OAuth](/cloud-app-security/app-permission-policy) para ayudar a administrar y a tomar medidas en caso de actividades anómalas de la aplicación en su organización.
    - Investigue y busque ataques de suplantación de identidad en el consentimiento siguiendo las instrucciones de [búsqueda avanzada con Microsoft 365 Defender](/microsoft-365/security/defender/advanced-hunting-overview).
* Permitir el acceso a las aplicaciones de confianza y protegerse de las que no lo son
    - Use aplicaciones cuyo publicador sea verificado. La [verificación del publicador](../develop/publisher-verification-overview.md) ayuda a los administradores y a los usuarios finales a reconocer la autenticidad de los desarrolladores de aplicaciones a través de un proceso de escrutinio respaldado por Microsoft.
    - [Configure el consentimiento del usuario](./configure-user-consent.md?tabs=azure-portal) para permitir a los usuarios que solo den su consentimiento a aplicaciones específicas de confianza, como aplicaciones desarrolladas por su organización o de publicadores verificados.
    - Cree directivas de [gobernanza de aplicaciones](/microsoft-365/compliance/app-governance-manage-app-governance) proactivas para supervisar el comportamiento de las aplicaciones de terceros en la plataforma Microsoft 365 y corregir comportamientos habituales de las aplicaciones sospechosas.

## <a name="next-steps"></a>Pasos siguientes

* [Investigación de la concesión de consentimiento de la aplicación](/security/compass/incident-response-playbook-app-consent)
* [Administración del acceso a las aplicaciones](./what-is-access-management.md)
* [Restricción de las operaciones de consentimiento del usuario en Azure AD](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations)
---
title: 'Directivas comunes de acceso condicional: Azure Active Directory'
description: Directivas comunes de acceso condicional para organizaciones
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e0fdbc31b6bb9719f2b5bfcac8c80e1d3fce64
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709938"
---
# <a name="common-conditional-access-policies"></a>Directivas de acceso condicional habituales

Los [valores predeterminados de seguridad](../fundamentals/concept-fundamentals-security-defaults.md) son ideales para algunas organizaciones, pero muchas otras necesitan más flexibilidad que la que ofrecen. Muchas organizaciones necesitan excluir cuentas específicas, como sus cuentas de administración de acceso de emergencia, de las directivas de acceso condicional. Las directivas a las que se hace referencia en este artículo se pueden personalizar en función de las necesidades de la organización. Las organizaciones [pueden usar el modo de solo informe para el acceso condicional a fin de determinar los resultados de las nuevas decisiones de directiva](concept-conditional-access-report-only.md).

## <a name="conditional-access-templates-preview"></a>Plantillas de acceso condicional (versión preliminar)

Las plantillas de acceso condicional están diseñadas para proporcionar un método cómodo para implementar nuevas directivas alineadas con las recomendaciones de Microsoft. Estas plantillas están diseñadas para proporcionar la máxima protección alineada con las directivas de uso frecuente en varios tipos y ubicaciones de clientes.

:::image type="content" source="media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png" alt-text="Directivas y plantillas de acceso condicional en Azure Portal." lightbox="media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png":::

Las 14 plantillas de directiva se dividen en directivas que se asignan a dispositivos o identidades de usuario. Busque las plantillas en **Azure Portal** > **Azure Active Directory** > **Seguridad** > **Acceso condicional** > **Crear una nueva directiva a partir de la plantilla**.

:::image type="content" source="media/concept-conditional-access-policy-common/create-policy-from-template-identity.png" alt-text="Cree una directiva de acceso condicional a partir de una plantilla preconfigurada en Azure Portal." lightbox="media/concept-conditional-access-policy-common/create-policy-from-template-identity.png":::

> [!IMPORTANT]
> Las directivas de plantilla de acceso condicional excluirán solo al usuario que crea la directiva de la plantilla. Si su organización necesita [excluir otras cuentas](../roles/security-emergency-access.md), abra la directiva y modifique los usuarios y grupos excluidos para incluirlos. 
> 
> De forma predeterminada, cada directiva se crea en un [modo de solo informe](concept-conditional-access-report-only.md), por lo que se recomienda que las organizaciones prueben y supervisen el uso, para garantizar el resultado previsto, antes de activar cada directiva.

- Identities
   - [Requerir autenticación multifactor a los administradores](howto-conditional-access-policy-admin-mfa.md)\*
   - [Protección del registro de información de seguridad](howto-conditional-access-policy-registration.md)
   - [Bloquear la autenticación heredada](howto-conditional-access-policy-block-legacy.md)\*
   - [Exigir la autenticación multifactor para todos los usuarios](howto-conditional-access-policy-all-users-mfa.md)\*
   - Requerir la autenticación multifactor para el acceso de invitado
   - [Requerir la autenticación multifactor para la administración de Azure](howto-conditional-access-policy-azure-management.md)\*
   - [Requerir una autenticación multifactor para el inicio de sesión de riesgo](howto-conditional-access-policy-risk.md) **Requiere Azure AD Premium P2**
   - [Requerir el cambio de contraseña para usuarios de alto riesgo](howto-conditional-access-policy-risk-user.md) **Requiere Azure AD Premium P2**
- Dispositivos
   - [Requerir un dispositivo unido a Azure AD híbrido o conforme para administradores](howto-conditional-access-policy-compliant-device.md)
   - Bloquear el acceso a una plataforma de dispositivo desconocida o no compatible
   - No hay ninguna sesión de explorador persistente
   - [Requerir aplicaciones cliente aprobadas o la protección de aplicaciones](howto-policy-approved-app-or-app-protection.md)
   - Requerir la autenticación multifactor o un dispositivo unido a Azure AD híbrido o compatible para todos los usuarios
   - Uso de restricciones impuestas por la aplicación para dispositivos no administrados

> \* Cuando se configuran juntas estas cuatro directivas, proporcionan una funcionalidad habilitada por [valores predeterminados de seguridad](../fundamentals/concept-fundamentals-security-defaults.md).

Las organizaciones que no se sientan cómodas al permitir que Microsoft cree estas directivas, pueden crearlas manualmente mediante la copia de la configuración del artículo **Ver el resumen de directivas** o pueden usar los artículos vinculados para crear dichas directivas. 

### <a name="other-policies"></a>Otras directivas

* [Bloquear el acceso por ubicación](howto-conditional-access-policy-location.md)
* [Bloquear el acceso, excepto para aplicaciones específicas](howto-conditional-access-policy-block-access.md)

## <a name="emergency-access-accounts"></a>Cuentas de acceso de emergencia

En los siguientes artículos encontrará más información sobre las cuentas de acceso de emergencia y por qué son importantes: 

* [Administración de cuentas de acceso de emergencia en Azure AD](../roles/security-emergency-access.md)
* [Crear una estrategia de administración de control de acceso resistente con Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Pasos siguientes

- [Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional.](troubleshoot-conditional-access-what-if.md)

- [Uso del modo de solo informe de acceso condicional para determinar los resultados de las nuevas decisiones de directiva.](concept-conditional-access-report-only.md)

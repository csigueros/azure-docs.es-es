---
title: Características compatibles de Azure AD
description: Obtenga información sobre las características de Azure AD que todavía se admiten en Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 99f6d58af1a8508b6e3582b4ec0139081a259046
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043004"
---
# <a name="supported-azure-ad-features"></a>Características compatibles de Azure AD

Un inquilino de Azure AD B2C es diferente de un inquilino de Azure Active Directory, que puede que ya tenga, pero todavía se basa en este. Las siguientes características de Azure AD se pueden usar en el inquilino de Azure AD B2C.

|Característica  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Grupos](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Los grupos se pueden usar para administrar cuentas administrativas y de usuario.| Los grupos se pueden usar para administrar cuentas administrativas. Las [cuentas de consumidor](user-overview.md#consumer-user) no pueden ser miembro de ningún grupo. |
| [Invitar a usuarios a External Identities](../active-directory//external-identities/add-users-administrator.md)| Puede invitar a usuarios y configurar las características de External Identities, como la federación y el inicio de sesión con cuentas de Facebook y Google. | Solo puede invitar a una cuenta de Microsoft o a un usuario de Azure AD a su inquilino de Azure AD para acceder a aplicaciones o administrar inquilinos. En el caso de las [cuentas de consumidor](user-overview.md#consumer-user), se usan directivas personalizadas y flujos de usuarios de Azure AD B2C para administrar usuarios y para el registro o el inicio de sesión con proveedores de identidades externos, como Google o Facebook. |
| [Roles y administradores](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Totalmente compatible con cuentas administrativas y de usuario. | Los roles no son compatibles con las [cuentas de consumidor](user-overview.md#consumer-user). Las cuentas de consumidor no tienen acceso a ningún recurso de Azure.|
| [Nombres de dominio personalizados](../active-directory/fundamentals/add-custom-domain.md) |  Puede usar los dominios personalizados de Azure AD solo para las cuentas administrativas. | Las [cuentas de consumidor](user-overview.md#consumer-user) pueden iniciar sesión con un nombre de usuario, un número de teléfono o cualquier dirección de correo electrónico. Puede usar los [dominios personalizados](custom-domain.md) en las direcciones URL de redireccionamiento.|
| [Acceso condicional](../active-directory/conditional-access/overview.md) | Totalmente compatible con cuentas administrativas y de usuario. | Se admite un subconjunto de características de acceso condicional de Azure AD con las [cuentas de consumidor](user-overview.md#consumer-user). Obtenga información sobre cómo configurar el [acceso condicional](conditional-access-user-flow.md) de Azure AD B2C.|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Totalmente compatible con las características de Azure AD Premium P1. Por ejemplo, [protección de contraseñas](../active-directory/authentication/concept-password-ban-bad.md), [identidades híbridas](../active-directory/hybrid/whatis-hybrid-identity.md), [acceso condicional](../active-directory/roles/permissions-reference.md#), [grupos dinámicos](../active-directory/enterprise-users/groups-create-rule.md), etc. | Se admite un subconjunto de características de acceso condicional de Azure AD con las [cuentas de consumidor](user-overview.md#consumer-user). Aprenda a configurar el [acceso condicional](conditional-access-user-flow.md) de Azure AD B2C.|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Totalmente compatible con las características de Azure AD Premium P2. Por ejemplo, [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) e [Identity Governance](../active-directory/governance/identity-governance-overview.md).  | Un subconjunto de características de Azure AD Identity Protection es compatible con las [cuentas de consumidor](user-overview.md#consumer-user). Aprenda a [investigar el riesgo con Identity Protection](identity-protection-investigate-risk.md) y a configurar el [acceso condicional](conditional-access-user-flow.md) de Azure AD B2C. |

> [!NOTE]
> **Otros recursos de Azure en el inquilino:** <br>En un inquilino de Azure AD B2C, no puede aprovisionar otros recursos de Azure, como máquinas virtuales, aplicaciones web de Azure o funciones de Azure. Debe crear estos recursos en el inquilino de Azure AD.
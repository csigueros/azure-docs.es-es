---
title: Proveedor de identidades de la cuenta de Azure AD
description: Use Azure AD para permitir que un usuario externo (invitado) inicie sesión en sus aplicaciones de Azure AD con su cuenta profesional de Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9d7a8e74767982d5089a1308ca36108eb5f106
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972551"
---
# <a name="azure-active-directory-azure-ad-identity-provider-for-external-identities"></a>Proveedor de identidades de Azure Active Directory (Azure AD) para External Identities

Azure Active Directory está disponible como opción de proveedor de identidades para la colaboración B2B de forma predeterminada. Si un usuario invitado externo tiene una cuenta de Azure AD a través de su trabajo o escuela, puede canjear las invitaciones de colaboración B2B o completar los flujos de usuario de registro mediante su cuenta de Azure AD.

## <a name="guest-sign-in-using-azure-active-directory-accounts"></a>Inicio de sesión de invitado mediante cuentas de Azure Active Directory

La cuenta de Azure Active Directory está disponible en la lista de proveedores de identidades de External Identities de manera predeterminada. No se necesita ninguna configuración adicional para permitir que los usuarios invitados inicien sesión con su cuenta de Azure AD mediante el flujo de invitación o un flujo de usuario de suscripción autoservicio.

![Cuenta de Azure AD en la lista de proveedores de identidades](media/azure-ad-account/azure-ad-account-identity-provider.png)

### <a name="azure-ad-account-in-the-invitation-flow"></a>Cuenta de Azure AD en el flujo de invitación

Al [invitar a un usuario](add-users-administrator.md) a una colaboración B2B, puede especificar su cuenta de Azure AD como dirección de correo electrónico que va a usar para iniciar sesión.

![Invitación mediante una cuenta de Azure AD](media/azure-ad-account/azure-ad-account-invite.png)

### <a name="azure-ad-account-in-self-service-sign-up-user-flows"></a>Cuenta de Azure AD en flujos de usuario de suscripción de autoservicio

La cuenta de Azure AD es una opción del proveedor de identidades para los flujos de usuario de suscripción autoservicio. Los usuarios pueden suscribirse a las aplicaciones con sus propias cuentas de Azure AD. En primer lugar, tiene que [habilitar la suscripción autoservicio](self-service-sign-up-user-flow.md) para el inquilino. A continuación, puede configurar un flujo de usuario para la aplicación y seleccionar Azure Active Directory como una de las opciones de inicio de sesión.

![Cuenta de Azure AD en un flujo de usuario de suscripción de autoservicio](media/azure-ad-account/azure-ad-account-user-flow.png)

## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de usuarios de colaboración B2B de Azure Active Directory](add-users-administrator.md)
- [Incorporación del registro de autoservicio a una aplicación](self-service-sign-up-user-flow.md)
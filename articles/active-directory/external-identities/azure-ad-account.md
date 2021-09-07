---
title: Proveedor de identidades de la cuenta de Azure AD
description: Use Azure AD para permitir que un usuario externo (invitado) inicie sesión en sus aplicaciones de Azure AD con su cuenta profesional de Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00edf144e06710204bf5d6eb477187668b3e5237
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182965"
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

## <a name="verifying-the-applications-publisher-domain"></a>Comprobación del dominio del publicador de la aplicación
Desde noviembre de 2020, los nuevos registros de aplicación se muestran como no comprobados en el mensaje de consentimiento del usuario, a menos que [se haya comprobado el dominio del publicador de la aplicación](../develop/howto-configure-publisher-domain.md) y, ***además***, se haya comprobado la identidad de la empresa con Microsoft Partner Network y asociado a la aplicación. [Obtenga más información](../develop/publisher-verification-overview.md) sobre este cambio. Tenga presente que, en los flujos de usuario de Azure AD, el dominio del publicador solo aparece cuando se usa una [cuenta de Microsoft](microsoft-account.md) u otro inquilino de Azure AD como proveedor de identidades. Para cumplir estos nuevos requisitos, haga lo siguiente:

1. [Compruebe la identidad de su empresa usando su cuenta de Microsoft Partner Network (MPN)](/partner-center/verification-responses). Este proceso comprueba la información sobre la empresa y el contacto principal de esta.
1. Complete el proceso de comprobación del publicador para asociar la cuenta de MPN con el registro de la aplicación mediante una de las siguientes opciones:
   - Si el registro de la aplicación para el proveedor de identidades de la cuenta de Microsoft está en un inquilino de Azure AD, [compruebe la aplicación en el portal de registro de aplicaciones](../develop/mark-app-as-publisher-verified.md).
   - Si el registro de la aplicación para el proveedor de identidades de la cuenta de Microsoft está en un inquilino de Azure AD B2C, [marque la aplicación como publicador comprobado mediante las API de Microsoft Graph](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (por ejemplo, mediante el explorador de Graph).

## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de usuarios de colaboración B2B de Azure Active Directory](add-users-administrator.md)
- [Incorporación del registro de autoservicio a una aplicación](self-service-sign-up-user-flow.md)
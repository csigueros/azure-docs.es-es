---
title: Proveedor de identidades de cuenta Microsoft (MSA) en Azure AD
description: Use Azure AD para permitir que un usuario externo (invitado) inicie sesión en sus aplicaciones de Azure AD con su cuenta Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2333ba6aec10d124d960dfc746bc9df2c449f3bb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178845"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities"></a>Proveedor de identidades de cuenta Microsoft (MSA) para External Identities

Los usuarios invitados de B2B pueden usar sus propias cuentas personales de Microsoft para la colaboración B2B sin necesidad de configuración adicional. Los usuarios invitados pueden canjear sus invitaciones de colaboración B2B o completar los flujos de usuario de suscripción con su cuenta Microsoft personal.

Las cuentas Microsoft son aquellas que un usuario configura para poder acceder a productos y servicios en la nube de Microsoft orientados al consumidor, como Outlook, OneDrive, Xbox LIVE o Microsoft 365. La cuenta se crea y almacena en el sistema de cuentas de identidad de consumidor de Microsoft que ejecuta Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Inicio de sesión de invitado con cuentas Microsoft

La cuenta Microsoft está disponible en la lista de proveedores de identidades de External Identities de manera predeterminada. No se necesita ninguna configuración adicional para permitir que los usuarios invitados inicien sesión con su cuenta Microsoft mediante el flujo de invitación o un flujo de usuario de suscripción autoservicio.

![Cuenta Microsoft en la lista de proveedores de identidades](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Cuenta Microsoft en el flujo de invitación

Al [invitar a un usuario](add-users-administrator.md) a una colaboración B2B, puede especificar su cuenta Microsoft como dirección de correo electrónico que va a usar para iniciar sesión.

![Invitación con una cuenta Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Cuenta Microsoft en flujos de usuario de registro de autoservicio

La cuenta Microsoft es una opción del proveedor de identidades para los flujos de usuario de registro de autoservicio. Los usuarios pueden suscribirse a las aplicaciones con sus propias cuentas Microsoft. En primer lugar, tiene que [habilitar la suscripción autoservicio](self-service-sign-up-user-flow.md) para el inquilino. Luego puede configurar un flujo de usuario para la aplicación y seleccionar la cuenta Microsoft como una de las opciones de inicio de sesión.

![Cuenta Microsoft en un flujo de usuario de suscripción autoservicio](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>Comprobación del dominio del publicador de la aplicación
Desde noviembre de 2020, los nuevos registros de aplicación se muestran como no comprobados en el mensaje de consentimiento del usuario, a menos que [se haya comprobado el dominio del publicador de la aplicación](../develop/howto-configure-publisher-domain.md) y, ***además***, se haya comprobado la identidad de la empresa con Microsoft Partner Network y asociado a la aplicación. [Obtenga más información](../develop/publisher-verification-overview.md) sobre este cambio. Tenga presente que, en los flujos de usuario de Azure AD, el dominio del publicador solo aparece cuando se usa una [cuenta Microsoft](azure-ad-account.md) u otro inquilino de Azure AD como proveedor de identidades. Para cumplir estos nuevos requisitos, haga lo siguiente:

1. [Compruebe la identidad de la empresa con la cuenta de Microsoft Partner Network (MPN)](/partner-center/verification-responses). Este proceso comprueba la información sobre su empresa y el contacto principal de esta.
1. Complete el proceso de comprobación del publicador para asociar su cuenta de MPN al registro de la aplicación usando una de las siguientes opciones:
   - Si el registro de la aplicación para el proveedor de identidades de la cuenta Microsoft está en un inquilino de Azure AD, [compruebe la aplicación en el portal de registro de aplicaciones](../develop/mark-app-as-publisher-verified.md).
   - Si el registro de la aplicación para el proveedor de identidades de cuenta Microsoft está en un inquilino de Azure AD B2C, [marque la aplicación como publicador comprobado usando las API de Microsoft Graph](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (por ejemplo, mediante el explorador de Graph).

## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de usuarios de colaboración B2B de Azure Active Directory](add-users-administrator.md)
- [Incorporación del registro de autoservicio a una aplicación](self-service-sign-up-user-flow.md)
---
title: Proveedores de identidades para External Identities - Azure AD
description: Aprenda a usar Azure AD como proveedor de identidades predeterminado para compartir con usuarios externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f867e0091d792ef88b79f1c1bb7f4272a35e928
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721367"
---
# <a name="identity-providers-for-external-identities"></a>Proveedores de identidades para External Identities

Un *proveedor de identidades* crea, mantiene y administra la información de identidad al tiempo que proporciona servicios de autenticación a las aplicaciones. Al compartir aplicaciones y recursos con usuarios externos, Azure AD es el proveedor de identidades predeterminado para el uso compartido. Esto significa que, al invitar a usuarios externos que ya tienen una cuenta de Azure AD o de Microsoft, estos pueden iniciar sesión automáticamente sin tener que realizar ninguna configuración adicional.

Además de cuentas de Azure AD, External Identities ofrece una variedad de proveedores de identidades.

- **Cuentas Microsoft**: los usuarios invitados pueden usar su propia cuenta Microsoft (MSA) personal para canjear las invitaciones de colaboración B2B. Al configurar un flujo de usuarios de registro de autoservicio, puede agregar la [Cuenta Microsoft](microsoft-account.md) como uno de los proveedores de identidades permitidos. Para que este proveedor de identidades esté disponible para los flujos de usuario, no es necesario realizar ninguna configuración adicional.

- **Código de acceso de un solo uso de correo electrónico**: al canjear una invitación o acceder a un recurso compartido, un usuario invitado puede solicitar un código temporal, que se envía a su dirección de correo electrónico. A continuación, escribe este código para continuar con el inicio de sesión. La característica de código de acceso de un solo uso por correo electrónico autentica a los usuarios invitados B2B cuando no pueden autenticarse por otros medios. Al configurar un flujo de usuarios de registro de autoservicio, puede agregar **Código de acceso de un solo uso de correo electrónico** como uno de los proveedores de identidades permitidos. Alguna configuración es necesaria; consulte [Autenticación mediante un código de acceso de un solo uso por correo electrónico](one-time-passcode.md).

- **Google**: La federación de Google permite a usuarios externos canjear invitaciones que les haya enviado si inician sesión en sus aplicaciones con sus propias cuentas de Gmail. La federación de Google también se puede usar en los flujos de usuario de registro de autoservicio. Consulte cómo [agregar Google como proveedor de identidades](google-federation.md).
   > [!IMPORTANT]
   >
   > - **A partir del 12 de julio de 2021**, si los clientes de Azure AD B2B configuran nuevas integraciones de Google para usarlas con el registro de autoservicio para sus aplicaciones personalizadas o de línea de negocio, la autenticación con identidades de Google no funcionará hasta que las autenticaciones se trasladen a las vistas web del sistema. [Más información](google-federation.md#deprecation-of-web-view-sign-in-support).
   > - **A partir del 30 de septiembre de 2021**, Google [retira la compatibilidad con el inicio de sesión en la vista web insertada](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si sus aplicaciones autentican a los usuarios con una vista web insertada y va a usar la federación de Google con [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) o Azure AD B2B para las [invitaciones de usuarios externos](google-federation.md) o el registro de autoservicio, los usuarios de Google Gmail no podrán autenticarse. [Más información](google-federation.md#deprecation-of-web-view-sign-in-support).


- **Facebook**: al compilar una aplicación, puede configurar el registro de autoservicio y habilitar la federación de Facebook para que los usuarios puedan suscribirse a la aplicación con sus propias cuentas de Facebook. Facebook solo se puede usar para flujos de usuario de registro de autoservicio y no está disponible como una opción de inicio de sesión cuando los usuarios canjean las invitaciones que les envíe. Consulte cómo [agregar Facebook como proveedor de identidades](facebook-federation.md).

- **Federación con un proveedor de identidades de SAML/WS-Fed**: también puede configurar la federación con cualquier IdP externo que admita los protocolos SAML o WS-Fed. La federación con un IdP de SAML/WS-Fed permite a los usuarios externos canjear invitaciones que les haya enviado si inician sesión en sus aplicaciones con sus cuentas empresariales o de redes sociales existentes. Consulte el artículo sobre la [Configuración de la federación con un IdP de SAML o WS-Fed](direct-federation.md).
   > [!NOTE]
   > Los IdP de SAML o WS-Fed federados no se pueden usar en los flujos de usuario de registro de autoservicio.

## <a name="adding-social-identity-providers"></a>Adición de proveedores de identidades sociales

Azure AD está habilitado de forma predeterminada para el registro de autoservicio, por lo que los usuarios siempre tienen la opción de registrarse con una cuenta de Azure AD. Sin embargo, puede habilitar otros proveedores de identidades, por ejemplo, los proveedores de identidades sociales, como Google o Facebook. Para configurar proveedores de identidades sociales en el inquilino de Azure AD, creará una aplicación en el proveedor de identidades y configurará las credenciales. Obtendrá un id. de cliente o de aplicación y un secreto de cliente o de aplicación, que puede agregar a su inquilino de Azure AD.

Una vez que haya agregado un proveedor de identidades a su inquilino de Azure AD:

- Cuando invite a un usuario externo a las aplicaciones o recursos de la organización, el usuario externo puede iniciar sesión con la cuenta que tenga con ese proveedor de identidades.
- Al habilitar el [registro de autoservicio](self-service-sign-up-overview.md) para las aplicaciones, los usuarios externos pueden registrarse en las aplicaciones con las cuentas de los proveedores de identidades que haya agregado. Dichos usuarios podrán seleccionar una de las opciones de proveedores de identidades sociales que estén disponibles en la página de registro:

   ![Captura de pantalla que muestra la pantalla de inicio de sesión con las opciones de Google y Facebook.](media/identity-providers/sign-in-with-social-identity.png)

Para conseguir una experiencia de inicio de sesión óptima, fedérese con los proveedores de identidades siempre que sea posible para que pueda proporcionar a sus invitados una experiencia de inicio de sesión sin problemas cuando accedan a las aplicaciones.  

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo agregar proveedores de identidades para iniciar sesión en las aplicaciones, consulte los siguientes artículos:

- [Adición de la autenticación con código de acceso de un solo uso por correo electrónico](one-time-passcode.md)
- [Adición de Google](google-federation.md) como proveedor de identidades social permitido
- [Adición de Facebook](facebook-federation.md) como proveedor de identidades social permitido
- [Configuración de la federación con un IdP de SAML o WS-Fed](direct-federation.md) con cualquier organización cuyo proveedor de identidades admita el protocolo SAML 2.0 o WS-Fed. Tenga en cuenta que la federación con un IdP de SAML o WS-Fed no es una opción para los flujos de usuario de registro de autoservicio.

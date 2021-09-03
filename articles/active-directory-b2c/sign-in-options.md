---
title: Opciones de inicio de sesión admitidas por Azure AD B2C
titleSuffix: Azure AD B2C
description: Obtenga información sobre las opciones de registro e inicio de sesión que puede usar con Azure Active Directory B2C, incluidos el nombre de usuario y la contraseña, el correo electrónico, el teléfono o la federación con proveedores de identidades sociales o externos.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: efb526f9a7418c0d7d2316ec91f355a7c4431404
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204841"
---
# <a name="sign-in-options-in-azure-ad-b2c"></a>Opciones de inicio de sesión en Azure AD B2C

Azure AD B2C ofrece varios métodos de registro e inicio de sesión para los usuarios de las aplicaciones. Cuando los usuarios se suscriben a la aplicación, debe determinar si usarán un nombre de usuario, una dirección de correo electrónico o un número de teléfono para crear cuentas locales en el inquilino de Azure AD B2C. También puede realizar la federación con proveedores de identidades sociales (como Facebook, LinkedIn y Twitter) y protocolos de identidad estándar (como OAuth 2.0, OpenID Connect y mucho más).

En este artículo se proporciona una introducción a las opciones de inicio de sesión de Azure AD B2C.

## <a name="email-sign-in"></a>Inicio de sesión mediante correo electrónico

El registro de correo electrónico se habilita de forma predeterminada en la configuración del proveedor de identidades de la cuenta local. Con la opción de correo electrónico, los usuarios pueden iniciar sesión y registrarse con su dirección de correo electrónico y contraseña.

- **Inicio de sesión**: los usuarios deben proporcionar su correo electrónico y contraseña.
- **Registro**: a los usuarios se les solicita una dirección de correo electrónico, que se comprueba durante el registro (opcional) y se convierte en su id. de inicio de sesión. Después, el usuario escribe toda la información que se le solicite en la página de registro, por ejemplo, el nombre para mostrar, el nombre y los apellidos. Luego, debe seleccionar **Continuar** para crear una cuenta.
- **Restablecimiento de contraseña**: los usuarios escriben y verifican su correo electrónico, y después pueden restablecer la contraseña.

![Experiencia de registro o inicio de sesión mediante correo electrónico](./media/sign-in-options/local-account-email-experience.png)

Aprenda a configurar el inicio de sesión por correo electrónico en el proveedor de identidades de la cuenta local.
## <a name="username-sign-in"></a>Inicio de sesión mediante nombre de usuario

El proveedor de identidades de la cuenta local incluye una opción Nombre de usuario que permite a los usuarios registrarse e iniciar sesión en la aplicación con un nombre de usuario y una contraseña.

- **Inicio de sesión**: los usuarios deben especificar su nombre de usuario y contraseña.
- **Registro**: los usuarios deberán especificar un nombre de usuario, que se convertirá en su id. de inicio de sesión. Los usuarios deberán especificar una dirección de correo electrónico, que se comprobará en el momento del registro. La dirección de correo electrónico se usará durante un flujo de restablecimiento de contraseña. El usuario debe especificar toda la información que se le solicite en la página de registro, por ejemplo, Nombre para mostrar, Nombre y Apellido. Luego, el usuario selecciona Continuar para crear la cuenta.
- **Restablecimiento de contraseña**: los usuarios deben escribir su nombre de usuario y la dirección de correo electrónico asociada. La dirección de correo electrónico se debe comprobar, después de lo cual el usuario puede restablecer la contraseña.

![Experiencia de registro o inicio de sesión mediante nombre de usuario](./media/sign-in-options/local-account-username-experience.png)

## <a name="phone-sign-in"></a>Inicio de sesión en el teléfono

El inicio de sesión mediante el teléfono es una opción sin contraseña en la configuración del proveedor de identidades de la cuenta local. Este método permite a los usuarios registrarse en la aplicación con un número de teléfono como identificador principal. Las contraseñas de un solo uso se envían a los usuarios mediante mensajes de texto SMS. La experiencia de los usuarios durante el registro y el inicio de sesión será la siguiente:

- **Inicio de sesión**: si el usuario tiene una cuenta con el número de teléfono como identificador, escribe su número de teléfono y selecciona *Iniciar sesión*. Confirma el país y el número de teléfono seleccionando *Continuar* y se envía un código de verificación de un solo uso a su teléfono. El usuario escribe el código de verificación y selecciona *Continuar* para iniciar sesión.
- **Registro**: si el usuario aún no tiene una cuenta para la aplicación, puede crear una si hace clic en el vínculo *Registrarse ahora*.
    1. Aparece una página de registro, donde el usuario selecciona su *país*, escribe su número de teléfono y selecciona *Enviar código*. 
    1. Se envía un código de verificación de un solo uso al número de teléfono del usuario. El usuario escribe el *código de verificación* en la página de registro y, luego, selecciona *Comprobar el código*. (Si el usuario no puede recuperar el código, puede seleccionar *Enviar nuevo código*).
    1. El usuario debe especificar toda la información que se le solicite en la página de registro, por ejemplo, Nombre para mostrar, Nombre y Apellido. Después, seleccione Continuar.
    1. A continuación, se pide al usuario que proporcione un **correo electrónico de recuperación**. El usuario escribe su dirección de correo electrónico y, luego, selecciona *Enviar código de comprobación*. Se envía un código a la bandeja de entrada de correo electrónico del usuario, que puede recuperar y escribir en el cuadro Código de verificación. A continuación, el usuario selecciona Comprobar código.
    1. Una vez que se comprueba el código, el usuario selecciona *Crear* para crear su cuenta.

![Experiencia de registro o inicio de sesión con el teléfono](./media/sign-in-options/local-account-phone-experience.png)

### <a name="pricing-for-phone-sign-in"></a>Precios del inicio de sesión con el teléfono

Las contraseñas de un solo uso se envían a los usuarios mediante mensajes de texto SMS. Es posible que se le cobre por cada mensaje enviado en función del operador de red móvil que tenga. Puede encontrar información sobre precios en la sección **Cargos aparte** de la página [Precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> La autenticación multifactor (MFA) se deshabilita de forma predeterminada al configurar un flujo de usuario con el registro telefónico. Puede habilitar MFA en los flujos de usuario con el registro telefónico, pero como el número de teléfono se usa como identificador principal, el código de acceso de un solo uso de correo electrónico es la única opción disponible para el segundo factor de autenticación.

### <a name="phone-recovery"></a>Recuperación con el teléfono

Al habilitar el registro e inicio de sesión telefónico para los flujos de usuario, también es buena idea habilitar la característica de correo electrónico de recuperación. Con esta característica, un usuario puede proporcionar una dirección de correo electrónico que se puede usar para recuperar su cuenta cuando no tiene su teléfono. Esta dirección de correo electrónico se usa solo para la recuperación de cuenta. No se puede usar para iniciar sesión.

- Cuando la solicitud de correo electrónico de recuperación está **activada**, a un usuario que se registre por primera vez se le pedirá que compruebe un correo electrónico de copia de seguridad. A un usuario que no haya proporcionado un correo electrónico de recuperación antes se le pedirá que compruebe un correo electrónico de copia de seguridad durante el siguiente inicio de sesión.

- Cuando el correo electrónico de recuperación está **desactivado**, la solicitud de correo electrónico no se mostrará a un usuario que se registre o inicie sesión.

Las capturas de pantallas siguientes muestran el flujo de recuperación con el teléfono:

![Flujo de usuario de recuperación con el teléfono](./media/sign-in-options/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in"></a>Inicio de sesión con el teléfono o mediante correo electrónico

Puede elegir combinar el [inicio de sesión con el teléfono](#phone-sign-in) y el [inicio de sesión con el correo electrónico](#email-sign-in) en la configuración del proveedor de identidades de la cuenta local. En la página de registro o de inicio de sesión, el usuario puede escribir un número de teléfono o una dirección de correo electrónico. En función de lo que el usuario haya especificado, Azure AD B2C lleva al usuario al flujo correspondiente.

![Experiencia de registro o inicio de sesión con el teléfono o mediante correo electrónico](./media/sign-in-options/local-account-phone-and-email-experience.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre las directivas integradas proporcionadas. Para ello, consulte [Flujos de usuario en Azure Active Directory B2C](user-flow-overview.md).
- [Configuración del proveedor de identidades de la cuenta local](identity-provider-local.md).
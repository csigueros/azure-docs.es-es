---
title: Configuración del proveedor de identidades de la cuenta local de Azure AD B2C
titleSuffix: Azure AD B2C
description: Defina los tipos de identidad que los usuarios pueden usar para registrarse o iniciar sesión (correo electrónico, nombre de usuario, número de teléfono) en el inquilino de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1de783a3e6696e9ca8a8f618ce3744a91e774692
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198861"
---
# <a name="set-up-the-local-account-identity-provider"></a>Configuración del proveedor de identidades de la cuenta local

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

En este artículo se describe cómo determinar los métodos de inicio de sesión para las cuentas locales de Azure AD B2C. Una cuenta local hace referencia a una cuenta que se crea en el directorio de Azure AD B2C cuando un usuario se suscribe a la aplicación o un administrador crea la cuenta. Los nombres de usuario y las contraseñas se almacenan localmente y Azure AD B2C actúa como proveedor de identidades para las cuentas locales.

Hay varios métodos de inicio de sesión disponibles para las cuentas locales:

- **Correo electrónico**: los usuarios pueden registrarse e iniciar sesión con su dirección de correo electrónico y contraseña. El registro de correo electrónico se habilita de forma predeterminada en la configuración del proveedor de identidades de la cuenta local.
- **Nombre de usuario**: los usuarios pueden registrarse e iniciar sesión con un nombre de usuario y una contraseña.
- **Teléfono (o "autenticación sin contraseña")** : los usuarios pueden registrarse e iniciar sesión en la aplicación con un número de teléfono como identificador de inicio de sesión principal. No tienen que crear contraseñas. Las contraseñas de un solo uso se envían a los usuarios mediante mensajes de texto SMS.
- **Teléfono o correo electrónico**: los usuarios pueden registrarse o iniciar sesión si escriben un número de teléfono o una dirección de correo electrónico. En función de lo que el usuario haya especificado, Azure AD B2C lleva al usuario al flujo correspondiente en la página de registro o de inicio de sesión.
- **Recuperación por teléfono**: si ha habilitado el registro o el inicio de sesión en el teléfono, la recuperación telefónica permite a los usuarios proporcionar una dirección de correo electrónico que se puede usar para recuperar su cuenta cuando no tienen su teléfono.

Para obtener más información sobre estos métodos, vea [Opciones de inicio de sesión](sign-in-options.md). 

A fin de configurar valores para identidades sociales o empresariales, donde la identidad del usuario se administra mediante un proveedor de identidades federado, como Facebook o Google, vea [Incorporación de un proveedor de identidades](add-identity-provider.md).

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Configuración del proveedor de identidades de la cuenta local


Puede elegir los métodos de inicio de sesión de la cuenta local (correo electrónico, nombre de usuario o número de teléfono) que quiere que estén disponibles en el inquilino mediante la configuración del proveedor de **cuentas local** en la lista de **Proveedores de identidades** de Azure AD B2C. Después, al configurar un flujo de usuario, puede elegir uno de los métodos de inicio de sesión de la cuenta local que ha habilitado para todo el inquilino. Solo puede seleccionar un método de inicio de sesión de cuenta local para un flujo de usuario, pero puede seleccionar una opción diferente para cada flujo de usuario.

Para establecer las opciones de inicio de sesión de la cuenta local en el nivel de inquilino: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el filtro **Directorio y suscripción** en el menú superior y luego el directorio que contiene el inquilino de Azure AD.
1. En **Servicios de Azure**, seleccione **Azure AD B2C**. O bien, use el cuadro de búsqueda para buscar y seleccionar **Azure AD B2C**.
1. En **Administrar**, seleccione **Proveedores de identidades**.
1. En la lista de proveedores de identidades, seleccione **Cuenta local**.
1. En la página **Configuración de IDP local**, seleccione uno o varios tipos de identidad que quiera habilitar para los flujos de usuario en el inquilino de Azure AD B2C. La selección de una opción aquí simplemente hace que esté disponible para su uso en todo el inquilino; al crear o modificar un flujo de usuario, podrá elegir entre las opciones que habilite aquí.

   - **Teléfono**: a los usuarios se les solicita un número de teléfono, que se comprueba durante el registro y se convierte en el identificador de usuario.
   - **Nombre de usuario**: los usuarios pueden crear su propio identificador de usuario único. Se recopilará y comprobará una dirección de correo electrónico del usuario.
   - **Correo electrónico**: a los usuarios se les pedirá una dirección de correo electrónico, que se comprobará durante el registro y se convertirá en su identificador de usuario.
1. Seleccione **Guardar**.

## <a name="configure-your-user-flow"></a>Configuración del flujo de usuario

1. En el menú izquierdo de Azure Portal, seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Flujos de usuario (directivas)** .
1. Seleccione el flujo de usuario para el que quiere configurar la experiencia de registro y de inicio de sesión.
1. Seleccione **Proveedores de identidades**.
1. En las **Cuentas locales**, seleccione una de las opciones siguientes: **Registro mediante correo electrónico**, **Registro mediante id. de usuario**, **Registro con el teléfono**, **Registro con el teléfono o mediante correo electrónico** o **Ninguno**.

### <a name="enable-the-recovery-email-prompt"></a>Habilitación de la solicitud de correo electrónico de recuperación

Si elige la opción **Registro con el teléfono**, **Registro con el teléfono o mediante correo electrónico**, habilite la solicitud de correo electrónico de recuperación.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En Azure AD B2C, en **Directivas**, seleccione **Flujos de usuario**.
1. Seleccione el flujo de usuario de la lista.
1. En **Configuración**, seleccione **Propiedades**.
1. Junto a **Habilitar solicitud de correo electrónico de recuperación para la suscripción del número de teléfono y el inicio de sesión (versión preliminar)** , seleccione:
   - **Activado** para mostrar la solicitud de correo electrónico de recuperación durante el registro e inicio de sesión.
   - **Desactivado** para ocultar la solicitud de correo electrónico de recuperación.
1. Seleccione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Obtención del paquete de inicio

Las directivas personalizadas son un conjunto de archivos XML que se cargan en el inquilino de Azure AD B2C para definir recorridos de usuario. Proporcionamos paquetes de inicio con varias directivas predefinidas. Descargue el paquete de inicio pertinente: 

- [Inicio de sesión mediante correo electrónico](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Inicio de sesión mediante nombre de usuario](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Inicio de sesión con el teléfono](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Seleccione la directiva de usuario de confianza [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml). 
- [Inicio de sesión con el teléfono o mediante correo electrónico](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Seleccione la directiva de usuario de confianza [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml).

Después, descargue el paquete de inicio.

1. En cada archivo, reemplace la cadena `yourtenant` por el nombre del inquilino de Azure AD B2C. Por ejemplo, si el nombre del inquilino de B2C es *contosob2c*, todas las instancias de `yourtenant.onmicrosoft.com` se convierten en `contosob2c.onmicrosoft.com`.

1. Complete los pasos de la sección [Agregar identificadores de aplicación a la directiva personalizada](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) de [Introducción a las directivas personalizadas en Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). Por ejemplo, actualice `/phone-number-passwordless/`**`Phone_Email_Base.xml`** con los valores de **Id. de aplicación (cliente)** de las dos aplicaciones que registró al completar los requisitos previos, *IdentityExperienceFramework* y *ProxyIdentityExperienceFramework*.
1. Carga de los archivos de directivas

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

- [Agregar proveedores de identidades externos](add-identity-provider.md)
- [Crear flujos de usuario](tutorial-create-user-flows.md)

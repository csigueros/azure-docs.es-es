---
title: Compatibilidad con inicio de sesión único y directivas de protección de aplicaciones en el desarrollo de aplicaciones móviles | Azure
titleSuffix: Microsoft identity platform
description: Explicación e información general sobre la creación de aplicaciones móviles que admiten el inicio de sesión único y directivas de protección de aplicaciones con la plataforma de identidad de Microsoft y la integración con Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.custom: has-adal-ref
ms.openlocfilehash: 43f66b7f44e7d30816da5ac59a9692807f257a2b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787270"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Compatibilidad con inicio de sesión único y directivas de protección de aplicaciones en el desarrollo de aplicaciones móviles

El inicio de sesión único (SSO) es una oferta clave de la plataforma de identidad de Microsoft y Azure Active Directory, que proporciona inicios de sesión sencillos y seguros para los usuarios de la aplicación. Además, las directivas de protección de aplicaciones (APP) permiten respaldar las directivas de seguridad principales que mantienen seguros los datos del usuario. Juntas, estas características permiten inicios de sesión de usuario seguros y la administración de los datos de la aplicación.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

En este artículo se explica por qué SSO y APP son importantes, y se proporcionan instrucciones de alto nivel para la creación de aplicaciones móviles que admitan estas características. Es aplicable tanto a aplicaciones de teléfonos como de tabletas. Si es un administrador de TI que quiere implementar el inicio de sesión único en el inquilino de Azure Active Directory de la organización, consulte nuestra [guía para planear una implementación de inicio de sesión único](../manage-apps/plan-sso-deployment.md).

## <a name="about-single-sign-on-and-app-protection-policies"></a>Acerca del inicio de sesión único y las directivas de protección de aplicaciones

El [inicio de sesión único (SSO)](../manage-apps/plan-sso-deployment.md) permite a los usuarios iniciar sesión una vez y obtener acceso a otras aplicaciones sin tener que volver a escribir las credenciales. Esto facilita el acceso a las aplicaciones y elimina la necesidad de que los usuarios recuerden largas listas de nombres de usuario y contraseñas. Cuando se implementa en la aplicación, facilita el acceso y el uso de esta.

Además, al habilitar el inicio de sesión único en la aplicación, se abre la puerta a nuevos mecanismos de autenticación que se incluyen con la autenticación moderna, como [inicios de sesión sin contraseña](../authentication/concept-authentication-passwordless.md). Los nombres de usuario y las contraseñas son uno de los vectores de ataque más conocidos contra las aplicaciones; sin embargo, con la habilitación del inicio de sesión único, puede mitigar este riesgo al aplicar acceso condicional o inicios de sesión sin contraseña que agregan seguridad adicional o se basan en mecanismos de autenticación más seguros. Por último, la habilitación del inicio de sesión único también habilita el [cierre de sesión único](v2-protocols-oidc.md#single-sign-out). Esto resulta útil en situaciones como aplicaciones de trabajo que se usarán en dispositivos compartidos.

Las [directivas de protección de aplicaciones (APP)](/mem/intune/apps/app-protection-policy) garantizan que los datos de una organización permanecen seguros y restringidos. Permiten a las empresas administrar y proteger sus datos dentro de una aplicación y les otorga el control sobre quién puede acceder a la aplicación y sus datos. La implementación de directivas de protección de aplicaciones permite a la aplicación conectar los usuarios a los recursos protegidos mediante directivas de acceso condicional y transferir datos de forma segura a otras aplicaciones protegidas y desde allí. Los escenarios que surgen con las directivas de protección de aplicaciones incluyen solicitar un PIN para abrir una aplicación, controlar el uso compartido de los datos entre aplicaciones y evitar que los datos de la aplicación de la empresa se guarden en una ubicación de almacenamiento personal.

## <a name="implementing-single-sign-on"></a>Implementación del inicio de sesión único

Para permitir que la aplicación aproveche las ventajas del inicio de sesión único, se recomienda lo siguiente.

### <a name="use-the-microsoft-authentication-library-msal"></a>Uso de la Biblioteca de autenticación de Microsoft (MSAL)

La mejor opción para implementar el inicio de sesión único en la aplicación es usar la [Biblioteca de autenticación de Microsoft (MSAL)](msal-overview.md). Con MSAL, puede agregar autenticación a la aplicación con un mínimo de código y llamadas API, obtener las características completas de la [plataforma de identidad de Microsoft](./index.yml) y dejar que Microsoft administre el mantenimiento de una solución de autenticación segura. De forma predeterminada, MSAL agrega compatibilidad con el inicio de sesión único en su aplicación. Además, el uso de MSAL es un requisito si también tiene previsto implementar directivas de protección de aplicaciones.

> [!NOTE]
> Es posible configurar MSAL para usar una vista web insertada. Esto impedirá el inicio de sesión único. Use el comportamiento predeterminado (es decir, el explorador web del sistema) para asegurarse de que el inicio de sesión único funcione.

Si actualmente usa la [biblioteca ADAL](../azuread-dev/active-directory-authentication-libraries.md) en la aplicación, le recomendamos encarecidamente que la [migre a MSAL](msal-migration.md), ya que [ADAL está en desuso](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

Para las aplicaciones iOS, tenemos un [inicio rápido](quickstart-v2-ios.md) que muestra cómo configurar inicios de sesión con MSAL, con [instrucciones para configurar MSAL para varios escenarios de inicio de sesión único](single-sign-on-macos-ios.md).

Para aplicaciones de Android, tenemos un [inicio rápido](quickstart-v2-android.md) que muestra cómo configurar inicios de sesión con MSAL y con instrucciones para saber [cómo habilitar el inicio de sesión único entre aplicaciones de Android mediante MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>Uso del explorador web del sistema

Se requiere un explorador web para la autenticación no interactiva. Para las aplicaciones móviles que usan bibliotecas de autenticación modernas distintas de MSAL (es decir, otras bibliotecas OpenID Connect o SAML), o si implementa su propio código de autenticación, debe usar el explorador del sistema como superficie de autenticación para habilitar el inicio de sesión único.

Google tiene instrucciones para hacerlo en aplicaciones Android: [Pestañas personalizadas de Chrome: Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Apple tiene instrucciones para hacerlo en aplicaciones iOS: [Autenticación de un usuario mediante un servicio web |Documentación para desarrolladores de Apple ](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> El [complemento de inicio de sesión único para dispositivos Apple](apple-sso-plugin.md) permite el inicio de sesión único en aplicaciones iOS que usan vistas web insertadas en dispositivos administrados con Intune. Se recomienda MSAL y el explorador del sistema como la mejor opción para desarrollar aplicaciones que permitan el inicio de sesión único para todos los usuarios; esto permitirá el inicio de sesión único en escenarios en los que, si no, no sería posible.

## <a name="enable-app-protection-policies"></a>Habilitación de directivas de protección de aplicaciones

Para habilitar directivas de protección de aplicaciones, use la [Biblioteca de autenticación de Microsoft (MSAL)](msal-overview.md). MSAL es la biblioteca de autenticación y autorización de la plataforma de identidad de Microsoft, y el SDK de Intune se ha desarrollado para trabajar conjuntamente con ella.

Además, debe usar una aplicación de agente para la autenticación. El agente requiere que la aplicación proporcione información de la aplicación y del dispositivo para garantizar su cumplimiento. Los usuarios de iOS usarán la [aplicación Microsoft Authenticator](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c) y los usuarios de Android usarán la aplicación Microsoft Authenticator o la [aplicación Portal de empresa](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) para la [autenticación asincrónica](./msal-android-single-sign-on.md). De forma predeterminada, MSAL utiliza un agente como primera opción para cumplimentar una solicitud de autenticación, por lo que su uso para la autenticación se habilitará automáticamente para la aplicación al usar MSAL de forma predefinida.

Por último, [agregue el SDK de Intune](/mem/intune/developer/app-sdk-get-started) a la aplicación para habilitar las directivas de protección de aplicaciones. El SDK sigue en su mayor parte un modelo de interceptación, y aplicará automáticamente las directivas de protección de aplicaciones para determinar si las acciones que realiza la aplicación se permiten o no. También hay API a las que puede llamar manualmente para indicar a la aplicación si hay restricciones en ciertas acciones.

## <a name="additional-resources"></a>Recursos adicionales

- [Planificación de una implementación de inicio de sesión único de Azure Active Directory](../manage-apps/plan-sso-deployment.md)
- [Procedimientos: Configuración del inicio de sesión único en macOS e iOS](single-sign-on-macos-ios.md)
- [Complemento de Microsoft Enterprise SSO para dispositivos Apple (versión preliminar)](apple-sso-plugin.md)
- [Autenticación con agente en Android](./msal-android-single-sign-on.md)
- [Agentes de autorización y cómo habilitarlos](./msal-android-single-sign-on.md)
- [Introducción al SDK para aplicaciones de Microsoft Intune](/mem/intune/developer/app-sdk-get-started)
- [Establecer la configuración de Intune App SDK](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Aplicaciones protegidas de Microsoft Intune](/mem/intune/apps/apps-supported-intune-apps)
---
title: Registro de aplicaciones móviles que llaman a las API web | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a compilar una aplicación móvil que llame a las API web (registro de aplicaciones)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/18/2021
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7fa12eca00bca011107f48c6f1b282dded4f3b5a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444553"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registro de aplicaciones móviles que llaman a las API web

Este artículo contiene instrucciones para ayudarle a registrar una aplicación móvil que está creando.

## <a name="supported-account-types"></a>Tipos de cuenta admitidos

Los tipos de cuenta admitidos en sus aplicaciones móviles dependen de la experiencia que quiera habilitar y los flujos que quiera usar.

### <a name="audience-for-interactive-token-acquisition"></a>Público para la adquisición interactiva de tokens

La mayoría de las aplicaciones móviles usan la autenticación interactiva. Si la aplicación usa esta forma de autenticación, puede iniciar la sesión de los usuarios desde cualquier [tipo de cuenta](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Audiencia para la autenticación integrada de Windows, con nombre de usuario y contraseña, y B2C

Si tiene una aplicación para la Plataforma universal de Windows (UWP), puede usar la autenticación integrada de Windows (IWA) para iniciar la sesión de los usuarios. Para usar IWA o la autenticación con un nombre de usuario y una contraseña, la aplicación debe iniciar la sesión de los usuarios en su propio inquilino de desarrollador de línea de negocio (LOB). En un escenario de fabricante de software independiente (ISV), la aplicación puede iniciar la sesión de los usuarios en organizaciones de Azure Active Directory. Estos flujos de autenticación no son compatibles con las cuentas personales de Microsoft.

También puede iniciar la sesión de los usuarios mediante identidades sociales que pasen una directiva y una autoridad de B2C. Para usar este método, solo puede usar la autenticación interactiva y la autenticación con nombre de usuario y contraseña. La autenticación con nombre de usuario y contraseña solo se admite actualmente en Xamarin.iOS, Xamarin.Android y UWP.

Para obtener más información, consulte [Escenarios y flujos de autenticación compatibles](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) y [Escenarios, plataformas y lenguajes compatibles](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Configuración de la plataforma e identificadores URI de redirección

### <a name="interactive-authentication"></a>Autenticación interactiva

Al compilar una aplicación móvil que usa la autenticación interactiva, el paso de registro más importante es el URI de redirección. Puede establecer la autenticación interactiva mediante la [configuración de la plataforma en la hoja **Autenticación**](https://aka.ms/MobileAppReg).

Esta experiencia permitirá a la aplicación obtener el inicio de sesión único (SSO) a través de Microsoft Authenticator (y el Portal de empresa de Intune en Android). También admite directivas de administración de dispositivos.

El portal de registro de aplicaciones proporciona una experiencia de versión preliminar para ayudarle a calcular el URI de respuesta asincrónico para aplicaciones iOS y Android:

1. En el portal de registro de aplicaciones, seleccione **Autenticación** > **Probar la nueva experiencia**.

   ![Hoja Autenticación, donde puede elegir una nueva experiencia](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Seleccione **Agregar una plataforma**.

   ![Agregar una plataforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Cuando se admita la lista de plataformas, seleccione **iOS**.

   ![Elegir una aplicación móvil](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Escriba el identificador del paquete y, a continuación, seleccione **Registrar**.

   ![Escribir el identificador de paquete](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Al completar los pasos, el URI de redirección se calcula automáticamente, como en la siguiente imagen.

![URI de redirección resultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Si prefiere configurar manualmente el URI de redirección, puede hacerlo mediante el manifiesto de aplicación. Este es el formato recomendado para el manifiesto:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Por ejemplo, escriba `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Puede generar el hash de firma de Android utilizando la tecla de liberación o la tecla de depuración a través del comando KeyTool.

### <a name="username-password-authentication"></a>Autenticación con nombre de usuario y contraseña

Si la aplicación solo utiliza la autenticación con nombre de usuario y contraseña, no es necesario que registre ningún URI de redirección para la aplicación. Este flujo realiza un recorrido de ida y vuelta a la Plataforma de identidad de Microsoft. No se volverá a llamar a la aplicación en ningún URI específico.

Sin embargo, exprese la aplicación como una aplicación cliente pública. Para ello:

1. En <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>, seleccione la aplicación en **Registros de aplicaciones** y, a continuación, seleccione **Autenticación**.
1. En **Configuración avanzada** > **Permitir flujos de cliente público** > **Habilitar los flujos móviles y de escritorio siguientes:** , seleccione **Sí**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Habilite la opción de cliente público en el panel Autenticación de Azure Portal":::

## <a name="api-permissions"></a>Permisos de API

Las aplicaciones móviles llaman a las API en nombre del usuario que tiene la sesión iniciada. La aplicación debe solicitar permisos delegados. Estos permisos también se denominan ámbitos. En función de la experiencia que desee, puede solicitar los permisos delegados de forma estática a través de Azure Portal. O bien, puede solicitarlos dinámicamente en tiempo de ejecución.

El registro estático de permisos permite a los administradores aprobar fácilmente su aplicación. Se recomienda el registro estático.

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Configuración del código de la aplicación](scenario-mobile-app-configuration.md).

---
title: Configuración de aplicaciones cliente (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre las opciones de configuración de aplicaciones cliente públicas y confidenciales mediante la Biblioteca de autenticación de Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 038646d4d6245398f5385e2a6a1595ce2909ee39
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418783"
---
# <a name="application-configuration-options"></a>Opciones de configuración de aplicaciones

Para la autenticación y adquisición de tokens, inicialice una aplicación cliente pública o confidencial en el código. Puede establecer varias opciones de configuración al inicializar la aplicación cliente en la Biblioteca de autenticación de Microsoft (MSAL). Estas opciones se dividen en dos grupos:

- Opciones de registro, incluidas:
  - [Autoridad](#authority) (compuesta por la [instancia](#cloud-instance) del proveedor de identidades y la [audiencia](#application-audience) de inicio de sesión para la aplicación, y posiblemente el identificador de inquilino)
  - [Id. de cliente](#client-id)
  - [URI de redirección](#redirect-uri)
  - [Secreto de cliente](#client-secret) (aplicaciones cliente confidenciales)
- [Opciones de registro](#logging), incluido el nivel de registro, control de los datos personales y el nombre del componente que usa la biblioteca

## <a name="authority"></a>Autoridad

La autoridad es una dirección URL que indica un directorio desde el que MSAL puede solicitar tokens.

Las autoridades comunes son:

| Direcciones URL de autoridad común | Cuándo se usa |
|--|--|
| `https://login.microsoftonline.com/<tenant>/` | En el inicio de sesión solo de los usuarios de una organización específica. `<tenant>` en la dirección URL es el identificador de inquilino del inquilino de Azure Active Directory (Azure AD) (un GUID) o su dominio de inquilino. |
| `https://login.microsoftonline.com/common/` | En el inicio de sesión de los usuarios con cuentas profesionales y educativas o cuentas personales de Microsoft. |
| `https://login.microsoftonline.com/organizations/` | En el inicio de sesión de los usuarios con cuentas profesionales y educativas. |
| `https://login.microsoftonline.com/consumers/` | En el inicio de sesión de los usuarios solo con cuentas personales de Microsoft (MSA). |

La autoridad que especifica en el código debe ser coherente con los **tipos de cuentas compatibles** especificados para la aplicación en **Registros de aplicaciones** en Azure Portal.

La autoridad puede ser:

- Una autoridad de Azure AD Cloud.
- Una autoridad de Azure AD B2C. Consulte [B2C specifics](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics) (Especificaciones de B2C).
- Una autoridad de Servicios de federación de Active Directory (AD FS). Consulte [AD FS Support](https://aka.ms/msal-net-adfs-support) (Compatibilidad con AD FS).

Las autoridades de Azure AD Cloud tienen dos partes:

- La *instancia* del proveedor de identidades.
- La *audiencia* de inicio de sesión para la aplicación.

La instancia y la audiencia se pueden concatenar y proporcionar como la dirección URL de la autoridad. En este diagrama se muestra cómo se compone la dirección URL de la autoridad:

![Cómo se compone la dirección URL de la autoridad](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Nombre de la instancia en la nube

La *instancia* se usa para especificar si la aplicación inicia la sesión de los usuarios desde la nube pública de Azure o desde nubes nacionales. Mediante el uso de MSAL en el código, puede establecer la instancia en la nube de Azure usando una enumeración o pasando la dirección URL a la [instancia en la nube nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como el miembro `Instance` (si la conoce).

MSAL.NET devolverá una excepción explícita si se especifican `Instance` y `AzureCloudInstance`.

Si no se especifica una instancia, la aplicación se dirigirá a la instancia en la nube pública de Azure (la instancia de la dirección URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Audiencia de la aplicación

La audiencia de inicio de sesión depende de las necesidades empresariales de la aplicación:

- Si es desarrollador de líneas de negocio (LOB), probablemente producirá una aplicación de inquilino único que se usará solo en su organización. En ese caso, especifique la organización por el Id. de inquilino (el Id. de la instancia de Azure AD) o por un nombre de dominio asociado a la instancia de Azure AD.
- Si es ISV, puede que quiera que los usuarios inicien sesión con sus cuentas profesionales o educativas en cualquier organización o en determinadas organizaciones (aplicación multiinquilino). Pero también es posible que quiera que los usuarios inicien sesión con sus cuentas personales de Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Cómo especificar la audiencia en el código o la configuración

Mediante el uso de MSAL en el código, se especifica la audiencia con uno de los siguientes valores:

- La enumeración de la audiencia de la autoridad de Azure AD.
- El Id. de inquilino, que puede ser:
  - Un GUID (el Id. de la instancia de Azure AD), para las aplicaciones de inquilino único.
  - Un nombre de dominio asociado a la instancia de Azure AD (también para las aplicaciones de inquilino único).
- Uno de estos marcadores de posición como un Id. de inquilino en lugar de la enumeración de la audiencia de la autoridad de Azure AD:
  - `organizations` para una aplicación multiinquilino,
  - `consumers` para iniciar la sesión de los usuarios solo con sus cuentas personales,
  - `common` para el inicio de sesión de los usuarios con sus cuentas profesionales y educativas o sus cuentas personales de Microsoft.

MSAL devolverá una excepción significativa si especifican la audiencia y el Id. de inquilino de la autoridad de Azure AD.

Si no se especifica una audiencia, la aplicación se dirigirá a cuentas de Azure AD y personales de Microsoft como audiencia (es decir, se comportará como si se especificara `common`).

### <a name="effective-audience"></a>Audiencia eficaz

La audiencia eficaz para su aplicación será la mínima (si hay una intersección) entre la audiencia que estableció en la aplicación y la audiencia especificada en el registro de aplicaciones. De hecho, la experiencia de [Registros de aplicaciones](https://aka.ms/appregistrations) permite especificar la audiencia (los tipos de cuenta compatibles) para la aplicación. Para más información, consulte [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](quickstart-register-app.md).

Actualmente, la única manera de que una aplicación inicie la sesión de los usuarios con solo cuentas personales de Microsoft es configurar estas dos opciones:

- Establezca la audiencia del registro de aplicaciones en `Work and school accounts and personal accounts`.
- Establezca la audiencia del código o de la configuración en `AadAuthorityAudience.PersonalMicrosoftAccount` (o `TenantID` ="consumers").

## <a name="client-id"></a>Id. de cliente

El Id. de cliente es el Id. de (cliente) aplicación único que Azure AD asignó a la aplicación cuando esta se registró.

## <a name="redirect-uri"></a>URI de redireccionamiento

El URI de redirección es el URI al que el proveedor de identidades enviará los tokens de seguridad.

### <a name="redirect-uri-for-public-client-apps"></a>URI de redirección para aplicaciones cliente públicas

Si es desarrollador de aplicaciones cliente públicas que usa MSAL:

- Puede usar `.WithDefaultRedirectUri()` en aplicaciones de escritorio o UWP (MSAL.net 4.1+). Este método establecerá la propiedad de URI de redireccionamiento de la aplicación cliente pública en el URI de redireccionamiento predeterminado recomendado para las aplicaciones cliente públicas.

  | Plataforma | URI de redireccionamiento |
  |--|--|
  | Aplicación de escritorio (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` |
  | UWP | Valor de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. Esto permite realizar el inicio de sesión único con el explorador, al establecer el valor en el resultado de WebAuthenticationBroker.GetCurrentApplicationCallbackUri(), que debe registrar. |
  | .NET Core | `https://localhost`. Esto permite que el usuario use el explorador del sistema para la autenticación interactiva, ya que .NET Core no tiene una interfaz de usuario para la vista web insertada en este momento. |

- No es necesario agregar un URI de redireccionamiento si se compila una aplicación Xamarin Android e iOS que no admite el URI de redireccionamiento del agente. Se establece automáticamente en `msal{ClientId}://auth` para Xamarin Android e iOS.

- Configure el URI de redireccionamiento en [Registros de aplicaciones](https://aka.ms/appregistrations):

   ![URI de redirección en Registros de aplicaciones](media/msal-client-application-configuration/redirect-uri.png)

Puede invalidar el URI de redirección mediante la propiedad `RedirectUri` (por ejemplo, si usa agentes). Estos son algunos ejemplos de URI de redirección para ese escenario:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Para más información sobre iOS, consulte [Migración de aplicaciones de iOS que usan Microsoft Authenticator de ADAL.NET a MSAL.NET](msal-net-migration-ios-broker.md) y [Aprovechamiento del agente en iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Para más información sobre Android, consulte [Autenticación con intermediación en Android](msal-android-single-sign-on.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI de redirección para aplicaciones cliente confidenciales

Para las aplicaciones web, el URI de redireccionamiento (o URL de respuesta) es el URI que Azure AD usará para enviar el token a la aplicación. Puede ser la dirección URL de la aplicación web o la API web si la aplicación confidencial es una de estas. El URI de redirección debe estar registrado en el registro de aplicaciones. Este registro es especialmente importante al implementar una aplicación que se ha probado inicialmente a nivel local. A continuación, debe agregar la dirección URL de respuesta de la aplicación implementada en el portal de registro de aplicaciones.

Para las aplicaciones demonio, no es necesario especificar un URI de redirección.

## <a name="client-secret"></a>Secreto del cliente

Esta opción especifica el secreto de cliente para la aplicación cliente confidencial. Este secreto (contraseña de la aplicación) lo proporcionan el portal de registro de aplicaciones o Azure AD durante el registro de la aplicación con PowerShell AzureAD, AzureRM o CLI de Azure.

## <a name="logging"></a>Registro
Para ayudar en los escenarios de solución de problemas de depuración y de errores de autenticación, la Biblioteca de autenticación de Microsoft proporciona compatibilidad con el registro integrado. El registro en cada biblioteca se trata en los siguientes artículos:

:::row:::
    :::column:::
        - [Registro en MSAL.NET](msal-logging-dotnet.md)
        - [Inicio de sesión en MSAL para Android](msal-logging-android.md)
        - [Registro en MSAL.js](msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Inicio de sesión en MSAL para iOS/macOS](msal-logging-ios.md)
        - [Registro en MSAL para Java](msal-logging-java.md)
        - [Inicio de sesión en MSAL para Python](msal-logging-python.md)
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [crear instancias de aplicaciones cliente mediante el uso de MSAL.NET](msal-net-initializing-client-applications.md) y cómo [crear instancias de aplicaciones cliente mediante el uso de MSAL.js](msal-js-initializing-client-applications.md).

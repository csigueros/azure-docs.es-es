---
title: Inicio de sesión único (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre la creación de experiencias de inicio de sesión único mediante la biblioteca de autenticación de Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e715b0b2176564c8a492df20b9193d66a8fb85c8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466093"
---
# <a name="single-sign-on-with-msaljs"></a>Inicio de sesión único con MASL.js

El inicio de sesión único (SSO) permite a los usuarios escribir sus credenciales una sola vez para iniciar sesión y establecer una sesión que se puede reutilizar en varias aplicaciones sin necesidad de volver a autenticarse. La sesión ofrece al usuario una experiencia fluida y reduce las continuas solicitudes de credenciales.

Cuando el usuario se autentica por primera vez, Azure Active Directory (Azure AD) proporciona funciones de inicio de sesión único a las aplicaciones mediante el establecimiento de una cookie de sesión. La biblioteca MSAL.js permite a las aplicaciones aplicar una cookie de sesión de varias maneras.

## <a name="sso-between-browser-tabs"></a>Inicio de sesión único entre pestañas del explorador

Cuando la aplicación se abre en varias pestañas y se inicia la sesión del usuario por primera vez en una pestaña, la sesión del usuario también se inicia en las demás pestañas sin ninguna solicitud. MSAL.js almacena en caché el token de identificación del usuario en el explorador `localStorage` e iniciará la sesión del usuario en la aplicación en las otras pestañas abiertas.

De manera predeterminada, MSAL.js usa `sessionStorage`, que no permite que la sesión se comparta entre pestañas. Para poder usar el inicio de sesión único entre pestañas, establezca la opción `cacheLocation` de MSAL.js en `localStorage`, tal como se muestra a continuación.

```javascript
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Inicio de sesión único entre aplicaciones

Cuando un usuario se autentica, se establece una cookie de sesión en el dominio de Azure AD del explorador. MSAL.js se basa en esta cookie de sesión para proporcionar un inicio de sesión único al usuario entre diferentes aplicaciones. MSAL.js también almacena en caché los tokens de identificador y tokens de acceso del usuario en el almacenamiento del explorador por dominio de aplicación. Como resultado, el comportamiento de inicio de sesión único varía en función del caso:

### <a name="applications-on-the-same-domain"></a>Aplicaciones en el mismo dominio

Cuando las aplicaciones se hospedan en el mismo dominio, el usuario puede iniciar sesión en una aplicación una vez y después autenticarse en las demás aplicaciones sin que se le pida. MSAL.js usa los tokens en caché para el usuario del dominio con el fin de ofrecer el inicio de sesión único.

### <a name="applications-on-different-domain"></a>Aplicaciones en diferentes dominios

Cuando las aplicaciones se hospedan en dominios diferentes, MSAL.js del dominio B no puede acceder a los tokens en caché del dominio A.

Cuando un usuario que ha iniciado sesión en el dominio A vaya a una aplicación en el dominio B, se le redirigirá a la página de inicio de sesión o se le solicitará que inicie sesión. Como Azure AD todavía tiene la cookie de sesión de usuario, iniciará la sesión del usuario y no se solicitarán credenciales.

Si el usuario tiene varias cuentas de usuario en la sesión con Azure AD, se le pedirá que elija la cuenta pertinente con la que quiere iniciar sesión.

### <a name="automatically-select-account-on-azure-ad"></a>Selección automática de la cuenta en Azure AD

En algunos casos, la aplicación tiene acceso al contexto de autenticación del usuario y es necesario evitar el mensaje de selección de cuenta de Azure AD cuando se inicia sesión con varias cuentas. La omisión del mensaje de selección de cuenta de Azure AD se puede realizar de varias maneras diferentes:

**Mediante el identificador de sesión**

El identificador de sesión (SID) es una [notificación opcional](active-directory-optional-claims.md) que se puede configurar en los tokens de identificación. Una notificación permite a la aplicación identificar la sesión de Azure AD del usuario independientemente del nombre de la cuenta del usuario o del nombre de usuario. Puede pasar el SID de los parámetros de la solicitud a la llamada `acquireTokenSilent`. En los parámetros de solicitud `acquireTokenSilent` permite a Azure AD omitir la selección de la cuenta. El SID está enlazado a la cookie de sesión y no cruzará los contextos del explorador.

```javascript
var request = {
  scopes: ["user.read"],
  sid: sid,
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

El SID solo se puede utilizar con las solicitudes de autenticación silenciosas realizadas por la llamada `acquireTokenSilent` en MSAL.js. Para encontrar los paso de configuración de las notificaciones opcionales en el manifiesto de aplicación, vea [Proporcionar notificaciones opcionales a la aplicación](active-directory-optional-claims.md).

**Usar la sugerencia de inicio de sesión**

Si no tiene configurada una notificación de SID o necesita omitir la petición de selección de cuenta en llamadas de autenticación interactivas, puede hacerlo si proporciona un valor `login_hint` en los parámetros de solicitud y, opcionalmente, un objeto `domain_hint` como `extraQueryParameters` en los métodos interactivos de MSAL.js (`loginPopup`, `loginRedirect`, `acquireTokenPopup` y `acquireTokenRedirect`). Por ejemplo:

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication.loginRedirect(request);
```

Para obtener los valores para login_hint y domain_hint puede leer las notificaciones devueltas en el token de identificación para el usuario.

- **loginHint** debe establecerse en la notificación `preferred_username` del token de identificación.

- **domain_hint** solo se tiene que pasar al utilizar la autoridad /common. La sugerencia de dominio viene determinada por el inquilino ID(tid). Si la notificación `tid` del token de identificación es `9188040d-6c67-4c5b-b112-36a304b66dad`, hace referencia a los consumidores. En caso contrario, hace referencia a las organizaciones.

Para más información sobre **login_hint** y **domain_hint**, vea [Flujo de concesión implícita](v2-oauth2-implicit-grant-flow.md).

## <a name="sso-without-msaljs-login"></a>Inicio de sesión único sin inicio de sesión en MSAL.js

De forma predeterminada, MSAL.js requiere que se llame a un método de inicio de sesión para establecer un contexto de usuario antes de obtener tokens para las API. Puesto que los métodos de inicio de sesión son interactivos, el usuario verá una solicitud.

Hay algunos casos en los que las aplicaciones tienen acceso al contexto del usuario autenticado o al token de identificación mediante una autenticación iniciada en otra aplicación y quieren usar el inicio de sesión único para adquirir tokens sin tener que iniciar sesión primero desde MSAL.js.

Ejemplo: un usuario ha iniciado sesión en una cuenta Microsoft en un explorador que hospeda otra aplicación de JavaScript que se ejecuta como complemento, para lo que se necesita un inicio de sesión de cuenta Microsoft.

La experiencia de inicio de sesión único en este escenario puede lograrse de la forma siguiente:

Pase `sid` si está disponible (o `login_hint` y, opcionalmente, `domain_hint`) como parámetros de solicitud a la llamada `acquireTokenSilent` de MSAL.js de la forma siguiente:

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Inicio de sesión único en ADAL.js para la actualización de MSAL.js

MSAL.js aporta paridad de características con ADAL.js para escenarios de autenticación de Azure AD. Para facilitar la migración de ADAL.js a MSAL.js y no tener que pedir a los usuarios que vuelvan a iniciar sesión, la biblioteca lee el token de identificación que representa la sesión del usuario en la memoria caché de ADAL.js e inicia la sesión del usuario en MSAL.js.

Para poder aprovechar el comportamiento de SSO al realizar la actualización desde ADAL.js, deberá asegurarse de que las bibliotecas usan `localStorage` para almacenar los tokens en caché. Al inicializar, establezca `cacheLocation` en `localStorage` en la configuración tanto de MSAL.js como de ADAL.js de la manera siguiente:

```javascript
// In ADAL.js
window.config = {
  clientId: "g075edef-0efa-453b-997b-de1337c29185",
  cacheLocation: "localStorage",
};

var authContext = new AuthenticationContext(config);

// In latest MSAL.js version
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

Una vez que se configure `cacheLocation`, MSAL.js puede leer el estado en caché del usuario autenticado en ADAL.js y usarlo para proporcionar el inicio de sesión único en MSAL.js.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre SSO, vea lo siguiente:

- [Protocolo SAML de inicio de sesión único](single-sign-on-saml-protocol.md)
- [Vigencia de tokens configurable](active-directory-configurable-token-lifetimes.md)

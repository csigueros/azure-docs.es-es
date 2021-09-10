---
title: Migración de su aplicación JavaScript de ADAL.js a MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: En este artículo se explica cómo actualizar una aplicación JavaScript con el objetivo de usar la biblioteca de autenticación de Microsoft (MSAL) para la autenticación y autorización en lugar de la biblioteca de autenticación de Active Directory (ADAL).
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/06/2021
ms.author: v-doeris
ms.custom: has-adal-ref
ms.openlocfilehash: a67f493666c458637a2cd48b7e0d78e0bcf59862
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418771"
---
# <a name="how-to-migrate-a-javascript-app-from-adaljs-to-msaljs"></a>Cómo migrar una aplicación de JavaScript de ADAL.js a MSAL.js

La [biblioteca de autenticación de Microsoft para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) (MSAL.js, también conocida como *msal-browser*) 2.x es la biblioteca de autenticación que recomendamos utilizar con aplicaciones JavaScript en la plataforma de identidad de Microsoft. En este artículo se resaltan los cambios que debe realizar para migrar una aplicación que usa ADAL.js para usar MSAL.js 2.x.

> [!NOTE]
> Se recomienda encarecidamente usar MSAL.js 2.x en lugar de MSAL.js 1.x. El flujo de concesión de código de autenticación es más seguro y permite que las aplicaciones de página única mantengan una buena experiencia de usuario a pesar de las medidas de privacidad que los exploradores como Safari han implementado para bloquear las cookies de terceros, entre otras ventajas.

## <a name="prerequisites"></a>Requisitos previos

- Debe establecer la **Plataforma** / **Tipo de URL de respuesta** en **Aplicación de una sola página** en el portal de registro de aplicaciones (si tiene otras plataformas en su registro de aplicaciones, como **Web**, debe asegurarse de que los URI de redirección no se solapen. Vea [Restricciones aplicables a los URI de redirección](./reply-url.md)).
- Debe proporcionar [polyfills](./msal-js-use-ie-browser.md) para las características de ES6 en las que se basa MSAL.js (por ejemplo, promesas) para ejecutar sus aplicaciones en **Internet Explorer**
- Asegúrese de haber migrado las aplicaciones de Azure AD al [punto de conexión v2](../azuread-dev/azure-ad-endpoint-comparison.md) si aún no lo ha hecho.

## <a name="install-and-import-msal"></a>Instalación e importación de MSAL

Hay dos maneras de instalar la biblioteca MSAL.js 2.x:

### <a name="via-npm"></a>A través de NPM:

```console
npm install @azure/msal-browser
```

A continuación, en función del sistema del módulo, impórtelo como se muestra a continuación:

```javascript
import * as msal from "@azure/msal-browser"; // ESM

const msal = require('@azure/msal-browser'); // CommonJS
```

### <a name="via-cdn"></a>A través de CDN:

Cargue el script en la sección de encabezado del documento HTML:

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js"></script>
  </head>
</html>
```

Para obtener vínculos alternativos de CDN y los procedimientos recomendados para utilizarlos, consulte [Uso de CDN](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md).

## <a name="initialize-msal"></a>Inicializar MSAL

En ADAL.js, se crea una instancia de la clase [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext), que luego expone los métodos que puede usar para lograr la autenticación (`login`, `acquireTokenPopup` etc.). Este objeto actúa como la representación de la conexión de la aplicación con el servidor de autorización o el proveedor de identidades. Al inicializarse, el único parámetro obligatorio es **clientId**:

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);
```

En MSAL.js, se crea una instancia de la clase [PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) en su lugar. Al igual que ADAL.js, el constructor espera un [objeto de configuración](#configure-msal) que contiene el parámetro `clientId` como mínimo. Para obtener más información, consulte [Inicialización de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md).

```javascript
const msalConfig = {
  auth: {
      clientId: 'YOUR_CLIENT_ID'
  }
};

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

Tanto en ADAL.js como en MSAL.js, el URI de autoridad tiene como valor predeterminado `https://login.microsoftonline.com/common` si no se especifica.

> [!NOTE]
> Si usa la autoridad `https://login.microsoftonline.com/common` en v2.0, permitirá a los usuarios iniciar sesión con una cuenta personal de Microsoft (MSA) o una de organización de Azure AD. En MSAL.js, si quiere restringir el inicio de sesión a cualquier cuenta de Azure AD (el mismo comportamiento que con ADAL.js), use `https://login.microsoftonline.com/organizations`.

## <a name="configure-msal"></a>Configuración de MSAL

Algunas de las [opciones de configuración de ADAL.js](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context) que se utilizan al inicializar [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) dejan de usarse en MSAL.js, mientras que se incluyen algunas nuevas. Vea la [lista completa de opciones disponibles](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md). Lo importante es que muchas de estas opciones, excepto `clientId`, pueden invalidarse durante la adquisición de tokens, lo que permite establecerlas en función de *cada solicitud*. Por ejemplo, puede utilizar un **URI de autoridad** o un **URI de redirección** diferente al que estableció durante la inicialización al adquirir los tokens.

Además, ya no es necesario especificar la experiencia de inicio de sesión (es decir, si se usan ventanas emergentes o se redirige la página) mediante las opciones de configuración. En su lugar, MSAL.js expone los métodos `loginPopup` y `loginRedirect` a través de la instancia de `PublicClientApplication`.

## <a name="enable-logging"></a>Habilitar registro

En ADAL.js, configure el registro por separado en cualquier lugar del código:

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);

var Logging = {
  level: 3,
  log: function (message) {
      console.log(message);
  },
  piiLoggingEnabled: false
};


authContext.log(Logging)
```

En MSAL.js, el registro forma parte de las opciones de configuración y se crea durante la inicialización de `PublicClientApplication`:

```javascript
const msalConfig = {
  auth: {
      // authentication related parameters
  },
  cache: {
      // cache related parameters
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
}

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

## <a name="switch-to-msal-api"></a>Cambio a la API de MSAL

La mayoría de los métodos públicos de ADAL.js tienen equivalentes en MSAL.js:

| ADAL                                | MSAL                              | Notas                                        |
|-------------------------------------|-----------------------------------|----------------------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | Se ha cambiado el nombre y ahora espera un objeto de [cuenta](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo). |
| `acquireTokenPopup`                 | `acquireTokenPopup`               | Ahora asincrónico y devuelve una promesa.              |
| `acquireTokenRedirect`              | `acquireTokenRedirect`            | Ahora asincrónico y devuelve una promesa.              |
| `handleWindowCallback`              | `handleRedirectPromise`           | Necesario si se usa la experiencia de redirección.          |
| `getCachedUser`                     | `getAllAccounts`                  | Se ha cambiado el nombre y ahora devuelve una matriz de cuentas.|

Otros quedaron en desuso, mientras que MSAL.js ofrece nuevos métodos:

| ADAL                              | MSAL                            | Notas                                            |
|-----------------------------------|---------------------------------|--------------------------------------------------|
| `login`                           | N/D                             | En desuso. Utilice `loginPopup` o `loginRedirect`.  |
| `logOut`                          | N/D                             | En desuso. Utilice `logoutPopup` o `logoutRedirect`.|
| N/D                               | `loginPopup`                    |                                                  |
| N/D                               | `loginRedirect`                 |                                                  |
| N/D                               | `logoutPopup`                   |                                                  |
| N/D                               | `logoutRedirect`                |                                                  |
| N/D                               | `getAccountByHomeId`            | Filtra las cuentas por identificador de inicio (oid + id. de inquilino).    |
| N/D                               | `getAccountLocalId`             | Filtra las cuentas por identificador local (útil para ADFS)   |
| N/D                               | `getAccountUsername`            | Filtra las cuentas por nombre de usuario (si existe).         |

Además, como MSAL.js se implementa en TypeScript a diferencia de ADAL.js, expone varios tipos e interfaces que puede usar en los proyectos. Consulte la [referencia de la API de MSAL.js](https://azuread.github.io/microsoft-authentication-library-for-js/ref/) para obtener más detalles.

## <a name="use-scopes-instead-of-resources"></a>Uso de ámbitos en lugar de recursos

Una diferencia importante entre los puntos de conexión **v1.0** y **v2.0** de Azure AD es cómo se accede a los recursos. Al utilizar ADAL.js con el punto de conexión **v1.0**, primero registraría un permiso en el portal de registro de aplicaciones y, a continuación, solicitaría un token de acceso para un recurso (como Microsoft Graph), como se muestra a continuación:

```javascript
authContext.acquireTokenRedirect("https://graph.microsoft.com", function (error, token) {
  // do something with the access token
});
```

MSAL.js admite los puntos de conexión **v1.0** y **v2.0**. El punto de conexión **v2.0** emplea un modelo *centrado en el ámbito* para acceder a los recursos. Por lo tanto, cuando solicite un token de acceso para un recurso, también debe especificar el ámbito de ese recurso:

```javascript
msalInstance.acquireTokenRedirect({
  scopes: ["https://graph.microsoft.com/User.Read"]
});
```

Una ventaja del modelo centrado en el ámbito es la posibilidad de usar *ámbitos dinámicos*. Al compilar aplicaciones con el punto de conexión v1.0, era necesario registrar el conjunto completo de permisos (llamados *ámbitos estáticos*) que requería la aplicación para que el usuario diera su consentimiento en el momento del inicio de sesión. En v2.0, puede usar el parámetro de ámbito para solicitar los permisos en el momento en que lo desee (por eso se llaman *ámbitos dinámicos*). Esto permite que el usuario dé su **consentimiento incremental** a los ámbitos. Por lo tanto, si al inicio solo quería que el usuario iniciara sesión en la aplicación y no necesita ningún tipo de acceso, puede hacerlo. Si posteriormente necesita poder leer el calendario del usuario, podrá solicitar el ámbito de calendario en los métodos acquireToken y obtener el consentimiento del usuario. Para obtener más información, consulte [Recursos y ámbitos](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md).

## <a name="use-promises-instead-of-callbacks"></a>Uso de promesas en lugar de devoluciones de llamada

En ADAL.js, las devoluciones de llamada se usan para cualquier operación después de que la autenticación se realice correctamente y se obtenga una respuesta:

```javascript
authContext.acquireTokenPopup(resource, extraQueryParameter, claims, function (error, token) {
  // do something with the access token
});
```

En MSAL.js, se usan promesas:

```javascript
msalInstance.acquireTokenPopup({
      scopes: ["User.Read"] // shorthand for https://graph.microsoft.com/User.Read
  }).then((response) => {
      // do something with the auth response
  }).catch((error) => {
      // handle errors
  });
```

También puede usar la sintaxis **async/await** que se incluye con ES8:

```javascript
const getAccessToken = async() => {
  try {
      const authResponse = await msalInstance.acquireTokenPopup({
          scopes: ["User.Read"]
      });
  } catch (error) {
      // handle errors
  }
}
```

## <a name="cache-and-retrieve-tokens"></a>Almacenamiento en caché y recuperación de tokens

Al igual que ADAL.js, MSAL.js almacena en caché los tokens y otros artefactos de autenticación en el almacenamiento del explorador, utilizando la [API de almacenamiento web](https://developer.mozilla.org/docs/Web/API/Web_Storage_API). Se recomienda utilizar la opción `sessionStorage` (consulte [configuración](#configure-msal)) porque es más segura para almacenar los tokens que adquieren sus usuarios, pero `localStorage` le proporcionará [Inicio de sesión único](./msal-js-sso.md) entre pestañas y sesiones de usuario.

Es importante destacar que no se debe acceder a la caché directamente. En su lugar, debe utilizar una API de MSAL.js adecuada para recuperar artefactos de autenticación como tokens de acceso o cuentas de usuario.

## <a name="renew-tokens-with-refresh-tokens"></a>Renovación de tokens con tokens de actualización

ADAL.js utiliza el [flujo implícito OAuth 2.0](./v2-oauth2-implicit-grant-flow.md), que no devuelve tokens de refresco por razones de seguridad (los tokens de actualización tienen una duración más larga que los tokens de acceso y, por tanto, son más peligrosos en manos de actores malintencionados). Por lo tanto, ADAL.js realiza la renovación de los tokens mediante un Iframe oculto para que no se pida al usuario que se autentique repetidamente.

Con el flujo de código de autenticación compatible con PKCE, las aplicaciones que utilizan MSAL.js 2.x obtienen tokens de actualización junto con tokens de identificación y acceso, que se pueden usar para renovarlos. El uso de los tokens de actualización se abstrae, y se supone que los desarrolladores no deben crear lógica en torno a ellos. En su lugar, MSAL administra la renovación de tokens mediante tokens de actualización por sí mismo. La caché de tokens anterior con ADAL.js no será transferible a MSAL.js, ya que el esquema de caché de tokens ha cambiado y es incompatible con el esquema usado en ADAL.js.

## <a name="handle-errors-and-exceptions"></a>Control de errores y excepciones

Al usar MSAL.js, el tipo de error más común al que podría enfrentarse es el error `interaction_in_progress`. Este error se produce cuando se invoca una API interactiva (`loginPopup`, `loginRedirect`, `acquireTokenPopup`, `acquireTokenRedirect`) mientras otra API interactiva sigue en curso. Las API `login*` y `acquireToken*` son *asincrónicas* por lo que deberá asegurarse de que las promesas resultantes se han resuelto antes de invocar otra.

Otro error común es `interaction_required`. Para resolverlo, en general basta con iniciar un símbolo del sistema de adquisición de tokens interactivo. Por ejemplo, la API web a la que intenta acceder podría tener una directiva de [acceso condicional](../conditional-access/overview.md), que requiere que el usuario realice la [autenticación multifactor](../authentication/concept-mfa-howitworks.md) (MFA). En ese caso, el control del error `interaction_required` mediante el desencadenamiento de `acquireTokenPopup` o `acquireTokenRedirect` solicitará al usuario que realice la autenticación multifactor, lo que le permitirá completarla.

Otro error común al que podría enfrentarse es `consent_required`, que se produce cuando el usuario no da su consentimiento a los permisos necesarios para obtener un token de acceso para un recurso protegido. Al igual que en el caso de `interaction_required`, la solución del error `consent_required` suele consistir en iniciar un símbolo del sistema de adquisición de tokens interactivo mediante el método `acquireTokenPopup` o `acquireTokenRedirect`.

Más información: [Errores comunes de MSAL.js y solución](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/errors.md)

## <a name="use-the-events-api"></a>Uso de la API de eventos

MSAL.js (>=v2.4) presenta una API de eventos que puede usar en sus aplicaciones. Estos eventos están relacionados con el proceso de autenticación y lo que hace MSAL en cualquier momento, y se pueden usar para actualizar la interfaz de usuario, mostrar mensajes de error, comprobar si hay alguna interacción en curso, etc. Por ejemplo, a continuación se muestra una devolución de llamada de evento a la que se llamará cuando se produce un error en el proceso de inicio de sesión por cualquier motivo:

```javascript
const callbackId = msalInstance.addEventCallback((message) => {
  // Update UI or interact with EventMessage here
  if (message.eventType === EventType.LOGIN_FAILURE) {
      if (message.error instanceof AuthError) {
          // Do something with the error
      }
    }
});
```

Para mejorar el rendimiento, es importante anular el registro de las devoluciones de llamada de eventos cuando ya no sean necesarias. Más información: [API de eventos de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/events.md)

## <a name="handle-multiple-accounts"></a>Control de varias cuentas

ADAL.js tiene el concepto de *usuario* para representar la entidad autenticada actualmente. MSAL.js sustituye *usuarios* por *cuentas*, dado que un usuario puede tener más de una cuenta asociada. Esto también significa que ahora debe controlar varias cuentas y elegir la adecuada con la que trabajar. El siguiente fragmento de código ilustra este proceso:

```javascript
let homeAccountId = null; // Initialize global accountId (can also be localAccountId or username) used for account lookup later, ideally stored in app state

// This callback is passed into `acquireTokenPopup` and `acquireTokenRedirect` to handle the interactive auth response
function handleResponse(resp) {
  if (resp !== null) {
      homeAccountId = resp.account.homeAccountId; // alternatively: resp.account.homeAccountId or resp.account.username
  } else {
      const currentAccounts = myMSALObj.getAllAccounts();
      if (currentAccounts.length < 1) { // No cached accounts
          return;
      } else if (currentAccounts.length > 1) { // Multiple account scenario
          // Add account selection logic here
      } else if (currentAccounts.length === 1) {
          homeAccountId = currentAccounts[0].homeAccountId; // Single account scenario
      }
  }
}
```

Para más información, consulte [Cuentas en MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/accounts.md).

## <a name="use-the-wrappers-libraries"></a>Uso de bibliotecas contenedoras

Si está desarrollando para marcos Angular y React, puede usar [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) y [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react), respectivamente. Estos contenedores exponen la misma API pública que MSAL.js mientras ofrecen métodos y componentes específicos del marco que pueden simplificar los procesos de autenticación y adquisición de tokens.

## <a name="run-the-app"></a>Ejecución la aplicación

Una vez realizados los cambios, ejecute la aplicación y pruebe el escenario de autenticación:

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>Ejemplo: Diferencias en la protección de aplicaciones web con el nodo ADAL y con el nodo MSAL

Los siguientes fragmentos de código demuestran el código mínimo necesario para una aplicación de página única que autentica a los usuarios con la plataforma de identidad de Microsoft y obtiene un token de acceso para Microsoft Graph utilizando primero ADAL.js y luego MSAL.js:

<table>
<tr><td> Uso de ADAL.js </td><td> Uso de MSAL.js </td></tr>
<tr>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script 
    type="text/javascript"
    src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.18/js/adal.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>

    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    var authContext = new AuthenticationContext({
        instance: 'https://login.microsoftonline.com/',
        clientId: "ENTER_CLIENT_ID",
        tenant: "ENTER_TENANT_ID",
        cacheLocation: "sessionStorage",
        redirectUri: "http://localhost:3000",
        popUp: true,
        callback: function (errorDesc, token, error, tokenType) {
            console.log('Hello ' + authContext.getCachedUser().profile.upn)

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        }
    });

    authContext.log({
        level: 3,
        log: function (message) {
            console.log(message);
        },
        piiLoggingEnabled: false
    });

    loginButton.addEventListener('click', function () {
        authContext.login();
    });

    logoutButton.addEventListener('click', function () {
        authContext.logOut();
    });

    tokenButton.addEventListener('click', () => {
        authContext.acquireTokenPopup(
            "https://graph.microsoft.com", 
            null, null, 
            function (error, token) {
                console.log(error, token);
            }
        )
    });
  </script>
</body>

</html>

```

</td>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script 
    type="text/javascript" 
    src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>
    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    const pca = new msal.PublicClientApplication({
        auth: {
            clientId: "ENTER_CLIENT_ID",
            authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
            redirectUri: "http://localhost:3000",
        },
        cache: {
            cacheLocation: "sessionStorage"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    });

    loginButton.addEventListener('click', () => {
        pca.loginPopup().then((response) => {
            console.log(`Hello ${response.account.username}!`);

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        })
    });

    logoutButton.addEventListener('click', () => {
        pca.logoutPopup().then((response) => {
            window.location.reload();
        })
    });

    tokenButton.addEventListener('click', () => {
        pca.acquireTokenPopup({
            scopes: ["User.Read"]
        }).then((response) => {
            console.log(response);
        })
    });
  </script>
</body>

</html>

```

</td>
</tr>
</table>

## <a name="next-steps"></a>Pasos siguientes

- [Referencia de API de MSAL.js](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)
- [Ejemplos de código de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples)

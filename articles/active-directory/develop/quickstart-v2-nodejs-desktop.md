---
title: 'Inicio rápido: Llamada a Microsoft Graph desde una aplicación de escritorio de Node.js | Azure'
titleSuffix: Microsoft identity platform
description: En este inicio rápido, obtendrá información sobre la forma en que una aplicación de escritorio de Node.js Electron puede iniciar sesión de usuarios y obtener un token de acceso para llamar a una API protegida por un punto de conexión de una plataforma de identidad de Microsoft.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 8e791392386f6a2f74f6d59e7999afeb272249aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605760"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Inicio rápido: Adquisición de un token de acceso y llamada a Microsoft Graph API desde una aplicación de escritorio de Electron

En este inicio rápido descargará y ejecutará un código de ejemplo que muestra cómo una aplicación de escritorio de Electron puede realizar el inicio de sesión de usuarios y adquirir tokens de acceso para llamar a Microsoft Graph API.

En este inicio rápido se usa la [biblioteca de autenticación de Microsoft para Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) con el [flujo del código de autorización con PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Prerrequisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) u otro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registro y descarga de la aplicación de ejemplo
>
> Para comenzar, siga estos pasos.
>
> #### <a name="step-1-register-the-application"></a>Paso 1: Registro de la aplicación
> Para registrar la aplicación y agregar la información de registro de la aplicación a la solución de forma manual, siga estos pasos:
>
> 1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Si tiene acceso a varios inquilinos, use el filtro **Directorios y suscripciones** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para ir al inquilino en el que quiere registrar la aplicación.
> 1. Busque y seleccione **Azure Active Directory**.
> 1. En **Administrar**, seleccione **Registros de aplicaciones** >  y, luego, **Nuevo registro**.
> 1. Escriba el **Nombre** de la aplicación, por ejemplo `msal-node-desktop`. Los usuarios de la aplicación pueden ver este nombre, el cual se puede cambiar más tarde.
> 1. Seleccione **Registrar** para crear la aplicación.
> 1. En **Administrar**, seleccione **Autenticación**.
> 1. Selecciones **Agregar una plataforma** > **Aplicaciones móviles y de escritorio**.
> 1. En la sección **URI de redirección**, escriba `msal://redirect`.
> 1. Seleccione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Paso 1: Configuración de la aplicación en Azure Portal
> Para que el código de ejemplo de este inicio rápido funcione, es preciso agregar una dirección URL de respuesta como **msal://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hacer este cambio por mí]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Ya configurada](media/quickstart-v2-windows-desktop/green-check.png) La aplicación está configurada con estos atributos.

#### <a name="step-2-download-the-electron-sample-project"></a>Paso 2: Descarga del proyecto de ejemplo Electron

> [!div renderon="docs"]
> [Descargar el código de ejemplo](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descargar el código de ejemplo](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Paso 3: Configuración del proyecto de ejemplo Electron
>
> 1. Extraiga el archivo ZIP en una carpeta local próxima a la raíz del disco, por ejemplo, *C:\Azure-Samples*.
> 1. Edite *.env* y sustituya los valores de los campos `TENANT_ID` y `CLIENT_ID` por el siguiente fragmento de código:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Donde:
>    - `Enter_the_Application_Id_Here`: es el **identificador de aplicación (cliente)** de la aplicación que registró.
>    - `Enter_the_Tenant_Id_Here`: sustituya este valor por el **identificador de inquilino** o el **nombre de inquilino** (por ejemplo, contoso.microsoft.com).
>
> > [!TIP]
> > Para buscar los valores de **identificador de aplicación (cliente)** e **identificador de directorio (inquilino)** , vaya a la página **Información general** de Azure Portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Paso 4: Ejecución de la aplicación

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Paso 4: Ejecución de la aplicación

Deberá instalar las dependencias de este ejemplo una vez:

```console
npm install
```

A continuación, ejecute la aplicación a través del símbolo del sistema o la consola:

```console
npm start
```

Debería ver la interfaz de usuario de la aplicación con el botón **Inicio de sesión**.

## <a name="about-the-code"></a>Sobre el código

A continuación, se describen algunos de los aspectos importantes de la aplicación de ejemplo.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) es la biblioteca que se usa para iniciar la sesión de los usuarios y solicitar los tokens que se usan para acceder a una API protegida por la plataforma de identidad de Microsoft. Para más información sobre cómo usar MSAL Node con aplicaciones de escritorio, consulte [este artículo](scenario-desktop-overview.md).

Para instalar MSAL Node, ejecute el siguiente comando npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicialización de MSAL

La referencia de MSAL se puede agregar mediante la incorporación del código siguiente:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

A continuación, realice la inicialización de MSAL con el siguiente código:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Donde: |Descripción |
> |---------|---------|
> | `clientId` | Es el **Identificador de aplicación (cliente)** de la aplicación registrada en Azure Portal. Puede encontrar este valor en la página **Información general** de la aplicación en Azure Portal. |
> | `authority`    | El punto de conexión STS para el usuario que se autenticará. Normalmente `https://login.microsoftonline.com/{tenant}` en la nube pública, donde {tenant} es el nombre o el identificador del inquilino.|

### <a name="requesting-tokens"></a>Solicitud de tokens

En el primer segmento del flujo de código de autorización con PKCE, prepare y envíe una solicitud de código de autorización con los parámetros adecuados. Luego, en el segundo segmento del flujo, escuche la respuesta del código de autorización. Una vez que se obtiene el código, se intercambia para obtener un token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Donde:| Descripción |
> |---------|---------|
> | `authWindow` | Ventana de Electron actual en proceso. |
> | `tokenRequest` | Contiene los ámbitos que se solicitan, como `"User.Read"` para Microsoft Graph o `"api://<Application ID>/access_as_user"` para las API web personalizadas. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el desarrollo de aplicaciones de escritorio de Electron con MSAL Node, consulte el tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Inicio de sesión por los usuarios y llamada a Microsoft Graph API en una aplicación de escritorio de Electron](tutorial-v2-nodejs-desktop.md)
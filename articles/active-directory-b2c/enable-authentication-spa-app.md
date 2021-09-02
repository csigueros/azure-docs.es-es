---
title: Habilitación de la autenticación en una aplicación de página única mediante los bloques de creación de Azure Active Directory B2C
description: En este artículo se abordan los bloques de creación de Azure Active Directory B2C para iniciar sesión y registrar usuarios en una aplicación de página única.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: e3d46a2843f84fa13c0d80288ccf43e1e474e2b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723219"
---
# <a name="enable-authentication-in-your-own-single-page-application-by-using-azure-ad-b2c"></a>Habilitación de la autenticación en su propia aplicación de página única mediante Azure AD B2C

En este artículo se muestra cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) en su propia aplicación de página única. Aprenda a crear una aplicación de página única mediante la [biblioteca de autenticación de Microsoft para JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js). 

Use este artículo junto con el artículo [Configuración de la autenticación en una aplicación web de ejemplo](./configure-authentication-sample-spa-app.md) cuando sustituya la aplicación de página única de ejemplo por la suya propia.

## <a name="overview"></a>Información general

En este artículo se usa Node.js y [Express](https://expressjs.com/) para crear una aplicación web de Node.js básica. Express es una plataforma mínima y flexible para aplicaciones web de Node.js que proporciona un conjunto de características para aplicaciones web y móviles.

La biblioteca de autenticación [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) es una biblioteca proporcionada por Microsoft que simplifica la adición de compatibilidad con autenticación y autorización a las aplicaciones de página única.

> [!TIP]
> El código MSAL.js se ejecuta en el lado cliente. Puede sustituir el código del lado servidor de Node.js y Express y por otras soluciones, como los lenguajes de scripting .NET Core, Java y Hypertext Preprocessor (PHP).

## <a name="prerequisites"></a>Requisitos previos

Revise los requisitos previos y las instrucciones de integración de [Configuración de la autenticación en una aplicación de página única de ejemplo](configure-authentication-sample-spa-app.md).

## <a name="step-1-create-an-spa-app-project"></a>Paso 1: Creación de un proyecto de aplicación de página única

Puede usar un proyecto de aplicación de página única existente o crear uno. Para crear un nuevo proyecto, haga lo siguiente:

1. Abra un shell de comandos y cree un nuevo directorio (por ejemplo, *myApp*). Este directorio contendrá el código de la aplicación, la interfaz de usuario y los archivos de configuración.

1. Especifique el directorio que ha creado.

1. Use el comando `npm init` para crear un archivo `package.json` para la aplicación. Este comando le pide información sobre la aplicación (por ejemplo, el nombre y la versión de la aplicación, y el nombre del punto de entrada inicial, el archivo *index.js*. Ejecute el siguiente comando y acepte los valores predeterminados:

```
npm init
```

## <a name="step-2-install-the-dependencies"></a>Paso 2: Instalación de las dependencias

Para instalar el paquete de Express, en el shell de comandos, ejecute el siguiente comando:

```
npm install express
```

Para localizar los archivos estáticos de la aplicación, el código en el lado servidor usa el paquete de [rutas de acceso](https://www.npmjs.com/package/path). 

Para instalar este paquete, en el shell de comandos, ejecute el siguiente comando:

```
npm install path
```

## <a name="step-3-configure-your-web-server"></a>Paso 3: Configuración del servidor web

En la carpeta *myApp*, cree un archivo llamado *index.js* que contenga el código siguiente:

```javascript
// Initialize express
const express = require('express');
const app = express();

// The port to listen to incoming HTTP requests
const port = 6420;

// Initialize path
const path = require('path');

// Set the front-end folder to serve public assets.
app.use(express.static('App'));

// Set up a route for the index.html
app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname + '/index.html'));
});

// Start the server, and listen for HTTP requests
app.listen(port, () => {
  console.log(`Listening on http://localhost:${port}`);
});
```

## <a name="step-4-create-the-spa-user-interface"></a>Paso 4: Creación de la interfaz de usuario de la aplicación de página única

Agregue el archivo `index.html` de la aplicación SAP. Este archivo implementa una interfaz de usuario que se crea con un marco de arranque e importa archivos de script para la configuración, autenticación y llamadas API web.

Los recursos a los que hace referencia el archivo *index.html* se detallan en la siguiente tabla: 

|Referencia |Definición|
|---|---|
|Biblioteca de MSAL.js| [Ruta de acceso CDN](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md) de la biblioteca de JavaScript de autenticación de MSAL.js.|
|[Hoja de estilos de arranque](https://getbootstrap.com/) | Un marco de front-end gratuito para un desarrollo web más rápido y sencillo. El marco incluye plantillas de diseño basadas en HTML y CSS. |
|[policies.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/policies.js): | Contiene las directivas personalizadas de Azure AD B2C y los flujos de usuario. |
|[authConfig.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authConfig.js): | Contiene parámetros de configuración de autenticación.|
|[authRedirect.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authRedirect.js) | Contiene la lógica de autenticación. |
|[apiConfig.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/apiConfig.js) | Contiene ámbitos de API web y la ubicación del punto de conexión de API. |
|[api.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/api.js) | Define el método que se usará para llamar a la API y controlar su respuesta.|
|[ui.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/ui.js) | Controla los elementos de la interfaz de usuario. |
| | |

Para representar el archivo de índice de la aplicación de página única, en la carpeta *myApp*, cree un archivo llamado *index.html* que contenga el siguiente fragmento de código HTML.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Azure AD B2C test app</title>
    </head>
    <body>
        <h2>My Azure AD B2C test app</h2>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign-in</button>
        <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign-out</button>
        <h5 id="welcome-div" class="card-header text-center d-none"></h5>
        <br />
        <!-- Content -->
        <div class="card">
            <div class="card-body text-center">
                <pre id="response" class="card-text"></pre>
                <button type="button" id="callApiButton" class="btn btn-primary d-none" onclick="passTokenToApi()">Call API</button>
            </div>
        </div>
        <script src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js" integrity="sha384-ggh+EF1aSqm+Y4yvv2n17KpurNcZTeYtUZUvhPziElsstmIEubyEB6AIVpKLuZgr" crossorigin="anonymous"></script>

        <!-- Importing app scripts (load order is important) -->
        <script type="text/javascript" src="./apiConfig.js"></script>
        <script type="text/javascript" src="./policies.js"></script>
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authRedirect.js"></script>
        <script type="text/javascript" src="./api.js"></script>
    </body>
</html>
 ```

## <a name="step-5-configure-the-authentication-library"></a>Paso 5: Configuración de la biblioteca de autenticación

Configure cómo se integra la biblioteca de MSAL.js con Azure AD B2C. La biblioteca de MSAL.js utiliza un objeto de configuración común para conectarse a los puntos de conexión de autenticación del inquilino de Azure AD B2C.

Para configurar la biblioteca de autenticación, haga lo siguiente:

1. En la carpeta *myApp*, cree una nueva carpeta llamada *App*. 
1. Dentro de la carpeta *App*, cree un archivo denominado *authConfig.js*.
1. Agregue el siguiente código de JavaScript al archivo *authConfig.js*:

    ```javascript
    const msalConfig = {
        auth: {
        clientId: "<Application-ID>", 
        authority: b2cPolicies.authorities.signUpSignIn.authority, 
        knownAuthorities: [b2cPolicies.authorityDomain], 
        redirectUri: "http://localhost:6420",
        },
        cache: {
        cacheLocation: "localStorage", .
        storeAuthStateInCookie: false, 
        }
    };

    const loginRequest = {
    scopes: ["openid", ...apiConfig.b2cScopes],
    };

    const tokenRequest = {
    scopes: [...apiConfig.b2cScopes],
    forceRefresh: false
    };
    ```

1. Reemplace `<Application-ID>` por el identificador de la aplicación de registro de la aplicación. Para más información, consulte [Configuración de la autenticación en una aplicación de página única de ejemplo](./configure-authentication-sample-spa-app.md#step-23-register-the-spa).

> [!TIP]
> Para conocer más opciones de configuración de objetos de MSAL, consulte el artículo [Opciones de autenticación](./enable-authentication-spa-app-options.md).

## <a name="step-6-specify-your-azure-ad-b2c-user-flows"></a>Paso 6: Especificación de los flujos de usuario de Azure AD B2C

Cree el archivo *policies.js*, el cual proporciona información sobre el entorno de Azure AD B2C. La biblioteca de MSAL.js utiliza esta información para crear solicitudes de autenticación para Azure AD B2C.

Para especificar los flujos de usuario de Azure AD B2C, haga lo siguiente:

1. En la carpeta *App*, cree un archivo denominado *policies.js*.
1. Agregue el código siguiente al archivo *policies.js*:

    ```javascript
    const b2cPolicies = {
        names: {
            signUpSignIn: "B2C_1_SUSI",
            editProfile: "B2C_1_EditProfile"
        },
        authorities: {
            signUpSignIn: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-SignInOrSignUp-Policy-Id",
            },
            editProfile: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-EditProfile-Policy-Id"
            }
        },
        authorityDomain: "contoso.b2clogin.com"
    }
    ```

1. Reemplace `B2C_1_SUSI` por el nombre de directiva de inicio de sesión de Azure AD B2C.
1. Reemplace `B2C_1_EditProfile` por el nombre de directiva de edición de perfiles de Azure AD B2C.
1. Reemplace todas las instancias de `contoso` por el [nombre del inquilino de Azure AD B2C](./tenant-management.md#get-your-tenant-name).

## <a name="step-7-use-the-msal-to-sign-in-the-user"></a>Paso 7: Uso de MSAL para iniciar sesión con el usuario

En este paso, implemente los métodos para inicializar el flujo de inicio de sesión, la adquisición de tokens de acceso de API y los métodos de cierre de sesión. 

Para más información, consulte la [referencia de la clase PublicClientApplication de MSAL](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) y el artículo [Uso de la biblioteca de autenticación de Microsoft (MSAL) para iniciar la sesión del usuario](../active-directory/develop/tutorial-v2-javascript-spa.md#use-the-microsoft-authentication-library-msal-to-sign-in-the-user).

Para iniciar la sesión del usuario, haga lo siguiente:

1. En la carpeta *App*, cree un archivo denominado *authRedirect.js*.
1. En *authRedirect.js*, copie y pegue el siguiente código:

    ```javascript
    // Create the main myMSALObj instance
    // configuration parameters are located at authConfig.js
    const myMSALObj = new msal.PublicClientApplication(msalConfig);

    let accountId = "";
    let idTokenObject = "";
    let accessToken = null;

    myMSALObj.handleRedirectPromise()
        .then(response => {
            if (response) {
                /**
                 * For the purpose of setting an active account for UI update, we want to consider only the auth response resulting
                 * from SUSI flow. "tfp" claim in the id token tells us the policy (NOTE: legacy policies may use "acr" instead of "tfp").
                 * To learn more about B2C tokens, visit https://docs.microsoft.com/en-us/azure/active-directory-b2c/tokens-overview
                 */
                if (response.idTokenClaims['tfp'].toUpperCase() === b2cPolicies.names.signUpSignIn.toUpperCase()) {
                    handleResponse(response);
                }
            }
        })
        .catch(error => {
            console.log(error);
        });


    function setAccount(account) {
        accountId = account.homeAccountId;
        idTokenObject = account.idTokenClaims;
        myClaims= JSON.stringify(idTokenObject);
        welcomeUser(myClaims);
    }

    function selectAccount() {

        /**
         * See here for more information on account retrieval: 
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */

        const currentAccounts = myMSALObj.getAllAccounts();

        if (currentAccounts.length < 1) {
            return;
        } else if (currentAccounts.length > 1) {
        
            /**
             * Due to the way MSAL caches account objects, the auth response from initiating a user-flow
             * is cached as a new account, which results in more than one account in the cache. Here we make
             * sure we are selecting the account with homeAccountId that contains the sign-up/sign-in user-flow, 
             * as this is the default flow the user initially signed-in with.
             */
            const accounts = currentAccounts.filter(account =>
                account.homeAccountId.toUpperCase().includes(b2cPolicies.names.signUpSignIn.toUpperCase())
                &&
                account.idTokenClaims.iss.toUpperCase().includes(b2cPolicies.authorityDomain.toUpperCase())
                &&
                account.idTokenClaims.aud === msalConfig.auth.clientId 
                );

            if (accounts.length > 1) {
                // localAccountId identifies the entity for which the token asserts information.
                if (accounts.every(account => account.localAccountId === accounts[0].localAccountId)) {
                    // All accounts belong to the same user
                    setAccount(accounts[0]);
                } else {
                    // Multiple users detected. Logout all to be safe.
                    signOut();
                };
            } else if (accounts.length === 1) {
                setAccount(accounts[0]);
            }

        } else if (currentAccounts.length === 1) {
            setAccount(currentAccounts[0]);
        }
    }

    // in case of page refresh
    selectAccount();

    async function handleResponse(response) {

        if (response !== null) {
            setAccount(response.account);
        } else {
            selectAccount();
        }
    }

    function signIn() {
        myMSALObj.loginRedirect(loginRequest);
    }

    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
        };

        myMSALObj.logoutRedirect(logoutRequest);
    }

    function getTokenRedirect(request) {
        request.account = myMSALObj.getAccountByHomeId(accountId); 

        return myMSALObj.acquireTokenSilent(request)
            .then((response) => {
                // In case the response from B2C server has an empty accessToken field
                // throw an error to initiate token acquisition
                if (!response.accessToken || response.accessToken === "") {
                    throw new msal.InteractionRequiredAuthError;
                } else {
                    console.log("access_token acquired at: " + new Date().toString());
                    accessToken = response.accessToken;
                    passTokenToApi();
                }
            }).catch(error => {
                console.log("Silent token acquisition fails. Acquiring token using popup. \n", error);
                if (error instanceof msal.InteractionRequiredAuthError) {
                    // fallback to interaction when silent call fails
                    return myMSALObj.acquireTokenRedirect(request);
                } else {
                    console.log(error);   
                }
        });
    }
    
    // Acquires and access token and then passes it to the API call
    function passTokenToApi() {
        if (!accessToken) {
            getTokenRedirect(tokenRequest);
        } else {
            try {
                callApi(apiConfig.webApi, accessToken);
            } catch(error) {
                console.log(error); 
            }
        }
    }

    function editProfile() {


        const editProfileRequest = b2cPolicies.authorities.editProfile;
        editProfileRequest.loginHint = myMSALObj.getAccountByHomeId(accountId).username;

        myMSALObj.loginRedirect(editProfileRequest);
    }
    ```

## <a name="step-8-configure-the-web-api-location-and-scope"></a>Paso 8: Configuración de la ubicación y el ámbito de la API web

Para permitir que la aplicación de página única llame a una API web, proporcione la ubicación del punto de conexión de la API web y los [ámbitos](./configure-authentication-sample-spa-app.md#app-registration-overview) que se usarán para autorizar el acceso a la API web.

Para configurar la ubicación y los ámbitos de la API web, haga lo siguiente:

1. En la carpeta *App*, cree un archivo denominado *apiConfig.js*.
1. En *apiConfig.js*, copie y pegue el siguiente código:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://contoso.onmicrosoft.com/tasks/tasks.read"],
        webApi: "https://mydomain.azurewebsites.net/tasks"
    };
    ```

1. Remplace `contoso` por el nombre del inquilino. El nombre del ámbito necesario se puede encontrar como se indica en el artículo [Configuración de ámbitos](./configure-authentication-sample-spa-app.md#step-22-configure-scopes).
1. Reemplace el valor de `webApi` por la ubicación del punto de conexión de la API web.

## <a name="step-9-call-your-web-api"></a>Paso 9: Llamada a la API web

Defina la solicitud HTTP al punto de conexión de API. La solicitud HTTP está configurada para pasar el token de acceso que se adquirió con *MSAL.js* en el encabezado HTTP `Authorization` de la solicitud.

El siguiente código define la solicitud HTTP `GET` para el punto de conexión de la API, pasando el token de acceso en el encabezado HTTP `Authorization`. La ubicación de la API se define en la clave `webApi` del archivo *apiConfig.js*. 

Para llamar a la API web mediante el token que adquirió, haga lo siguiente:

1. En la carpeta *App*, cree un archivo denominado *api.js*.
1. Agregue el código siguiente al archivo *api.js*:

    ```javascript
    function callApi(endpoint, token) {
        
        const headers = new Headers();
        const bearer = `Bearer ${token}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        logMessage('Calling web API...');
        
        fetch(endpoint, options)
        .then(response => response.json())
        .then(response => {

            if (response) {
            logMessage('Web API responded: ' + response.name);
            }
            
            return response;
        }).catch(error => {
            console.error(error);
        });
    }
    ```

## <a name="step-10-add-the-ui-elements-reference"></a>Paso 10: Adición de la referencia de los elementos de la interfaz de usuario

La aplicación de página única usa JavaScript para controlar los elementos de la interfaz de usuario. Por ejemplo, muestra los botones de inicio y de cierre de sesión, y representa las notificaciones del token de identificador de usuario en la pantalla.

Para agregar la referencia de los elementos de la interfaz de usuario, haga lo siguiente:

1. En la carpeta *App*, cree un archivo denominado *ui.js*.
1. Agregue el código siguiente al archivo *ui.js*:

    ```javascript
    // Select DOM elements to work with
    const signInButton = document.getElementById('signIn');
    const signOutButton = document.getElementById('signOut')
    const titleDiv = document.getElementById('title-div');
    const welcomeDiv = document.getElementById('welcome-div');
    const tableDiv = document.getElementById('table-div');
    const tableBody = document.getElementById('table-body-div');
    const editProfileButton = document.getElementById('editProfileButton');
    const callApiButton = document.getElementById('callApiButton');
    const response = document.getElementById("response");
    const label = document.getElementById('label');

    function welcomeUser(claims) {
        welcomeDiv.innerHTML = `Token claims: </br></br> ${claims}!`

        signInButton.classList.add('d-none');
        signOutButton.classList.remove('d-none');
        welcomeDiv.classList.remove('d-none');
        callApiButton.classList.remove('d-none');
    }

    function logMessage(s) {
        response.appendChild(document.createTextNode('\n' + s + '\n'));
    }
    ```
  
## <a name="step-11-run-your-spa-application"></a>Paso 11: Ejecución de la aplicación de página única

En el shell de comandos, ejecute los siguientes comandos:

``` powershell
npm install  
npm ./index.js
```

1. Ir a https://localhost:6420. 
1. Seleccione **Iniciar sesión**.
1. Complete el proceso de inicio de sesión o registro.

Una vez que se haya autenticado correctamente, el token del identificador analizado se muestra en la pantalla. Seleccione `Call API` para llamar al punto de conexión de la API.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [ejemplo de código](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa).
* Configure las [opciones de autenticación en su propia aplicación de página única mediante Azure AD B2C](enable-authentication-spa-app-options.md)
* [Habilite la autenticación en su propia API web](enable-authentication-web-api.md).

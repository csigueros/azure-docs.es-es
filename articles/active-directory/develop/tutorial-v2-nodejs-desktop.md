---
title: 'Tutorial: Inicio de sesión por los usuarios y llamada a Microsoft Graph API en una aplicación de escritorio de Electron | Azure'
titleSuffix: Microsoft identity platform
description: En este tutorial creará una aplicación de escritorio de Electron con la que los usuarios pueden iniciar sesión y usar el flujo de código de autenticación para obtener un token de acceso de la plataforma de identidad de Microsoft y llamar a Microsoft Graph API.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: 083c31b427c0f3d8cc261af7c9033d4ec1ffbeae
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500476"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Tutorial: Inicio de sesión por los usuarios y llamada a Microsoft Graph API en una aplicación de escritorio de Electron

En este tutorial creará una aplicación de escritorio de Electron con la que los usuarios inician sesión y que llama a Microsoft Graph mediante el flujo de código de autorización con PKCE. La aplicación de escritorio que cree usa la [biblioteca de autenticación de Microsoft (MSAL) para Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga los pasos de este tutorial para lo siguiente:

> [!div class="checklist"]
> - Registrar la aplicación en Azure Portal
> - Creación de un proyecto de aplicación de escritorio de Electron
> - Incorporación de la lógica de autenticación a la aplicación
> - Incorporación de un método para llamar a una API web
> - Incorporación de detalles del registro de la aplicación
> - Prueba de la aplicación

## <a name="prerequisites"></a>Prerrequisitos

- [Node.js](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) u otro editor de código

## <a name="register-the-application"></a>Registro de la aplicación

En primer lugar, complete los pasos descritos en [Registro de una aplicación en la plataforma de identidad de Microsoft](quickstart-register-app.md) para registrar la aplicación.

Use la siguiente configuración para el registro de la aplicación:

- Nombre: `ElectronDesktopApp` (sugerido)
- Tipos de cuenta admitidos: **Cuentas en cualquier directorio organizativo (cualquier directorio de Azure AD - multiinquilino) y cuentas Microsoft personales (por ejemplo, Skype, Xbox)**
- Tipo de plataforma: **Aplicaciones móviles y de escritorio**
- URI de redirección: `msal://redirect`

## <a name="create-the-project"></a>Creación del proyecto

Cree una carpeta para hospedar la aplicación, por ejemplo, *ElectronDesktopApp*.

1. En primer lugar, cambie al directorio del proyecto en el terminal y, a continuación, ejecute los siguientes comandos de `npm`:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. A continuación, cree una carpeta llamada *App*. Dentro de esta carpeta, cree un archivo denominado *index.html* que sirva como interfaz de usuario. Agréguele el siguiente código:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Después, cree un archivo llamado *main.js* y agregue el código siguiente:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

En el fragmento de código anterior se inicializa un objeto de ventana principal de Electron y se crean algunos controladores de eventos para las interacciones con la ventana de Electron. También se importan parámetros de configuración, se crean instancias de la clase *authProvider* para controlar el inicio de sesión, el cierre de sesión y la adquisición de tokens, y se llama a Microsoft Graph API.

4. En la misma carpeta (*App*), cree otro archivo denominado *renderer.js* y agregue el código siguiente:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Por último, cree un archivo denominado *constants.js* que almacenará las constantes de las cadenas para describir los **eventos** de la aplicación:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Ahora tiene una interfaz gráfica de usuario e interacciones sencillas para su aplicación de Electron. Después de completar el resto del tutorial, la estructura de archivos y carpetas del proyecto será similar a la siguiente:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Incorporación de la lógica de autenticación a la aplicación

En carpeta *App*, cree un archivo denominado *AuthProvider.js*. Este contendrá una clase de proveedor de autenticación que controlará el inicio de sesión, el cierre de sesión, la adquisición de tokens, la selección de cuentas y las tareas de autenticación relacionadas mediante el nodo de MSAL. Agréguele el siguiente código:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

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

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

En el fragmento de código anterior, primero se pasó un objeto de configuración (`msalConfig`) para inicializar el nodo de MSAL `PublicClientApplication`. A continuación, se expusieron los métodos `login`, `logout` y `getToken` a los que llamará el módulo principal (*main.js*). En `login` y `getToken` se adquieren tokens de identificador y de acceso, respectivamente; para ello, primero se solicita un código de autorización y, luego, se intercambia con un token mediante la API pública `acquireTokenByCode` del nodo de MSAL.

## <a name="add-a-method-to-call-a-web-api"></a>Incorporación de un método para llamar a una API web

Cree otro archivo denominado *fetch.js*. Este archivo contendrá un cliente HTTP de Axios para realizar llamadas de REST a Microsoft Graph API.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Incorporación de detalles del registro de la aplicación

Por último, cree un archivo de entorno para almacenar los detalles de registro de la aplicación que se usarán al adquirir tokens. Para ello, cree un archivo denominado *.env* dentro de la carpeta raíz del ejemplo (*ElectronDesktopApp*) y agregue el código siguiente:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Rellene estos detalles con los valores que se obtienen del portal de registro de aplicaciones de Azure:

- `Enter_the_Tenant_Id_here` debe ser una de las siguientes:
  - Si la aplicación admite *cuentas de este directorio organizativo*, reemplace este valor por los valores de **Id. de inquilino** o **Nombre de inquilino**. Por ejemplo, `contoso.microsoft.com`.
  - Si la aplicación admite *cuentas de cualquier directorio organizativo*, reemplace este valor por `organizations`.
  - Si la aplicación admite *cuentas de cualquier directorio organizativo y cuentas Microsoft personales*, reemplace este valor por `common`.
  - Para restringir la compatibilidad a *solo cuentas de Microsoft personales*, reemplace este valor por `consumers`.
- `Enter_the_Application_Id_Here`: el **identificador de la aplicación (cliente)** de la aplicación que registró.
- `Enter_the_Cloud_Instance_Id_Here`: Instancia en la nube de Azure en la que se registra la aplicación.
  - En el caso de la nube principal (o *global*) de Azure, escriba `https://login.microsoftonline.com/`.
  - En el caso de las nubes **nacionales** (por ejemplo, China), puede encontrar los valores adecuados en las [nubes nacionales](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` es la instancia de Microsoft Graph API con la que la aplicación debe comunicarse.
  - En el caso del punto de conexión de Microsoft Graph API **global**, reemplace ambas instancias de esta cadena por `https://graph.microsoft.com/`.
  - Para obtener información sobre los puntos de conexión en las implementaciones de nubes **nacionales**, consulte [Implementaciones de nube nacionales](/graph/deployments) en la documentación de Microsoft Graph.

## <a name="test-the-app"></a>Prueba de la aplicación

Ha terminado la creación de la aplicación y ya está listo para iniciar la aplicación de escritorio de Electron y probar la funcionalidad de la aplicación.

1. Inicie la aplicación mediante la ejecución del siguiente comando desde la raíz de la carpeta del proyecto:

```console
electron App/main.js
```

2. En la ventana principal de la aplicación debería ver el contenido del archivo *index.html* y el botón **Iniciar sesión**.

## <a name="test-sign-in-and-sign-out"></a>Prueba del inicio y el cierre de sesión

Una vez cargado el archivo *index.html*, seleccione **Iniciar sesión**. Se le pedirá que inicie sesión con la plataforma de identidad de Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="Solicitud de inicio de sesión":::

Si da su consentimiento a los permisos solicitados, las aplicaciones web muestran el nombre de usuario, lo que significa que el inicio de sesión se ha realizado correctamente:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="Inicio de sesión correcto":::

## <a name="test-web-api-call"></a>Prueba de la llamada de API web

Después de iniciar sesión, seleccione **Ver perfil** para ver la información de perfil de usuario devuelta en la respuesta de la llamada a Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Información del perfil de Microsoft Graph":::

Seleccione **Read Mails** (Leer correo electrónico) para ver los mensajes de la cuenta de usuario. Se mostrará una pantalla de consentimiento:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="Pantalla de consentimiento para el permiso read.mail.":::

Tras otorgar el consentimiento, verá los mensajes en la respuesta de la llamada a Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Información de correo electrónico de Microsoft Graph.":::

## <a name="how-the-application-works"></a>Cómo funciona la aplicación

Cuando un usuario selecciona el botón **Iniciar sesión** por primera vez, se llama al método get `getTokenInteractive` de *AuthProvider.js*. Este método redirige al usuario para que inicie sesión con el *punto de conexión de la plataforma de identidad de Microsoft* y valida sus credenciales, después, obtiene un **código de autorización**. A continuación, este código se intercambia por un token de acceso mediante la API pública `acquireTokenByCode` del nodo de MSAL.

En este momento, se envía un código de autorización protegido por PKCE al punto de conexión del token protegido con CORS y se intercambia por tokens. La aplicación recibe un token de identificador, un token de acceso y un token de actualización, los cuales procesa el modo de MSAL; la información contenida en los tokens se almacena en caché.

El token de identificador contiene información básica sobre el usuario, como su nombre para mostrar. El token de acceso tiene una duración limitada y expira después de 24 horas. Si piensa usar estos tokens para acceder a un recurso protegido, *debe* comprobarlo en el servidor back-end para asegurarse de que el token se emitió para un usuario válido en la aplicación.

La aplicación de escritorio que ha creado en este tutorial realiza una llamada de REST a Microsoft Graph API mediante un token de acceso, como token de portador en el encabezado de solicitud ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

Microsoft Graph API requiere el ámbito *user.read* para leer el perfil del usuario. De forma predeterminada, este ámbito se agrega automáticamente en todas las aplicaciones que se registran en Azure Portal. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Por ejemplo, Microsoft Graph API requiere el ámbito *Mail.Read* para mostrar el correo electrónico del usuario.

A medida que se agregan ámbitos, puede que se pida a los usuarios que proporcionen consentimiento adicional para los ámbitos agregados.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Si quiere profundizar más en el desarrollo de aplicaciones de escritorio de Electron en la plataforma de identidad de Microsoft, consulte nuestra serie de escenarios de varias partes:

> [!div class="nextstepaction"]
> [Escenario: Aplicación de escritorio que llama a las API web](scenario-desktop-overview.md)

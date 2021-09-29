---
title: 'Tutorial: Llamada a Microsoft Graph desde una aplicación de consola de Node.js | Azure'
titleSuffix: Microsoft identity platform
description: En este tutorial, creará una aplicación de consola para llamar Microsoft Graph a una aplicación de consola de Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 94e5675a4710e1d90ca053727c32219bb98d80c1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786339"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Tutorial: Llamada a Microsoft Graph API en una aplicación de consola de Node.js

En este tutorial, creará una aplicación de consola que llama a Microsoft Graph API mediante su propia identidad. La aplicación de consola que cree usa la [biblioteca de autenticación de Microsoft (MSAL) para Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga los pasos de este tutorial para:

> [!div class="checklist"]
> - Registrar la aplicación en Azure Portal
> - Crear un proyecto de aplicación de consola de Node.js
> - Agregar la lógica de autenticación a la aplicación
> - Agregar detalles del registro de la aplicación
> - Agregar un método para llamar a una API web
> - Prueba de la aplicación

## <a name="prerequisites"></a>Prerrequisitos

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) u otro editor de código

## <a name="register-the-application"></a>Registro de la aplicación

En primer lugar, complete los pasos descritos en [Registro de una aplicación en la plataforma de identidad de Microsoft](quickstart-register-app.md) para registrar la aplicación.

Use la siguiente configuración para el registro de la aplicación:

- Nombre: `NodeConsoleApp` (sugerido)
- Tipos de cuenta admitidos: **Solo las cuentas de este directorio organizativo**
- Permisos de API: **API de Microsoft** > **Microsoft Graph** > **Permisos de la aplicación** > `User.Read.All`
- Secreto de cliente: `*********` (registre este valor para usarlo en un paso posterior, se muestra solo una vez)

## <a name="create-the-project"></a>Creación del proyecto

Cree una carpeta para hospedar la aplicación, por ejemplo, *NodeConsoleApp*.

1. En primer lugar, cambie al directorio del proyecto en el terminal y, a continuación, ejecute los siguientes comandos de NPM:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. A continuación, cree una carpeta denominada *bin*. Después, dentro de esta carpeta, cree un archivo denominado *index.js* y agregue el código siguiente:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Este archivo hace referencia a otros dos módulos de nodo: *auth.js* que contiene una implementación de MSAL Node para adquirir tokens de acceso y *fetch.js* que contiene un método para hacer una solicitud HTTP a Microsoft Graph API con un token de acceso. Después de completar el resto del tutorial, la estructura de archivos y carpetas del proyecto será similar a la siguiente:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Adición de la lógica de autenticación

Dentro de la carpeta *bin*, cree otro archivo llamado *auth.js* y agregue el siguiente código para adquirir un token de acceso que se presente al llamar a Microsoft Graph API.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '/.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + '/v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

En el fragmento de código anterior, primero se crea un objeto de configuración (*msalConfig*) y se pasa para inicializar un objeto [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) de MSAL. A continuación, creamos un método para adquirir tokens a través de las **credenciales del cliente** y, por último, exponemos este módulo para que *main.js* pueda acceder a él. Los parámetros de configuración de este módulo se extraen de un archivo de entorno, que se creará en el paso siguiente.

## <a name="add-app-registration-details"></a>Agregar detalles del registro de la aplicación

Cree un archivo de entorno para almacenar los detalles de registro de la aplicación que se usarán al adquirir tokens. Para ello, cree un archivo denominado *.env* dentro de la carpeta raíz del ejemplo (*NodeConsoleApp*) y agregue el código siguiente:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Rellene estos detalles con los valores que se obtienen del portal de registro de aplicaciones de Azure:

- `Enter_the_Tenant_Id_here` debe ser una de las siguientes:
  - Si la aplicación admite *cuentas de este directorio organizativo*, reemplace este valor por los valores de **Id. de inquilino** o **Nombre de inquilino**. Por ejemplo, `contoso.microsoft.com`.
  - Si la aplicación admite *cuentas de cualquier directorio organizativo*, reemplace este valor por `organizations`.
  - Si la aplicación admite *cuentas de cualquier directorio organizativo y cuentas Microsoft personales*, reemplace este valor por `common`.
  - Para restringir la compatibilidad a *solo cuentas de Microsoft personales*, reemplace este valor por `consumers`.
- `Enter_the_Application_Id_Here`: el **identificador de la aplicación (cliente)** de la aplicación que registró.
- `Enter_the_Cloud_Instance_Id_Here`: Instancia en la nube de Azure en la que se registra la aplicación.
  - En el caso de la nube principal (o *global*) de Azure, escriba `https://login.microsoftonline.com`.
  - En el caso de las nubes **nacionales** (por ejemplo, China), puede encontrar los valores adecuados en las [nubes nacionales](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` es la instancia de Microsoft Graph API con la que la aplicación debe comunicarse.
  - En el caso del punto de conexión de Microsoft Graph API **global**, reemplace ambas instancias de esta cadena por `https://graph.microsoft.com`.
  - Para obtener información sobre los puntos de conexión en las implementaciones de nubes **nacionales**, consulte [Implementaciones de nube nacionales](/graph/deployments) en la documentación de Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Agregar un método para llamar a una API web

Dentro de la carpeta *bin*, cree otro archivo denominado *fetch.js* y agregue el código siguiente para realizar llamadas de REST a Microsoft Graph API:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Aquí, el método `callApi` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token de acceso. A continuación, el método devuelve el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. Aquí, el recurso protegido es el [punto de conexión de los usuarios](/graph/api/user-list) de Microsoft Graph API, que muestra a los usuarios del inquilino donde se registra esta aplicación.

## <a name="test-the-app"></a>Prueba de la aplicación

Ha completado la creación de la aplicación y ya está listo para probar la funcionalidad de la aplicación.

Inicie el servidor web de Node.js mediante la ejecución del siguiente comando desde la raíz de la carpeta del proyecto:

```console
node . --op getUsers
```

Esto debería producir alguna respuesta JSON de Microsoft Graph API y debería ver una matriz de objetos de usuario en la consola:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Interfaz de línea de comandos que muestra la respuesta de Graph":::

## <a name="how-the-application-works"></a>Cómo funciona la aplicación

Esta aplicación usa la [concesión de credenciales de cliente de OAuth 2.0](./v2-oauth2-client-creds-grant-flow.md). Este tipo de concesión se usa principalmente para las interacciones entre servidores que se deben ejecutar en segundo plano, sin la interacción inmediata con un usuario. El flujo de concesión de credenciales permite que un servicio web (cliente confidencial) use sus propias credenciales para autenticarse al llamar a otro servicio web, en lugar de suplantar a un usuario. El tipo de aplicaciones admitidas con este modelo de autenticación suele ser **daemons** o **cuentas de servicio**.

El ámbito que va a solicitar para un flujo de credenciales de cliente es el nombre del recurso seguido de `/.default`. Esta notación indica a Azure Active Directory (Azure AD) que use los permisos de nivel de aplicación declarados estáticamente durante el registro de la aplicación. Además, un **administrador de inquilinos** debe conceder estos permisos de API.

## <a name="next-steps"></a>Pasos siguientes

Si quiere profundizar más en el desarrollo de aplicaciones de consola de Node.js en la Plataforma de identidad de Microsoft, consulte nuestra serie de escenarios dividida en varias partes:

> [!div class="nextstepaction"]
> [Escenario: Aplicación de demonio](scenario-daemon-overview.md)

---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 52549e92a066df3caea479b0a20b21c747cb5ed4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461201"
---
## <a name="set-up-prerequisites"></a>Configuración de requisitos previos

- Versiones de [Node.js](https://nodejs.org/), Active LTS y Maintenance LTS (se recomiendan 8.11.1 y 10.14.1).

## <a name="set-up"></a>Configuración

### <a name="create-a-new-nodejs-application"></a>Creación de una nueva aplicación Node.js

Abra la ventana de comandos o de terminal, cree un nuevo directorio para la aplicación y navegue hasta este.

```console
mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
```

Ejecute `npm init -y` para crear un archivo `package.json` con la configuración predeterminada.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar el paquete

Use el comando `npm install` para instalar Identity SDK de Azure Communication Services para JavaScript.

```console

npm install @azure/communication-identity@beta --save
npm install @azure/msal-node --save
npm install express --save

```

La opción `--save` muestra la biblioteca como dependencia en el archivo **package.json**.

## <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Desde el directorio del proyecto:

1. Abra un nuevo archivo de texto en el editor de código.
1. Agregue una llamada `require` para cargar el `CommunicationIdentityClient`.
1. Cree la estructura del programa, incluido un control de excepciones básico.

    ```javascript
    const { CommunicationIdentityClient } = require('@azure/communication-identity');
    const express = require("express");
    const msal = require('@azure/msal-node');
    
    const SERVER_PORT = process.env.PORT || 80;
    const REDIRECT_URI = "http://localhost"; 
    
    // Quickstart code goes here
    
    app.listen(SERVER_PORT, () => console.log(`Communication access token application started on ${SERVER_PORT}!`))
    
    ```

1. Guarde el nuevo archivo como `issue-communication-access-token.js` en el directorio `access-tokens-quickstart`.

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Paso 1: Recibir el token de usuario de Azure AD mediante la biblioteca MSAL

El primer paso del flujo de intercambio de tokens es obtener un token para el usuario de Teams mediante [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```javascript
const msalConfig = {
    auth: {
        clientId: "<contoso_application_id>",
        authority: "https://login.microsoftonline.com/<contoso_tenant_id>",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const provider = new msal.CryptoProvider();

const app = express();

app.get('/', async (req, res) => {
    const {verifier, challenge} = await provider.generatePkceCodes();
    const authCodeUrlParameters = {
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
        codeChallenge: challenge, 
        codeChallengeMethod: "S256"
    };

    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

app.get('/redirect', async (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("Response: ", response);
        //TODO: the following code snippets go here
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Paso 2: Inicializar CommunicationIdentityClient

Cree una instancia de un objeto `CommunicationIdentityClient` con su cadena de conexión. El código siguiente recupera la cadena de conexión para el recurso de una variable de entorno denominada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Aprenda a [administrar la cadena de conexión del recurso](../create-communication-resource.md#store-your-connection-string).

Agregue el código siguiente al método `then`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Paso 3: Intercambiar el token de usuario de Azure AD para el token de acceso de Teams

Use el método `getTokenForTeamsUser` para emitir un token de acceso para el usuario de Teams que se puede usar con los SDK de Azure Communication Services.

```javascript
let accessToken = await identityClient.getTokenForTeamsUser(teamsToken);
console.log(`Token: ${accessToken}`);
```

## <a name="run-the-code"></a>Ejecución del código

Desde un símbolo del sistema de la consola, vaya al directorio que contiene el archivo *issue-communication-access-token.js* y, a continuación, ejecute el comando `node` siguiente para ejecutar la aplicación.

```console
node ./issue-communication-access-token.js
```

---
title: 'Tutorial: Inicio de sesión de usuarios en una aplicación web de Node.js y Express | Azure'
titleSuffix: Microsoft identity platform
description: En este tutorial, agregará compatibilidad para el inicio de sesión de usuarios en una aplicación web.
services: active-directory
author: KarenH444
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: karenhoran
ms.openlocfilehash: 4767d7aa31d705e25f8d87ca24f928c64fe022f1
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161616"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Tutorial: Inicio de sesión de usuarios en una aplicación web de Node.js y Express

En este tutorial, creará una aplicación web que inicia sesión para los usuarios. La aplicación web que cree usa la [biblioteca de autenticación de Microsoft (MSAL) para Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga los pasos de este tutorial para:

> [!div class="checklist"]
> - Registrar la aplicación en Azure Portal
> - Crear un proyecto de aplicación web Express
> - Instalar los paquetes de la biblioteca de autenticación
> - Agregar detalles del registro de la aplicación
> - Agregar código para el inicio de sesión del usuario
> - Prueba de la aplicación

## <a name="prerequisites"></a>Prerrequisitos

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) u otro editor de código

## <a name="register-the-application"></a>Registro de la aplicación

En primer lugar, complete los pasos descritos en [Registro de una aplicación en la plataforma de identidad de Microsoft](quickstart-register-app.md) para registrar la aplicación.

Use la siguiente configuración para el registro de la aplicación:

- Nombre: `ExpressWebApp` (sugerido)
- Tipos de cuenta admitidos: **Cuentas en cualquier directorio organizativo (cualquier directorio de Azure AD - multiinquilino) y cuentas Microsoft personales (por ejemplo, Skype, Xbox)**
- Tipo de plataforma: **Web**
- URI de redirección: `http://localhost:3000/redirect`
- Secreto de cliente: `*********` (registre este valor para usarlo en un paso posterior, se muestra solo una vez)

## <a name="create-the-project"></a>Creación del proyecto

Cree una carpeta para hospedar la aplicación, por ejemplo, *ExpressWebApp*.

1. En primer lugar, cambie al directorio del proyecto en el terminal y, a continuación, ejecute los siguientes comandos de `npm`:

```console
    npm init -y
    npm install --save express
```

2. Después, cree un archivo llamado *index.js* y agregue el código siguiente:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Ahora tiene un servidor web simple que se ejecuta en el puerto 3000. La estructura de archivos y carpetas del proyecto será similar a la siguiente:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Instalación de la biblioteca de autenticación

Busque la raíz del directorio del proyecto en un terminal e instale el paquete MSAL Node mediante NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Agregar detalles del registro de la aplicación

En el archivo *index.js* que ha creado anteriormente, agregue el código siguiente:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
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
    };
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
- `Enter_the_Client_secret`: sustituya este valor por el secreto de cliente que creó anteriormente. Para generar una nueva clave, use **Certificados y secretos** en la configuración del registro de aplicaciones en Azure Portal.

> [!WARNING]
> Cualquier secreto de texto no cifrado que haya en el código fuente supone un aumento del riesgo de seguridad. En este artículo se usa un secreto de cliente de texto no cifrado exclusivamente en aras de una mayor simplicidad. Use las [credenciales de certificado](active-directory-certificate-credentials.md), en lugar de secretos de cliente, en las aplicaciones cliente confidenciales, especialmente en las que desee implementar en producción.

## <a name="add-code-for-user-login"></a>Agregar código para el inicio de sesión del usuario

En el archivo *index.js* que ha creado anteriormente, agregue el código siguiente:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Prueba de inicio de sesión

Ha completado la creación de la aplicación y ya está listo para probar la funcionalidad de la aplicación.

1. Inicie el servidor web de Node.js mediante la ejecución del siguiente comando desde la raíz de la carpeta del proyecto:

```console
   node index.js
```

2. Abra una ventana del explorador y vaya a `http://localhost:3000`. Debería ver una pantalla de inicio de sesión:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Pantalla de inicio de sesión de Azure AD":::

3. Una vez que escriba las credenciales, debería ver una pantalla de consentimiento que le pide que apruebe los permisos para la aplicación.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Pantalla de consentimiento de Azure AD":::

## <a name="how-the-application-works"></a>Cómo funciona la aplicación

En este tutorial, ha inicializado un objeto de MSAL Node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) pasándole un objeto de configuración (*msalConfig*) que contiene los parámetros obtenidos del registro de la aplicación Azure AD en Azure Portal. La aplicación web que creó usa el [flujo de concesión de código de autorización de OAuth 2.0](./v2-oauth2-auth-code-flow.md) para iniciar sesión a los usuarios y obtener los tokens de identificador y de acceso.

## <a name="next-steps"></a>Pasos siguientes

Si quiere profundizar más en el desarrollo de aplicaciones web de Node.js y Express en la Plataforma de identidad de Microsoft, consulte nuestra serie de escenarios dividida en varias partes:

> [!div class="nextstepaction"]
> [Escenario: Aplicación web que permite iniciar sesión a los usuarios](scenario-web-app-sign-user-overview.md)

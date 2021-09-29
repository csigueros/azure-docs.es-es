---
title: Configuración de la autenticación en una aplicación de página única de ejemplo mediante Azure Active Directory B2C
description: En este artículo se analizar cómo usar Azure Active Directory B2C para que los usuarios se registren e inicien sesión en una aplicación de página única.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1ea2ae3644ae22fd06a5bae650fa3f5beb955aba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572518"
---
# <a name="configure-authentication-in-a-sample-single-page-application-by-using-azure-ad-b2c"></a>Configuración de la autenticación en una aplicación de página única mediante Azure AD B2C

En este artículo se usa un ejemplo de aplicación de página única (SPA) para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a sus aplicaciones SPA.

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación que se basa en OAuth 2.0. Puede usarlo para que un usuario inicie sesión en una aplicación de manera segura. Este ejemplo de aplicación de página única usa [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) y el flujo PKCE de OIDC. MSAL.js es una biblioteca proporcionada por Microsoft que simplifica la adición de compatibilidad con autenticación y autorización a las aplicaciones SPA.

### <a name="sign-in-flow"></a>Flujo de inicio de sesión

El flujo de inicio de sesión consta de los pasos siguientes:

1. El usuario va a la aplicación web y selecciona **Iniciar sesión**.
1. La aplicación inicia la solicitud de autenticación y redirige a los usuarios a Azure AD B2C.
1. Los usuarios [se registran o inician sesión](add-sign-up-and-sign-in-policy.md) y [restablecen la contraseña](add-password-reset-policy.md). También pueden iniciar sesión con una [cuenta de red social](add-identity-provider.md).
1. Una vez que los usuarios inician sesión, Azure AD B2C devuelve un código de autorización a la aplicación.
1. La aplicación de página única valida el token de identificador, lee las notificaciones y, a su vez, permite a los usuarios llamar a recursos o API protegidos.

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llamar a una API web, registre dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de la **aplicación web** permite que la aplicación inicie sesión con Azure AD B2C. Durante el registro, hay que especificar el *URI de redirección*. El URI de redirección es el punto de conexión al que Azure AD B2C redirige a los usuarios después de que se autentican en este servicio. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *id. de cliente*, que permite identificar de forma exclusiva la aplicación.

- El  registro de **API web** permite que la aplicación llame a una API web segura. El registro incluye los *ámbitos* de la API web. Los ámbitos ofrecen una manera de administrar permisos para los recursos protegidos, como la API web. A la aplicación web se le conceden permisos para los ámbitos de la API web. Cuando se selecciona un token de acceso, la aplicación especifica los permisos que desea en el parámetro de ámbito de la solicitud.  

En el siguiente diagrama se muestran los registros y la arquitectura de la aplicación:

![Diagrama de una aplicación web con registros y tokens de llamadas de API web.](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Llamada a una API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>Flujo de cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecute:

* [Visual Studio Code](https://code.visualstudio.com/) u otro editor de código.
* [Entorno de ejecución de Node.js](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>Paso 2: Registro de la SPA y la API

En este paso, creará la aplicación SPA y los registros de aplicación de API web, y especificará los ámbitos de la API web.

### <a name="step-21-register-the-web-api-application"></a>Paso 2.1: Registro de la aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-scopes"></a>Paso 2.2: Configuración de ámbitos

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-23-register-the-spa"></a>Paso 2.3: Registro de la SPA

Para crear la configuración de la aplicación SPA, haga lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación (por ejemplo, *MyApp*).
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Aplicación de página única (SPA)** y, a continuación, escriba `http://localhost:6420` en el cuadro de texto de dirección URL.
1. En **Permisos**, active la casilla **Conceder consentimiento de administrador para openid y permisos de acceso sin conexión**.
1. Seleccione **Registrar**.

### <a name="step-24-enable-the-implicit-grant-flow"></a>Paso 2.4: Habilitación del flujo de concesión implícita

A continuación, habilite el flujo de concesión implícita:

1. En **Administrar**, seleccione **Autenticación**.

1. Seleccione **Probar la nueva experiencia** (si se muestra).

1. En **Concesión implícita**, active la casilla **Tokens de id.** .

1. Seleccione **Guardar**.

   Anote el **Id. de aplicación (cliente)** para usarlo más adelante al configurar la aplicación web.

    ![Captura de pantalla de la página de información general de la aplicación web para registrar el identificador de la aplicación web.](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-25-grant-permissions"></a>Paso 2.5: Concesión de permisos

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>Paso 3: Obtención del código de ejemplo de SPA

En este ejemplo se muestra cómo una aplicación de página única puede usar Azure AD B2C para el registro e inicio de sesión de usuarios. A continuación, la aplicación adquiere un token de acceso y llama a una API web protegida. 

Para obtener el código de ejemplo de la aplicación SPA, puede realizar una de las acciones siguientes: 

* [Descargue un archivo ZIP](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip). 
* Clone el ejemplo de GitHub ejecutando el comando siguiente:

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

### <a name="step-31-update-the-spa-sample"></a>Paso 3.1: Actualización del ejemplo de SPA

Ahora que ha obtenido el ejemplo de SPA, actualice el código con sus valores de Azure AD B2C y la API web. En la carpeta `App` de la carpeta de ejemplo, abra los archivos de JavaScript que se muestran en la tabla siguiente y, luego, actualícelos con los valores correspondientes.  


|Archivo  |Clave  |Valor  |
|---------|---------|---------|
|authConfig.js|clientId| El id. de la aplicación SPA del [paso 2.3](#step-23-register-the-spa).|
|policies.js| nombres| Los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|
|policies.js|autoridades|[Nombre del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`). Luego, reemplácelo por los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow) (por ejemplo, `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`).|
|policies.js|authorityDomain|[Nombre del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`).|
|apiConfig.js|b2cScopes|Los ámbitos de API web que creó en el [paso 2.2](#step-22-configure-scopes) (por ejemplo, `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`).|
|apiConfig.js|webApi|Dirección URL de la API web, `http://localhost:5000/tasks`.|
| | | |

El código resultante tendrá un aspecto similar al del ejemplo siguiente:

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>Paso 4: Obtención del código de ejemplo de la API web

Ahora que ha registrado la API web y ha definido sus ámbitos, configure el código de la API web para que funcione con el inquilino de Azure AD B2C. 

Para obtener el código de ejemplo de la API web, haga una de las acciones siguientes:

* [Descargue un \*archivo .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip).

* Clone el proyecto de la API web de ejemplo de GitHub ejecutando el comando siguiente:

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
    ```

* También puede ir directamente al proyecto [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) en GitHub.

### <a name="step-41-update-the-web-api"></a>Paso 4.1: Actualización de la API web

1. Abra el archivo *config.json* en el editor de código.
1. Modifique los valores de las variables con el registro de aplicación creado anteriormente. Y actualice `policyName` con el flujo de usuario que creó como parte de los requisitos previos (por ejemplo, *b2c_1_susi*).
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="step-42-enable-cors"></a>Paso 4.2: Habilitación de CORS

Para permitir que la aplicación de página única llame a la API web de Node.js, debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://expressjs.com/en/resources/middleware/cors.html) en la API web. En una aplicación de producción, tenga cuidado con el dominio que realiza la solicitud. En este ejemplo, permite solicitudes de cualquier dominio.

Para habilitar CORS, utilice el siguiente middleware. En el código de ejemplo de la API web de Node.js que descargó, ya se ha agregado al archivo *index.js*.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>Paso 5: Ejecución de la SPA y la API web

Ya está preparado para probar el acceso de ámbito de la aplicación de página única a la API. Ejecute la API web de Node.js y la aplicación de página única de JavaScript de ejemplo en la máquina local. A continuación, inicie sesión en la aplicación de página única y seleccione el botón **Call API** (Llamar a la API) para iniciar una solicitud a la API protegida.

### <a name="run-the-nodejs-web-api"></a>Ejecución de la API web de Node.js

1. Abra una ventana de consola y cambie al directorio que contiene la API web de Node.js de ejemplo. Por ejemplo:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Ejecute los comandos siguientes:

    ```console
    npm install && npm update
    node index.js
    ```

    La ventana de la consola muestra el número de puerto en el que se hospeda la aplicación.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Ejecución de una aplicación de página única

1. Abra otra ventana de consola y cambie al directorio que contiene la aplicación de página única de JavaScript de ejemplo. Por ejemplo:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Ejecute los comandos siguientes:

    ```console
    npm install && npm update
    npm start
    ```

    La ventana de la consola muestra el número de puerto donde se hospeda la aplicación.

    ```console
    Listening on port 6420...
    ```

1. Para ver la aplicación, vaya a `http://localhost:6420` en el explorador.

    ![Captura de pantalla de la aplicación SPA de ejemplo que se muestra en la ventana del explorador.](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. Complete el proceso de inicio de sesión o registro. Una vez que inicia sesión correctamente, debería ver el mensaje "El usuario \<your username> inició sesión".
1. Seleccione el botón **Llamada a API**. La SPA envía el token de acceso en una solicitud a la API web protegida, que devuelve el nombre para mostrar del usuario que ha iniciado sesión:

    ![Captura de pantalla de la aplicación SPA en una ventana del explorador en la que se muestra el resultado JSON del nombre de usuario devuelto por la API.](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>Implementación de aplicación 

En una aplicación de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com/signin-oidc`. 

Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento. Las siguientes restricciones se aplican a los URI de redireccionamiento:

* La dirección URL de respuesta debe comenzar con el esquema `https`.
* La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los conceptos que se tratan en este artículo:
* [Más información sobre el código de ejemplo](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa).
* [Habilitación de la autenticación en su propia aplicación SPA](enable-authentication-spa-app.md).
* [Configuración de las opciones de autenticación en la aplicación SPA](enable-authentication-spa-app-options.md).
* [Habilitación de la autenticación en su propia API web](enable-authentication-web-api.md).

---
title: Configuración de la autenticación en una aplicación SPA de Angular de ejemplo usando Azure Active Directory B2C
description: Uso de Azure Active Directory B2C para registrar usuarios e iniciar su sesión en una aplicación SPA de Angular.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 61c771e1e415b88f89b073301952aac2b09aff1f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779876"
---
# <a name="configure-authentication-in-a-sample-angular-single-page-application-using-azure-active-directory-b2c"></a>Configuración de la autenticación en una aplicación de página única de Angular de ejemplo usando Azure Active Directory B2C

En este artículo se usa un ejemplo de aplicación de página única (SPA) de Angular para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a sus aplicaciones SPA.

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que un usuario inicie sesión de forma segura en una aplicación. En este ejemplo de Angular se usan [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) y [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser). MSAL es una biblioteca proporcionada por Microsoft que simplifica el proceso para que las aplicaciones SPA de Angular admitan autenticación y autorización.

### <a name="sign-in-flow"></a>Flujo de inicio de sesión

El flujo de inicio de sesión consta de los siguientes pasos:

1. El usuario accede a la aplicación y selecciona **Iniciar sesión**. 
1. La aplicación inicia la solicitud de autenticación y redirige al usuario a Azure AD B2C.
1. El usuario [se registra o inicia sesión en ](add-sign-up-and-sign-in-policy.md), [restablece la contraseña](add-password-reset-policy.md) o inicia sesión con una [cuenta de redes sociales](add-identity-provider.md).
1. Una vez que se inicia correctamente la sesión, Azure AD B2C devuelve un código de autorización a la aplicación. La aplicación realiza las siguientes acciones:
  1. Intercambia el código de autorización por un token de identificación, un token de acceso y un token de actualización.
  1. Lee las notificaciones del token de identificación.
  1. Almacena el token de acceso y el token de actualización en una caché en memoria para usarlos más adelante. El token de acceso permite al usuario llamar a recursos protegidos, como una API web. El token de actualización se usa para adquirir un nuevo token de acceso.

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llame a una API web, debe registrar dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de **aplicación de página única** le permite a su aplicación iniciar sesión con Azure AD B2C. Durante el registro de la aplicación, especificará el *URI de redirección*. El URI de redireccionamiento es el punto de conexión al que se redirige al usuario una vez que este se autentica con Azure AD B2C. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación. Por ejemplo, **Id. de aplicación: 1**.

- El registro de **API web** permite que su aplicación llame a una API web protegida. El registro expone los permisos (ámbitos) de la API web. El proceso de registro de la aplicación genera un *Id. de aplicación* que identifica de forma exclusiva la API web. Por ejemplo, **Id. de aplicación: 2**. Conceda a su aplicación (Id. de aplicación: 1) permisos para los ámbitos de la API web (Id. de aplicación: 2).  

En los diagramas siguientes se describen los registros de aplicaciones y la arquitectura de la aplicación.

![En el diagrama se describe una aplicación SPA con API web, registros y tokens.](./media/configure-authentication-sample-angular-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Llamada a una API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>Flujo de cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecute:

* [Visual Studio Code](https://code.visualstudio.com/) u otro editor de código
* [Node.js runtime](https://nodejs.org/en/download/) y [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* [Angular LCI](https://angular.io/cli)

## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-angular-spa-and-api"></a>Paso 2: Registro de la SPA de Angular y la API

En este paso, creará la aplicación SPA de Angular y los registros de aplicación de API web, y especificará los ámbitos de la API web.

### <a name="21-register-the-web-api-application"></a>2.1 Registro de la aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 Configuración de ámbitos

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-angular-app"></a>2.3 Registro de la aplicación de Angular

Siga estos pasos para crear el registro de la aplicación de Angular:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *MyApp*.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Aplicación de página única (SPA)** y, a continuación, escriba `http://localhost:4200` en el cuadro de texto de dirección URL.
1. En **Permisos**, active la casilla **Conceder consentimiento de administrador para openid y permisos de acceso sin conexión**.
1. Seleccione **Registrar**.
1. Anote el **Id. de aplicación (cliente)** ; lo usará en un paso posterior al configurar la aplicación web.
    ![Captura de pantalla en la que se muestra cómo obtener el Id. de aplicación de Angular](./media/configure-authentication-sample-angular-spa-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 Concesión de permisos

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-angular-sample-code"></a>Paso 3: Obtención del código de ejemplo de Angular

En este ejemplo se muestra cómo una aplicación de página única de Angular puede usar Azure AD B2C para el registro e inicio de sesión de usuarios. A continuación, la aplicación adquiere un token de acceso y llama a una API web protegida. Descargue el código de ejemplo siguiente:

  [Descargue un archivo ZIP](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/archive/refs/heads/main.zip) o clone el ejemplo del [repositorio de GitHub](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/):

  ```
  git clone https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial.git
  ```

### <a name="31-configure-the-angular-sample"></a>3.1 Configuración del ejemplo de Angular

Ahora que ha obtenido el ejemplo de aplicación SPA, actualice el código con sus valores de Azure AD B2C y la API web. Acceda a la carpeta de ejemplo y, en `src/app`, abra el archivo `auth-config.ts` y actualice con claves los valores correspondientes:  


|Sección  |Key  |Valor  |
|---------|---------|---------|
| b2cPolicies | nombres |Flujo de usuario o directiva personalizada que ha creado en el [paso 1](#step-1-configure-your-user-flow). |
| b2cPolicies | autoridades | Reemplace `your-tenant-name` por su [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `contoso.onmicrosoft.com`. Luego, reemplace el nombre de la directiva por el flujo de usuario o la directiva personalizada que ha creado en el [paso 1](#step-1-configure-your-user-flow). Por ejemplo, `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. |
| b2cPolicies | authorityDomain|Su [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `contoso.onmicrosoft.com`. |
| Configuración | clientId | Id. de la aplicación de Angular del [paso 2.3](#23-register-the-angular-app). |
| protectedResources| endpoint| Dirección URL de la API web, `http://localhost:5000/api/todolist`. |
| protectedResources| ámbitos| Ámbitos de API web que ha creado en el [paso 2.2](#22-configure-scopes). Por ejemplo, `b2cScopes: ["https://<your-tenant-namee>.onmicrosoft.com/tasks-api/tasks.read"]`. |

El código *src/app/auth-config.ts* resultante debería tener un aspecto similar al del siguiente ejemplo:

```typescript
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn.
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
    // More configuration here
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
```

## <a name="step-4-get-the-web-api-sample-code"></a>Paso 4: Obtención del código de ejemplo de la API web

Ahora que ha registrado la API web y ha definido sus ámbitos, configure el código de la API web para que funcione con el inquilino de Azure AD B2C. Descargue el código de ejemplo siguiente:

[Descargue un \*archivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) o clone el proyecto de API web de ejemplo de GitHub. También puede ir directamente al proyecto [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) en GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-configure-the-web-api"></a>4.1 Configuración de la API web

En la carpeta de ejemplo, abra el archivo *config.json*. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación de API web usa esta información para validar el token de acceso que la aplicación web pasa como token de portador. Actualice las siguientes propiedades de la configuración de la aplicación:

|Sección  |Key  |Valor  |
|---------|---------|---------|
|credentials|tenantName| La primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `contoso`.|
|credentials|clientID| Id. de aplicación de API web del [paso 2.1](#21-register-the-web-api-application). En el [diagrama anterior](#app-registration-overview), es la aplicación con *Id. de aplicación: 2*.|
|credentials| issuer| (Opcional) Valor de notificación `iss` del emisor del token. Azure AD B2C devuelve de manera predeterminada el token en el siguiente formato: `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`. Reemplace `<your-tenant-name>` por la primera parte del [nombre del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Reemplace `<your-tenant-ID>` por el [Id. de inquilino de Azure AD B2C](tenant-management.md#get-your-tenant-id). |
|directivas|policyName|Flujo de usuario o directiva personalizada que ha creado en el [paso 1](#step-1-configure-your-user-flow). Si la aplicación usa varios flujos de usuario o directivas personalizadas, especifique solo uno. Por ejemplo, el flujo de usuario de registro o de inicio de sesión.|
| resource| scope | Ámbitos del registro de la aplicación de API web del paso [2.5])(#25-grant-permissions). |

El archivo de configuración final debería tener un aspecto parecido al del siguiente JSON:

```json
{
    "credentials": {
        "tenantName": "<your-tenant-namee>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    // More settings here
} 
```

## <a name="step-5-run-the-angular-spa-and-web-api"></a>Paso 5: Ejecución de la SPA de Angular y la API web

Ya tiene todo listo para probar el acceso con ámbito de Angular a la API. En este paso, ejecute la API web y la aplicación de Angular de ejemplo en su equipo local. A continuación, inicie sesión en la aplicación de Angular y seleccione el botón **TodoList** (Lista de tareas) para iniciar una solicitud a la API protegida.

### <a name="run-the-web-api"></a>Ejecución de la API web

1. Abra una ventana de consola y cambie al directorio que contiene la API web de ejemplo. Por ejemplo:

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

### <a name="run-the-angular-application"></a>Ejecución de la aplicación de Angular

1. Abra otra ventana de consola y cambie al directorio que contiene la aplicación de Angular. Por ejemplo:

    ```console
    cd ms-identity-javascript-angular-tutorial-main/3-Authorization-II/2-call-api-b2c/SPA
    ```

1. Ejecute los comandos siguientes:

    ```console
    npm install && npm update
    npm start
    ```

    La ventana de la consola muestra el número de puerto donde se hospeda la aplicación.

    ```console
    Listening on port 4200...
    ```

1. Vaya a `http://localhost:4200` en el explorador para ver la aplicación.
1. Seleccione **Login** (Iniciar sesión).

    ![Captura de pantalla en la que se muestra la aplicación de Angular de ejemplo con el vínculo de inicio de sesión](./media/configure-authentication-sample-angular-spa-app/sample-app-sign-in.png)

1. Complete el proceso de inicio de sesión o registro.
1. Tras iniciar sesión correctamente, debería aparecer su perfil. En el menú, seleccione **ToDoList**.

    ![Captura de pantalla en la que se muestra la aplicación de Angular de ejemplo con el perfil de usuario y la llamada a la lista de tareas](./media/configure-authentication-sample-angular-spa-app/sample-app-result.png)

1. **Agregue** nuevos elementos a la lista, o bien **elimine** o **edite** elementos.

    ![Captura de pantalla en la que se muestra la llamada a la lista de tareas de la aplicación de Angular de ejemplo](./media/configure-authentication-sample-angular-spa-app/sample-app-calls-web-api.png)

## <a name="deploy-your-application"></a>Implementación de aplicación 

En una aplicación de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com`. 

Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento. Las siguientes restricciones se aplican a los URI de redireccionamiento:

* La dirección URL de respuesta debe comenzar con el esquema `https`.
* La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. 

## <a name="next-steps"></a>Pasos siguientes

* Más información [sobre el ejemplo de código](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)
* [Habilitación de la autenticación en su propia aplicación de Angular](enable-authentication-angular-spa-app.md)
* Configuración de [opciones de autenticación en su aplicación de Angular](enable-authentication-angular-spa-app-options.md)
* [Habilitación de la autenticación en su propia API web](enable-authentication-web-api.md)

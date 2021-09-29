---
title: Configuración de la autenticación en una aplicación web de Python de ejemplo mediante Azure Active Directory B2C
description: En este artículo, se explica cómo usar Azure Active Directory B2C para que los usuarios se registren e inicien sesión en una aplicación web de Python.
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
ms.openlocfilehash: 1b315343b524dc165fb6464728a5a9700d4e22c8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572642"
---
# <a name="configure-authentication-in-a-sample-python-web-app-by-using-azure-ad-b2c"></a>Configuración de la autenticación en una aplicación web de Python de ejemplo mediante Azure AD B2C

En este artículo, se usa un ejemplo de aplicación web de Python para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones web.

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación que se basa en OAuth 2.0. Puede usar OIDC para que los usuarios inicien sesión de forma segura en una aplicación. Este ejemplo de aplicación web usa la [biblioteca de autenticación de Microsoft (MSAL) para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python). MSAL para Python simplifica la adición de compatibilidad con la autenticación y la autorización a aplicaciones web de Python. 

El flujo de inicio de sesión consta de los pasos siguientes:

1. El usuario va a la aplicación web y selecciona **Iniciar sesión**. 
1. La aplicación inicia la solicitud de autenticación y redirige a los usuarios a Azure AD B2C.
1. El [registro o inicio de sesión](add-sign-up-and-sign-in-policy.md) de los usuarios, el [restablecimiento de la contraseña](add-password-reset-policy.md) o el inicio de sesión con una [cuenta de red social](add-identity-provider.md).
1. Una vez que los usuarios hayan iniciado sesión correctamente, Azure AD B2C devuelve un token de identificador a la aplicación.
1. La aplicación intercambia el código de autorización con un token de identificador, valida el token de identificador, lee las notificaciones y, a continuación, devuelve una página segura a los usuarios.


### <a name="sign-out"></a>Cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecute: 

* [Visual Studio Code](https://code.visualstudio.com/) u otro editor de código
* [Python](https://nodejs.org/en/download/) 2.7+ o 3+ 

## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>Paso 2: Registro de una aplicación web

Para permitir que la aplicación inicie sesión con Azure AD B2C, registre la aplicación en el directorio de Azure AD B2C. El registro de la aplicación establece una relación de confianza entre la aplicación y Azure AD B2C.  

Durante el registro de la aplicación, especificará el *URI de redirección*. El identificador URI de redirección es el punto de conexión al que Azure AD B2C redirige a los usuarios después de que estos se autentican con Azure AD B2C. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación. Una vez registrada la aplicación, Azure AD B2C usará el identificador de aplicación y el identificador URI de redirección para crear solicitudes de autenticación. 

### <a name="step-21-register-the-app"></a>Paso 2.1: Registro de la aplicación

Para crear el registro de la aplicación web, haga lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. En **Nombre**, escriba un nombre para la aplicación (por ejemplo, *webapp1*).
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Web** y luego, en el cuadro Dirección URL, escriba `http://localhost:5000/getAToken`.
1. En **Permisos**, active la casilla **Conceder consentimiento de administrador para openid y permisos de acceso sin conexión**.
1. Seleccione **Registrar**.
1. Seleccione **Información general**.
1. Anote el **Id. de aplicación (cliente)** para usarlo más adelante al configurar la aplicación web.

    ![Captura de pantalla de la página de información general de la aplicación web para registrar el identificador de la aplicación web.](./media/configure-authentication-sample-python-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-create-a-web-app-client-secret"></a>Paso 2.2: Creación de un secreto de cliente de la aplicación web

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]

## <a name="step-3-get-the-web-app-sample"></a>Paso 3: Obtención de la aplicación web de ejemplo

[Descargue el archivo zip](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub. 

```bash
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

Extraiga el archivo de ejemplo en una carpeta en la que la longitud total de caracteres de la ruta de acceso sea igual o inferior a 260.

## <a name="step-4-configure-the-sample-web-app"></a>Paso 4: Configuración de la aplicación web de ejemplo

En el directorio raíz del proyecto, haga lo siguiente:

1. Cambie el nombre del archivo *app_config.py* a *app_config.py.OLD*.
1. Cambie el nombre del archivo *app_config_b2c.py* a *app_config.py*. 

Abra el archivo *app_config.py*. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. Actualice las siguientes propiedades de la configuración de la aplicación: 

|Clave  |Value  |
|---------|---------|
|`b2c_tenant`| Primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `contoso`).|
|`CLIENT_ID`| Identificador de aplicación de la API web del [paso 2.1](#step-21-register-the-app).|
|`CLIENT_SECRET`| Secreto de cliente que creó en el [paso 2.2](#step-22-create-a-web-app-client-secret). Para ayudar a aumentar la seguridad, considere la posibilidad de almacenarlo en una variable de entorno, como se recomienda en los comentarios. |
|`*_user_flow`|Los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|
| | |

El archivo de configuración final debería tener un aspecto parecido al del siguiente código de Python:

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.
```

> [!IMPORTANT]
> Como se indica en los comentarios del fragmento de código, se recomienda *no almacenar secretos en texto no cifrado* en el código de la aplicación. En el ejemplo de código se usa la variable codificada de forma rígida *únicamente por comodidad*. Considere la posibilidad de usar una variable de entorno o un almacén de secretos, como Azure Key Vault.


## <a name="step-5-run-the-sample-web-app"></a>Paso 5: Ejecución de la aplicación web de ejemplo

1. En la consola o el terminal, cambie al directorio que contiene el ejemplo. Por ejemplo:

    ```console
    cd ms-identity-python-webapp
    ```
1. Ejecute los siguientes comandos para instalar los paquetes necesarios desde PyPi y ejecutar la aplicación web en la máquina local:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    La ventana de consola muestra el número de puerto de la aplicación que se ejecuta localmente:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on `http://localhost:5000/` (Press CTRL+C to quit)
    ```

 
1. Vaya a `http://localhost:5000` para ver la aplicación web que se ejecuta en la máquina local. 

1. Seleccione **Iniciar sesión**.

    ![Captura de pantalla que muestra el inicio de sesión con Azure AD B2C.](./media/configure-authentication-sample-python-web-app/web-app-sign-in.png)


1. Complete el proceso de inicio de sesión o registro.

1. Después de una autenticación correcta, verá el nombre para mostrar, como se muestra aquí: 

    ![Captura de pantalla que muestra la notificación de nombre para mostrar del token de la aplicación web.](./media/configure-authentication-sample-python-web-app/web-app-token-claims.png)


## <a name="step-6-call-to-a-web-api"></a>Paso 6: Llamada a una API web

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llame a una API web, debe registrar dos aplicaciones en el directorio de Azure AD B2C.  

- Registro de la **aplicación web** (Python) que ya creó en el [paso 2](#step-2-register-a-web-application). Este registro de aplicación permite que la aplicación inicie sesión con Azure AD B2C.  El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación. Por ejemplo, **Id. de aplicación: 1**.

- El registro de **API web** permite que su aplicación llame a una API web protegida. El registro expone los permisos (ámbitos) de la API web. El proceso de registro de la aplicación genera un *identificador de aplicación* que identifica de forma única la API web (por ejemplo, *id. de aplicación: 2*). Conceda a su aplicación (Id. de aplicación: 1) permisos para los ámbitos de la API web (Id. de aplicación: 2).  

Los registros y la arquitectura de la aplicación se describen en los diagramas siguientes:

![En el diagrama se describe una aplicación web con API web, registros y tokens.](./media/configure-authentication-sample-python-web-app/web-app-with-api-architecture.png) 

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="step-61-register-the-web-api-app"></a>Paso 6.1: Registro de la aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-62-configure-scopes"></a>Paso 6.2: Configuración de ámbitos

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-63-grant-the-web-app-permissions"></a>Paso 6.3: Concesión de permisos a la aplicación web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

### <a name="step-64-configure-your-web-api"></a>Paso 6.4: Configuración de la API web

En este ejemplo, se adquiere un token de acceso con los ámbitos pertinentes que la aplicación web puede usar para una API web. Para llamar a una API web desde el código, use una API web existente o cree una nueva. Para más información, consulte [Habilitación de la autenticación en su propia API web mediante Azure AD B2C](enable-authentication-web-api.md).

### <a name="step-65-configure-the-sample-app-with-the-web-api"></a>Paso 6.5: Configuración de la aplicación de ejemplo con la API web

Abra el archivo *app_config.py*. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. Actualice las siguientes propiedades de la configuración de la aplicación: 

|Clave  |Value  |
|---------|---------|
|`ENDPOINT`| Identificador URI de la API web (por ejemplo, `https://localhost:44332/hello`).|
|`SCOPE`| [Ámbitos](#step-62-configure-scopes) de la API web que creó.|
| | |

El archivo de configuración final debería tener un aspecto parecido al del siguiente código de Python:

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.

### More code here

# This is the API resource endpoint
ENDPOINT = 'https://localhost:44332' 


SCOPE = ["https://contoso.onmicrosoft.com/api/demo.read", "https://contoso.onmicrosoft.com/api/demo.write"] 
```

### <a name="step-66-run-the-sample-app"></a>Paso 6.6: Ejecución de la aplicación de ejemplo

1. En la consola o el terminal, cambie al directorio que contiene el ejemplo. 
1. Detenga la aplicación. y, a continuación, vuelva a ejecutarla.
1. Seleccione **Call Microsoft Graph API** (Llamar a Microsoft Graph API).

    ![Captura de pantalla en la que se muestra cómo llamar a una API web.](./media/configure-authentication-sample-python-web-app/call-web-api.png)

## <a name="step-7-deploy-your-application"></a>Paso 7: Implementación de la aplicación 

En una aplicación de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com/getAToken`. 

Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento. Las siguientes restricciones se aplican a los URI de redireccionamiento:

* La dirección URL de respuesta debe comenzar con el esquema `https`.
* La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. 

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre la [Habilitación de las opciones de autenticación en una aplicación web de Python mediante Azure AD B2C](enable-authentication-python-web-app-options.md).

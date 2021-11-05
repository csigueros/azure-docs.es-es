---
title: Configuración de la autenticación en una aplicación de Android de ejemplo mediante Azure Active Directory B2C
description: En este artículo, se explica cómo usar Azure Active Directory B2C para que los usuarios se registren e inicien sesión en una aplicación de Android.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 699e548809b123589466062f1e9600edee9e7b78
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007940"
---
# <a name="configure-authentication-in-a-sample-android-app-by-using-azure-ad-b2c"></a>Configuración de la autenticación en una aplicación Android de ejemplo mediante Azure AD B2C

En este artículo se usa una aplicación de Android de ejemplo (Kotlin y Java) para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones móviles.

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación que se basa en OAuth 2.0. Puede usar OIDC para que los usuarios inicien sesión de forma segura en una aplicación. En este ejemplo de aplicación móvil, se usa la [Biblioteca de autenticación de Microsoft (MSAL)](../active-directory/develop/msal-overview.md) con el flujo PKCE del código de autorización de OIDC. MSAL es una biblioteca proporcionada por Microsoft que simplifica la adición de compatibilidad con la autenticación y la autorización a las aplicaciones móviles. 

El flujo de inicio de sesión consta de los pasos siguientes:

1. Los usuarios abren la aplicación y seleccionan **Iniciar sesión**.
1. La aplicación abre el explorador del sistema del dispositivo móvil e inicia una solicitud de autenticación en Azure AD B2C.
1. El [registro o inicio de sesión](add-sign-up-and-sign-in-policy.md) de los usuarios, el [restablecimiento de la contraseña](add-password-reset-policy.md) o el inicio de sesión con una [cuenta de red social](add-identity-provider.md).
1. Una vez que el usuario inicia sesión correctamente, Azure AD B2C devuelve un código de autorización a la aplicación.
1. La aplicación realiza las siguientes acciones:
    1. Intercambia el código de autorización por un token de identificador, un token de acceso y un token de actualización.
    1. Lee las notificaciones del token de identificación.
    1. Almacena los tokens en una caché en memoria para su uso posterior.

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llamar a una API web, registre dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de la **aplicación móvil** permite que la aplicación inicie sesión con Azure AD B2C. Durante el registro de la aplicación, especifique el *identificador URI de redirección*. El identificador URI de redirección es el punto de conexión al que Azure AD B2C redirige a los usuarios después de que estos se hayan autenticado con Azure AD B2C. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *id. de cliente*, que identifica de forma única la aplicación móvil (por ejemplo, *id. de aplicación: 1*).

- El registro de **API web** permite que su aplicación llame a una API web protegida. El registro expone los permisos de la API web (ámbitos). El proceso de registro de la aplicación genera un *identificador de aplicación*, que identifica de forma exclusiva la API web (por ejemplo, *id. de aplicación: 2*). Conceda a la aplicación móvil (Id. de aplicación: 1) permisos para los ámbitos de la API web (Id. de aplicación: 2). 


El registro de aplicaciones y la arquitectura de la aplicación se muestran en los diagramas siguientes:

![Diagrama de una aplicación móvil con registros y tokens de llamadas de API web.](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Llamada a una API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Flujo de cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecute: 


- [Kit de desarrollo de Java (JDK) 8 o posterior](https://openjdk.java.net/)
- [Apache Maven](https://maven.apache.org/)
- [Nivel de API de Android 16 o posterior](https://developer.android.com/studio/releases/platforms)
- [Android Studio](https://developer.android.com/studio) u otro editor de código


## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Paso 2: Registro de aplicaciones móviles

Cree el registro de la aplicación móvil y de la aplicación de API web, y especifique los ámbitos de la API web.

### <a name="step-21-register-the-web-api-app"></a>Paso 2.1: Registrar la aplicación de la API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Paso 2.2: Configurar los ámbitos de la aplicación de la API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-mobile-app"></a>Paso 2.3: Registro de la aplicación móvil

Para crear el registro de la aplicación móvil, haga lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. En **Nombre**, escriba un nombre para la aplicación (por ejemplo, *android-app1*).
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Cliente público/nativo (móvil y escritorio)** y, a continuación, en el cuadro de la dirección URL, escriba uno de los siguientes identificadores URI:
    - Para el ejemplo de Kotlin: `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Para el ejemplo de Java: `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. Seleccione **Registrar**.
1. Una vez completado el registro de la aplicación, seleccione **Información general.**
1. Anote el **Id. de aplicación (cliente)** para usarlo más adelante al configurar la aplicación móvil.

    ![Captura de pantalla en la que se resalta el identificador de la aplicación de Android](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-24-grant-the-mobile-app-permissions-for-the-web-api"></a>Paso 2.4: Concesión de permisos a la aplicación móvil para la API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>Paso 3: Obtención de la aplicación móvil de Android de ejemplo

Realice cualquiera de las siguientes acciones:

- Descargue cualquiera de estos ejemplos: 
   - [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip)
   - [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip) 

   Extraiga el archivo ZIP de ejemplo en la carpeta de trabajo.

- Clone la aplicación móvil de Android de ejemplo desde GitHub. 

    #### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
    ```

    #### <a name="java"></a>[Java](#tab/java)

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-android-java
    ```

    --- 


## <a name="step-4-configure-the-sample-web-api"></a>Paso 4: Configuración de la API web de ejemplo

En este ejemplo, se adquiere un token de acceso con los ámbitos pertinentes que la aplicación móvil puede usar para una API web. Para llamar a una API web desde código, haga lo siguiente:

1. Use una API web existente o cree una nueva. Para más información, consulte [Habilitación de la autenticación en su propia API web mediante Azure AD B2C](enable-authentication-web-api.md).
1. Cambie el código de ejemplo para [llamar a una API web](enable-authentication-android-app.md#step-6-call-a-web-api).

## <a name="step-5-configure-the-sample-mobile-app"></a>Paso 5: Configuración de la aplicación móvil de ejemplo

Abra el proyecto de ejemplo con Android Studio u otro editor de código y, a continuación, abra el archivo */app/src/main/res/raw/auth_config_b2c.json*. 

El archivo de configuración *auth_config_b2c.json* contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación móvil usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión de los usuarios y obtener tokens y validarlos. 

Actualice las siguientes propiedades de la configuración de la aplicación:

|Clave  |Valor  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | El identificador de la aplicación móvil del [paso 2.3](#step-23-register-the-mobile-app). | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | El URI de redirección de la aplicación móvil del [paso 2.3](#step-23-register-the-mobile-app). | 
| [autoridades](../active-directory/develop/msal-client-application-configuration.md#authority)| La autoridad es una dirección URL que indica un directorio desde el que MSAL puede solicitar tokens. Use el siguiente formato: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. Reemplace `<your-tenant-name>` por su [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Luego, reemplace `<your-sign-in-sign-up-policy>` por los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow). |
| | | 


Abra la clase `B2CConfiguration` y actualice los siguientes miembros de la clase:

|Clave  |Valor  |
|---------|---------|
| Directivas| Lista de los flujos de usuario o directivas personalizadas que creó en el [paso 1](#step-1-configure-your-user-flow).|
| azureAdB2CHostName| Primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `https://contoso.b2clogin.com`).|
| tenantName| [Nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`).|
| ámbitos| Ámbitos de la API web que creó en el [paso 2.4](#step-24-grant-the-mobile-app-permissions-for-the-web-api).|
| | |


## <a name="step-6-run-and-test-the-mobile-app"></a>Paso 6: Ejecución y prueba de la aplicación móvil

1. Compile y ejecute el proyecto.
1. En la parte superior izquierda, seleccione el icono de hamburguesa (también denominado icono de menú contraído), como se muestra aquí:
    
    ![Captura de pantalla que resalta el icono de hamburguesa o menú contraído.](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. En el panel izquierdo, seleccione **B2C Mode** (Modo B2C).

    ![Captura de pantalla que resalta el comando "Modo B2C" en el panel izquierdo.](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. Seleccione **Ejecutar flujo de usuario**.

    ![Captura de pantalla en la que se resalta el botón "Ejecutar flujo de usuario".](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Regístrese o inicie sesión con su cuenta local o social de Azure AD B2C.

1. Después de una autenticación correcta, verá el nombre para mostrar en el panel **B2C mode** (Modo B2C).

    ![Captura de pantalla en la que se muestra una autenticación correcta, con el usuario que ha iniciado sesión y la directiva.](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo:
* [Habilitación de la autenticación en una aplicación de Android propia mediante Azure AD B2C](enable-authentication-android-app.md)
* [Configuración de las opciones de autenticación en una aplicación de Android mediante Azure AD B2C](enable-authentication-android-app-options.md)
* [Habilitación de la autenticación en su propia API web](enable-authentication-web-api.md)

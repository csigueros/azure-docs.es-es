---
title: Configuración de la autenticación en una aplicación de Android de ejemplo mediante Azure Active Directory B2C
description: Uso de Azure Active Directory B2C para iniciar la sesión de los usuarios en una aplicación de Android y registrarlos.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b77ef28c5161c92bf8eb6c22cf62d5af41698441
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "121779835"
---
# <a name="configure-authentication-in-a-sample-android-application-using-azure-active-directory-b2c"></a>Configuración de la autenticación en una aplicación de Android de ejemplo mediante Azure Active Directory B2C

En este artículo se usa una aplicación de Android de ejemplo (Kotlin y Java) para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones móviles.

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que los usuarios inicien sesión de forma segura en una aplicación. Esta aplicación móvil de ejemplo usa la biblioteca [MSAL](../active-directory/develop/msal-overview.md) con el flujo PKCE del código de autorización de OpenId Connect. MSAL es una biblioteca proporcionada por Microsoft que simplifica la adición de compatibilidad con autenticación y autorización a las aplicaciones SPA. 

El flujo de inicio de sesión consta de los siguientes pasos:

1. El usuario abre la aplicación y selecciona **iniciar sesión**.
1. La aplicación abre el explorador del sistema del dispositivo móvil e inicia una solicitud de autenticación en Azure AD B2C.
1. El usuario [se suscribe o inicia sesión](add-sign-up-and-sign-in-policy.md), [restablece la contraseña](add-password-reset-policy.md) o inicia sesión con una [cuenta de redes sociales](add-identity-provider.md).
1. Tras iniciar sesión correctamente, Azure AD B2C devuelve un código de autorización a la aplicación.
1. La aplicación realiza las siguientes acciones:
    1. Intercambia el código de autorización por un token de identificador, un token de acceso y un token de actualización.
    1. Lee las notificaciones del token de identificador.
    1. Almacena los tokens en una caché en memoria para su uso posterior.

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llamar a una API web, registre dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de la **aplicación móvil** permite que la aplicación inicie sesión con Azure AD B2C. Durante el registro de la aplicación, especifique el *URI de redirección*. El URI de redirección es el punto de conexión al que Azure AD B2C redirige al usuario después de que este se autentica en este servicio. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación móvil. Por ejemplo, **Id. de aplicación: 1**.

- El registro de **API web** permite que la aplicación llame a una API protegida. El registro expone los permisos de API web (ámbitos). El proceso de registro de la aplicación genera un *identificador de aplicación*, que identifica de forma exclusiva la API web.  Por ejemplo, **Id. de aplicación: 2**. Conceda a la aplicación móvil (Id. de aplicación: 1) permisos para los ámbitos de la API web (Id. de aplicación: 2). 


En los diagramas siguientes se describen los registros de aplicaciones y la arquitectura de la aplicación.

![Aplicación móvil con registros y tokens de llamadas de API web](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Llamada a una API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecute: 


- [Kit de desarrollo de Java (JDK)](https://openjdk.java.net/) 8 o superior.
- [Apache Maven](https://maven.apache.org/)
- [Nivel de API de Android 16](https://developer.android.com/studio/releases/platforms) o superior.
- [Android Studio](https://developer.android.com/studio) u otro editor de código.


## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Paso 2: Registro de aplicaciones móviles

En este paso, creará la aplicación móvil y el registro de aplicación de API web, y especificará los ámbitos de la API web.

### <a name="21-register-the-web-api-app"></a>2.1 Registro de la aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Configuración de los ámbitos de aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 Registro de la aplicación móvil

Siga estos pasos para crear el registro de la aplicación móvil:

1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *android-app1*.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Public client/native (mobile & desktop)** (Cliente público/nativo [móvil y escritorio]) y, luego, en el cuadro de texto de dirección URL; escriba uno de los siguientes URI:
    - Para el ejemplo de Kotlin: `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Para el ejemplo de Java: `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. Seleccione **Registrar**.
1. Una vez completado el registro de la aplicación, seleccione **Información general.**
1. Anote el valor de **Id. de aplicación (cliente)** ; lo usará en un paso posterior al configurar la aplicación móvil.

    ![Obtención del identificador de la aplicación móvil](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 Concesión de permisos de aplicación móvil para la API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>Paso 3: Obtención de la aplicación móvil de Android de ejemplo

Descargue uno de los siguientes ejemplos: [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip) o [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip). Extraiga el archivo ZIP de ejemplo en la carpeta de trabajo.

O bien, clone la aplicación móvil de Android de ejemplo desde GitHub. 

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

En este ejemplo se adquiere un token de acceso con los ámbitos pertinentes que la aplicación móvil puede usar para una API web. Para llamar a una API web mediante código, siga estos pasos:

1. Use una API web existente o cree una nueva. Para más información, consulte [Habilitación de la autenticación en su propia API web mediante Azure AD B2C](enable-authentication-web-api.md).
1. Cambie el código de ejemplo para [llamar a una API web](enable-authentication-android-app.md#call-a-web-api).

## <a name="step-5-configure-the-sample-mobile-app"></a>Paso 5: Configuración de la aplicación móvil de ejemplo

Abra el proyecto de ejemplo con Android Studio u otro editor de código.  Luego, abra el archivo `/app/src/main/res/raw/auth_config_b2c.json`. 

El archivo de configuración *auth_config_b2c.json* contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación móvil usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión del usuario y adquirir tokens y validarlos. 

Actualice las siguientes propiedades de la configuración de la aplicación:

|Clave  |Valor  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | El identificador de la aplicación móvil del [paso 2.3](#23-register-the-mobile-app). | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | El URI de redirección de la aplicación móvil del [paso 2.3](#23-register-the-mobile-app). | 
| [autoridades](../active-directory/develop/msal-client-application-configuration.md#authority)| La autoridad es una dirección URL que indica un directorio desde el que MSAL puede solicitar tokens. Use el siguiente formato: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. Reemplace `<your-tenant-name>` por el [nombre del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Luego, reemplace `<your-sign-in-sign-up-policy>` por los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow). | 


Abra la clase `B2CConfiguration` y actualice los siguientes miembros de la clase:

|Clave  |Valor  |
|---------|---------|
| Directivas| Lista de los flujos de usuario o las directivas personalizadas que creó en el [paso 1](#step-1-configure-your-user-flow).|
| azureAdB2CHostName| La primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `https://contoso.b2clogin.com`.|
| tenantName| El [nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C. Por ejemplo, `contoso.onmicrosoft.com`.|
| ámbitos| Los ámbitos de API web que creó en el [paso 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api).|


## <a name="step-6-run-and-test-the-mobile-app"></a>Paso 6: Ejecución y prueba de la aplicación móvil

1. Compile y ejecute el proyecto.
1. Seleccione el icono de hamburguesa.
    
    ![Captura de pantalla que muestra cómo seleccionar el icono de hamburguesa.](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. Seleccione **B2C mode** (Modo B2C).

    ![Captura de pantalla que muestra cómo seleccionar el modo B2C.](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. Seleccione **EJECUTAR FLUJO DE USUARIO**.

    ![Captura de pantalla que muestra cómo comenzar el flujo de inicio de sesión.](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Regístrese o inicie sesión con su cuenta local o social de Azure AD B2C.

1. Después de la autenticación correcta, verá el nombre para mostrar en la barra de navegación.

    ![Token de acceso o identificador de usuario de Azure AD B2C.](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [habilitar la autenticación en su propia aplicación de Android](enable-authentication-android-app.md).
* [Configuración de opciones de autenticación en una aplicación de Android](enable-authentication-android-app-options.md)
* [Habilitación de la autenticación en su propia API web](enable-authentication-web-api.md)

---
title: Configuración de la autenticación en una aplicación de escritorio de WPF de ejemplo mediante Azure Active Directory B2C
description: Uso de Azure Active Directory B2C para iniciar la sesión de los usuarios en una aplicación de escritorio de WPF.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 9e61065e209a22d3ded08cf205350a737f7fe94f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770175"
---
# <a name="configure-authentication-in-a-sample-wpf-desktop-application-using-azure-active-directory-b2c"></a>Configuración de la autenticación en una aplicación de escritorio de WPF de ejemplo mediante Azure Active Directory B2C

En este artículo se usa una aplicación de [escritorio de WPF](/visualstudio/designers/getting-started-with-wpf) de ejemplo para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones de escritorio.

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que los usuarios inicien sesión de forma segura en una aplicación. Esta aplicación de escritorio de ejemplo usa la biblioteca [MSAL](../active-directory/develop/msal-overview.md) con el flujo PKCE del código de autorización de OpenId Connect. MSAL es una biblioteca proporcionada por Microsoft que simplifica la adición de compatibilidad con autenticación y autorización a las aplicaciones de escritorio. 

El flujo de inicio de sesión consta de los siguientes pasos:

1. El usuario abre la aplicación y selecciona **iniciar sesión**.
1. La aplicación abre el explorador del sistema del dispositivo de escritorio e inicia una solicitud de autenticación en Azure AD B2C.
1. El usuario [se suscribe o inicia sesión](add-sign-up-and-sign-in-policy.md), [restablece la contraseña](add-password-reset-policy.md) o inicia sesión con una [cuenta de redes sociales](add-identity-provider.md).
1. Tras iniciar sesión correctamente, Azure AD B2C devuelve un código de autorización a la aplicación.
1. La aplicación realiza las acciones siguientes:
    1. Intercambia el código de autorización por un token de identificador, un token de acceso y un token de actualización.
    1. Lee las notificaciones del token de identificador.
    1. Almacena los tokens en una caché en memoria para su uso posterior.

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llamar a una API web, registre dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de la **aplicación de escritorio** permite que la aplicación inicie sesión con Azure AD B2C. Durante el registro de la aplicación, especifique el *URI de redirección*. El URI de redirección es el punto de conexión al que Azure AD B2C redirige al usuario después de que este se autentica con Azure AD B2C. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación de escritorio. Por ejemplo, **Id. de aplicación: 1**.

- El registro de **API web** permite que la aplicación llame a una API protegida. El registro expone los permisos de API web (ámbitos). El proceso de registro de la aplicación genera un *identificador de aplicación*, que identifica de forma exclusiva la API web.  Por ejemplo, **Id. de aplicación: 2**. Conceda a la aplicación de escritorio (Id. de aplicación: 1) permisos para los ámbitos de la API web (Id. de aplicación: 2). 

En los diagramas siguientes se describen los registros de aplicaciones y la arquitectura de la aplicación.

![En el diagrama se describe una aplicación de escritorio con API web, registros y tokens.](./media/configure-authentication-sample-wpf-desktop-app/desktop-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Llamada a una API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecuta [Visual Studio 2019](https://www.visualstudio.com/downloads/) con **Desarrollo de escritorio de .NET**.

## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-applications"></a>Paso 2: Registro de la aplicación

En este paso, creará la aplicación de escritorio y el registro de aplicación de API web, y especificará los ámbitos de la API web.

### <a name="21-register-the-web-api-app"></a>2.1 Registro de la aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Configuración de los ámbitos de aplicación de API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-desktop-app"></a>2.3 Registro de la aplicación de escritorio

Siga estos pasos para crear el registro de la aplicación de escritorio:

1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *desktop-app1*.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Cliente público/nativo (escritorio y escritorio)** y luego escriba: `https://your-tenant-name.b2clogin.com/oauth2/nativeclient`. Reemplace `your-tenant-name` por el [nombre del inquilino](tenant-management.md#get-your-tenant-name). Para ver más opciones, consulte [Configuración del URI de redireccionamiento](enable-authentication-wpf-desktop-app-options.md#configure-redirect-uri).
1. Seleccione **Registrar**.
1. Una vez completado el registro de la aplicación, seleccione **Información general.**
1. Anote el valor de **Id. de aplicación (cliente)** ; lo usará en un paso posterior al configurar la aplicación de escritorio.
    ![Captura de pantalla que muestra cómo obtener el identificador de aplicación de escritorio.](./media/configure-authentication-sample-wpf-desktop-app/get-azure-ad-b2c-app-id.png)  

### <a name="24-grant-the-desktop-app-permissions-for-the-web-api"></a>2.4 Concesión de permisos de aplicación de escritorio para la API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>Paso 3: Configuración de la API web de ejemplo

En este ejemplo se adquiere un token de acceso con los ámbitos pertinentes que la aplicación de escritorio puede usar para una API web.  Para llamar a una API web mediante código, siga estos pasos:

1. Use una API web existente o cree una nueva. Para más información, consulte [Habilitación de la autenticación en su propia API web mediante Azure AD B2C](enable-authentication-web-api.md).
1. Después de configurar la API web, copie el URI del punto de conexión de la API web. Usará el punto de conexión de la API web en los pasos siguientes.

> [!TIP]
> Si no tiene una API web, puede ejecutar este ejemplo. En este caso, la aplicación devuelve el token de acceso, pero no podrá llamar a la API web. 

## <a name="step-4-get-the-wpf-desktop-app-sample"></a>Paso 4: Obtención del ejemplo de aplicación de escritorio de WPF

1. [Descargue el archivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git) o clone la aplicación web de ejemplo desde el [repositorio de GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git). 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ``` 

1. Abra la solución **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) en Visual Studio.



## <a name="step-5-configure-the-sample-desktop-app"></a>Paso 5: Configuración de la aplicación de escritorio de ejemplo

En el proyecto **active-directory-b2c-wpf**, abra el archivo *App.xaml.cs*. Los miembros de la clase `App.xaml.cs` contienen información sobre el proveedor de identidades de Azure AD B2C. La aplicación de escritorio usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión del usuario y adquirir tokens y validarlos. 

Actualice los miembros siguientes:

|Clave  |Value  |
|---------|---------|
|`TenantName`|La primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `contoso.b2clogin.com`.|
|`ClientId`|El identificador de la aplicación de escritorio del [paso 2.3](#23-register-the-desktop-app).|
|`PolicySignUpSignIn`| Flujo de usuario de registro o inicio de sesión o directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|
|`PolicyEditProfile`|Flujo de usuario del perfil de edición o inicio de sesión o directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|
|`ApiEndpoint`| (Opcional) El punto de conexión de API web que creó en el [paso 3](#step-3-configure-the-sample-web-api). Por ejemplo, `https://contoso.azurewebsites.net/hello`.|
| `ApiScopes` | Los ámbitos de API web que creó en el [paso 2.4](#24-grant-the-desktop-app-permissions-for-the-web-api).| 

El archivo *App.xaml.cs* final debería tener un aspecto parecido al del código de C# siguiente:

```csharp
public partial class App : Application
{

private static readonly string TenantName = "contoso";
private static readonly string Tenant = $"{TenantName}.onmicrosoft.com";
private static readonly string AzureAdB2CHostname = $"{TenantName}.b2clogin.com";
private static readonly string ClientId = "<web-api-app-application-id>";
private static readonly string RedirectUri = $"https://{TenantName}.b2clogin.com/oauth2/nativeclient";

public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string[] ApiScopes = { $"https://{Tenant}//api/tasks.read" };
public static string ApiEndpoint = "https://contoso.azurewebsites.net/hello";
```

## <a name="step-6-run-and-test-the-desktop-app"></a>Paso 6: Ejecución y prueba de la aplicación de escritorio

1. [Restaure los paquetes NuGet](/nuget/consume-packages/package-restore).
1. Presione **F5** para compilar y ejecutar el ejemplo.
1. Seleccione **Iniciar sesión**. A continuación, regístrese o inicie sesión con su cuenta Azure AD B2C local o social.

    ![Captura de pantalla que muestra cómo comenzar el flujo de inicio de sesión.](./media/configure-authentication-sample-wpf-desktop-app/sign-in.png)

1. Después de un registro o un inicio de sesión correctos, en el panel inferior de la aplicación de WPF se muestran los detalles del token.

    ![Captura de pantalla que muestra el token de acceso y el id. de usuario de Azure AD B2C.](./media/configure-authentication-sample-wpf-desktop-app/post-signin.png) 

1. Seleccione **Llamar a API** para llamar a la API web.


## <a name="next-steps"></a>Pasos siguientes

* [Configuración de las opciones de autenticación en una aplicación de escritorio de WPF mediante Azure Active Directory B2C](enable-authentication-wpf-desktop-app-options.md)

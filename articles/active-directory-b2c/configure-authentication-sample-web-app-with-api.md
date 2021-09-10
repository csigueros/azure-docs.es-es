---
title: Configuración de la autenticación en una aplicación web de ejemplo que llama a una API web mediante Azure Active Directory B2C
description: En este artículo se describe cómo usar Azure Active Directory B2C para el inicio de sesión y registro de usuarios en una aplicación web ASP.NET que llama a una API web.
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
ms.openlocfilehash: fec8af9d4153736738cf658e7e77bb1e9506fedd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868579"
---
# <a name="configure-authentication-in-a-sample-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Configuración de la autenticación en una aplicación web de ejemplo que llama a una API web mediante Azure AD B2C

En este artículo se usa una aplicación web ASP.NET de ejemplo para ilustrar cómo agregar autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones web.

> [!IMPORTANT]
> La aplicación web ASP.NET de ejemplo a la que se hace referencia en este artículo se usa para llamar a una API web con un token de portador. Para usar una aplicación web que no llama a una API web, consulte [Configuración de la autenticación en una aplicación web de ejemplo mediante Azure Active Directory B2C](configure-authentication-sample-web-app.md).  

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación basado OAuth 2.0 que se puede usar para que un usuario inicie sesión en una aplicación de manera segura. En este ejemplo de aplicación web se usa [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web). Microsoft Identity Web es un conjunto de bibliotecas ASP.NET Core que facilitan la adición de compatibilidad con la autenticación y la autorización en aplicaciones web con capacidad para llamar a una API web segura. 

El flujo de inicio de sesión consta de los siguientes pasos:

1. El usuario va a la aplicación web y selecciona **Iniciar sesión**.
1. La aplicación inicia la solicitud de autenticación y redirige al usuario a Azure AD B2C.
1. El usuario [se registra o inicia sesión](add-sign-up-and-sign-in-policy.md) y [restablece la contraseña](add-password-reset-policy.md). También puede iniciar sesión con una [cuenta de red social](add-identity-provider.md).
1. Una vez que el usuario inicia sesión, Azure AD B2C devuelve un código de autorización a la aplicación.
1. Luego, la aplicación hace lo siguiente:
 
   a. Intercambia el código de autorización por un token de identificador, un token de acceso y un token de actualización.  
   b. Lee las notificaciones del token de identificador y conserva una cookie de autorización de aplicación.  
   c. Almacena el token de actualización en una caché en memoria para usarlos más adelante.

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llamar a una API web, registre dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de la *aplicación web* permite que la aplicación inicie sesión con Azure AD B2C. Durante el registro, hay que especificar el *URI de redirección*. El URI de redirección es el punto de conexión al que Azure AD B2C redirige a los usuarios después de que se autentican en este servicio. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación. También se crea un *secreto de cliente*, que la aplicación usa para obtener los tokens de forma segura.

- El  registro de *API web* permite que la aplicación llame a una API web segura. El registro incluye los *ámbitos* de la API web. Los ámbitos ofrecen una manera de administrar permisos para los recursos protegidos, como la API web. A la aplicación web se le conceden permisos para los ámbitos de la API web. Cuando se selecciona un token de acceso, la aplicación especifica los permisos que desea en el parámetro de ámbito de la solicitud.  

En el siguiente diagrama se muestran los registros y la arquitectura de la aplicación:

![Diagrama de una aplicación web con registros y tokens de llamadas de API web.](./media/configure-authentication-sample-web-app-with-api/web-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Llamada a una API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecute: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Versión 16.8 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**
* [SDK de .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code (versión más reciente)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK de .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-web-applications"></a>Paso 2: Registrar aplicaciones web

En este paso, se crea la aplicación web y los registros de aplicación de la API web, y se especifican los ámbitos de la API web.

### <a name="step-21-register-the-web-api-app"></a>Paso 2.1: Registrar la aplicación de la API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Paso 2.2: Configurar los ámbitos de la aplicación de la API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-web-app"></a>Paso 2.3: Registrar la aplicación web

Haga lo siguiente para crear el registro de la aplicación web:

1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. En **Nombre**, escriba un nombre para la aplicación (por ejemplo, *webapp1*).
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Web** y luego, en el cuadro Dirección URL, escriba `https://localhost:5000/signin-oidc`.
1. En **Permisos**, active la casilla **Conceder consentimiento de administrador para openid y permisos de acceso sin conexión**.
1. Seleccione **Registrar**.
1. Una vez completado el registro de la aplicación, seleccione **Información general.**
1. Anote el **identificador de aplicación (cliente)** para usarlo más adelante al configurar la aplicación web.

    ![Captura de pantalla de la página de información general de la aplicación web para registrar el identificador de la aplicación web](./media/configure-authentication-sample-web-app-with-api/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-create-a-web-app-client-secret"></a>Paso 2.4: Crear un secreto de cliente de aplicación web

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]


### <a name="step-25-grant-the-web-app-permissions-for-the-web-api"></a>Paso 2.5: Conceder permisos de la aplicación web para la API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-web-app-sample"></a>Paso 3: Obtención de la aplicación web de ejemplo

[Descargue el archivo zip](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)o ejecute el siguiente comando de Bash para clonar la aplicación web de ejemplo desde GitHub. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extraiga el archivo de ejemplo en una carpeta donde la longitud total de la ruta de acceso sea inferior a 260 caracteres.

## <a name="step-4-configure-the-sample-web-api"></a>Paso 4: Configuración de la API web de ejemplo

En la carpeta de ejemplo, en la carpeta *4-WebApp-your-API/4-2-B2C/TodoListService*, abra el proyecto **TodoListService.csproj** con Visual Studio o Visual Studio Code. 

En la carpeta raíz del proyecto, abra el archivo *appsettings.json*. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación de la API web usa esta información para validar el token de acceso que la aplicación web pasa como token de portador. Actualice las siguientes propiedades de la configuración de la aplicación:

| Sección | Key | Valor |
| --- | --- | --- |
|AzureAdB2C|Instancia| La primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `https://contoso.b2clogin.com`.|
|AzureAdB2C|Domain| El [nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C. Por ejemplo, `contoso.onmicrosoft.com`.|
|AzureAdB2C|ClientId| Identificador de aplicación de la API web del [paso 2.1](#step-21-register-the-web-api-app).|
|AzureAdB2C|SignUpSignInPolicyId|Los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|
| | | |

El archivo de configuración final debería tener un aspecto parecido al del siguiente archivo JSON:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

### <a name="step-41-set-the-permission-policy"></a>Paso 4.1: Establecer la directiva de permisos

La API web comprueba que el usuario se ha autenticado con el token de portador y que el token de portador tiene configurados los ámbitos aceptados. Si el token de portador no tiene ninguno de estos ámbitos aceptados, la API web devuelve el código de estado HTTP 403 (Prohibido) y escribe en el cuerpo de la respuesta un mensaje que indica qué ámbitos se esperan en el token. 

Para configurar los ámbitos aceptados, abra la clase `Controller/TodoListController.cs` y establezca el nombre del ámbito, sin el URI completo.

```csharp
[RequiredScope("tasks.read")]
```

### <a name="step-42-run-the-sample-web-api-app"></a>Paso 4.2: Ejecutar la aplicación de la API web de ejemplo

Para permitir que la aplicación web llame al ejemplo de API web, ejecute la API web del siguiente modo:

1. Restaure las dependencias si así se le solicita.
1. Compile y ejecute el proyecto.
1. Una vez creado el proyecto, Visual Studio o Visual Studio Code inicia la API web en los exploradores con la siguiente dirección: https://localhost:44332.

## <a name="step-5-configure-the-sample-web-app"></a>Paso 5: Configurar la aplicación web de ejemplo

En la carpeta de ejemplo, en la carpeta `4-WebApp-your-API/4-2-B2C/Client`, abra el proyecto **TodoListClient.csproj** con Visual Studio o Visual Studio Code. 

En la carpeta raíz del proyecto, abra el archivo `appsettings.json`. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación web usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión de los usuarios y obtener tokens y validarlos. Actualice las siguientes propiedades de la configuración de la aplicación:

| Sección | Key | Valor |
| --- | --- | --- |
| AzureAdB2C | Instancia | Primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| [Nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| Identificador de la aplicación web del [paso 2.3](#step-23-register-the-web-app).|
|AzureAdB2C | ClientSecret | Secreto de la aplicación web del [paso 2.4](#step-24-create-a-web-app-client-secret). | 
|AzureAdB2C|SignUpSignInPolicyId|Los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|
| TodoList | TodoListScope | Los ámbitos de API web que creó en el [paso 2.5](#step-25-grant-the-web-app-permissions-for-the-web-api).|
| TodoList | TodoListBaseAddress | URI base de la API web (por ejemplo, `https://localhost:44332`). |
| | | |

El archivo de configuración final debería tener un aspecto parecido al del siguiente JSON:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-app-application-id>",
    "ClientSecret": "<web-app-application-secret>",  
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  "TodoList": {
    "TodoListScope": "https://contoso.onmicrosoft.com/api/demo.read",
    "TodoListBaseAddress": "https://localhost:44332"
  }
}
```


## <a name="step-6-run-the-sample-web-app"></a>Paso 6: Ejecutar la aplicación web de ejemplo

1. Compile y ejecute el proyecto.
1. Vaya a `https://localhost:5000`. 
1. Complete el proceso de inicio de sesión o registro.

Después de la autenticación correcta, verá el nombre para mostrar en la barra de navegación. Para ver las notificaciones que devuelve el token de Azure AD B2C a la aplicación, seleccione **TodoList**.

![Captura de pantalla de las notificaciones de token de la aplicación web](./media/configure-authentication-sample-web-app-with-api/web-api-to-do-list.png)


## <a name="deploy-your-application"></a>Implementación de aplicación

En una aplicación de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com/signin-oidc`. 

Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento. Las siguientes restricciones se aplican a los URI de redireccionamiento:

* La dirección URL de respuesta debe comenzar con el esquema `https`.
* La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. 

### <a name="token-cache-for-a-web-app"></a>Caché de tokens de una aplicación web

La aplicación web de ejemplo usa la serialización de caché de tokens en memoria. Esta implementación es excelente para pruebas. También funciona bien en aplicaciones de producción, siempre que no le importe que la caché de tokens se pierda cuando la aplicación web se reinicie. 

En un entorno de producción, recomendamos usar una caché de memoria distribuida como una caché de Redis, NCache o una caché de SQL Server. Para obtener más información sobre las implementaciones de caché de memoria distribuida, consulte [Serialización de la caché de tokens](../active-directory/develop/msal-net-token-cache-serialization.md).


## <a name="next-steps"></a>Pasos siguientes

* Más información [sobre el código de ejemplo](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)
* Información sobre cómo [habilitar la autenticación en su propia aplicación web mediante Azure AD B2C](enable-authentication-web-application.md)
* Información sobre cómo [habilitar la autenticación en su propia API web](enable-authentication-web-api.md)

---
title: Configuración de la autenticación en una aplicación web de ejemplo mediante Azure Active Directory B2C
description: En este artículo se analiza cómo usar Azure Active Directory B2C para que los usuarios se registren e inicien sesión en una aplicación web de ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/23/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b05aa59f465eafd65fa111fff811d2f0909f601d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471865"
---
# <a name="configure-authentication-in-a-sample-web-app-by-using-azure-ad-b2c"></a>Configuración de la autenticación en una aplicación web de ejemplo mediante Azure AD B2C

En este artículo se usa un ejemplo de aplicación web para ASP.NET para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones web.

> [!IMPORTANT]
> La aplicación web para ASP.NET de ejemplo a la que se hace referencia en este artículo no se puede usar para llamar a una API REST porque devuelve un token de identificador y no un token de acceso. Para obtener una aplicación web que pueda llamar a una API REST, consulte [Protección de una API web creada con ASP.NET Core mediante Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).  

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación que se basa en OAuth 2.0. Puede usar OIDC para que los usuarios inicien sesión de forma segura en una aplicación. En este ejemplo de aplicación web se usa [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web). Microsoft Identity Web es un conjunto de bibliotecas de ASP.NET Core que simplifican la adición de la compatibilidad con la autenticación y la autorización en aplicaciones web. 

El flujo de inicio de sesión consta de los pasos siguientes:

1. El usuario va a la aplicación web y selecciona **Iniciar sesión**. 
1. La aplicación inicia la solicitud de autenticación y redirige a los usuarios a Azure AD B2C.
1. Los usuarios [se registran o inician sesión](add-sign-up-and-sign-in-policy.md) y [restablecen la contraseña](add-password-reset-policy.md). También pueden iniciar sesión con una [cuenta de red social](add-identity-provider.md).
1. Una vez que los usuarios hayan iniciado sesión correctamente, Azure AD B2C devuelve un token de identificador a la aplicación.
1. La aplicación valida el token de identificador, lee las notificaciones y devuelve una página segura a los usuarios.

Cuando el token de identificador ha expirado o se invalida la sesión de la aplicación, esta inicia una nueva solicitud de autenticación y redirige a los usuarios a Azure AD B2C. Si la [sesión de SSO](session-behavior.md) de Azure AD B2C está activa, Azure AD B2C emite un token de acceso sin pedir a los usuarios que vuelvan a iniciar sesión. Si la sesión de Azure AD B2C expira o deja de ser válida, se pedirá a los usuarios que vuelvan a iniciar sesión.

### <a name="sign-out"></a>Cierre de sesión

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>Prerrequisitos

Un equipo que ejecuta uno de los siguientes: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Versión 16.8 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo Desarrollo web y ASP.NET
* [SDK de .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code (versión más reciente)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [SDK de .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>Paso 1: Configuración del flujo de usuario

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>Paso 2: Registro de una aplicación web

Para permitir que la aplicación inicie sesión con Azure AD B2C, registre la aplicación en el directorio de Azure AD B2C. El registro de la aplicación establece una relación de confianza entre la aplicación y Azure AD B2C.  

Durante el registro de la aplicación, especificará el *URI de redirección*. El URI de redirección es el punto de conexión al que Azure AD B2C redirige a los usuarios después de que estos se autentican con Azure AD B2C. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *identificador de cliente*, que permite identificar de forma exclusiva la aplicación. Una vez registrada la aplicación, Azure AD B2C usará el identificador de aplicación y el URI de redirección para crear solicitudes de autenticación. 

### <a name="step-21-register-the-app"></a>Paso 2.1: Registro de la aplicación

Para crear el registro de la aplicación web, haga lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. Busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. En **Nombre**, escriba un nombre para la aplicación (por ejemplo, *webapp1*).
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **Redirect URI** (URI de redirección), seleccione **Web** y luego, en el cuadro Dirección URL, escriba `https://localhost:5001/signin-oidc`.
1. En **Permisos**, active la casilla **Conceder consentimiento de administrador para openid y permisos de acceso sin conexión**.
1. Seleccione **Registrar**.
1. Seleccione **Información general**.
1. Anote el **Id. de aplicación (cliente)** para usarlo más adelante al configurar la aplicación web.

    ![Captura de pantalla de la página de información general de la aplicación web para registrar el identificador de la aplicación web.](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-enable-id-tokens"></a>Paso 2.2: Habilitación de tokens de identificador

Para las aplicaciones web que solicitan un token de identificador directamente a Azure AD B2C, habilite el flujo de concesión implícita en el registro de la aplicación.

1. En el panel izquierdo, en **Administrar**, seleccione **Autenticación**.
1. En **Concesión implícita**, active las casillas **Tokens de id. (usados para flujos híbridos e implícitos)** y **Tokens de acceso (usados para flujos implícitos)** .
1. Seleccione **Guardar**.

## <a name="step-3-get-the-web-app-sample"></a>Paso 3: Obtención de la aplicación web de ejemplo

[Descargue el archivo zip](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip) o clone la aplicación web de ejemplo desde GitHub. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extraiga el archivo de ejemplo en una carpeta en la que la longitud total de caracteres de la ruta de acceso sea inferior a 260.

## <a name="step-4-configure-the-sample-web-app"></a>Paso 4: Configuración de la aplicación web de ejemplo

En la carpeta de ejemplo, en la carpeta *1-WebApp-OIDC/1-5-B2C/* , abra el proyecto **WebApp-OpenIDConnect-DotNet.csproj** con Visual Studio o Visual Studio Code. 

En la carpeta raíz del proyecto, abra el archivo *appsettings.json*. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. Actualice las siguientes propiedades de la configuración de la aplicación: 

|Sección  |Key  |Valor  |
|---------|---------|---------|
|AzureAdB2C|Instancia| Primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| [Nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| El identificador de aplicación de la API web del [paso 2](#step-2-register-a-web-application).|
|AzureAdB2C|SignUpSignInPolicyId|Los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|

El archivo de configuración final debería tener un aspecto parecido al del siguiente JSON:

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-web-app"></a>Paso 5: Ejecución de la aplicación web de ejemplo

1. Compile y ejecute el proyecto.
1. Ir a `https://localhost:5001`. 
1. Seleccione **Registrarse/Iniciar sesión**.

    ![Captura de pantalla del botón "Registrarse/Iniciar sesión" en la página principal del proyecto.](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. Complete el proceso de inicio de sesión o registro.

Después de la autenticación correcta, verá el nombre para mostrar en la barra de navegación. Para ver las notificaciones que el token de Azure AD B2C devuelve a la aplicación, seleccione **Notificaciones**.

![Captura de pantalla de las notificaciones de token de la aplicación web.](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>Implementación de aplicación 

En una aplicación de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com/signin-oidc`. 

Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento. Las siguientes restricciones se aplican a los URI de redireccionamiento:

* La dirección URL de respuesta debe comenzar con el esquema `https`.
* La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. 

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [ejemplo de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code).
* Aprenda a [Habilitar la autenticación en su propia aplicación web mediante Azure AD B2C](enable-authentication-web-application.md).

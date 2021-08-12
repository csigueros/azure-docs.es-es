---
title: Configuración de la autenticación en una aplicación web de ejemplo mediante Azure Active Directory B2C
description: Uso de Azure Active Directory B2C para registrar e iniciar sesión con usuarios en una aplicación web para ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 23b66bef9de9fc83884f882eee3ad21aba695b48
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071354"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c"></a>Configuración de la autenticación en una aplicación web de ejemplo mediante Azure Active Directory B2C

En este artículo se usa un ejemplo de aplicación web para ASP.NET para ilustrar cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a las aplicaciones web.

> [!IMPORTANT]
> La aplicación web para ASP.NET de ejemplo a la que se hace referencia en este artículo no se puede usar para llamar a una API REST porque devuelve un token de identificador, pero no un token de acceso. Para obtener una aplicación web que pueda llamar a una API REST, consulte [Protección de una API web creada con ASP.NET Core mediante Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).  

## <a name="overview"></a>Información general

OpenID Connect (OIDC) es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que un usuario inicie sesión de forma segura en una aplicación. En este ejemplo de aplicación web se usa [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web). Microsoft Identity Web es un conjunto de bibliotecas de ASP.NET Core que simplifican la adición de la compatibilidad con la autenticación y la autorización en aplicaciones web. 

El flujo de inicio de sesión consta de los siguientes pasos:

1. El usuario va a la aplicación web y selecciona **Iniciar sesión**. 
1. La aplicación inicia la solicitud de autenticación y redirige al usuario a Azure AD B2C.
1. El [registro o inicio de sesión](add-sign-up-and-sign-in-policy.md) con usuarios, el [restablecimiento de la contraseña](add-password-reset-policy.md) o el inicio de sesión con una [cuenta de red social](add-identity-provider.md).
1. Tras iniciar sesión correctamente, Azure AD B2C devuelve un token de identificador a la aplicación.
1. La aplicación valida el token de identificador, lee las notificaciones y devuelve una página segura al usuario.

Cuando el token de identificador ha expirado o se invalida la sesión de la aplicación, esta inicia una nueva solicitud de autenticación y redirige al usuario a Azure AD B2C. Si la [sesión de SSO](session-behavior.md) de Azure AD B2C está activa, Azure AD B2C emite un token de acceso sin pedir al usuario que vuelva a iniciar sesión. Si la sesión de Azure AD B2C expira o deja de ser válida, se solicitará al usuario que vuelva a iniciar sesión.

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

## <a name="step-2-register-a-web-application"></a>Paso 2: Registro de una aplicación web

Para permitir que la aplicación inicie sesión con Azure AD B2C, registre la aplicación en el directorio de Azure AD B2C. El registro de la aplicación establece una relación de confianza entre la aplicación y Azure AD B2C.  

Durante el registro de la aplicación, especificará el **URI de redirección**. El URI de redirección es el punto de conexión al que Azure AD B2C redirige al usuario después de que este se autentica con Azure AD B2C. El proceso de registro de la aplicación genera un **identificador de aplicación**, también conocido como **identificador de cliente**, que permite identificar de forma exclusiva la aplicación. Una vez registrada la aplicación, Azure AD B2C usará el identificador de aplicación y el URI de redirección para crear solicitudes de autenticación. 

### <a name="register-the-app"></a>Registre la aplicación

Siga estos pasos para crear el registro de la aplicación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Registros de aplicaciones** y luego **Nuevo registro**.
1. Escriba un **Nombre** para la aplicación. Por ejemplo, *webapp1*.
1. En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier proveedor de identidades o directorio de la organización (para autenticar usuarios con flujos de usuario)** . 
1. En **URI de redirección**, seleccione **Web** y escriba `https://localhost:5001/signin-oidc` en el cuadro de texto.
1. En **Permisos**, active la casilla **Conceda consentimiento del administrador a los permisos openid y offline_access**.
1. Seleccione **Registrar**.
1. Seleccione **Información general**.
1. Anote el **Id. de aplicación (cliente)** ; lo usará en un paso posterior al configurar la aplicación web.

    ![Obtención del identificador de la aplicación](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="enable-id-tokens"></a>Habilitación de tokens de identificador

Para las aplicaciones web que solicitan un token de identificador directamente a Azure AD B2C, habilite el flujo de concesión implícita en el registro de la aplicación.

1. En el menú izquierdo, en **Administrar**, seleccione **Autenticación**.
1. En **Concesión implícita**, active la casilla **Tokens de id.**
1. Seleccione **Guardar**.

## <a name="step-3-get-the-web-app-sample"></a>Paso 3: Obtención de la aplicación web de ejemplo

[Descargue el archivo zip](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip) o clone la aplicación web de ejemplo desde GitHub. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

Extraiga el archivo de ejemplo en una carpeta en la que la longitud total de caracteres de la ruta de acceso sea inferior a 260.

## <a name="step-4-configure-the-sample-application"></a>Paso 4: Configuración de la aplicación de ejemplo

En la carpeta de ejemplo, en la carpeta `1-WebApp-OIDC/1-5-B2C/`, abra el proyecto **WebApp-OpenIDConnect-DotNet.csproj** con Visual Studio o Visual Studio Code. 

En la carpeta raíz del proyecto, abra el archivo `appsettings.json`. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. Actualice las siguientes propiedades de la configuración de la aplicación: 

|Sección  |Key  |Valor  |
|---------|---------|---------|
|AzureAdB2C|Instancia| La primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Por ejemplo, `https://contoso.b2clogin.com`.|
|AzureAdB2C|Domain| El [nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C. Por ejemplo, `contoso.onmicrosoft.com`.|
|AzureAdB2C|ClientId| El identificador de aplicación de la API web del [paso 2](#step-2-register-a-web-application).|
|AzureAdB2C|SignUpSignInPolicyId|Los flujos de usuario o la directiva personalizada que creó en el [paso 1](#step-1-configure-your-user-flow).|

El archivo de configuración final debería tener un aspecto parecido al del siguiente JSON:

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-application"></a>Paso 5: Ejecución de la aplicación de ejemplo

1. Compile y ejecute el proyecto.
1. Vaya a https://localhost:5001. 
1. Seleccione **SignIn/Up** (Iniciar sesión o registrarse).

    ![Selección de inicio de sesión o registro](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. Complete el proceso de inicio de sesión o registro.

Después de la autenticación correcta, verá el nombre para mostrar en la barra de navegación. Para ver las notificaciones que devuelve el token de Azure AD B2C a la aplicación, seleccione **Notificaciones**.

![Notificaciones del token de la aplicación web](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>Implementación de aplicación 

En una aplicación de producción, el URI de redirección del registro de la aplicación suele ser un punto de conexión accesible públicamente donde se ejecuta la aplicación, como `https://contoso.com/signin-oidc`. 

Puede agregar y modificar los URI de redireccionamiento en las aplicaciones registradas en cualquier momento. Las siguientes restricciones se aplican a los URI de redireccionamiento:

* La dirección URL de respuesta debe comenzar con el esquema `https`.
* La dirección URL de respuesta distingue mayúsculas de minúsculas. Sus mayúsculas o minúsculas deben coincidir con las de la ruta de acceso de la dirección URL de la aplicación en ejecución. 

## <a name="next-steps"></a>Pasos siguientes

* Más información [sobre el ejemplo de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)
* Aprenda a [Habilitar la autenticación en su propia aplicación web mediante Azure Active Directory B2C](enable-authentication-web-application.md)
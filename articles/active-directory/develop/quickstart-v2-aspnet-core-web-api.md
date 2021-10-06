---
title: 'Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: En esta guía de inicio rápido, descargará y modificará un ejemplo de código que muestra cómo proteger una API web de ASP.NET Core mediante la plataforma de identidad de Microsoft para la autorización.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: f70c35f55256ef98dd261a13058f1b6ad5330d9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616809"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Inicio rápido: Protección de una API web de ASP.NET Core con la plataforma de identidad de Microsoft

En este inicio rápido, descargará un código de ejemplo de una API web de ASP.NET Core y revisará cómo restringe el acceso a los recursos solo a las cuentas autorizadas. El ejemplo admite la autorización de cuentas personales de Microsoft y cuentas de cualquier organización de Azure Active Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Requisitos previos
>
> - Cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Inquilino de Azure Active Directory](quickstart-create-new-tenant.md)
> - [SDK de .NET Core 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) o [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Paso 1: Registro de la aplicación
>
> En primer lugar, registre la API web en el inquilino de Azure AD y agregue un ámbito siguiendo estos pasos:
>
> 1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Si tiene acceso a varios inquilinos, use el filtro **Directorios y suscripciones** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para ir al inquilino en el que quiere registrar la aplicación.
> 1. Busque y seleccione **Azure Active Directory**.
> 1. En **Administrar**, seleccione **Registros de aplicaciones** >  y, luego, **Nuevo registro**.
> 1. Escriba un **nombre** para la aplicación. Por ejemplo, escriba **AspNetCoreWebApi-Quickstart**. Los usuarios de la aplicación verán este nombre, que puede cambiar más tarde.
> 1. Seleccione **Registrar**.
> 1. En **Administrar**, seleccione **Exponer una API** > **Agregar un ámbito**. Acepte el valor predeterminado de **URI de id. de la aplicación**, para lo que debe seleccionar **Guardar y continuar**, y escriba los siguientes datos:
>    - **Nombre de ámbito**: `access_as_user`
>    - **¿Quién puede dar el consentimiento?** : **Administradores y usuarios**
>    - **Nombre para mostrar del consentimiento del administrador**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descripción del consentimiento del administrador**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Nombre para mostrar del consentimiento del usuario**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descripción del consentimiento del usuario**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Estado**: **Enabled**
> 1. Seleccione **Agregar ámbito** para completar la adición del ámbito.

## <a name="step-2-download-the-aspnet-core-project"></a>Paso 2: Descarga del proyecto de ASP.NET Core

> [!div renderon="docs"]
> [Descargue la solución de ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) de GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Paso 3: Configuración del proyecto de ASP.NET Core
>
> En este paso, configure el código de ejemplo para que funcione con el registro de la aplicación que creó anteriormente.
>
> 1. Extraiga el archivo. zip en una carpeta cerca de la raíz de la unidad. Por ejemplo, en *C:\Azure-Samples*.
>
>    Se recomienda extraer el archivo en un directorio próximo a la raíz de la unidad para evitar errores provocados por las limitaciones de longitud de la ruta de acceso en Windows.
>
> 1. Abra la solución en la carpeta *webapi* en el editor de código.
> 1. Abra el archivo *appsettings.json* y modifique el código siguiente:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Reemplace `Enter_the_Application_Id_here` por el identificador de aplicación (cliente) que ha registrado en Azure Portal. Puede encontrar el valor del identificador de aplicación (cliente) en la página **Información general** de la aplicación.
>    - Reemplace `Enter_the_Tenant_Info_Here` por una de las opciones siguientes:
>       - Si la aplicación admite **Solo las cuentas de este directorio organizativo**, reemplace este valor por el identificador de directorio (inquilino) (un GUID) o por el nombre del inquilino (por ejemplo, `contoso.onmicrosoft.com`). Puede encontrar el identificador de directorio (inquilino) en la página **Información general** de la aplicación.
>       - Si la aplicación admite **Cuentas en cualquier directorio organizativo**, reemplace este valor por `organizations`.
>       - Si la aplicación admite **Todos los usuarios de cuentas Microsoft**, establezca el valor en `common`.
>
> Para esta guía de inicio rápido, no cambie ningún otro valor del archivo *appsettings.json*.

## <a name="how-the-sample-works"></a>Funcionamiento del ejemplo

La API web recibe un token de una aplicación cliente, y el código de la API web valida lo valida. Este escenario se explica con más detalle en [Escenario: API web protegida](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Clase Startup

El middleware *Microsoft.AspNetCore.Authentication* usa una clase `Startup` que se ejecuta cuando se inicializa el proceso de hospedaje. En su método `ConfigureServices` , se llama al método de extensión `AddMicrosoftIdentityWebApi` proporcionado por *Microsoft.Identity.Web*.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

El método `AddAuthentication()` configura el servicio para agregar la autenticación basada en JwtBearer.

La línea que contiene `.AddMicrosoftIdentityWebApi` agrega la autorización de la plataforma de identidad de Microsoft a la API web. Después se configura para validar los tokens de acceso emitidos por la plataforma de identidad de Microsoft según la información de la sección `AzureAD` del archivo de configuración *appsettings.json*:

| Clave *appsettings.json* | Descripción                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | El Id. de aplicación (cliente) de la aplicación registrada en Azure Portal.                                                                                       |
| `Instance`             | El punto de conexión del servicio de token de seguridad (STS) para que el usuario se autentique. Normalmente, este valor es `https://login.microsoftonline.com/`, que indica la nube pública de Azure. |
| `TenantId`             | Nombre o id. del inquilino (un GUID), o bien `common`, para el inicio de sesión de usuarios con cuentas profesionales o educativas o cuentas personales de Microsoft.                             |

El método `Configure()` contiene dos métodos importantes, `app.UseAuthentication()` y `app.UseAuthorization()`, que habilitan su funcionalidad con nombre:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Protección de un controlador, del método de un controlador o de una página de Razor

Puede proteger un controlador o los métodos de controlador mediante el atributo `[Authorize]`. Este atributo restringe el acceso al controlador o los métodos al permitir únicamente usuarios autenticados. Si el usuario aún no se ha autenticado, se puede iniciar un desafío de autenticación para acceder al controlador.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Validación del ámbito en el controlador

El código de la API comprueba que los ámbitos necesarios están en el token mediante `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`:

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Pasos siguientes

El repositorio de GitHub que contiene este ejemplo de código de API web de ASP.NET Core incluye instrucciones y más ejemplos de código que muestran cómo:

- Agregar autenticación a una nueva API web de ASP.NET Core.
- Llamar a la API web desde una aplicación de escritorio.
- Llamar a las API de bajada como Microsoft Graph y otras API de Microsoft.

> [!div class="nextstepaction"]
> [Tutoriales de API web de ASP.NET Core en GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)

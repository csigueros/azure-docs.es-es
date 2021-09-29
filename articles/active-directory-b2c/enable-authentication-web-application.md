---
title: Habilitación de la autenticación en una aplicación web mediante los bloques de creación de Azure Active Directory B2C
description: En este artículo se describe cómo usar los bloques de creación de Azure Active Directory B2C para registrar e iniciar sesión con usuarios en una aplicación web para ASP.NET.
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
ms.openlocfilehash: 8cb836752ff75a32465d5c8df0e88210c323fb26
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740117"
---
# <a name="enable-authentication-in-your-own-web-app-by-using-azure-ad-b2c"></a>Habilitación de la autenticación en una aplicación web propia mediante Azure AD B2C

En este artículo se muestra cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a su propia aplicación web de ASP.NET. Aprenda a crear una aplicación web de ASP.NET Core con middleware de ASP.NET Core que usa el protocolo [OpenID Connect](openid-connect.md). 

Use este artículo junto con [Configuración de la autenticación en una aplicación web de ejemplo](configure-authentication-sample-web-app.md) y sustituya la aplicación web de ejemplo por una propia.

## <a name="prerequisites"></a>Requisitos previos

Para revisar los requisitos previos y las instrucciones de integración, vea [Configuración de la autenticación en una aplicación web de ejemplo](configure-authentication-sample-web-app.md).

## <a name="step-1-create-a-web-app-project"></a>Paso 1: Creación de un proyecto de aplicación web

Puede usar un proyecto existente de aplicación web Modelo-Vista-Controlador (MVC) de ASP.NET o crear uno. Para crear un proyecto, abra un shell de comandos y escriba el comando siguiente:

```dotnetcli
dotnet new mvc -o mywebapp
```

El comando anterior hace lo siguiente:

* Crea una aplicación web MVC.  
* El parámetro `-o mywebapp` crea un directorio llamado *mywebapp* con los archivos de código fuente de la aplicación.

## <a name="step-2-add-the-authentication-libraries"></a>Paso 2: Adición de las bibliotecas de autenticación

Agregue la biblioteca Microsoft Identity Web, que es un conjunto de bibliotecas de ASP.NET Core que simplifican la adición de la compatibilidad con la autenticación y la autorización de Azure AD B2C en la aplicación web. La biblioteca Microsoft Identity Web configura la canalización de autenticación con la autenticación basada en cookies. Se encarga de enviar y recibir mensajes de autenticación HTTP, la validación de tokens, la extracción de notificaciones, etc.

Para agregar la biblioteca Microsoft Identity Web, instale los paquetes mediante la ejecución de los siguientes comandos: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```dotnetcli
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.UI
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
Install-Package Microsoft.Identity.Web
Install-Package Microsoft.Identity.Web.UI 
```

---


## <a name="step-3-initiate-the-authentication-libraries"></a>Paso 3: Inicio de las bibliotecas de autenticación

El middleware Microsoft Identity Web usa una clase de inicio que se ejecuta cuando se inicia el proceso de hospedaje. En este paso, agregará el código necesario para iniciar las bibliotecas de autenticación.

Abra el archivo *Startup.cs* y, a continuación, agregue las siguientes declaraciones `using` al principio de la clase:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Dado que Microsoft Identity Web usa la autenticación basada en cookies para proteger la aplicación web, el código siguiente establece la configuración de cookies *SameSite*. Después, lee la configuración de la aplicación `AzureAdB2C` e inicia el controlador de middleware con su vista. 

Reemplace la función `ConfigureServices(IServiceCollection services)` por el siguiente fragmento de código: 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.Unspecified;
        // Handling SameSite cookie according to https://docs.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-3.1
        options.HandleSameSiteCookieCompatibility();
    });

    // Configuration to sign-in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

El código siguiente agrega la directiva de cookies y usa el modelo de autenticación. Reemplace la función `Configure` por el siguiente fragmento de código: 

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    // Add the Microsoft Identity Web cookie policy
    app.UseCookiePolicy();
    app.UseRouting();
    // Add the ASP.NET Core authentication service
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // Add endpoints for Razor pages
        endpoints.MapRazorPages();
    });
};
```

## <a name="step-4-add-the-ui-elements"></a>Pas 4: Adición de elementos de la interfaz de usuario

Para agregar elementos de la interfaz de usuario, use una vista parcial que contenga lógica para comprobar si los usuarios han iniciado sesión. Si los usuarios no han iniciado sesión, la vista parcial representa el botón de inicio de sesión. Si han iniciado sesión, se muestra el nombre para mostrar del usuario y el botón de cierre de sesión.
  
Cree un archivo *\_LoginPartial.cshtml* dentro de la carpeta */Views/Shared* con el fragmento de código siguiente:

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package and
            it defines some well known actions such as SignUp/In, SignOut and EditProfile-->
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="EditProfile">
                <button type="submit" class="btn btn-primary" style="margin-right:5px">Edit Profile</button>
            </form>
        </li>
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">
                <button type="submit" class="btn btn-primary">Sign Out</button>
            </form>
        </li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">
                <button type="submit" class="btn btn-primary">Sign Up/In</button>
            </form>
        </li>
    </ul>
}
```

Modifique el archivo */Views/Shared_Layout.cshtml* para incluir el archivo *_LoginPartial.cshtml* que ha agregado. El archivo *_Layout.cshtml* es un diseño común que proporciona a los usuarios una experiencia coherente a medida que navegan entre páginas. El diseño incluye elementos comunes de la interfaz de usuario, como el encabezado y el pie de página de la aplicación.

> [!NOTE]
> En función de la versión de .NET Core que ejecute y de si va a agregar el inicio de sesión a una aplicación existente, los elementos de la interfaz de usuario podrían tener otro aspecto. Si es así, asegúrese de incluir *_LoginPartial* en la ubicación adecuada dentro del diseño de página.

Abra el archivo */Views/Shared/_Layout.cshtml* y agregue el siguiente elemento `div`.

```razor
<div class="navbar-collapse collapse">
...
</div>
```

Reemplace este elemento con el siguiente código de Razor:

```razor
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Claims">Claims</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

El código de Razor anterior incluye un vínculo a la acción `Claims` que creará en el paso siguiente.

## <a name="step-5-add-the-claims-view"></a>Paso 5: Adición de la vista de notificaciones

Para ver las notificaciones del token de identificador en la carpeta */Views/Home*, agregue la vista *Claims.cshtml*.

```razor
@using System.Security.Claims

@{
  ViewData["Title"] = "Claims";
}
<h2>@ViewData["Title"].</h2>

<table class="table-hover table-condensed table-striped">
  <tr>
    <th>Claim Type</th>
    <th>Claim Value</th>
  </tr>

  @foreach (Claim claim in User.Claims)
  {
    <tr>
      <td>@claim.Type</td>
      <td>@claim.Value</td>
    </tr>
  }
</table>
```

En este paso, agregará la acción `Claims` que vincula la vista *Claims.cshtml* al controlador *Home*. El atributo `Claims` usa el atributo `Authorize`, que limita el acceso a la acción para los usuarios autenticados.  

En el controlador */Controllers/HomeController.cs*, agregue la siguiente acción:

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

Agregue la siguiente declaración `using` al inicio de la clase:

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-app-settings"></a>Paso 6: Adición de la configuración de la aplicación

La configuración del proveedor de identidades de Azure AD B2C se almacena en el archivo *appsettings.json*. Abra *appsettings.json* y agregue los valores siguientes:

```JSon
"AzureAdB2C": {
  "Instance": "https://<your-tenant-name>.b2clogin.com",
  "ClientId": "<web-app-application-id>",
  "Domain": "<your-b2c-domain>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

La información necesaria se describe en el artículo [Configuración de la autenticación en una aplicación web de ejemplo](configure-authentication-sample-web-app.md). Use la configuración siguiente:

* **Instancia**: reemplace `<your-tenant-name>` por la primera parte del [nombre del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `https://contoso.b2clogin.com`).
* **Dominio**: reemplace `<your-b2c-domain>` por el [nombre completo del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`).
* **Identificador de cliente**: reemplace `<web-app-application-id>` por el identificador de la aplicación del [paso 2](configure-authentication-sample-web-app.md#step-2-register-a-web-application).
* **Nombre de directiva**: reemplace `<your-sign-up-in-policy>` por los flujos de usuario que ha creado en el [paso 1](configure-authentication-sample-web-app.md#step-1-configure-your-user-flow).

## <a name="step-7-run-your-application"></a>Paso 7: Ejecución de la aplicación

1. Compile y ejecute el proyecto.
1. Ir a `https://localhost:5001`. 
1. Seleccione **Registrarse/Iniciar sesión**.
1. Complete el proceso de inicio de sesión o registro.

Después de autenticarse correctamente, verá el nombre para mostrar en la barra de navegación. Para ver las notificaciones que el token de Azure AD B2C devuelve a la aplicación, seleccione **Notificaciones**.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [personalizar y mejorar la experiencia de autenticación de Azure AD B2C para la aplicación web](enable-authentication-web-application-options.md).

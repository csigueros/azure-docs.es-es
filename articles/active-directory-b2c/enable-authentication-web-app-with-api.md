---
title: Habilitación de la autenticación en aquellas aplicación web que llaman a una API web mediante bloques de creación de Azure Active Directory B2C
description: En este artículo se tratan los bloques de creación de una aplicación web de ASP.NET que llama a una API web mediante Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: fd1d47f879ead2913d5fbfa85febde50a8950907
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723198"
---
# <a name="enable-authentication-in-web-apps-that-call-a-web-api-by-using-azure-ad-b2c"></a>Habilitación de la autenticación en aplicaciones web que llaman a una API web mediante Azure AD B2C

En este artículo se muestra cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a una aplicación web de ASP.NET que llama a una API web. Aprenda a crear una aplicación web de ASP.NET Core con middleware de ASP.NET Core que use el protocolo [OpenID Connect](openid-connect.md). 

Para usar este artículo con el artículo sobre la [configuración de la autenticación en una aplicación web de ejemplo que llama a una API web](configure-authentication-sample-web-app-with-api.md), sustituya la aplicación web de ejemplo por la suya propia.

Este artículo se centra en el proyecto de una aplicación web. Para obtener instrucciones sobre cómo crear la API web, consulte el [ejemplo de API web de una lista de tareas](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).

## <a name="prerequisites"></a>Requisitos previos

Consulte los requisitos previos y los pasos de integración en el artículo sobre la [configuración de la autenticación en una aplicación web de ejemplo que llama a una API web](configure-authentication-sample-web-app-with-api.md).

En las siguientes secciones se indica paso a paso cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a una aplicación web de ASP.NET.

## <a name="step-1-create-a-web-app-project"></a>Paso 1: Creación de un proyecto de aplicación web

Puede usar un proyecto de la aplicación web Model View Controller (MVC) de ASP.NET existente o crear uno nuevo. Para crear un proyecto, abra un shell de comandos y ejecute el siguiente comando:

```dotnetcli
dotnet new mvc -o mywebapp
```

El comando anterior crea una aplicación web MVC. El parámetro `-o mywebapp` crea un directorio llamado *mywebapp* con los archivos de código fuente de la aplicación.

## <a name="step-2-add-the-authentication-libraries"></a>Paso 2: Adición de las bibliotecas de autenticación

En primer lugar, agregue la biblioteca web de identidad de Microsoft. Se trata de un conjunto de bibliotecas de ASP.NET Core que simplifican la adición de la compatibilidad con la autenticación y la autorización de Azure AD B2C en la aplicación web. La biblioteca Microsoft Identity Web configura la canalización de autenticación con la autenticación basada en cookies. Se encarga de enviar y recibir mensajes de autenticación HTTP, la validación de tokens, la extracción de notificaciones, etc.

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

## <a name="step-3-initiate-the-authentication-libraries"></a>Paso 3: Inicio de las bibliotecas de autenticación

El middleware Microsoft Identity Web usa una clase de inicio que se ejecuta cuando se inicia el proceso de hospedaje. En este paso, agregará el código necesario para iniciar las bibliotecas de autenticación.

Abra `Startup.cs` y agregue las siguientes declaraciones `using` al principio de la clase :

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Dado que Microsoft Identity Web usa la autenticación basada en cookies para proteger la aplicación web, el código siguiente establece la configuración de cookies *SameSite*. A continuación, lee la configuración de la aplicación `AzureADB2C` e inicia el controlador de middleware con su vista. 

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

    // Configuration to sign in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C")
            // Enable token acquisition to call downstream web API
            .EnableTokenAcquisitionToCallDownstreamApi(new string[] { Configuration["TodoList:TodoListScope"] })
            // Add refresh token in-memory cache
            .AddInMemoryTokenCaches();

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

El siguiente código agrega la directiva de cookies y usa el modelo de autenticación. Reemplace la función `Configure` por el siguiente fragmento de código: 

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

Para agregar elementos de la interfaz de usuario, use una vista parcial. La vista parcial contiene lógica para comprobar si un usuario ha iniciado sesión. Si el usuario no ha iniciado sesión, la vista parcial representa el botón de inicio de sesión. Si el usuario ha iniciado sesión, aparece el nombre para mostrar del usuario y el botón para cerrar sesión.
  
Cree un nuevo archivo `_LoginPartial.cshtml` dentro de la carpeta `Views/Shared` con el siguiente fragmento de código:

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package, and it defines some well-known actions, such as SignUp/In, SignOut, and EditProfile. -->
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

Modifique `Views\Shared\_Layout.cshtml` para incluir el archivo *_LoginPartial.cshtml* que agregó. El archivo *_Layout.cshtml* es un diseño común que proporciona al usuario una experiencia coherente a medida que navega de página a página. El diseño incluye elementos comunes de la interfaz de usuario, como el encabezado y el pie de página de la aplicación.

> [!NOTE]
> Dependiendo de la versión de .NET Core y de si va a agregar el inicio de sesión a una aplicación existente, los elementos de la interfaz de usuario podrían tener un aspecto diferente. Si es así, asegúrese de incluir *_LoginPartial* en la ubicación adecuada dentro del diseño de página.

Abra */Views/Shared/_Layout.cshtml* y agregue el siguiente elemento `div`.

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
    <li><a asp-area="" asp-controller="Home" asp-action="TodoList">To do list</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

El código de Razor anterior incluye un vínculo a las acciones `Claims` y `TodoList` que creará en los siguientes pasos.

## <a name="step-5-add-the-claims-view"></a>Paso 5: Adición de la vista de notificaciones

Para ver las notificaciones del token de identificador en la carpeta `Views/Home`, agregue la vista `Claims.cshtml`.

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

En este paso, agregará la acción `Claims` que vincula la vista *Claims.cshtml* al controlador *Home*. Usa el atributo `[Authorize]`, que limita el acceso a la acción Claims a los usuarios autenticados.

En el controlador */Controllers/HomeController.cs*, agregue la siguiente acción:

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

Agregue la siguiente declaración `using` al comienzo de la clase:

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-todolistcshtml-view"></a>Paso 6: Adición de la vista TodoList.cshtml

Para llamar a la API web TodoList.cshtml, debe tener un token de acceso con los ámbitos adecuados. En este paso, se agrega una acción al controlador `Home`. En la carpeta `Views/Home`, agregue la vista `TodoList.cshtml`.

```razor
@{
    ViewData["Title"] = "To do list";
}

<div class="text-left">
  <h1 class="display-4">Your access token</h1>
  @* Remove following line in production environments *@
  <code>@ViewData["accessToken"]</code>
</div>
```

Después de agregar la vista, agregue la acción `TodoList` que vincula la vista *TodoList.cshtml* al controlador *Home*. Usa el atributo `[Authorize]`, que limita el acceso a la acción TodoList a los usuarios autenticados.  

En el controlador */Controllers/HomeController.cs*, agregue el siguiente miembro de clase de acción e inyecte el servicio de adquisición de tokens en el controlador.

```csharp
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    // Add the token acquisition service member variable
    private readonly ITokenAcquisition _tokenAcquisition; 
    
    // Inject the acquisition service
    public HomeController(ILogger<HomeController> logger, ITokenAcquisition tokenAcquisition)
    {
        _logger = logger;
        // Set the acquisition service member variable
        _tokenAcquisition = tokenAcquisition;
    }

    // More code...
}
```

Ahora agregue la siguiente acción, que muestra cómo llamar a una API web junto con el token de portador. 

```csharp
[Authorize]
public async Task<IActionResult> TodoListAsync()
{
    // Acquire an access token with the relevant scopes.
    var accessToken = await _tokenAcquisition.GetAccessTokenForUserAsync(new[] { "https://your-tenant.onmicrosoft.com/tasks-api/tasks.read", "https://your-tenant.onmicrosoft.com/tasks-api/tasks.write" });
    
    // Remove this line in production environments    
    ViewData["accessToken"] = accessToken;

    using (HttpClient client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

        HttpResponseMessage response = await client.GetAsync("https://path-to-your-web-api");
    }

    return View();
}
```

## <a name="step-7-add-the-app-settings"></a>Paso 7: Adición de la configuración de la aplicación

La configuración del proveedor de identidades de Azure AD B2C se almacena en el archivo *appsettings.json*. Abra *appsettings.json* y agregue la configuración de la aplicación, como se describe en "Paso 5: Configuración de la aplicación web de ejemplo" del artículo sobre la [configuración de la autenticación en una aplicación web de ejemplo que llama a una API web mediante Azure AD B2C](configure-authentication-sample-web-app-with-api.md#step-5-configure-the-sample-web-app).

## <a name="step-8-run-your-application"></a>Paso 8: Ejecución de la aplicación

1. Compile y ejecute el proyecto.
1. Vaya a https://localhost:5001 y seleccione **SignIn/Up**.
1. Complete el proceso de inicio de sesión o de registro.

Una vez que se haya autenticado correctamente en la aplicación, compruebe el nombre para mostrar en la barra de navegación. 

* Para ver las notificaciones que el token de Azure AD B2C devuelve a la aplicación, seleccione **Notificaciones**.
* Para ver el token de acceso, seleccione **Lista de tareas pendientes**.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a :
* [Personalizar y mejorar la experiencia de la autenticación de Azure AD B2C en su aplicación web](enable-authentication-web-application-options.md)
* [Habilitar la autenticación en su propia API web](enable-authentication-web-api.md)

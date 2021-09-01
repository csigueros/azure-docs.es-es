---
title: Comprobación de ámbitos y roles de aplicación con la API web protegida | Azure
titleSuffix: Microsoft identity platform
description: Compruebe que la llamada a la API la realizan solo aplicaciones en nombre de usuarios que tienen los ámbitos apropiados y aplicaciones de demonio que tienen los roles de aplicación correctos.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2243f149ebe89bcb3d52d5940ba930891925d788
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724847"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API web protegida: Comprobación de ámbitos y roles de aplicación

En este artículo se describe cómo se puede agregar autorización a la API web. Esta protección garantiza que solo llamen a la API:

- Aplicaciones en nombre de usuarios que tienen los ámbitos correctos.
- Aplicaciones de demonio que tienen los roles de aplicación correctos.

> [!NOTE]
> Los fragmentos de código de este artículo se han extraído de los ejemplos de código siguientes en GitHub:
>
> - [Tutorial incremental de la API web de ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [API web de ASP.NET de ejemplo](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Para proteger una API web de ASP.NET o ASP.NET Core, debe agregar el atributo `[Authorize]` en uno de los siguientes elementos:

- El propio controlador, si quiere que todas las acciones del controlador estén protegidas.
- La acción de controlador individual para la API.

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     // ...
    }
```

Aun así, esta protección no es suficiente. Solo garantiza que ASP.NET y ASP.NET Core validen el token. La API debe verificar que el token usado para llamar a la API se haya solicitado con las notificaciones esperadas. Estas notificaciones requieren una verificación en particular:

- Los *ámbitos*, si se llama a la API en nombre de un usuario.
- Los *roles de aplicación*, si se puede llamar a la API desde una aplicación de demonio.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificación de los ámbitos de las API a las que se llama en nombre de los usuarios

Si una aplicación cliente llama a la API en nombre de un usuario, la API debe solicitar un token de portador con ámbitos específicos para la API. Para más información, consulte [Configuración del código | Token de portador](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

En ASP.NET Core, puede usar Microsoft.Identity.Web para comprobar los ámbitos de cada acción del controlador. También puede comprobarlos en el nivel del controlador o para toda la aplicación.

#### <a name="verify-the-scopes-on-each-controller-action"></a>Verificación de los ámbitos en cada acción de controlador

Puede comprobar los ámbitos de la acción del controlador mediante el atributo `[RequiredScope]`. Este atributo tiene varias invalidaciones. Una que toma directamente los ámbitos necesarios y otra que toma una clave para la configuración.

##### <a name="verify-the-scopes-on-a-controller-action-with-hardcoded-scopes"></a>Comprobación de los ámbitos de una acción del controlador con ámbitos codificados de forma rígida

El siguiente fragmento de código muestra el uso del atributo `[RequiredScope]` con ámbitos codificados de forma rígida.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    [RequiredScope(scopeRequiredByApi)]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-action-with-scopes-defined-in-configuration"></a>Comprobación de los ámbitos de una acción del controlador con ámbitos definidos en la configuración

También puede declarar estos ámbitos necesarios en la configuración y hacer referencia a la clave de configuración:

Por ejemplo, si en el archivo appsettings.json tiene la siguiente configuración:

```JSon
{
 "AzureAd" : {
   // more settings
   "Scopes" : "access_as_user access_as_admin"
  }
}
```

Haga referencia a ella en el atributo `[RequiredScope]`:

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    [RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-scopes-conditionally"></a>Comprobación condicional de ámbitos

Hay casos en los que se quiere comprobar los ámbitos de forma condicional. Puede hacerlo mediante el método de extensión `VerifyUserHasAnyAcceptedScope` de `HttpContext`.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-at-the-level-of-the-controller"></a>Comprobación de los ámbitos en el nivel del controlador

También puede comprobar los ámbitos de todo el controlador.

##### <a name="verify-the-scopes-on-a-controller-with-hardcoded-scopes"></a>Comprobación de los ámbitos de un controlador con ámbitos codificados de forma rígida

El siguiente fragmento de código muestra el uso del atributo `[RequiredScope]` con ámbitos codificados de forma rígida en el controlador.

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(scopeRequiredByApi)]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-with-scopes-defined-in-configuration"></a>Comprobación de los ámbitos de un controlador con ámbitos definidos en la configuración

Al igual que en una acción, también puede declarar estos ámbitos necesarios en la configuración y hacer referencia a la clave de configuración:

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-more-globally"></a>Verificación de los ámbitos más globalmente

La definición de ámbitos pormenorizados para la API web y la verificación de los ámbitos en cada acción del controlador es el enfoque recomendado. Sin embargo, también es posible comprobar los ámbitos en el nivel de la aplicación o de un controlador. Para obtener más información, consulte [Autorización basada en notificaciones](/aspnet/core/security/authorization/claims) en la documentación de ASP.NET Core.

#### <a name="what-is-verified"></a>¿Qué se comprueba?

El atributo `[RequiredScope]` y el método `VerifyUserHasAnyAcceptedScope` hacen algo similar a los pasos siguientes:

- Verificar que hay una notificación denominada `http://schemas.microsoft.com/identity/claims/scope` o `scp`.
- Verificar que la notificación tiene un valor que contiene el ámbito que espera la API.

### <a name="aspnet-classic"></a>[ASP.NET clásico](#tab/aspnet)

En una aplicación de ASP.NET, puede validar los ámbitos de la siguiente manera:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateScopes(new[] {"read"; "admin" } );
       // ...
    }
```

A continuación, se muestra una versión simplificada de `ValidateScopes`:

```csharp
private void ValidateScopes(IEnumerable<string> acceptedScopes)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim scopeClaim = ClaimsPrincipal.Current.FindFirst("scp");
    if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Forbidden,
            ReasonPhrase = $"The 'scp' claim does not contain '{scopeClaim}' or was not found"
        });
    }
}
```

Para obtener una versión completa de `ValidateScopes` para ASP.NET Core, consulte el código del archivo [*ScopesRequiredHttpContextExtensions.cs*](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/ScopesRequiredHttpContextExtensions.cs).

---

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verificación de los roles de aplicación de las API a las que llaman aplicaciones de demonio

Si una [aplicación de demonio](scenario-daemon-overview.md) llama a la API web, la aplicación debe solicitar un permiso de aplicación a la API web. Como se muestra en [Exposición de los permisos de aplicación (roles de aplicación)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles), la API expone estos permisos. Un ejemplo es el rol de aplicación `access_as_application`.

Ahora debe hacer que la API verifique que el token recibido contiene la notificación `roles` y que esta notificación tiene el valor esperado. El código de verificación es similar al código que verifica los permisos delegados, con la diferencia de que la acción del controlador prueba los roles en lugar de los ámbitos:

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

El siguiente fragmento de código muestra cómo comprobar el rol de la aplicación.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        // ...
    }
```

En su lugar, puede usar los atributos de [Authorize("role")] en el controlador o una acción (o una página de Razor).

```CSharp
[Authorize("role")]
MyController : ApiController
{
    // ...
}
```

Pero para ello, deberá asignar la notificación del rol a "roles" en el archivo Startup.cs:


```CSharp
 services.Configure<OpenIdConnectOptions>(OpenIdConnectDefaults.AuthenticationScheme, options =>
 {
    // The claim in the Jwt token where App roles are available.
    options.TokenValidationParameters.RoleClaimType = "roles";
 });
```

Esta no es la mejor solución si también tiene que realizar la autorización basada en grupos.

Para más información, consulte el tutorial incremental de la aplicación web sobre la [autorización por roles y grupos](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ).

### <a name="aspnet-classic"></a>[ASP.NET clásico](#tab/aspnet)

En una aplicación de ASP.NET, puede validar los roles de la aplicación de la siguiente manera:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateAppRole("access_as_application");
       // ...
    }
```

Esta es una versión simplificada de `ValidateAppRole`:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
```

Para obtener una versión completa de `ValidateAppRole` para ASP.NET Core, consulte el código del archivo [*RolesRequiredHttpContextExtensions.cs*](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs).

---

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceptación de tokens solo de aplicación si la API web debe invocarse únicamente mediante aplicaciones de demonio

Los usuarios también pueden usar notificaciones de roles en patrones de asignación de usuarios, tal como se muestra en [Procedimientos: Agregar roles de aplicación en la aplicación y recibirlos en el token](howto-add-app-roles-in-azure-ad-apps.md). Si los roles se asignan a ambos, comprobar los roles permitirá a las aplicaciones iniciar sesión como usuarios y a los usuarios iniciar sesión como aplicaciones. Le recomendamos que declare roles diferentes para los usuarios y las aplicaciones a fin de evitar esta confusión.

Si quiere que únicamente las aplicaciones de demonio llamen a la API web, al validar el rol de aplicación, agregue una condición de que el token es solo de aplicación.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnly = oid != null && sub != null && oid == sub;
```

Si aplica la condición inversa, permite que llamen a la API solo las aplicaciones que inician la sesión de un usuario.

### <a name="using-acl-based-authorization"></a>Uso de la autorización basada en ACL

Como alternativa a la autorización basada en roles de aplicación, puede proteger la API web con un patrón de autorización basado en una lista de control de acceso (ACL) para [controlar los tokens sin la notificación `roles`](v2-oauth2-client-creds-grant-flow.md#controlling-tokens-without-the-roles-claim).

Si usa Microsoft.Identity.Web en ASP.NET Core, deberá declarar que usa la autorización basada en ACL; de lo contrario, Microsoft.Identity.Web producirá una excepción cuando no haya roles ni ámbitos en las notificaciones proporcionadas:

```Text
System.UnauthorizedAccessException: IDW10201: Neither scope or roles claim was found in the bearer token.
```

 Para evitar esta excepción, establezca la propiedad de configuración `AllowWebApiToBeAuthorizedByACL` en true en el archivo appsettings.json o mediante programación.

```Json
{
 "AzureAD"
 {
  // other properties
  "AllowWebApiToBeAuthorizedByACL" : true,
  // other properties
 }
}
```

Si establece `AllowWebApiToBeAuthorizedByACL` en true, es **su responsabilidad** garantizar el mecanismo de la lista de control de acceso.

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Paso a producción](scenario-protected-web-api-production.md).

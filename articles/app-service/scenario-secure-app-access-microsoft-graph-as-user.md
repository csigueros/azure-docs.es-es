---
title: 'Tutorial: Accesos de la aplicación web a Microsoft Graph como usuario | Azure'
description: En este tutorial, aprenderá a establecer el acceso a los datos en Microsoft Graph para un usuario con la sesión iniciada.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 09/23/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 332552d361c4c8c43b7b4bfa981050c829a79762
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624527"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Tutorial: Acceso a Microsoft Graph desde una aplicación segura como usuario

Aprenda a acceder a Microsoft Graph desde una aplicación web que se ejecuta en Azure App Service.

:::image type="content" alt-text="Diagrama que muestra el acceso a Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Quiere agregar acceso a Microsoft Graph desde la aplicación web y realizar alguna acción como el usuario que ha iniciado sesión. En esta sección se describe cómo conceder permisos delegados a la aplicación web y cómo obtener la información de perfil del usuario con la sesión iniciada desde Azure Active Directory (Azure AD).

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Conceder permisos delegados a una aplicación web
> * Llamar a Microsoft Graph desde una aplicación web para el usuario que ha iniciado sesión

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una aplicación web que se ejecuta en Azure App Service que tiene el [módulo de autenticación y autorización de App Service habilitado](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Concesión de acceso de front-end para llamar a Microsoft Graph

Ahora que ha habilitado la autenticación y la autorización en la aplicación web, esta se registra con la plataforma de identidad de Microsoft y se respalda mediante una aplicación de Azure AD. En este paso, concederá a la aplicación web permisos para acceder a Microsoft Graph para el usuario (técnicamente, asignará a la aplicación de Azure AD de la aplicación web los permisos para acceder a la aplicación de Azure AD de Microsoft Graph para el usuario).

En el menú de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory** o busque y seleccione **Azure Active Directory** desde cualquier página.

Seleccione **Registros de aplicaciones** > **Aplicaciones propias** > **View all applications in this directory** (Ver todas las aplicaciones de este directorio). Seleccione el nombre de la aplicación web y, después, **Permisos de API**.

Seleccione **Agregar un permiso**; después, seleccione las API de Microsoft y Microsoft Graph.

Seleccione **Permisos delegados** y, después, **User.Read** en la lista. Seleccione **Agregar permisos**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Configuración de App Service para devolver un token de acceso que se pueda usar

La aplicación web ya tiene los permisos necesarios para acceder a Microsoft Graph como usuario con la sesión iniciada. En este paso, configurará la autenticación y autorización de App Service para proporcionarle un token de acceso que se pueda usar para acceder a Microsoft Graph. Para este paso, debe agregar el ámbito User.Read para el servicio de bajada (Microsoft Graph): `https://graph.microsoft.com/User.Read`.

> [!IMPORTANT]
> Si no configura App Service para que devuelva un token de acceso utilizable, recibirá un error ```CompactToken parsing failed with error code: 80049217``` al llamar a Microsoft Graph API en el código.

# <a name="azure-resource-explorer"></a>[Azure Resource Explorer](#tab/azure-resource-explorer)
Vaya a [Azure Resource Explorer](https://resources.azure.com/) y, mediante el árbol de recursos, busque la aplicación web. La dirección URL del recurso debe ser similar a `https://resources.azure.com/subscriptions/subscriptionId/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`.

Se abre ahora Azure Resource Explorer con la aplicación web seleccionada en el árbol de recursos. En la parte superior de la página, seleccione **Read/Write** (Lectura/escritura) para permitir la edición de los recursos de Azure.

En el explorador izquierdo, explore en profundidad hasta **config** > **authsettingsV2**.

En la vista **authsettingsV2**, seleccione **Editar**. Busque la sección **login** (iniciar sesión) de **identityProviders** -> **azureActiveDirectory** y agregue la siguiente configuración de **loginParameters**: `"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`.

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

Seleccione **PUT** para guardar la configuración. Puede que esta configuración tarde varios minutos en surtir efecto. La aplicación web ya está configurada para acceder a Microsoft Graph con un token de acceso adecuado. Si no lo hace, Microsoft Graph devuelve un error que indica que el formato del token compacto no es correcto.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use la CLI de Azure para llamar a las API de REST de la aplicación web de App Service para [obtener](/rest/api/appservice/web-apps/get-auth-settings) y [actualizar](/rest/api/appservice/web-apps/update-auth-settings) la configuración de autenticación a fin de que la aplicación web pueda llamar a Microsoft Graph. Abra una ventana de comandos e inicie sesión en la CLI de Azure:

```azurecli
az login
```

Obtenga la configuración 'config/authsettingsv2’ existente y guárdela en el archivo *authsettings.json* local.

```azurecli
az rest --method GET --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2/list?api-version=2020-06-01' > authsettings.json
```

Abra el archivo authsettings.json con el editor de texto que prefiera. Busque la sección **login** (iniciar sesión) de **identityProviders** -> **azureActiveDirectory** y agregue la siguiente configuración de **loginParameters**: `"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`.

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

Guardar los cambios en el archivo *authsettings.json* y cargue la configuración local a la aplicación web:

```azurecli
az rest --method PUT --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2?api-version=2020-06-01' --body @./authsettings.json
```
---

## <a name="call-microsoft-graph-net"></a>Llamada a Microsoft Graph (.NET)

La aplicación web dispone ahora de los permisos necesarios y también agrega el identificador de cliente de Microsoft Graph a los parámetros de inicio de sesión. Mediante la [biblioteca Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/), la aplicación web obtiene un token de acceso para la autenticación con Microsoft Graph. En la versión 1.2.0 y versiones posteriores, la biblioteca Microsoft.Identity.Web se integra con el módulo de autenticación y autorización de App Service y se puede ejecutar junto con este. Microsoft.Identity.Web detecta que la aplicación web se hospeda en App Service y obtiene el token de acceso del módulo de autenticación y autorización de App Service. Después, el token de acceso se pasa a las solicitudes autenticadas con Microsoft Graph API.

Para ver este código como parte de una aplicación de ejemplo, consulte el [ejemplo en GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf).

> [!NOTE]
> La biblioteca Microsoft.Identity.Web no es necesaria en la aplicación web para la autenticación o autorización básicas, ni para autenticar solicitudes con Microsoft Graph. Es posible [llamar de forma segura a las API de bajada](tutorial-auth-aad.md#call-api-securely-from-server-code) solo con el módulo de autenticación y autorización de App Service habilitado.
> 
> Sin embargo, este módulo está diseñado para escenarios de autenticación más básicos. Para escenarios más complejos (por ejemplo, el control de notificaciones personalizadas), necesita la biblioteca Microsoft.Identity.Web o la [biblioteca de autenticación de Microsoft](../active-directory/develop/msal-overview.md). Hay un poco más de trabajo de configuración al principio, pero la biblioteca Microsoft.Identity.Web se puede ejecutar junto con el módulo de autenticación y autorización de App Service. Más adelante, cuando la aplicación web necesite trabajar con escenarios más complejos, puede deshabilitar el módulo de autenticación y autorización de App Service y la biblioteca Microsoft.Identity.Web ya formará parte de la aplicación.

### <a name="install-client-library-packages"></a>Instalación de los paquetes de biblioteca cliente

Instale los paquetes NuGet [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) and [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) en el proyecto utilizando la interfaz de la línea de comandos de .NET Core o la consola del administrador de paquetes de Visual Studio.

# <a name="command-line"></a>[Línea de comandos](#tab/command-line)

Abra una línea de comandos y cambie al directorio que contiene el archivo del proyecto.

Ejecute los comandos de instalación.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Administrador de paquetes](#tab/package-manager)

Abra el proyecto o la solución en Visual Studio y abra la consola mediante **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

Ejecute los comandos de instalación.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

En el archivo *Startup.cs*, el método ```AddMicrosoftIdentityWebApp``` permite agregar Microsoft.Identity.Web a la aplicación web. El método ```AddMicrosoftGraph``` agrega compatibilidad con Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* especifica la configuración de la biblioteca Microsoft.Identity.Web. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory** en el menú del portal y, después, **Registros de aplicaciones**. Seleccione el registro de aplicaciones creado al habilitar el módulo de autenticación y autorización de App Service; este registro debe tener el mismo nombre que la aplicación web. Puede encontrar el identificador de inquilino y el identificador de cliente en la página de información general del registro de aplicaciones. El nombre de dominio se puede encontrar en la página de información general de Azure AD del inquilino.

*Graph* especifica el punto de conexión de Microsoft Graph y los ámbitos iniciales que necesita la aplicación.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

En el ejemplo siguiente se muestra cómo llamar a Microsoft Graph como el usuario con la sesión iniciada y cómo obtener la información del usuario. El objeto ```GraphServiceClient``` se ha insertado en el controlador y la biblioteca Microsoft.Identity.Web ha configurado la autenticación.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya ha terminado con este tutorial y no necesita la aplicación web ni los recursos asociados, [elimine los recursos que creó](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
>
> * Conceder permisos delegados a una aplicación web
> * Llamar a Microsoft Graph desde una aplicación web para el usuario que ha iniciado sesión

> [!div class="nextstepaction"]
> [App Service accede a Microsoft Graph como aplicación](scenario-secure-app-access-microsoft-graph-as-app.md)

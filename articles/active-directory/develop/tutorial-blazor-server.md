---
title: 'Tutorial: Creación de una aplicación Blazor Server que usa la plataforma de identidad de Microsoft para la autenticación | Azure'
titleSuffix: Microsoft identity platform
description: En este tutorial, configurará la autenticación mediante la plataforma de identidad de Microsoft en una aplicación Blazor Server.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: f7d62b6fa3523accbeba9a083c74330ff344e2ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786377"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Tutorial: Creación de una aplicación Blazor Server que usa la plataforma de identidad de Microsoft para la autenticación

En este tutorial, va a crear una aplicación de Blazor Server que inicia la sesión de usuario y obtiene datos de Microsoft Graph mediante la Plataforma de identidad de Microsoft y el registro de la aplicación en Azure Active Directory (Azure AD).

También hay disponible un tutorial para [Blazor WASM](tutorial-blazor-webassembly.md).

En este tutorial:

> [!div class="checklist"]
> * Crear una nueva aplicación Blazor Server configurada para usar Azure Active Directory (Azure AD) para la autenticación.
> * Controlar la autenticación y la autorización mediante Microsoft.Identity.Web.
> * Recuperar datos de una API web protegida, Microsoft Graph.

## <a name="prerequisites"></a>Requisitos previos

- [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Un inquilino de Azure AD donde pueda registrar una aplicación. Si no tiene acceso a un inquilino de Azure AD, puede obtener uno al registrarse con el [programa de desarrolladores de Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) o mediante la creación de una [cuenta gratuita de Azure](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registro de la aplicación en Azure Portal

Todas las aplicaciones que utilizan Azure Active Directory (Azure AD) para la autenticación deben registrarse con Azure AD. Siga las instrucciones de [Registro de una aplicación](quickstart-register-app.md) con estas adiciones:

- Para la opción **Tipos de cuenta admitidos**, seleccione **Solo las cuentas de este directorio organizativo**.
- Deje la lista desplegable **URI de redirección** establecida en **web** y escriba `https://localhost:5001/signin-oidc`. El puerto predeterminado de una aplicación que se ejecuta en Kestrel es 5001. Si la aplicación está disponible en un puerto diferente, especifique el número de puerto en lugar de `5001`.

En **Administrar**, seleccione **Autenticación** > **Implicit grant and hybrid flows** (Concesión implícita y flujos híbridos). Seleccione **Tokens de id.** y, después, **Guardar**.

Por último, dado que la aplicación llama a una API protegida (en este caso, Microsoft Graph), necesita un secreto de cliente para comprobar su identidad cuando solicita un token de acceso para llamar a esa API.

1. En el mismo registro de aplicaciones, en **Administrar**, seleccione **Certificados y secretos**.
2. Cree un **Nuevo secreto de cliente** que nunca expire.
3. Tome nota del **Valor** del secreto, ya que lo usará en el siguiente paso. No podrá acceder a este una vez que salga de este panel. Sin embargo, puede volver a crearlo si es necesario.

## <a name="create-the-app-using-the-net-cli"></a>Creación de la aplicación mediante la CLI de .NET

Ejecute el siguiente comando para descargar las plantillas de Microsoft.Identity.Web, que vamos a usar en este tutorial.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates
```

Después, ejecute el siguiente comando para crear la aplicación. Reemplace los marcadores de posición del comando por la información adecuada de la página de información general de la aplicación y ejecute el comando en un shell de comandos. La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}" --domain "{DOMAIN}"
```

| Marcador de posición   | Nombre de Azure Portal       | Ejemplo                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Id. de aplicación (cliente) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Id. de directorio (inquilino)   | `e86c78e2-0000-0000-0000-918e0565a45e` |
| `{DOMAIN}`    | Dominio principal          | `tenantname.onmicrosoft.com`           |

Ahora, vaya a la nueva aplicación Blazor en el editor, agregue el secreto de cliente al archivo *appsettings.json* y reemplace el texto "secret-from-app-registration".

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>Pruebas de la aplicación

Ya puede compilar y ejecutar la aplicación. Al ejecutar esta aplicación de plantilla, debe especificar el marco que se ejecutará con --framework. En este tutorial se usa el SDK de .NET Core 3.1.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

En el explorador, vaya a `https://localhost:5001` e inicie sesión con una cuenta de usuario de Azure AD para ver la aplicación en ejecución.

## <a name="retrieving-data-from-microsoft-graph"></a>Recuperación de datos de Microsoft Graph

[Microsoft Graph](/graph/overview) ofrece una serie de API que proporcionan acceso a los datos de Microsoft 365 de los usuarios. Al usar la plataforma de identidad de Microsoft como proveedor de identidades para la aplicación, tiene acceso más fácil a esta información, ya que Microsoft Graph admite directamente los tokens emitidos por dicha plataforma. En esta sección, agregará código para mostrar los correos electrónicos del usuario con la sesión iniciada en la página de "captura de datos" de la aplicación.

Antes de empezar, cierre la sesión de la aplicación, ya que realizará cambios en los permisos necesarios y el token actual no funcionará. Si aún no lo ha hecho, vuelva a ejecutar la aplicación y seleccione **Cerrar sesión** antes de actualizar el código siguiente.

Ahora se actualizará el registro y el código de la aplicación para extraer el correo electrónico de un usuario y mostrar los mensajes en la aplicación. Para lograrlo, extienda primero los permisos de registro de la aplicación en Azure AD para permitir el acceso a los datos del correo electrónico. A continuación, agregue el código a la aplicación Blazor para recuperar y mostrar estos datos en una de las páginas.

1. En Azure Portal, seleccione la aplicación en **Registros de aplicaciones**.
1. En **Administrar**, seleccione **Permisos de API**.
1. Seleccione **Agregar un permiso** > **Microsoft Graph**.
1. Seleccione **Permisos delegados** y, a continuación, busque y seleccione el permiso **Mail.Read**.
1. Seleccione **Agregar permisos**.

En el archivo *appsettings.json*, actualice el código para que capture el token adecuado con los permisos correctos. Agregue "mail.read" después del ámbito "user.read" en "DownstreamAPI". Esto especifica a qué ámbitos (o permisos) solicitará acceso la aplicación.

```json
"Scopes": "user.read mail.read"
```

Después, actualice el código del archivo *FetchData.razor* para recuperar los datos del correo electrónico en lugar de los detalles meteorológicos (aleatorios) predeterminados. Reemplace el código del archivo por el siguiente:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

        if (dataRequest.IsSuccessStatusCode)
        {
            var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
            userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
        }

        var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

        if (mailRequest.IsSuccessStatusCode)
        {
            var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
            var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

            foreach (var m in messagesArray)
            {
                var message = new MailMessage();
                message.Subject = m.GetProperty("subject").GetString();
                message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                messages.Add(message);
            }
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Iniciar la aplicación. Observará que se le piden los permisos recién agregados, lo que indica que todo funciona según lo previsto. Ahora, más allá de los datos básicos del perfil de usuario, la aplicación solicita acceso a los datos del correo electrónico.

Después de conceder el consentimiento, vaya a la página "Captura de datos" para leer algún correo electrónico.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Captura de pantalla de la aplicación final. Tiene un encabezado que dice &quot;Hello Nicholas&quot; y muestra una lista de correos electrónicos que pertenecen a Nicholas.":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la llamada a aplicaciones web en las que los usuarios pueden iniciar sesión en nuestra serie de tutoriales que consta de varias partes:

> [!div class="nextstepaction"]
> [Escenario: Aplicación web que permite iniciar sesión a los usuarios](scenario-web-app-sign-user-overview.md)

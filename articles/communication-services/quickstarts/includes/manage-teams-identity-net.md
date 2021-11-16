---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 717eefe0795809a9dd974ce6557d39486fbf9dd7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461190"
---
## <a name="set-up-prerequisites"></a>Configuración de requisitos previos

- La versión más reciente del [SDK de .NET](https://dotnet.microsoft.com/download/dotnet) para su sistema operativo.

## <a name="set-up"></a>Configuración

### <a name="create-a-new-c-application"></a>Creación de una aplicación de C#

En una ventana de consola (por ejemplo, cmd, PowerShell o Bash), use el comando `dotnet new` para crear una nueva aplicación de consola con el nombre `CommunicationAccessTokensQuickstart`. Este comando crea un sencillo proyecto "Hola mundo" de C# con un solo archivo de origen: **Program.cs**.

```console
dotnet new console -o CommunicationAccessTokensQuickstart
```

Cambie el directorio a la carpeta de la aplicación recién creada y use el comando `dotnet build` para compilar la aplicación.

```console
cd CommunicationAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar el paquete

En el directorio de aplicaciones, instale el paquete de la biblioteca de identidades de Azure Communication Services para .NET usando el comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity --prerelease
dotnet add package Microsoft.Identity.Client --version 4.36.2
```

### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Desde el directorio del proyecto:

1. Abra el archivo **Program.cs** en un editor de texto.
1. Agregue una directiva `using` para incluir el espacio de nombres `Azure.Communication.Identity`.
1. Actualice la declaración del método `Main` para admitir código asincrónico.

Use el código siguiente para empezar:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace CommunicationAccessTokensQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Teams Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Paso 1: Recibir el token de usuario de Azure AD mediante la biblioteca MSAL

El primer paso del flujo de intercambio de tokens es obtener un token para el usuario de Teams mediante [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```csharp
string appId = "<contoso_application_id>";
string tenantId = "<contoso_tenant_id>";
string authority = $"https://login.microsoftonline.com/{tenantId}";
string redirectUri = "http://localhost";

var aadClient = PublicClientApplicationBuilder
                .Create(appId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

string scope = "https://auth.msft.communication.azure.com/VoIP";

var teamsUserAadToken = await aadClient
                        .AcquireTokenInteractive(new List<string> { scope })
                        .ExecuteAsync();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Paso 2: Inicializar CommunicationIdentityClient

Inicialice un objeto `CommunicationIdentityClient` con su cadena de conexión. El código siguiente recupera la cadena de conexión para el recurso de una variable de entorno denominada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Aprenda a [administrar la cadena de conexión del recurso](../create-communication-resource.md#store-your-connection-string).

Agregue el código siguiente al método `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Paso 3: Intercambiar el token de usuario de Azure AD para el token de acceso de Teams

Use el método `GetTokenForTeamsUser` para emitir un token de acceso para el usuario de Teams que se puede usar con los SDK de Azure Communication Services.

```csharp
var accessToken = await client.GetTokenForTeamsUser(teamsUserAadToken.AccessToken);
Console.WriteLine($"Token: {accessToken.Value.Token}");
```

## <a name="run-the-code"></a>Ejecución del código

Ejecute la aplicación desde el directorio de la aplicación con el comando `dotnet run`.

```console
dotnet run
```

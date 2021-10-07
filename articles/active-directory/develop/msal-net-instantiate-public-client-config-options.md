---
title: Creación de una instancia para una aplicación cliente pública (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo crear una instancia de una aplicación cliente pública con opciones de configuración mediante la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 68f4437ce75bfe2a9017133ed523bb5e9ce10a8c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787137"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Crear una instancia de una aplicación cliente pública con opciones de configuración mediante MSAL.NET

En este artículo se describe cómo crear una instancia de una [aplicación cliente pública](msal-client-applications.md) mediante la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).  La instancia de la aplicación se crea con opciones de configuración definidas en un archivo de configuración.

Antes de inicializar una aplicación, primero tendrá que [registrarla](quickstart-register-app.md) para que se pueda integrar con la plataforma de identidad de Microsoft. Después del registro, es posible que necesite la información siguiente (que puede encontrar en Azure Portal):

- El identificador de cliente (una cadena que representa un GUID).
- La URL del proveedor de identidades (la instancia) y la audiencia de inicio de sesión para la aplicación. De forma conjunta, estos dos parámetros se conocen como la autoridad.
- El identificador del inquilino si va a escribir una aplicación de línea de negocio exclusivamente para la organización (también denominada aplicación de un único inquilino).
- Para las aplicaciones web y, a veces, para las aplicaciones cliente públicas (concretamente cuando la aplicación debe usar a un agente), también habrá establecido la redirectUri donde el proveedor de identidades contactará con la aplicación mediante los tokens de seguridad.

## <a name="default-reply-uri"></a>URI de respuesta predeterminado

En MSAL.NET 4.1+, el URI de redirección predeterminado (URI de respuesta) ahora se puede establecer con el método `public PublicClientApplicationBuilder WithDefaultRedirectUri()`. Este método establecerá la propiedad del URI de redirección de la aplicación cliente pública en el valor predeterminado recomendado.

El comportamiento de este método depende de la plataforma que se usa en ese momento. En esta tabla se describe qué URI de redirección se establece en determinadas plataformas:

Plataforma  | URI de redireccionamiento  
---------  | --------------
Aplicación de escritorio (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
UWP | valor de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`
.NET Core | `http://localhost`

Para la plataforma UWP, se mejora la experiencia al habilitar el inicio de sesión único con el explorador al establecer el valor en el resultado de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. 

Para .NET Core, MSAL.Net establece el valor en el host local para permitir que el usuario use el explorador del sistema para la autenticación interactiva.

> [!NOTE]
> En el caso de los exploradores insertados en escenarios de escritorio, MSAL intercepta el URI de redirección utilizado para detectar que se devuelve una respuesta del proveedor de identidades que indique que se ha devuelto un código de autenticación. Por lo tanto, este URI se puede usar en cualquier nube sin ver una redirección real a ese URI. Esto significa que puede y debe usar `https://login.microsoftonline.com/common/oauth2/nativeclient` en cualquier nube. Si lo prefiere, también puede usar cualquier otro URI siempre y cuando configure el URI de redirección correctamente con MSAL y en el registro de la aplicación. Al especificar el URI predeterminado en el registro de la aplicación, menor será la configuración necesaria en MSAL.


Una aplicación de consola .NET Core podría tener el siguiente archivo de configuración *appsettings.json*:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

El siguiente código lee este archivo mediante el marco de configuración de .NET:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

El código siguiente crea la aplicación con la configuración del archivo de configuración:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```


---
title: Proporcionar HttpClient y proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a proporcionar su propio HttpClient y proxy para conectarse a Azure AD mediante la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 054dbb46f6a13a70b5b2cebbeb1d07d29d799762
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054835"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Proporcionar su propio HttpClient y proxy mediante MSAL.NET
Al [inicializar una aplicación cliente](msal-net-initializing-client-applications.md), puede usar `.WithHttpClientFactory method` para proporcionar su propio HttpClient.  Cuando proporciona su propio HttpClient, tiene acceso a escenarios avanzados; así, puede controlar un proxy HTTP de forma pormenorizada, personalizar los encabezados de agente de usuario o forzar a que MSAL use un HttpClient específico (por ejemplo, en aplicaciones web o API de ASP.NET Core).

Está diseñado que se cree una instancia de `HttpClient` una vez y se reutilice durante todo el ciclo de vida de una aplicación. Vea [Comentarios](/dotnet/api/system.net.http.httpclient?view=net-5.0#remarks).

## <a name="initialize-with-httpclientfactory"></a>Inicialización con HttpClientFactory
En el ejemplo siguiente se muestra cómo crear un objeto `HttpClientFactory` y, luego, inicializar una aplicación cliente pública con él:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="example-implementation-using-a-proxy"></a>Implementación de ejemplo mediante un proxy
```csharp
public class HttpFactoryWithProxy : IMsalHttpClientFactory
{
    private static HttpClient _httpClient;

    public HttpFactoryWithProxy()
    {
        // Consider using Lazy<T> 
        if (_httpClient == null) 
        {
            var proxy = new WebProxy
            {
                Address = new Uri($"http://{proxyHost}:{proxyPort}"),
                BypassProxyOnLocal = false,
                UseDefaultCredentials = false,
                Credentials = new NetworkCredential(
                    userName: proxyUserName,
                    password: proxyPassword)
            };

            // Now create a client handler which uses that proxy
            var httpClientHandler = new HttpClientHandler
            {
                Proxy = proxy,
            };

            _httpClient = new HttpClient(handler: httpClientHandler);
        }
    }

    public HttpClient GetHttpClient()
    {
        return _httpClient;
    }
}
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient y Xamarin iOS
Al usar Xamarin iOS, se recomienda crear un objeto `HttpClient` que use explícitamente el controlador basado en `NSURLSession`-para iOS 7 y versiones más recientes. MSAL.NET crea automáticamente un objeto `HttpClient` que usa `NSURLSessionHandler` para iOS 7 y versiones más recientes. Para más información, lea la [documentación de Xamarin iOS para HttpClient](/xamarin/cross-platform/macios/http-stack).

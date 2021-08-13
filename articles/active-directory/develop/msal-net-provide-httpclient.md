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
ms.openlocfilehash: 4546e1531b33f2717c544a76d477f5e7d77c88ad
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110664279"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Proporcionar su propio HttpClient y proxy mediante MSAL.NET
Al [inicializar una aplicación cliente](msal-net-initializing-client-applications.md), puede usar `.WithHttpClientFactory method` para proporcionar su propio HttpClient.  Cuando proporciona su propio HttpClient, tiene acceso a escenarios avanzados; así, puede controlar un proxy HTTP de forma pormenorizada, personalizar los encabezados de agente de usuario o forzar a que MSAL use un HttpClient específico (por ejemplo, en aplicaciones web o API de ASP.NET Core).

## <a name="initialize-with-httpclientfactory"></a>Inicialización con HttpClientFactory
En el ejemplo siguiente se muestra cómo crear un objeto `HttpClientFactory` y, luego, inicializar una aplicación cliente pública con él:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient y Xamarin iOS
Al usar Xamarin iOS, se recomienda crear un objeto `HttpClient` que use explícitamente el controlador basado en `NSURLSession`-para iOS 7 y versiones más recientes. MSAL.NET crea automáticamente un objeto `HttpClient` que usa `NSURLSessionHandler` para iOS 7 y versiones más recientes. Para más información, lea la [documentación de Xamarin iOS para HttpClient](/xamarin/cross-platform/macios/http-stack).

---
title: Ámbitos para las aplicaciones de la versión 1.0 (MSAL) | Azure
description: Conozca los ámbitos para una aplicación de v1.0 mediante la biblioteca de autenticación de Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 67273a5e0c29a8a3ba7b7fdfcc3ccba2e5105c7c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429058"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Ámbitos para una API web que acepta tokens de la versión 1.0

Los permisos de OAuth2 son ámbitos de permisos que expone una aplicación de API web (recurso) de Azure Active Directory (Azure AD) para desarrolladores (v1.0) a las aplicaciones cliente. Estos ámbitos de permisos pueden concederse a las aplicaciones cliente durante el consentimiento. Consulte la sección sobre `oauth2Permissions` en la [referencia del manifiesto de aplicación de Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Ámbitos para solicitar acceso a permisos específicos de OAuth2 de una aplicación de la versión 1.0

Si va a adquirir tokens para ámbitos específicos de una aplicación v1.0 (por ejemplo, Microsoft Graph API, que es https://graph.microsoft.com), cree ámbitos mediante la concatenación de un identificador de recurso deseado con un permiso de OAuth2 deseado para dicho recurso.

Por ejemplo, para el acceso en nombre del usuario de una API web v1.0 donde es el URI de identificador de aplicación es `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Para leer y escribir con MSAL.NET Azure AD mediante Microsoft Graph API (https:\//graph.microsoft.com/), cree una lista de ámbitos, como se indica en los ejemplos siguientes:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Para escribir el ámbito correspondiente a la API de Azure Resource Manager (https:\//management.core.windows.net/), solicite el ámbito siguiente (observe las dos barras diagonales):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Use dos porque la API de Azure Resource Manager espera una barra diagonal en su notificación de la audiencia (aud), y, después, hay una barra diagonal para separar el nombre de la API del ámbito.

La lógica que usa Azure AD es la siguiente:

- Para el punto de conexión de ADAL (Azure AD v1.0) con un token de acceso de la versión 1.0 (el único posible), aud = resource.
- En el caso de MSAL (Plataforma de identidad de Microsoft) que pide un token de acceso para un recurso que acepta tokens v2.0, `aud=resource.AppId`.
- En el caso de MSAL (punto de conexión de la versión 2.0) que pide un token de acceso para un recurso que acepta tokens de la versión 1.0 (que es el caso anterior), Azure AD analiza la audiencia deseada desde el ámbito solicitado tomando todo el contenido antes de la última barra diagonal y usándolo como el identificador del recurso. Por lo tanto, si https:\//database.windows.net espera una audiencia de "htpps\//database.windows.net/", deberá solicitar un ámbito de "https:\//database.windows.net//.default". Consulte también el problema de GitHub [#747: falta la barra diagonal final de la dirección URL del recurso, lo que ha provocado un error de autenticación de SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Ámbitos para solicitar acceso a todos los permisos de una aplicación de la versión 1.0

Si quiere adquirir un token para todos los ámbitos estáticos de una aplicación v1.0, anexe ".default" al URI del identificador de aplicación de la API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Ámbitos para solicitar el flujo de credenciales de un cliente o la aplicación Daemon

En el caso de flujo de credenciales de cliente, el ámbito que se pasa también sería `/.default`. Esto indica a Azure AD: "todos los permisos de nivel de aplicación a los que el administrador ha dado su consentimiento para el registro de la aplicación".

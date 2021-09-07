---
title: Identidades de usuario en AuthN/AuthZ
description: Obtenga información sobre cómo acceder a las identidades de usuario al usar la autenticación y autorización integradas en App Service.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: ae483e4213ec17cfa3a4532fbf615a40c760ad6e
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230438"
---
# <a name="work-with-user-identities-in-azure-app-service-authentication"></a>Uso de identidades de usuario en la autenticación de Azure App Service

En este artículo se muestra cómo trabajar con identidades de usuarios mientras usa la [autenticación y autorización integradas en App Service](overview-authentication-authorization.md). 

## <a name="access-user-claims-in-app-code"></a>Acceso a notificaciones de usuario en el código de la aplicación

En todos los marcos de lenguaje, App Service hace que las notificaciones del token de entrada (tanto si proceden de un usuario final autenticado como de una aplicación cliente) estén disponibles para el código. Para ello, se insertan en los encabezados de solicitud. Las solicitudes externas no están autorizadas para establecer estos encabezados, por lo que solo están presentes si App Service los establece. A continuación puede ver algunos encabezados de ejemplo:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

El código escrito en cualquier lenguaje o plataforma puede obtener la información que necesita de estos encabezados. 

> [!NOTE]
> Los distintos marcos de lenguaje pueden presentar estos encabezados al código de la aplicación en formatos diferentes, como minúsculas o mayúsculas.

Para las aplicaciones de ASP.NET 4.6, App Service rellena [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) con las notificaciones del usuario autenticado, de forma que usted puede seguir el patrón de código de .NET estándar, incluido el atributo `[Authorize]`. De forma similar, para las aplicaciones PHP, App Service rellena la variable `_SERVER['REMOTE_USER']`. En el caso de las aplicaciones Java, se puede acceder a las notificaciones [desde el servlet Tomcat](configure-language-java.md#authenticate-users-easy-auth).

Para [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` no está relleno para el código .NET, pero todavía puede encontrar las notificaciones de usuario en los encabezados de solicitudes, u obtener el objeto `ClaimsPrincipal` del contexto de la solicitud o incluso a través de un parámetro de enlace. Consulte [Uso de identidades de cliente en Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) para más información.

Para .NET Core, [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) admite rellenar el usuario actual con la autenticación de App Service. Para más información, vea la [wiki de Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/1.2.0#integration-with-azure-app-services-authentication-of-web-apps-running-with-microsoftidentityweb) o consulte una demostración en este [tutorial para una aplicación web que accede a Microsoft Graph](./scenario-secure-app-access-microsoft-graph-as-user.md?tabs=command-line#install-client-library-packages).

## <a name="access-user-claims-using-the-api"></a>Acceso a notificaciones de usuario mediante la API

Si el [almacén de tokens](overview-authentication-authorization.md#token-store) está habilitado para la aplicación, también puede obtener detalles adicionales sobre el usuario autenticado mediante una llamada a `/.auth/me`. Los SDK del servidor de Mobile Apps proporcionan métodos de asistente para trabajar con estos datos. Para más información, consulte [Uso del SDK de Node.js de Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) y [Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Autenticación y autorización de usuarios de un extremo a otro](tutorial-auth-aad.md)
---
title: Cómo proteger una aplicación web de página única con inicio de sesión no interactivo en Microsoft Azure Maps
titleSuffix: Azure Maps
description: Procedimiento para configurar una aplicación web de página única con el control de acceso basado en rol de Azure (RBAC de Azure) no interactivo y el SDK web de Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js, subject-rbac-steps
ms.openlocfilehash: 9bf18a9122bbe8406b76cfd822cc2a5a86339a52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122868495"
---
# <a name="how-to-secure-a-single-page-web-application-with-non-interactive-sign-in"></a>Protección de una aplicación web de página única con el inicio de sesión no interactivo

En este artículo se describe cómo proteger una aplicación web de página única con Azure Active Directory (Azure AD), cuando el usuario no puede iniciar sesión en Azure AD.

Para crear este flujo de autenticación no interactivo, crearemos un servicio web seguro de Azure Functions que será responsable de adquirir tokens de acceso de Azure AD. Este servicio web estará disponible exclusivamente para la aplicación web de página única.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps puede admitir tokens de acceso de flujos interactivos o de inicio de sesión de usuario. Puede usar los flujos interactivos para lograr un ámbito más restringido de administración de secretos y revocación de acceso.

## <a name="create-an-azure-function"></a>Creación de una función de Azure

Para crear una aplicación de servicio web segura que sea responsable de la autenticación en Azure AD:

1. Cree una función en Azure Portal. Para obtener más información, consulte [Introducción a Azure Functions](../azure-functions/functions-get-started.md).

2. Configure la directiva CORS en la función de Azure para que la aplicación web de página única pueda acceder a ella. La directiva CORS protege a los clientes del explorador en los orígenes permitidos de la aplicación web. Para más información, consulte [Adición de funcionalidad CORS](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Agregue una identidad asignada por el sistema](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) en la función de Azure para habilitar la creación de una entidad de servicio para autenticarse en Azure AD.  

4. Conceda a la cuenta de Azure Maps el acceso basado en roles para la identidad asignada por el sistema. Consulte [Concesión de acceso basado en roles](#grant-role-based-access-for-users-to-azure-maps) para más información.

5. Escriba código para que la función de Azure obtenga tokens de acceso de Azure Maps mediante la identidad asignada por el sistema con uno de los mecanismos admitidos o con el protocolo de REST. Para más información, consulte [Obtención de tokens para recursos de Azure](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity).

    Este es un ejemplo de protocolo REST:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Este es un ejemplo de respuesta:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Configure la seguridad de la función de Azure HttpTrigger:

   1. [Creación de una clave de acceso de la función](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys).
   1. [Punto de conexión HTTP seguro](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) para la función de Azure en producción.

7. Configure el SDK web de Azure Maps de la aplicación web. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el escenario de aplicación de página única:
> [!div class="nextstepaction"]
> [Aplicación de página única](../active-directory/develop/scenario-spa-overview.md)

Descubra las métricas de uso de API de la cuenta de Azure Maps:
> [!div class="nextstepaction"]
> [Visualización de métricas de uso](how-to-view-api-usage.md)

Explore otros ejemplos acerca de cómo integrar Azure AD con Azure Maps:
> [!div class="nextstepaction"]
> [Ejemplos de Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)

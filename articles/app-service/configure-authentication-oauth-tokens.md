---
title: Tokens de OAuth en AuthN/AuthZ
description: Aprenda a recuperar tokens, actualizar tokens y extender sesiones cuando utilice la autenticación y autorización integradas en App Service.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f158f9f76820635a65737b75f3c016ff67a3a92a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352090"
---
# <a name="work-with-oauth-tokens-in-azure-app-service-authentication"></a>Trabajo con tokens de OAuth en la autenticación de Azure App Service

En este artículo se muestra cómo trabajar con tokens de OAuth mientras utiliza la [autenticación y autorización integradas en App Service](overview-authentication-authorization.md). 

## <a name="retrieve-tokens-in-app-code"></a>Recuperación de los tokens en el código de aplicación

Desde el código de servidor, los tokens específicos del proveedor se inyectan en el encabezado de solicitud, por lo que puede acceder a ellos fácilmente. En la siguiente tabla se muestran los posibles nombres de encabezado de token:

| Proveedor | Nombres de encabezados |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token de Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

> [!NOTE]
> Los distintos marcos de lenguaje pueden presentar estos encabezados al código de la aplicación en formatos diferentes, como minúsculas o mayúsculas.

Desde el código de cliente (por ejemplo, una aplicación móvil o JavaScript en el explorador), envíe la solicitud `GET` HTTP a `/.auth/me` (el [almacén de tokens](overview-authentication-authorization.md#token-store) debe estar habilitado). El JSON devuelto tiene los tokens específicos del proveedor.

> [!NOTE]
> Los tokens de acceso son para acceder a recursos de proveedor, por lo que solo están presentes si se configura el proveedor con un secreto de cliente. Para ver cómo obtener tokens de actualización, consulte Tokens de acceso de actualización.

## <a name="refresh-auth-tokens"></a>Actualización de tokens de autenticación

Cuando el token de acceso de su proveedor (no el [token de sesión](#extend-session-token-expiration-grace-period)) expire, debe volver a autenticar al usuario antes de volver a usar ese token. Puede evitar la expiración del token mediante la realización de una llamada `GET` al punto de conexión `/.auth/refresh` de la aplicación. Cuando se llama, App Service actualiza automáticamente los tokens de acceso en el [almacén de tokens](overview-authentication-authorization.md#token-store) para el usuario autenticado. Las solicitudes posteriores para los tokens por código de aplicación obtienen los tokens actualizados. Sin embargo, para que la actualización de token funcione, el almacén de tokens debe contener [tokens de actualización](https://auth0.com/learn/refresh-tokens/) para el proveedor. Cada proveedor documenta la manera de obtener tokens de actualización, pero en la lista siguiente se muestra un breve resumen:

- **Google**: anexe un parámetro de cadena de consulta `access_type=offline` en su llamada API `/.auth/login/google`. Si usa el SDK de Mobile Apps, puede agregar el parámetro a una de las sobrecargas `LogicAsync` (vea [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Tokens de actualización de Google)).
- **Facebook**: no proporciona tokens de actualización. Los tokens de larga duración expiran en 60 días (vea [Facebook Expiration and Extension of Access Tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) (Expiración y extensión de tokens de acceso de Facebook)).
- **Twitter**: los tokens de acceso no expiran [vea [Twitter OAuth FAQ](https://developer.twitter.com/en/docs/authentication/faq) (Preguntas más frecuentes sobre Twitter OAuth)].
- **Microsoft**: en [https://resources.azure.com](https://resources.azure.com), haga lo siguiente:
    1. En la parte superior de la página, seleccione **Lectura y escritura**.
    2. En el explorador de la izquierda, vaya a **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name>_** > **config** > **authsettingsV2**.
    3. Haga clic en **Editar**.
    4. Modifique la siguiente propiedad.

        ```json
        "identityProviders": {
          "azureActiveDirectory": {
            "login": {
              "loginParameters": ["scope=openid profile email offline_access"]
            }
          }
        }
        ```

    5. Haga clic en **Put**.
    
    > [!NOTE]
    > El ámbito que proporciona un token de actualización es [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access). Consulte como se usa en [Tutorial: Autenticación y autorización de usuarios de extremo a extremo en Azure App Service](tutorial-auth-aad.md). App Service ya ha solicitado los demás ámbitos de forma predeterminada. Para información sobre estos ámbitos predeterminados, consulte [Ámbitos de conexión de OpenID](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes).

Una vez configurado el proveedor, puede [buscar el token de actualización y el tiempo de expiración para el token de acceso](#retrieve-tokens-in-app-code) en el almacén de tokens. 

Para actualizar el token de acceso en cualquier momento, simplemente llame a `/.auth/refresh` en cualquier lenguaje. El fragmento de código siguiente utiliza jQuery para actualizar los tokens de acceso de un cliente de JavaScript.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Si un usuario revoca los permisos concedidos a la aplicación, la llamada a `/.auth/me` puede producir un error con un respuesta `403 Forbidden`. Para diagnosticar errores, compruebe los registros de aplicación para más detalles.

## <a name="extend-session-token-expiration-grace-period"></a>Ampliación del período de gracia de expiración del token de sesión

La sesión autenticada expira después de 8 horas. Después de que expire una sesión autenticada, hay un período de gracia de 72 horas de forma predeterminada. Dentro de este período de gracia, puede actualizar el token de sesión con App Service sin volver a autenticar al usuario. Simplemente puede llamar a `/.auth/refresh` cuando el token de sesión deje de ser válido y no es necesario que realice un seguimiento usted mismo de la expiración del token. Una vez que transcurra el período de gracia de 72 horas, el usuario debe iniciar sesión de nuevo para obtener un token de sesión válido.

Si 72 horas no es tiempo suficiente para usted, puede ampliar este período de expiración. Extender la expiración durante un período prolongado podría tener implicaciones de seguridad importantes (por ejemplo, cuando un token de autenticación se pierde o roba). Por tanto, debe dejarla en el valor predeterminado de 72 horas o establecer el período de extensión en el valor más pequeño.

Para extender el período de expiración predeterminado, ejecute el siguiente comando en [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> El período de gracia solo se aplica a la sesión autenticada de App Service, no a los tokens de los proveedores de identidades. No hay ningún período de gracia para los tokens del proveedor expirados. 
>

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Autenticación y autorización de usuarios de un extremo a otro](tutorial-auth-aad.md)

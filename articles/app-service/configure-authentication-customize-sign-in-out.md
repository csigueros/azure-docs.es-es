---
title: Personalización de los inicios y cierres de sesión
description: Use la autenticación y autorización integradas en App Service y, al mismo tiempo, personalice el comportamiento de inicio y cierre de sesión.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 80ad2725419db590cb470f5b7809c675090b37bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743901"
---
# <a name="customize-sign-in-and-sign-out-in-azure-app-service-authentication"></a>Personalización del inicio y cierre de sesión en la autenticación de Azure App Service

En este artículo se muestra cómo personalizar los inicios y cierres de sesión al usar la [autenticación y autorización integradas en App Service](overview-authentication-authorization.md). 

## <a name="use-multiple-sign-in-providers"></a>Uso de varios proveedores de inicio de sesión

La configuración del portal no ofrece una manera preparada para presentar varios proveedores de inicio de sesión a los usuarios (por ejemplo, Facebook y Twitter). Sin embargo, no es difícil agregar la funcionalidad a la aplicación. A continuación se describen los pasos necesarios:

Primero, en la página **Autenticación / Autorización** de Azure Portal, configure cada uno de los proveedores de identidades que desea habilitar.

En **Action to take when request is not authenticated** (Acción necesaria cuando la solicitud no está autenticada), seleccione **Allow Anonymous requests (no action)** (Permitir solicitudes anónimas (sin acción)).

En la página de inicio de sesión, en la barra de navegación o en cualquier otra ubicación de la aplicación, agregue un vínculo de inicio de sesión a cada uno de los proveedores que ha habilitado (`/.auth/login/<provider>`). Por ejemplo:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Cuando el usuario haga clic en uno de los vínculos, la página de inicio de sesión correspondiente se abrirá para que el usuario inicie sesión.

Para redirigir al usuario después del inicio de sesión a una dirección URL personalizada, use el parámetro de cadena de consulta `post_login_redirect_url` (que no debe confundirse con el URI de redireccionamiento den su configuración de proveedor de identidades). Por ejemplo, para redirigir al usuario a `/Home/Index` después de iniciar sesión, utilice el siguiente código HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="client-directed-sign-in"></a>Inicio de sesión dirigido por el cliente

En un inicio de sesión dirigido por el cliente, la aplicación inicia la sesión del usuario en el proveedor de identidades mediante un SDK específico del proveedor. Luego, el código de la aplicación envía el token de autenticación resultante a App Service para su validación (consulte [Flujo de autenticación](overview-authentication-authorization.md#authentication-flow)) mediante una solicitud HTTP POST. Los [SDK de Azure Mobile Apps](https://github.com/Azure/azure-mobile-apps) usan este flujo de inicio de sesión. Esta validación en sí misma no le otorga acceso a los recursos de aplicación que quiere, pero una validación exitosa le dará un token de sesión que puede usar para obtener acceso a los recursos de la aplicación.

Para validar el token del proveedor, la aplicación App Service debe configurarse primero con el proveedor que quiera usar. En el entorno de ejecución, después de recuperar el token de autenticación de su proveedor, publique el token en `/.auth/login/<provider>` para su validación. Por ejemplo:

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

El formato del token varía ligeramente según el proveedor. Consulte la tabla siguiente para más detalles:

| Valor del proveedor | Necesario en el cuerpo de la solicitud | Comentarios |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | Las propiedades `id_token`, `refresh_token` y `expires_in`son opcionales. |
| `microsoftaccount` | `{"access_token":"<access_token>"}` o `{"authentication_token": "<token>"`| `authentication_token` se prefiere a `access_token`. La propiedad `expires_in` es opcional. <br/> Al solicitar el token de los servicios Live, solicite siempre el ámbito `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | La propiedad `authorization_code` es opcional. Si se proporciona un valor de `authorization_code`, se agregarán un token de acceso y un token de actualización al almacén de tokens. Cuando se especifica, `authorization_code` también puede ir acompañado opcionalmente por una propiedad `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Use un [token de acceso de usuario](https://developers.facebook.com/docs/facebook-login/access-tokens) válido de Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Si el token del proveedor se valida correctamente, la API regresa con un elemento `authenticationToken` en el cuerpo de la respuesta, que es su token de sesión. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Una vez que tenga este token de sesión, puede obtener acceso a los recursos de aplicación protegidos agregando el encabezado `X-ZUMO-AUTH` a sus solicitudes HTTP. Por ejemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Cierre de sesión de una sesión

Los usuarios pueden iniciar un cierre de sesión mediante el envío de una solicitud `GET` al punto de conexión de la aplicación `/.auth/logout`. La solicitud `GET` hace lo siguiente:

- Borra las cookies de autenticación de la sesión actual.
- Elimina los tokens del usuario actual del almacén de tokens.
- Para Azure Active Directory y Google, realiza un cierre de sesión del servidor en el proveedor de identidades.

A continuación, se muestra un vínculo de cierre de sesión simple en una página web:

```html
<a href="/.auth/logout">Sign out</a>
```

De forma predeterminada, un cierre de sesión correcto redirige el cliente a la dirección URL `/.auth/logout/done`. Puede cambiar la página de redirección del inicio de sesión posterior agregando el parámetro de consulta `post_logout_redirect_uri`. Por ejemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Se recomienda [codificar](https://wikipedia.org/wiki/Percent-encoding) el valor de `post_logout_redirect_uri`.

Al utilizar direcciones URL completas, la dirección URL debe hospedarse en el mismo dominio o configurarse como una dirección URL de redirección externa permitida para la aplicación. En el ejemplo siguiente, para redirigir a `https://myexternalurl.com` que no se hospedado en el mismo dominio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Ejecute el siguiente comando en [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Conservación de los fragmentos de dirección URL

Después de que los usuarios inician sesión en la aplicación, normalmente desean redirigirse a la misma sección de la misma página, como `/wiki/Main_Page#SectionZ`. Sin embargo, dado que los [fragmentos de dirección URL](https://wikipedia.org/wiki/Fragment_identifier) (por ejemplo, `#SectionZ`) nunca se envían al servidor, no se conservan de forma predeterminada después de que el inicio de sesión de OAuth se complete y se redirija a la aplicación. Los usuarios obtienen una experiencia poco óptima cuando necesitan volver a navegar al delimitador deseado. Esta limitación se aplica a todas las soluciones de autenticación del servidor.

En la autenticación de App Service, puede conservar los fragmentos de dirección URL en el inicio de sesión de OAuth. Para ello, establezca un valor de aplicación denominado `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` en `true`. Puede hacerlo el [Azure Portal](https://portal.azure.com), o simplemente ejecute el siguiente comando en [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitación del dominio de cuentas de inicio de sesión

Cuenta Microsoft y Azure Active Directory le permiten iniciar sesión desde varios dominios. Por ejemplo, Cuenta Microsoft permite cuentas de _outlook.com_, _live.com_ y _hotmail.com_. Azure AD permite cualquier número de dominios personalizados para las cuentas de inicio de sesión. Sin embargo, puede que quiera acelerar el proceso para que los usuarios accedan directamente a su propia página de inicio de sesión personalizada de Azure AD (por ejemplo, `contoso.com`). Para sugerir el nombre de dominio de las cuentas de inicio de sesión, siga estos pasos.

En [https://resources.azure.com](https://resources.azure.com), vaya a **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name> _** > **config** > **authsettings**. 

Haga clic en **Editar**, modifique la propiedad siguiente y luego haga clic en **Put**. Asegúrese de reemplazar _\<domain\_name>_ por el dominio que desee.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Esta configuración anexa el parámetro de cadena de consulta `domain_hint` a la dirección URL de redireccionamiento de inicio de sesión. 

> [!IMPORTANT]
> Es posible que el cliente quite el parámetro `domain_hint` después de recibir la dirección URL de redireccionamiento y, a continuación, inicie sesión con otro dominio. Por lo tanto, aunque esta función es cómoda, no es una característica de seguridad.
>

## <a name="authorize-or-deny-users"></a>Autorización o denegación de usuarios

Si bien App Service se encarga del caso de autorización más sencillo (es decir, rechazar las solicitudes no autenticadas), es posible que la aplicación requiera un comportamiento de autorización más específico, como limitar el acceso a un solo grupo específico de usuarios. En algunos casos, debe escribir código de aplicación personalizado para permitir o denegar el acceso al usuario que inició sesión. En otros casos, App Service o el proveedor de identidades pueden ayudar sin necesidad de realizar cambios en el código.

- [Nivel de servidor](#server-level-windows-apps-only)
- [Nivel de proveedor de identidades](#identity-provider-level)
- [Nivel de aplicación](#application-level)

### <a name="server-level-windows-apps-only"></a>Nivel de servidor (solo aplicaciones de Windows)

En cualquier aplicación de Windows, puede definir el comportamiento de la autorización del servidor web de IIS editando el archivo *Web.config*. Las aplicaciones de Linux no usan IIS y no se pueden configurar a través de *Web. config*.

1. Vaya a `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. En el explorador de archivos de App Service, vaya a *site/wwwroot*. Si no existe un archivo *Web.config*, créelo seleccionando **+**  > **Nuevo archivo**. 

1. Seleccione el lápiz de *Web. config* para editarlo. Agregue el código de configuración siguiente y haga clic en **Guardar**. Si ya existe *Web.config*, simplemente agregue el elemento `<authorization>` con todo lo que contiene. Agregue las cuentas que desea permitir al elemento `<allow>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Nivel de proveedor de identidades

El proveedor de identidades puede proporcionar cierta autorización llave en mano. Por ejemplo:

- Para [Azure App Service](configure-authentication-provider-aad.md), puede [administrar el acceso de nivel empresarial](../active-directory/manage-apps/what-is-access-management.md) directamente en Azure AD. Para obtener instrucciones, consulte [Cómo quitar el acceso de un usuario a una aplicación](../active-directory/manage-apps/methods-for-removing-user-access.md).
- En el caso de [Google](configure-authentication-provider-google.md), los proyectos de la API de Google que pertenecen a una [organización](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) se pueden configurar para permitir el acceso solo a los usuarios de su organización (consulte la [página de soporte técnico de **configuración de OAuth 2.0** de Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Nivel de aplicación

Si alguno de los otros niveles no proporciona la autorización que necesita, o si no se admite la plataforma o el proveedor de identidades, debe escribir código personalizado para autorizar a los usuarios en función de las [notificaciones de usuario](configure-authentication-user-identities.md).

## <a name="more-resources"></a>Más recursos

- [Tutorial: Autenticación y autorización de usuarios de un extremo a otro](tutorial-auth-aad.md)
- [Variables de entorno y configuración de la aplicación para la autenticación](reference-app-settings.md#authentication--authorization)
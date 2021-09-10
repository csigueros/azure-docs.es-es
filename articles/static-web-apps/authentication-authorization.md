---
title: Autenticación y autorización para Azure Static Web Apps
description: Aprenda a usar distintos proveedores de autorización para proteger su aplicación estática.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 1ba278b80d75e64b4394bfeb1b98cd0ec2d805a8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469964"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps"></a>Autenticación y autorización para Azure Static Web Apps

Azure Static Web Apps proporciona una experiencia de autenticación simplificada. De forma predeterminada, tiene acceso a una serie de proveedores preconfigurados o a la opción de [registrar un proveedor personalizado](./authentication-custom.md).

- Cualquier usuario puede autenticarse con un proveedor habilitado.
- Una vez iniciada la sesión, los usuarios pertenecen a los roles `anonymous` y `authenticated` de forma predeterminada.
- Los usuarios autorizados obtienen acceso a [rutas](configuration.md#routes) restringidas mediante reglas definidas en el [archivo staticwebapp.config.json](./configuration.md).
- Los usuarios se unen a los roles personalizados a través de [invitaciones](#invitations) específicas del proveedor o a través de un [registro de proveedor de Azure Active Directory personalizado](./authentication-custom.md).
- Todos los proveedores de autenticación están habilitados de forma predeterminada.
  - Para restringir un proveedor de autenticación, [bloquee el acceso](#block-an-authorization-provider) con una regla de ruta personalizada.
- Estos son los proveedores preconfigurados:
  - Azure Active Directory
  - GitHub
  - Twitter

Los temas de autenticación y autorización se superponen significativamente con los conceptos de enrutamiento, que se detallan en la [guía de configuración de la aplicación](configuration.md#routes).

## <a name="roles"></a>Roles

Cada usuario que tiene acceso a una aplicación web estática pertenece a uno o varios roles. Existen dos roles integrados a los que los usuarios pueden pertenecer:

- **anónimo**: Todos los usuarios pertenecen automáticamente al rol _anónimo_.
- **autenticado**: Todos los usuarios que inician sesión pertenecen al rol _autenticado_.

Además de los roles integrados, puede crear nuevos roles, asignarlos a los usuarios a través de invitaciones y hacer referencia a ellos en el archivo _staticwebapp.config.json_.

## <a name="role-management"></a>Administración de roles

### <a name="add-a-user-to-a-role"></a>Adición de un usuario a un rol

Para agregar un usuario a un rol, genere invitaciones que le permitan asociar usuarios a roles concretos. Los roles se definen y mantienen en el archivo _staticwebapp.config.json_.

> [!NOTE]
> Puede optar por [registrar un proveedor de Azure Active Directory personalizado](./authentication-custom.md) para evitar la emisión de invitaciones para la administración de grupos.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Creación de una invitación

Las invitaciones son específicas de los proveedores de autorización individuales; por lo tanto, tenga en cuenta las necesidades de la aplicación al seleccionar los proveedores que se van a admitir. Algunos proveedores exponen la dirección de correo electrónico de los usuarios, mientras que otros solo proporcionan el nombre de usuario del sitio.

<a name="provider-user-details" id="provider-user-details"></a>

| Proveedor de autorización | Con respecto al usuario, expone |
| ---------------------- | ---------------- |
| Azure Active Directory | Dirección de correo electrónico    |
| GitHub                 | username         |
| Twitter                | username         |

1. Vaya a un recurso de Static Web Apps en [Azure Portal](https://portal.azure.com).
1. En _Configuración_, haga clic en **Administración de roles**.
1. Haga clic en el botón **Invitar**.
1. Seleccione un _Proveedor de autorización_ de la lista de opciones.
1. Agregue el nombre de usuario o la dirección de correo electrónico del destinatario en el cuadro _Invitee details_ (Detalles del invitado).
   - En GitHub y Twitter, escriba el nombre de usuario. Para todos los demás, escriba la dirección de correo electrónico del destinatario.
1. Seleccione el dominio del sitio estático de la lista desplegable _Dominio_.
   - El dominio que seleccione será el dominio que aparezca en la invitación. Si tiene un dominio personalizado asociado a su sitio, es probable que desee elegir el dominio personalizado.
1. Agregue una lista separada por comas de nombres de rol en el cuadro _Rol_.
1. Especifique el número máximo de horas que desea que la invitación siga siendo válida.
   - El límite máximo posible es de 168 horas, es decir, 7 días.
1. Haga clic en el botón **Generate** (Generar).
1. Copie el vínculo del cuadro _Vínculo de invitación_.
1. Envíe el vínculo de la invitación por correo electrónico a la persona a la que va a conceder acceso a su aplicación.

Cuando el usuario haga clic en el vínculo de la invitación, se le pedirá que inicie sesión con su cuenta correspondiente. Una vez que inicie sesión correctamente, se asocian al usuario los roles seleccionados.

> [!CAUTION]
> Asegúrese de que las reglas de ruta no entren en conflicto con los proveedores de autenticación seleccionados. Bloquear un proveedor con una regla de ruta impedirá que los usuarios acepten invitaciones.

### <a name="update-role-assignments"></a>Actualización de las asignaciones de roles

1. Vaya a un recurso de Static Web Apps en [Azure Portal](https://portal.azure.com).
1. En _Configuración_, haga clic en **Administración de roles**.
1. Haga clic en el usuario en la lista.
1. Edite la lista de roles en el cuadro _Rol_.
1. Haga clic en el botón **Actualizar**.

### <a name="remove-user"></a>Quitar usuario

1. Vaya a un recurso de Static Web Apps en [Azure Portal](https://portal.azure.com).
1. En _Configuración_, haga clic en **Administración de roles**.
1. Busque el usuario en la lista.
1. Active la casilla en la fila del usuario.
1. Haga clic en el botón **Eliminar** .

Al quitar un usuario, tenga en cuenta los elementos siguientes:

1. Al quitar un usuario, se invalidan sus permisos.
1. La propagación mundial puede tardar unos minutos.
1. Si se vuelve a agregar el usuario a la aplicación, [`userId` cambia](user-information.md).

## <a name="remove-personal-identifying-information"></a>Eliminación de la información de identificación personal

Al conceder consentimiento a una aplicación como usuario final, la aplicación tiene acceso a su dirección de correo electrónico o a su nombre de usuario, según el proveedor de identidades. Una vez que se proporcione esta información, el propietario de la aplicación decidirá cómo administrar la información de identificación personal.

Los usuarios finales deben ponerse en contacto con los administradores de las aplicaciones web individuales para revocar esta información de sus sistemas.

Para quitar información de identificación personal de la plataforma de Azure Static Web Apps e impedir que la plataforma proporcione esta información en futuras solicitudes, envíe una solicitud mediante la dirección URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Para evitar que la plataforma proporcione esta información en futuras solicitudes a aplicaciones individuales, envíe una solicitud a la siguiente dirección URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Tenga en cuenta que si usa Azure Active Directory, use `aad` como valor para el marcador de posición `<AUTHENTICATION_PROVIDER_NAME>`.

## <a name="system-folder"></a>Carpeta del sistema

Azure Static Web Apps usa la carpeta del sistema `/.auth` para proporcionar acceso a las API relacionadas con la autorización. En lugar de exponer las rutas de la carpeta `/.auth` directamente a los usuarios finales, piense en la posibilidad de crear [reglas de enrutamiento](configuration.md#routes) para crear direcciones URL descriptivas.

## <a name="login"></a>Inicio de sesión

Use la siguiente tabla para buscar la ruta específica de los proveedores.

| Proveedor de autorización | Ruta de inicio de sesión             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| GitHub                 | `/.auth/login/github`   |
| Twitter                | `/.auth/login/twitter`  |

Por ejemplo, para iniciar sesión con GitHub, podría incluir un vínculo como el siguiente fragmento de código:

```html
<a href="/.auth/login/github">Login</a>
```

Si decide admitir más de un proveedor, debe exponer un vínculo específico del proveedor para cada uno en su sitio web.

Puede usar una [regla de ruta](./configuration.md#routes) para asignar un proveedor predeterminado a una ruta descriptiva, como _/login_.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Redireccionamiento posterior al inicio de sesión

Si quiere que un usuario vuelva a una página específica después de iniciar sesión, proporcione una dirección URL completa en el parámetro de cadena de consulta `post_login_redirect_uri`.

Por ejemplo:

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Logout

La ruta `/.auth/logout` cierra la sesión de los usuarios en el sitio web. Puede agregar un vínculo a la navegación del sitio para permitir que el usuario cierre la sesión, tal y como se muestra en el ejemplo siguiente.

```html
<a href="/.auth/logout">Log out</a>
```

Puede usar una [regla de ruta](./configuration.md#routes) para asignar una ruta descriptiva, como _/logout_.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Redireccionamiento posterior al cierre de sesión

Si quiere que un usuario vuelva a una página específica después de cerrar sesión, proporcione una dirección URL en el parámetro de cadena de consulta `post_logout_redirect_uri`.

## <a name="block-an-authorization-provider"></a>Bloqueo de un proveedor de autorización

Puede que desee evitar que la aplicación use un proveedor de autorización. Por ejemplo, es posible que quiera estandarizar la aplicación para únicamente en los [proveedores que exponen las direcciones de correo electrónico](#provider-user-details).

Para bloquear un proveedor, puede crear [reglas de ruta](configuration.md#routes) para devolver un error 404 para las solicitudes a la ruta específica del proveedor bloqueado. Por ejemplo, para restringir Twitter como proveedor, agregue la regla de ruta siguiente.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Restricciones

Vea el [artículo sobre cuotas](quotas.md) para conocer las restricciones y limitaciones generales.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Acceso a datos de autenticación y autorización de usuarios](user-information.md)

<sup>1</sup> Pendiente de certificación externa.

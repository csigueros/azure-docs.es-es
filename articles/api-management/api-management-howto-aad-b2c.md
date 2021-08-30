---
title: Autorización de cuentas de desarrollador con Azure Active Directory B2C
titleSuffix: Azure API Management
description: Aprenda a autorizar a los usuarios del portal para desarrolladores de Azure API Management mediante Azure Active Directory B2C.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 07/07/2021
ms.author: apimpm
ms.openlocfilehash: 6385f87eec00820d535845df21a44965e1708a55
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568235"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory B2C en Azure API Management


Azure Active Directory B2C es una solución de administración de identidades en la nube, destinada a aplicaciones móviles y web orientadas al consumidor. Puede usarla para administrar el acceso al portal para desarrolladores de API Management. 

Esta guía le muestra la configuración que se necesita en el servicio de API Management para integrarse con Azure Active Directory B2C. Si usa el portal para desarrolladores heredado ya en desuso, algunos pasos difieren, como se indica en este artículo.

Para más información sobre cómo habilitar el acceso al portal para desarrolladores con Azure Active Directory clásico, consulte [Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory](api-management-howto-aad.md).

## <a name="prerequisites"></a>Requisitos previos

* Un inquilino de Azure Active Directory B2C en el que crear una aplicación. Para más información, consulte [Introducción a Azure Active Directory B2C](../active-directory-b2c/overview.md).
* Si no tiene una instancia del servicio API Management, realice el tutorial siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="configure-sign-up-and-sign-in-user-flow"></a>Configuración del flujo de usuario de registro e inicio de sesión

En esta sección, creará un flujo de usuario en el inquilino de Azure Active Directory B2C que contenga directivas de registro e inicio de sesión. Para ver los pasos detallados, consulte [Creación de flujos de usuario y directivas personalizadas en Azure Active Directory B2C](../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-us).

1. En [Azure Portal](https://portal.azure.com), acceda al inquilino de Azure Active Directory B2C.
1. En **Directivas**, seleccione **Flujos de usuario** >  **+ Nuevo flujo de usuario**.
1. En la página **Crear un flujo de usuario**, seleccione el flujo de usuario **Registrarse e iniciar sesión**.
1. Proporcione la siguiente información:
    1. Especifique un nombre único para el flujo de usuario.
    1. En **Proveedores de identidades**, seleccione **Registro por correo electrónico**.
    1. En **Atributos de usuario y notificaciones de token**, seleccione los atributos y notificaciones necesarios para el portal para desarrolladores de API Management (no es necesario para el portal para desarrolladores heredado).
         ![Notificaciones de la aplicación](./media/api-management-howto-aad-b2c/api-management-application-claims.png)
        * **Atributos**: nombre dado, apellido
        * **Notificaciones**: direcciones de correo electrónico, nombre dado, apellido, objectID del usuario
1. Seleccione **Crear**.

## <a name="configure-identity-provider-for-developer-portal"></a>Configuración del proveedor de identidades para el portal para desarrolladores

1. En una pestaña distinta de [Azure Portal](https://portal.azure.com), vaya a la instancia de API Management.
1. En **Portal para desarrolladores**, seleccione **Identidades** >  **+ Agregar**.
1. En la página **Agregar proveedor de identidades**, seleccione **Azure Active Directory B2C**.
1. En la ventana **Agregar proveedor de identidades**, copie el valor de **URL de direccionamiento**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-identity-provider-redirect-url.png" alt-text="Copia de la URL de redireccionamiento":::    

1. Vuelva a la pestaña del explorador del inquilino de Azure Active Directory B2C en Azure Portal. Seleccione **Registros de aplicaciones** >   **+ Nuevo registro**.
1. Cuando aparezca la página **Registrar una aplicación**, escriba la información de registro de la aplicación.
    * En la sección **Nombre**, escriba un nombre de aplicación de su elección.
    * En la sección **Tipos de cuenta admitidos**, elija el tipo de cuenta que sea adecuado para su escenario. Para dirigirse a un conjunto amplio de clientes, seleccione **Accounts in any identity provider or organizational directory (for authenticating users with user flows)** (Cuentas de cualquier proveedor de identidades o directorio de organización [para autenticar usuarios con flujos de usuario]). Para más información, consulte [Registro de una aplicación](../active-directory/develop/quickstart-register-app.md#register-an-application).
    * En **URI de redirección**, escriba la dirección URL de redireccionamiento que copió de la instancia de API Management.
    * En **Permisos**, seleccione **Conceda consentimiento del administrador a los permisos openid y offline_access**.
    * Seleccione **Registrar** para crear la aplicación.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-registration.png" alt-text="Registro de una nueva aplicación":::

1. En la página **Información general** de la aplicación, busque el valor de **Id. de aplicación (cliente)** y cópielo en el portapapeles.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-id.png" alt-text="Identificador de la aplicación":::
1. Vuelva al panel **Agregar proveedor de identidades** de API Management y pegue el identificador en el cuadro de texto **Id. de cliente**.
1. Vuelva al cambiar al registro de la aplicación B2C. Seleccione **Certificados y secretos** >  y, después, **+ Nuevo secreto de cliente**. 
    :::image type="content" source="media/api-management-howto-aad-b2c/generate-app-key.png" alt-text="Creación de un secreto de cliente"::: 
   * En la página **Add a client secret** (Agregar un secreto de cliente), rellene el campo **Descripción** y seleccione **Agregar**.
   * Anote la clave en un lugar seguro. Este valor secreto no se volverá a mostrar una vez que abandone esta página.
1. Vuelva a cambiar al panel **Agregar proveedor de identidades** de API Management y pegue la clave en el cuadro de texto **Secreto de cliente**.
1. Vuelva al cambiar al registro de la aplicación B2C. En el menú izquierdo, en **Administrar**, seleccione **Autenticación**.
    * En **Concesión implícita**, active la casilla **Tokens de acceso**.
    * Seleccione **Guardar**.
1. Vuelva a cambiar a la página **Agregar proveedor de identidades** de API Management.
    * En **Inquilino de inicio de sesión**, especifique el nombre de dominio del inquilino de Azure Active Directory B2C.
    * El campo **Autoridad** le permite controlar la dirección URL de inicio de sesión de Azure AD B2C que se usará. Establezca el valor en **<nombre_de_su_inquilino_de_b2c>. b2clogin.com**.
    * Especifique la **directiva de registro** y la **directiva de inicio de sesión** en las directivas del inquilino de B2C.
    * Si lo desea, proporcione la **directiva de edición de perfil** y la **directiva de restablecimiento de contraseña**.

         :::image type="content" source="media/api-management-howto-aad-b2c/add-identity-provider.png" alt-text="Configuración del proveedor de identidades de Active Directory B2C":::
1. Después de especificar la configuración deseada, seleccione **Guardar**.

Una vez que se guardan los cambios, los desarrolladores podrán crear cuentas nuevas e iniciar sesión en el portal para desarrolladores con Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal para desarrolladores: incorporación de autenticación de cuenta de Azure AD B2C

> [!IMPORTANT]
> Debe [volver a publicar el portal para desarrolladores](api-management-howto-developer-portal-customize.md#publish) al crear o actualizar la configuración de Azure Active Directory B2C para que los cambios surtan efecto.

En el portal para desarrolladores, inicie sesión con Azure AD B2C con el widget **Botón de inicio de sesión: OAuth**. El widget ya está incluido en la página de inicio de sesión del contenido predeterminado del portal para desarrolladores.

1. Para iniciar sesión mediante Azure Active Directory B2C, abra una nueva ventana del explorador y vaya al portal para desarrolladores. Haga clic en **Iniciar sesión**.

1. En la página **Iniciar sesión**, seleccione **Azure Active Directory B2C**.

    :::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-sign-in.png" alt-text="Inicio de sesión en el portal para desarrolladores":::
1. Se le redirigirá a la directiva de registro que configuró en la sección anterior. Elija registrarse con su dirección de correo electrónico en el inquilino de Active Directory B2C.

Una vez completado el registro, se le redirigirá de nuevo al portal para desarrolladores. Ahora ya inició sesión en el portal para desarrolladores de la instancia del servicio API Management.

:::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-home.png" alt-text="Inicio de sesión en el portal para desarrolladores finalizado":::.

Aunque se crea automáticamente una cuenta cada vez que un nuevo usuario inicia sesión con Azure AD B2C, puede considerar la idea de agregar el mismo widget a la página de registro.

El widget **Sign-up form: OAuth** (Formulario de inicio de sesión: OAuth) representa un formulario que se utiliza para registrarse con OAuth.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Portal para desarrolladores heredado: registro con Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Para registrar una cuenta de desarrollador con Azure AD B2C, abra una nueva ventana del explorador y vaya al portal para desarrolladores heredado. Haga clic en el botón **Registrarse**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal.png" alt-text="Registro en el portal para desarrolladores heredado":::
1. Elija registrarse con **Azure Active Directory B2C**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-button.png" alt-text="Registro con Azure Active Directory B2C":::

3. Se le redirigirá a la directiva de registro que configuró en la sección anterior. Elija registrarse con la dirección de correo electrónico o una de sus cuentas sociales existentes.

   > [!NOTE]
   > Si Azure Active Directory B2C es la única opción habilitada en la pestaña **Identidades** del portal para editores, se le redirigirá a la directiva de registro de forma directa.

   :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-options.png" alt-text="Opciones de registro en el portal para desarrolladores heredado":::

   Una vez completado el registro, se le redirigirá de nuevo al portal para desarrolladores. Ahora ya inició sesión en el portal para desarrolladores de la instancia del servicio API Management.

## <a name="next-steps"></a>Pasos siguientes

*  [Introducción a Azure Active Directory B2C]
*  [Azure Active Directory B2C: marco de directivas extensible]
*  [Uso de una cuenta Microsoft como proveedor de identidades en Azure Active Directory B2C]
*  [Uso de una cuenta de Google como proveedor de identidades en Azure Active Directory B2C]
*  [Uso de una cuenta de LinkedIn como proveedor de identidades en Azure Active Directory B2C]
*  [Uso de una cuenta de Facebook como proveedor de identidades en Azure Active Directory B2C]


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Introducción a Azure Active Directory B2C]: ../active-directory-b2c/overview.md
[How to authorize developer accounts using Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C: marco de directivas extensible]: ../active-directory-b2c/user-flow-overview.md
[Uso de una cuenta Microsoft como proveedor de identidades en Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Uso de una cuenta de Google como proveedor de identidades en Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Uso de una cuenta de Facebook como proveedor de identidades en Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Uso de una cuenta de LinkedIn como proveedor de identidades en Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

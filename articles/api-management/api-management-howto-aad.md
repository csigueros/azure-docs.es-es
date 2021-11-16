---
title: Autorización de cuentas de desarrollador con Azure Active Directory
titleSuffix: Azure API Management
description: Aprenda a autorizar a los usuarios mediante Azure Active Directory en API Management.
services: api-management
documentationcenter: API Management
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/20/2021
ms.author: danlep
ms.openlocfilehash: fd45ad3a77ddfe9bea46c9c816b67be0c2cf1fac
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579167"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorización de las cuentas de desarrollador mediante Azure Active Directory en Azure API Management

En este artículo, aprenderá a:
> [!div class="checklist"]
> * Habilite el acceso al portal para desarrolladores para usuarios de Azure Active Directory (Azure AD).
> * Administre grupos de usuarios de Azure AD agregando grupos externos que contengan a dichos usuarios.

## <a name="prerequisites"></a>Prerrequisitos

- Complete el inicio rápido [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
- [Importe y publique](import-and-publish.md) una instancia de Azure API Management.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorización de las cuentas de desarrollador con Azure AD

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
1. Seleccionar ![Icono de flecha.](./media/api-management-howto-aad/arrow.png).
1. Busque y seleccione **Servicios API Management**.
1. Seleccione su instancia de servicio API Management.
1. En **Portal para desarrolladores**, seleccione **Identidades**.
1. Seleccione **+Agregar** en la parte superior para abrir el panel **Agregar proveedor de identidades** a la derecha.
1. En **Tipo**, seleccione **Azure Active Directory** en el menú desplegable.
    * Una vez seleccionado, podrá especificar otra información necesaria, 
    * como **Id. de cliente** y **Secreto de cliente**. 
    * Más adelante en el artículo obtendrá información sobre estos controles.
1. Guarde la **URL de redireccionamiento** para más adelante.
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Agregar un proveedor de identidades en Azure Portal":::

    > [!NOTE]
    > Hay dos URL de redireccionamiento:<br/>
    > * **URL de redireccionamiento** apunta al portal para desarrolladores más reciente de API Management.
    > * **URL de redireccionamiento (portal en desuso)** apunta al portal para desarrolladores en desuso de API Management.
    >
    > Se recomienda usar la URL de redireccionamiento del portal para desarrolladores más reciente.
   
1. En el explorador, abra Azure Portal en una nueva pestaña. 
1. Navegue hasta [Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar una aplicación en Active Directory.
1. Seleccione **Nuevo registro**. En la página **Registrar una aplicación**, establezca los valores de la manera siguiente:
    
    * Establezca **Nombre** en un nombre con sentido; por ejemplo, *portal-desarrollador*
    * Establezca **Tipos de cuenta admitidos** en **Solo las cuentas de este directorio organizativo**. 
    * Establezca **URI de redireccionamiento** en el valor guardado en el paso 9. 
    * Seleccione **Registrar**. 

1.  Una vez registrada la aplicación, copie el **Id. de aplicación (cliente)** de la página **Información general**. 
1. Cambie a la pestaña del explorador con su instancia de API Management. 
1. En la ventana **Agregar proveedor de identidades**, pegue el valor de **Id. de aplicación (cliente)** en el cuadro **Id. de cliente**.
1. Cambie a la pestaña del explorador con el registro de aplicación.
1. Seleccione el registro de aplicación correspondiente.
1. En la sección **Administrar** del menú lateral, seleccione **Certificados y secretos**. 
1. En la página **Certificados y secretos**, seleccione el botón **Nuevo secreto de cliente** en **Secretos de cliente**. 
    * Escriba una **Descripción**.
    * Seleccione cualquier opción para **Expiración**.
    * Seleccione **Agregar**. 
1. Copie el **valor del secreto** del cliente antes de salir de la página. Lo necesitará más adelante. 
1. En la sección **Administrar** del menú lateral, seleccione **Autenticación**.
1. En la sección **Flujos de concesión implícita e híbridos**, seleccione **Tokens de id.**
1. Cambie a la pestaña del explorador con su instancia de API Management. 
1. Pegue el secreto en el campo **Secreto de cliente** del panel **Agregar proveedor de identidades**.

    > [!IMPORTANT]
    > Actualice el **secreto de cliente** antes de que expire la clave. 

1. En el campo **Inquilinos permitidos** del panel **Agregar proveedor de identidades**, especifique los dominios de instancias de Azure AD a los que quiera conceder acceso a las API de instancia del servicio API Management. 
    * Puede separar varios dominios mediante nuevas líneas, espacios o comas.

    > [!NOTE]
    > Puede especificar varios dominios en la sección **Allowed Tenants** (Inquilinos permitidos). Una administración global debe conceder a la aplicación acceso a datos de directorio antes de que los usuarios puedan iniciar sesión desde un dominio diferente al de registro de aplicación original. Para conceder permiso, el administrador global debe:
    > 1. Ir a `https://<URL of your developer portal>/aadadminconsent` (por ejemplo, `https://contoso.portal.azure-api.net/aadadminconsent`).
    > 1. Escribir el nombre de dominio del inquilino de Azure AD al que quiera conceder acceso.
    > 1. Seleccione **Submit** (Enviar). 

1.  Después de especificar la configuración deseada, seleccione **Agregar**.

Una vez guardados los cambios, los usuarios de la instancia de Azure AD especificada pueden [iniciar sesión en el portal para desarrolladores mediante una cuenta de Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Incorporación de un grupo externo de Azure AD

Ahora que ha habilitado el acceso para los usuarios en un inquilino de Azure AD, puede hacer lo siguiente:
* Agregar grupos de Azure AD a API Management. 
* Controlar la visibilidad del producto usando grupos de Azure AD.

Siga estos pasos para conceder lo siguiente:
* Permiso de aplicación `Directory.Read.All` para Microsoft Graph API y Azure Active Directory Graph API.
* Permiso delegado `User.Read` para Microsoft Graph API. 

1. Actualice las tres primeras líneas del siguiente script de PowerShell para que coincidan con su entorno y ejecutarlo. 
   ```powershell
   $subId = "Your Azure subscription ID" #e.g. "1fb8fadf-03a3-4253-8993-65391f432d3a"
   $tenantId = "Your Azure AD Tenant or Organization ID" #e.g. 0e054eb4-e5d0-43b8-ba1e-d7b5156f6da8"
   $appObjectID = "Application Object ID that has been registered in AAD" #e.g. "2215b54a-df84-453f-b4db-ae079c0d2619"
   #Login and Set the Subscription
   az login
   az account set --subscription $subId
   #Assign the following permissions: Microsoft Graph Delegated Permission: User.Read, Microsoft Graph Application Permission: Directory.ReadAll,  Azure Active Directory Graph Application Permission: Directory.ReadAll (legacy)
   az rest --method PATCH --uri "https://graph.microsoft.com/v1.0/$($tenantId)/applications/$($appObjectID)" --body "{'requiredResourceAccess':[{'resourceAccess': [{'id': 'e1fe6dd8-ba31-4d61-89e7-88639da4683d','type': 'Scope'},{'id': '7ab1d382-f21e-4acd-a863-ba3e13f7da61','type': 'Role'}],'resourceAppId': '00000003-0000-0000-c000-000000000000'},{'resourceAccess': [{'id': '5778995a-e1bf-45b8-affa-663a9f3f4d04','type': 'Role'}], 'resourceAppId': '00000002-0000-0000-c000-000000000000'}]}"
   ```
2. Cierre la sesión y vuelva a iniciar sesión en Azure Portal.
3. Vaya a la página Registro de aplicaciones de la aplicación que registró en [la sección anterior](#authorize-developer-accounts-by-using-azure-ad). 
4. Haga clic en **Permisos de API**. Es necesario que consulte los permisos que haya concedido el script de PowerShell en el paso 1. 
5. Seleccione **Conceder consentimiento del administrador para {tenantname}** con el fin de conceder acceso a todos los usuarios de este directorio. 

Ahora los grupos externos de Azure AD se pueden agregan desde la pestaña **Grupos** de la instancia de API Management.

1. En la sección **Portal para desarrolladores** del menú lateral, seleccione **Grupos**.
2. Seleccione el botón **Agregar grupo de AAD**.

   ![Botón "Add A A D group" (Agregar grupo de AAD)](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Seleccione **Inquilino** en el menú desplegable. 
2. Busque y seleccione el grupo que quiera agregar.
3. Presione el botón **Select** (Seleccionar).

Una vez que agregue un grupo externo de Azure AD, puede revisar y configurar sus propiedades: 
1. Seleccione el nombre del grupo en la pestaña **Groups** (Grupos). 
2. Edite la información de **Nombre** y **Descripción** del grupo.
 
Los usuarios de la instancia de Azure AD configurada ya pueden hacer lo siguiente:
* Iniciar sesión en el portal para desarrolladores. 
* Ver los grupos para los que tengan visibilidad y suscribirse a ellos.

> [!NOTE]
> Para obtener más información sobre la diferencia entre los tipos de permiso **delegado** y **de aplicación**, consulte el artículo [Permisos y consentimiento en la Plataforma de identidad de Microsoft](../active-directory/develop/v2-permissions-and-consent.md#permission-types).

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Portal para desarrolladores: incorporación de la autenticación de la cuenta de Azure AD

En el portal para desarrolladores, puede iniciar sesión con Azure AD usando el widget **Botón de inicio de sesión: OAuth** incluido en la página de inicio de sesión del contenido predeterminado del portal para desarrolladores.

Aunque se creará automáticamente una cuenta cada vez que un nuevo usuario inicie sesión con Azure AD, considere la posibilidad de agregar el mismo widget a la página de registro. El widget **Sign-up form: OAuth** (Formulario de inicio de sesión: OAuth) representa un formulario que se utiliza para registrarse con OAuth.

> [!IMPORTANT]
> Debe [volver a publicar el portal](api-management-howto-developer-portal-customize.md#publish) para que los cambios de Azure AD surtan efecto.

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>Portal para desarrolladores heredado: inicio de sesión con Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Para iniciar sesión en el portal para desarrolladores usando una cuenta de Azure AD que ha configurado en las secciones anteriores:

1. Abra una nueva ventana del explorador usando la dirección URL de inicio de sesión de la configuración de aplicación de Active Directory. 
2. Seleccione **Azure Active Directory**.

   ![Página de inicio de sesión][api-management-dev-portal-signin]

1. Escriba las credenciales de uno de los usuarios de Azure AD.
2. Seleccione **Iniciar sesión**.

   ![Inicio de sesión con el nombre de usuario y la contraseña][api-management-aad-signin]

1. En caso de que haga falta más información, puede que se le solicite mediante un formulario de registro. 
2. Seleccione **Suscribirse**.

   ![Botón Suscribirse del formulario de registro][api-management-complete-registration]

Ahora ya inició sesión en el portal para desarrolladores de la instancia del servicio API Management.

![Portal para desarrolladores una vez completado el registro][api-management-registration-complete]

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [proteger un back-end de API web en Azure API Management usando la autorización de OAuth 2.0 con Azure AD](./api-management-howto-protect-backend-with-aad.md).
- Obtenga más información sobre [Escenarios de autenticación para Azure AD](../active-directory/develop/authentication-vs-authorization.md).
- Consulte más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de API.
- Para conocer otras formas de proteger el servicio back-end, consulte [Autenticación de certificado mutua](./api-management-howto-mutual-certificates.md).
- [Creación de una instancia del servicio de API Management](./get-started-create-service-instance.md).
- [Administración de su primera API en Administración de API de Azure](./import-and-publish.md).

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account

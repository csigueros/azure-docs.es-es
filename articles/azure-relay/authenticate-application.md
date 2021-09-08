---
title: 'Autenticación desde una aplicación: Azure Relay (versión preliminar)'
description: En este artículo, se proporciona información sobre cómo autenticar una aplicación con Azure Active Directory para acceder a recursos de Azure Relay.
ms.topic: article
ms.date: 07/02/2021
ms.openlocfilehash: 2304dd58332cb95c40e7492451cf010ee46d76be
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654210"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-relay-entities-preview"></a>Autenticación y autorización de una aplicación con Azure Active Directory para acceder a entidades de Azure Relay (versión preliminar)
Azure Relay admite el uso de Azure Active Directory (Azure AD) para autorizar solicitudes a entidades de Azure Relay (conexiones híbridas, retransmisiones WCF). Con Azure AD, puede usar el control de acceso basado en rol de Azure (Azure RBAC) para conceder permisos a una entidad de seguridad, que puede ser un usuario, un grupo o una entidad de servicio de aplicación. Para más información sobre los roles y las asignaciones de roles, consulte [Descripción de los distintos roles](../role-based-access-control/overview.md).   


[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="authenticate-from-an-app"></a>Autenticación desde una aplicación
Una ventaja clave del uso de Azure AD con Azure Relay es que ya no necesita almacenar las credenciales en el código. En su lugar, puede solicitar un token de acceso de OAuth 2.0 desde la Plataforma de identidad de Microsoft. Azure AD autentica la entidad de seguridad (un usuario, grupo o entidad de servicio) ejecutando la aplicación. Si la autenticación se realiza correctamente, Azure AD devuelve el token de acceso a la aplicación y esta puede usar el token de acceso para autorizar las solicitudes a Azure Relay.

En las secciones siguientes, se muestra cómo configurar la aplicación de consola para la autenticación con la Plataforma de identidad de Microsoft 2.0. Para más información, consulte [¿Qué es la plataforma de identidad de Microsoft?](../active-directory/develop/v2-overview.md)

Para información general sobre el flujo de concesión de código OAuth 2.0, consulte [Autorización del acceso a aplicaciones web de Azure Active Directory mediante el flujo de concesión de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registro de la aplicación con un inquilino de Azure AD
El primer paso para usar Azure AD con el fin de autorizar las entidades de Azure Relay es registrar la aplicación cliente con un inquilino de Azure AD desde Azure Portal. Al registrar la aplicación cliente, facilita información sobre la aplicación a AD. Azure AD proporciona un id. de cliente (también denominado id. de aplicación) que se puede usar para asociar la aplicación con el runtime de Azure AD. 

Para obtener instrucciones paso a paso para registrar la aplicación con Azure AD, consulte [Registro de una aplicación](../active-directory/develop/quickstart-register-app.md#register-an-application).

> [!IMPORTANT]
> Anote el **identificador de directorio (inquilino)** y el **identificador de aplicación (cliente)** . Necesitará estos valores para ejecutar la aplicación de ejemplo.

### <a name="create-a-client-secret"></a>Creación de un secreto de cliente   
La aplicación necesita un secreto de cliente para demostrar su identidad al solicitar un token. En el mismo artículo vinculado anteriormente, consulte la sección [Agregar un secreto de cliente](../active-directory/develop/quickstart-register-app.md#add-a-client-secret) para crear un secreto de cliente. 

> [!IMPORTANT]
> Anote el **secreto de cliente**. Lo necesitará para ejecutar la aplicación de ejemplo.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Asignación de roles de Azure mediante Azure Portal
Asigne uno de los roles de Azure Relay a la entidad de servicio de la aplicación en el ámbito deseado (entidad de Relay, espacio de nombres, grupo de recursos, suscripción). Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="run-the-sample"></a>Ejecución del ejemplo

1. Descargue la aplicación de consola de ejemplo desde [GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol).
1. Ejecute la aplicación localmente en el equipo según las instrucciones del [artículo LÉAME](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample).

    > [!NOTE]
    > Siga los mismos pasos anteriores para ejecutar la [aplicación de consola de ejemplo para WCF Relay](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl). 

#### <a name="highlighted-code-from-the-sample"></a>Código resaltado del ejemplo
Este es el código del ejemplo que muestra cómo usar la autenticación de Azure AD para conectarse al servicio Azure Relay.  

1. Cree un objeto [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) con el método `TokenProvider.CreateAzureActiveDirectoryTokenProvider`. 

    Si aún no ha creado un registro de aplicación, consulte la sección [Registro de la aplicación con un inquilino de Azure AD](#register-your-application-with-an-azure-ad-tenant) para crearlo y, a continuación, cree un secreto de cliente como se menciona en la sección [Creación de un secreto de cliente](#create-a-client-secret).

    Si desea usar un registro de aplicación existente, siga estas instrucciones para obtener el **identificador de aplicación (cliente)** y el **identificador de directorio (inquilino)** . 

    1. Inicie sesión en [Azure Portal](https://portal.azure.com).
    1. Busque y seleccione **Azure Active Directory** con la barra de búsqueda de la parte superior.
    1. En la página **Azure Active Directory**, seleccione **Registros de aplicaciones** en la sección **Administrar** del menú izquierdo. 
    1. Seleccione el registro de la aplicación. 
    1. En la página del registro de la aplicación, verá los valores del **identificador de aplicación (cliente)** y el **identificador de directorio (inquilino)** . 
    
    Para obtener el **secreto de cliente**, siga estos pasos:
    1. En la página del registro de la aplicación, seleccione **Certificados y secretos** en el menú de la izquierda. 
    1. Use el botón de copia de la columna **Valor** del secreto en la sección **Secretos de cliente**. 

    
    ```csharp
    static TokenProvider GetAadTokenProvider(string clientId, string tenantId, string clientSecret)
    {
        return TokenProvider.CreateAzureActiveDirectoryTokenProvider(
            async (audience, authority, state) =>
            {
                IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
                    .WithAuthority(authority)
                    .WithClientSecret(clientSecret)
                    .Build();

                var authResult = await app.AcquireTokenForClient(new [] { $"{audience}/.default" }).ExecuteAsync();
                return authResult.AccessToken;
            },
            $"https://login.microsoftonline.com/{tenantId}");
    }
    ```
1. Cree un objeto [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) o [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) mediante el paso del identificador URI de la conexión híbrida y el proveedor de tokens que creó en el paso anterior.

    **Agente de escucha:**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **Remitente:**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre Azure RBAC, consulte [¿Qué es el control de acceso basado en rol de Azure (Azure RBAC) de Azure?](../role-based-access-control/overview.md)
- Para información sobre cómo asignar y administrar las asignaciones de roles de Azure con Azure PowerShell, la CLI de Azure o la API de REST, consulte estos artículos:
    - [Incorporación o eliminación de asignaciones de roles de Azure con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Incorporación o eliminación de asignaciones de roles de Azure mediante la CLI de Azure](../role-based-access-control/role-assignments-cli.md)
    - [Incorporación o eliminación de asignaciones de roles de Azure mediante la API REST](../role-based-access-control/role-assignments-rest.md)
    - [Incorporación o eliminación de asignaciones de roles de Azure mediante plantillas de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Para más información sobre Azure Relay, consulte los siguientes temas:
- [¿Qué es Relay?](relay-what-is-it.md)
- [Introducción a WebSockets de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)









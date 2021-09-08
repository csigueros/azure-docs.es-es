---
title: Autenticación con identidades administradas para recursos de Azure Relay (versión preliminar)
description: En este artículo, se describe cómo usar identidades administradas para acceder a recursos de Azure Relay.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: fa82f41056f3a5a8617bab572b8b4f312861419f
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654226"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-relay-resources-preview"></a>Autenticación de una identidad administrada con Azure Active Directory para acceder a recursos de Azure Relay (versión preliminar)
[Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md) es una característica de Azure que permite crear una identidad segura asociada a la implementación en la que se ejecuta el código de la aplicación. A continuación, puede asociar esa identidad con los roles de control de acceso que conceden permisos personalizados para acceder a recursos específicos de Azure que la aplicación necesita.

Con las identidades administradas, la plataforma Azure administra esta identidad en tiempo de ejecución. No es necesario almacenar y proteger las claves de acceso en la configuración o el código de la aplicación, ya sea para la propia identidad o para los recursos a los que necesita acceder. Una aplicación cliente de Relay que se ejecuta dentro de una aplicación de Azure App Service o en una máquina virtual con entidades administradas habilitadas para admitir recursos de Azure no necesita controlar las reglas y las claves SAS ni cualquier otro token de acceso. La aplicación cliente solo necesita la dirección del punto de conexión del espacio de nombres de Relay. Cuando se conecta la aplicación, Relay enlaza el contexto de la entidad administrada con el cliente en una operación, como se muestra en un ejemplo más adelante en este artículo. Una vez asociado a una identidad administrada, el cliente de Relay puede realizar todas las operaciones autorizadas. La autorización se concede mediante la asociación de una entidad administrada con roles de Relay.

[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="enable-managed-identity"></a>Habilitación de una entidad administrada
En primer lugar, habilite la identidad administrada para el recurso de Azure que necesita acceder a entidades de Azure Relay (conexiones híbridas o retransmisiones WCF). Por ejemplo, si la aplicación cliente de Relay se ejecuta en una máquina virtual de Azure, habilite la identidad administrada de la máquina virtual según las instrucciones del artículo [Configurar identidades administradas para recursos de Azure en una VM mediante Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). Una vez habilitada esta configuración, se crea una nueva identidad de servicio administrada en Azure Active Directory (Azure AD).

Para ver una lista de los servicios que admiten identidades administradas, consulte [Servicios que admiten identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="assign-an-azure-relay-role-to-the-managed-identity"></a>Asignación de un rol de Azure Relay a la identidad administrada
Después de habilitar la identidad administrada, asigne uno de los roles de Azure Relay (Propietario de Azure Relay, Cliente de escucha de Azure Relay o Remitente de Azure Relay) a la identidad en el ámbito adecuado. Cuando se asigna el rol de Azure a una identidad administrada, se le concede acceso a las entidades de Relay en el ámbito adecuado.

En la sección siguiente, se usa una aplicación sencilla que se ejecuta como una identidad administrada en una instancia de máquina virtual de Azure y accede a los recursos de Relay.

## <a name="sample-app-on-vm-accessing-relay-entities"></a>Aplicación de ejemplo de una máquina virtual que accede a entidades de Relay

1. Descargue la [aplicación de consola de ejemplo de Conexiones híbridas](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol) en el equipo desde GitHub.
1. [Cree una máquina virtual de Azure](../virtual-machines/windows/quick-create-portal.md). Para este ejemplo, use una imagen de Windows 10. 
1. Habilite la identidad asignada por el sistema o una identidad asignada por el usuario para la máquina virtual de Azure. Para obtener instrucciones, consulte [Configurar identidades administradas para recursos de Azure en una VM mediante Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). 
1. Asigne uno de los roles de Relay a la identidad de servicio administrada en el ámbito deseado (entidad de Relay, espacio de nombres de Relay, grupo de recursos, suscripción). Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).
1. Compile la aplicación de consola localmente en el equipo local según las instrucciones del [documento LÉAME](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample). 
1. Copie el archivo ejecutable en la carpeta \<your local path\>\RoleBasedAccessControl\bin\Debug en la máquina virtual. Puede usar RDP para conectarse a la máquina virtual de Azure. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](../virtual-machines/windows/connect-logon.md).
1. Ejecute el archivo RoleBasedAccessControl.exe en la máquina virtual de Azure según las instrucciones del [documento LÉAME](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample). 

    > [!NOTE]
    > Siga los mismos pasos anteriores para ejecutar la [aplicación de consola para WCF Relay](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl).

#### <a name="highlighted-code-from-the-sample"></a>Código resaltado del ejemplo
Este es el código del ejemplo que muestra cómo usar la autenticación de Azure AD para conectarse al servicio Azure Relay.  

1. Cree un objeto [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) con el método `TokenProvider.CreateManagedIdentityTokenProvider`. 
    
    - Si usa una **identidad administrada asignada por el sistema:**
        ```csharp
        TokenProvider.CreateManagedIdentityTokenProvider();
        ```
    - Si usa una **identidad administrada asignada por el usuario**, obtenga el **identificador de cliente** de la identidad asignada por el usuario en la página **Identidad administrada** de Azure Portal. Para obtener instrucciones, consulte [Enumerar identidades administradas asignadas por el usuario](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azp#list-user-assigned-managed-identities).
        ```csharp
        var managedCredential = new ManagedIdentityCredential(clientId);
        tokenProvider = TokenProvider.CreateManagedIdentityTokenProvider(managedCredential);    
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
Para más información sobre Azure Relay, consulte los siguientes temas:
- [¿Qué es Relay?](relay-what-is-it.md)
- [Introducción a WebSockets de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)




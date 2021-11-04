---
title: Configuración de Azure Active Directory para la autenticación de cliente
description: Obtenga información sobre cómo configurar Azure Active Directory (Azure AD) para autenticar a los clientes de los clústeres de Service Fabric.
ms.topic: conceptual
ms.date: 6/28/2019
ms.custom: ignite-fall-2021
ms.openlocfilehash: e960b753e7d1c76992e59cb03133f27f7e305ea1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004081"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configuración de Azure Active Directory para la autenticación de cliente

Para los clústeres que se ejecutan en Azure, es recomendable proteger el acceso a los puntos de conexión de administración con Azure Active Directory (Azure AD). En este artículo se describe cómo configurar Azure AD para autenticar a los clientes de un clúster de Service Fabric.

En este artículo, el término "aplicación" se usará para hacer referencia a [aplicaciones de Azure Active Directory](../active-directory/develop/developer-glossary.md#client-application), no a aplicaciones de Service Fabric; la distinción se realizará cuando sea necesario. Azure AD permite a las organizaciones (conocidas como inquilinos) administrar el acceso de los usuarios a las aplicaciones.

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como [Service Fabric Explorer][service-fabric-visualizing-your-cluster] y [Visual Studio][service-fabric-manage-application-in-visual-studio] basados en web. Como consecuencia, creará dos aplicaciones de Azure AD para controlar el acceso al clúster: una aplicación web y una aplicación nativa. Una vez creadas las aplicaciones, asignará usuarios a los roles de solo lectura y administrador.

> [!NOTE]
> En Linux, debe realizar los pasos siguientes antes de crear el clúster. En Windows, también tiene la opción de [configurar la autenticación de Azure AD para un clúster existente](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Es un [problema conocido](https://github.com/microsoft/service-fabric/issues/399) que las aplicaciones y los nodos de los clústeres habilitados para AAD de Linux no se pueden ver en Azure Portal.



## <a name="prerequisites"></a>Prerrequisitos
En este artículo se supone que ya ha creado un inquilino. En caso de que no lo haya hecho, lea [Obtención de un inquilino de Azure Active Directory][active-directory-howto-tenant].

Para simplificar algunos de los pasos necesarios para configurar Azure AD con un clúster de Service Fabric, hemos creado un conjunto de scripts de Windows PowerShell.

1. [Clone el repositorio](https://github.com/Azure-Samples/service-fabric-aad-helpers) en su equipo.
2. [Asegúrese de cumplir todos los requisitos previos](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) para los scripts instalados.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Creación de aplicaciones de Azure AD y asignación de usuarios a roles

Usaremos dos aplicaciones de Azure AD para controlar el acceso al clúster: una aplicación web y una aplicación nativa. Una vez que cree las aplicaciones para representar el clúster, creará usuarios para los [roles compatibles con Service Fabric](service-fabric-cluster-security-roles.md): solo lectura y administrador.

Ejecute `SetupApplications.ps1` y proporcione el identificador de inquilino, el nombre del clúster y la dirección URL de respuesta de la aplicación web como parámetros.  Especifique también los nombres de usuario y las contraseñas para los usuarios. Por ejemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para las nubes nacionales (por ejemplo, Azure Government, Azure China, Azure Alemania), también debe especificar el parámetro `-Location`.

Ejecute el comando `Get-AzureSubscription` de PowerShell para encontrar su *TenantId*. Al ejecutar este comando se muestra el valor de TenantId para cada suscripción.

El valor de *ClusterName* se usa como prefijo en las aplicaciones de Azure AD que crea el script. No es necesario que coincida exactamente con el nombre del clúster real. Está diseñado solo para facilitar la asignación de artefactos de Azure AD al clúster de Service Fabric con el que se utilizan.

El valor de *WebApplicationReplyUrl* es el punto de conexión predeterminado al que Azure AD devuelve a los usuarios cuando terminan el inicio de sesión. Establézcalo como el punto de conexión de Service Fabric Explorer para el clúster. Si va a crear aplicaciones de Azure AD para representar un clúster existente, asegúrese de que esta dirección URL coincida con el punto de conexión del clúster existente. Si va a crear aplicaciones para un nuevo clúster, planifique el punto de conexión que tendrá el clúster y asegúrese de no usar el punto de conexión de un clúster existente. De forma predeterminada, el punto de conexión Service Fabric Explorer es:

https://&lt;cluster_domain&gt;:19080/Explorer

Se le pedirá que inicie sesión en una cuenta con privilegios administrativos para el inquilino de Azure AD. Una vez iniciada la sesión, el script creará las aplicaciones web y nativa para representar el clúster de Service Fabric. Si examina las aplicaciones del inquilino en [Azure Portal][azure-portal], debería ver dos entradas nuevas:

   * *ClusterName*\_Clúster
   * *ClusterName*\_Cliente

El script imprimirá el código JSON que necesita la plantilla de Azure Resource Manager al [crear el clúster habilitado por AAD](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), por lo que es buena idea mantener abierta la ventana de PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ayuda de solución de problemas para configurar Azure Active Directory
Configurar y usar Azure AD puede resultar complicado, así que a continuación se proporcionan algunas indicaciones de lo que puede hacer para depurar el problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer le solicita que seleccione el certificado
#### <a name="problem"></a>Problema
Después de conectarse correctamente a Azure AD en Service Fabric Explorer, el explorador vuelve a la página principal, pero un mensaje le pide que seleccione un certificado.

![Cuadro de diálogo de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
Al usuario no se le ha asignado un rol en la aplicación de clúster de Azure AD. Por tanto, se produce un error en la autenticación de Azure AD en el clúster de Service Fabric. Service Fabric Explorer recurre a la autenticación de certificado.

#### <a name="solution"></a>Solución
Siga las instrucciones de configuración de Azure AD y de asignación de roles de usuario. Además, se recomienda activar "Asignación de usuario necesaria para acceder a la aplicación", como hace `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La conexión con PowerShell genera un error: "Las credenciales especificadas no son válidas".
#### <a name="problem"></a>Problema
Cuando usa PowerShell para conectarse al clúster en el modo de seguridad "AzureActiveDirectory", después de iniciar sesión correctamente en Azure AD, se produce un error de conexión con el mensaje: "Las credenciales especificadas no son válidas".

#### <a name="solution"></a>Solución
Esta solución es la mismo que la anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer devuelve un error al iniciar sesión: "AADSTS50011"
#### <a name="problem"></a>Problema
Al intentar iniciar sesión en Azure AD y Service Fabric Explorer, la página devuelve un error: "AADSTS50011: La dirección de respuesta &lt;url&gt; no coincide con las direcciones de respuesta configuradas para la aplicación: &lt;guid&gt;".

![La dirección de respuesta SFX no coincide][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
La aplicación del clúster (web) que representa Service Fabric Explorer intenta la autenticación en Azure AD y, como parte de la solicitud, proporciona la dirección URL de retorno de redireccionamiento. Pero no aparece en la lista **URL DE RESPUESTA** de la aplicación Azure AD.

#### <a name="solution"></a>Solución
En la página de registro de la aplicación de Azure AD del clúster, seleccione **Autenticación** y, en la sección **URI de redireccionamiento**, agregue a la lista la dirección URL de Service Fabric Explorer. Guarde el cambio.

![URL de respuesta de la aplicación web][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Al usar la autenticación de Azure AD para conectarse al clúster a través de PowerShell se produce un error de inicio de sesión: "AADSTS50011"
#### <a name="problem"></a>Problema
Cuando intenta conectarse a un clúster de Service Fabric con Azure AD a través de PowerShell, la página de inicio de sesión devuelve un error: "AADSTS50011: la dirección URL de respuesta especificada en la solicitud no coincide con las direcciones URL de respuesta configuradas para la aplicación: &lt;guid&gt;".

#### <a name="reason"></a>Motivo
De forma similar al problema anterior, PowerShell intenta autenticarse en Azure AD, que proporciona una dirección URL de redireccionamiento que no aparece en la lista de **URL de respuesta** de la aplicación de Azure AD.  

#### <a name="solution"></a>Solución
Use el mismo proceso que en el problema anterior, pero establezca la dirección URL en `urn:ietf:wg:oauth:2.0:oob` (un redireccionamiento especial para la autenticación de la línea de comandos).

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conexión del clúster mediante la autenticación de Azure AD a través de PowerShell
Para conectar el clúster de Service Fabric, use el siguiente ejemplo de comando de PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para obtener más información, consulte el [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>¿Se puede reutilizar el mismo inquilino de Azure AD para varios clústeres?
Sí. Pero recuerde agregar la dirección URL de Service Fabric Explorer a la aplicación del clúster (web). De lo contrario, Service Fabric Explorer no funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>¿Por qué todavía necesito el certificado de servidor con Azure AD habilitado?
FabricClient y FabricGateway realizan una autenticación mutua. Durante la autenticación de Azure AD, la integración de Azure AD proporciona una identidad del cliente al servidor y el cliente usa el certificado de servidor para comprobar la identidad del servidor. Para más información sobre cómo funciona el certificado en Service Fabric, consulte [Certificados X.509 y Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Pasos siguientes
Después de configurar las aplicaciones de Azure Active Directory y establecer los roles para los usuarios, [configure e implemente un clúster](service-fabric-cluster-creation-via-arm.md).

<!-- Links -->

[azure-cli]: /cli/azure/get-started-with-azure-cli
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png

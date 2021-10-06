---
title: Configuración y creación de tokens de acceso de Teams
titleSuffix: An Azure Communication Services quickstart
description: Creación de un servicio que proporciona tokens de acceso de Teams
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 5fb6632fa31143c26d3cad84d42d1417aaf97496
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081346"
---
# <a name="quickstart-set-up-and-manage-teams-access-tokens"></a>Inicio rápido: Configuración y administración de tokens de acceso de Teams

> [!IMPORTANT]
> Esta característica solo está disponible en versión preliminar. Para habilitar o deshabilitar la experiencia del punto de conexión de Teams personalizado, [complete y envíe este formulario](https://forms.office.com/r/B8p5KqCH19).

En este inicio rápido, compilará una aplicación de consola de .NET para autenticar a un usuario de Microsoft 365 mediante la Biblioteca de autenticación de Microsoft (MSAL) y la recuperación de un token de usuario Azure Active Directory (Azure AD). A continuación, intercambiaremos ese token por un token de acceso de Teams con el SDK de identidad de Azure Communication Services. Después, el SDK de llamadas de Communication Services puede usar el token de acceso de Teams para crear un punto de conexión de Teams personalizado.

> [!NOTE]
> Cuando se encuentra en un entorno de producción, se recomienda implementar este mecanismo de intercambio en servicios back-end, ya que las solicitudes para un intercambio se firman con un secreto.

## <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Recurso activo de Azure Communication Services y una cadena de conexión. Para más información, consulte [Creación de un recurso de Azure Communication Services](./create-communication-resource.md).
- Para habilitar una experiencia de punto de conexión de Teams personalizada, [complete y envíe este formulario](https://forms.office.com/r/B8p5KqCH19).
- Una instancia de Azure Active Directory con usuarios que tengan licencia de Teams.

## <a name="introduction"></a>Introducción

Las identidades de Teams están enlazadas a inquilinos de Azure Active Directory. Los usuarios del mismo inquilino o de otro inquilino pueden usar la aplicación. En este inicio rápido, trabajaremos en un caso de uso multiinquilino con varios actores: usuarios, desarrolladores y administradores de las empresas ficticias Contoso y Fabrikam. En este caso de uso, Contoso es una empresa que está creando una solución de software como servicio (SaaS) para Fabrikam. 

Las secciones siguientes le guiarán por los pasos para administradores, desarrolladores y usuarios. En los diagramas se muestra el caso de uso de varios inquilinos. Si trabaja con un solo inquilino, ejecute todos los pasos de Contoso y Fabrikam en un solo inquilino.

## <a name="administrator-actions"></a>Acciones de administrador

El rol Administrador tiene permisos extendidos en Azure AD. Los miembros de este rol pueden aprovisionar recursos y pueden leer información de Azure Portal. En el diagrama siguiente, puede ver todas las acciones que deben ejecutar los administradores.

![Acciones del administrador para habilitar la experiencia del punto de conexión de Teams personalizado](./media/teams-identities/teams-identity-admin-overview.png)

1. El administrador de Contoso crea o selecciona una *aplicación* existente en Azure Active Directory. La propiedad *Tipos de cuenta admitidos* define si los usuarios de varios inquilinos se pueden autenticar en la aplicación. La propiedad *Identificador URI de redirección* redirige una solicitud de autenticación correcta al *servidor* de Contoso.
1. El administrador de Contoso extiende el manifiesto de la aplicación con permiso para VoIP de Communication Services. 
1. El administrador de Contoso permite el flujo de cliente público para la aplicación.
1. Opcionalmente, el administrador de Contoso puede realizar la actualización.
1. El administrador de Contoso habilita la experiencia [completando y enviando este formulario](https://forms.office.com/r/B8p5KqCH19).
1. El administrador de Contoso crea o selecciona los servicios de comunicación existentes, que se usarán para la autenticación de las solicitudes de intercambio. Los tokens de usuario de Azure AD se intercambiarán por tokens de acceso de Teams. Para más información, consulte [Inicio rápido: Creación y administración de recursos de Communication Services](./create-communication-resource.md).
1. El administrador de Fabrikam configura una nueva entidad de servicio para Communication Services en el inquilino de Fabrikam.
1. El administrador de Fabrikam concede el permiso para VoIP de Communication Services a la aplicación Contoso. Este paso solo es necesario si la aplicación de Contoso no está verificada. 

### <a name="step-1-create-an-azure-ad-application-registration-or-select-an-azure-ad-application"></a>Paso 1: Crear un registro de aplicación de Azure AD o seleccionar una aplicación de Azure AD 

Los usuarios deben autenticarse en las aplicaciones de Azure AD con el permiso para VoIP de Azure Communication Service. Si no tiene una aplicación existente que quiere usar para este inicio rápido, puede crear un registro de aplicación. 

La siguiente configuración de la aplicación influye en la experiencia:
- La propiedad *Tipos de cuenta admitidos* define si la aplicación es de un solo inquilino ("Solo cuentas de este directorio organizativo") o multiinquilino ("Cuentas de cualquier directorio organizativo"). Para este escenario, puede usar la opción de multiinquilino.
- El *identificador URI de redirección* define el identificador URI donde se redirige la solicitud de autenticación después de la autenticación. En este escenario, puede usar **Cliente público/nativo (móvil y escritorio)** y escribir **`http://localhost`** como identificador URI.

Para más información, consulte [Registro de una aplicación con la plataforma de identidad de Microsoft](../../active-directory/develop/quickstart-register-app.md#register-an-application). 

Cuando la aplicación está registrada, verá un identificador en la información general. Este identificador, *Id. de aplicación (cliente)* , se usa en los pasos siguientes:

### <a name="step-2-allow-public-client-flows"></a>Paso 2: Permitir flujos de clientes públicos

En el panel **Autenticación** de la aplicación, puede ver la plataforma configurada para *Cliente público/nativo (móvil y escritorio)* con el identificador URI de redirección que apunta a *localhost*. En la parte inferior del panel, verá el control de alternancia *Permitir flujos de cliente públicos*, que para este inicio rápido debe establecerse en **Sí**.

### <a name="step-3-optional-update-the-publisher-domain"></a>Paso 3: (Opcional) Actualizar el dominio del publicador 
En el panel **Personalización de marca**, puede actualizar el dominio del publicador para la aplicación. Esto es útil para las aplicaciones multiinquilino, donde la aplicación se marcará como verificada por Azure. Para más información, consulte [Configuración del dominio de editor de una aplicación](../../active-directory/develop/howto-configure-publisher-domain.md).

### <a name="step-4-define-the-communication-services-voip-permission-in-the-application"></a>Paso 4: Definir el permiso para VoIP de Communication Services en la aplicación

Vaya a los detalles de la aplicación, seleccione el panel **Manifiesto** y busque la propiedad *requiredResourceAccess.* . Es una matriz de objetos que definen los permisos de la aplicación. Extienda el manifiesto con los permisos para VoIP para la aplicación de primera entidad de Communication Services. Agregue el siguiente objeto a la matriz.

> [!NOTE] 
> No cambie los GUID del fragmento de código, ya que identifican de forma única la aplicación y los permisos.

```json
{
   "resourceAppId": "1fd5118e-2576-4263-8130-9503064c837a",
   "resourceAccess": [
      {
         "id": "31f1efa3-6f54-4008-ac59-1bf1f0ff9958",
         "type": "Scope"
      }
   ]
}
```

Seleccione **Guardar** para aplicar los cambios. Ahora puede ver el permiso para *VoIP de Azure Communication Services* en el panel **Permisos de API**.

### <a name="step-5-enable-a-custom-teams-endpoint-experience-for-the-application"></a>Paso 5: Habilitar una experiencia de punto de conexión de Teams personalizado para la aplicación

Para habilitar la experiencia de punto de conexión de Teams personalizado para la aplicación, el administrador de Azure AD [completa y envía este formulario](https://forms.office.com/r/B8p5KqCH19).

### <a name="step-6-create-or-select-a-communication-services-resource"></a>Paso 6: Crear o seleccionar un recurso de Communication Services

El recurso de Communication Services se utiliza para autenticar todas las solicitudes de intercambio de tokens de usuario de Azure AD por tokens de acceso de Teams. Puede desencadenar este intercambio mediante el SDK de Communication Services Identity, que puede autenticar con una clave de acceso o mediante el control de acceso basado en rol (RBAC) de Azure. Puede obtener la clave de acceso en Azure Portal o mediante la configuración de RBAC de Azure mediante el panel **Control de acceso (IAM)** .

Si desea crear un nuevo recurso de Communication Services, consulte [Creación y administración de recursos de Communication Services](./create-communication-resource.md).

### <a name="step-7-set-up-a-communication-services-service-principal"></a>Paso 7: Configurar una entidad de servicio de Communication Services

Para habilitar la experiencia del punto de conexión de Teams personalizado en el inquilino de Fabrikam, el administrador de Azure AD de Fabrikam debe configurar la entidad de servicio llamada Azure Communication Services con el identificador de aplicación: *1fd5118e-2576-4263-8130-9503064c837a*. Si no ve esta aplicación en el panel **Aplicaciones empresariales** de Azure Active Directory, se debe agregar manualmente.

El administrador de Azure AD de Fabrikam se conecta al inquilino de Azure a través de PowerShell. 

> [!NOTE]
> En el siguiente comando, reemplace [Tenant_ID] por el identificador del inquilino, que encontrará en Azure Portal en la página de información general de la instancia de Azure AD.

```azurepowershell
Connect-AzureAD -TenantId "[Tenant_ID]"
```

Si recibe el error de que "no se encuentra el comando", significa que el módulo AzureAD no está instalado en PowerShell. Cierre PowerShell y vuelva a abrirlo como administrador. Ahora puede instalar el paquete de AzureAD mediante la ejecución del siguiente comando:

```azurepowershell
Install-Module AzureAD
```

Después de conectarse y autenticarse en Azure Portal, configure la entidad de servicio de Communication Services mediante la ejecución del comando siguiente: 

> [!NOTE]
> El parámetro AppId hace referencia a la aplicación de primera entidad de Communication Services. No cambie este valor.

```azurepowershell
New-AzureADServicePrincipal -AppId "1fd5118e-2576-4263-8130-9503064c837a"
```

### <a name="step-8-provide-administrator-consent"></a>Paso 8: Proporcionar consentimiento del administrador

Si no se comprueba la aplicación Contoso, el administrador de Azure AD debe conceder permiso a la aplicación Contoso para VoIP de Communication Services. El administrador de Azure AD de Fabrikam proporciona consentimiento mediante una dirección URL única. 

Para construir una dirección URL de consentimiento del administrador, el administrador de Azure AD de Fabrikam hace lo siguiente:

1. En la dirección URL, *https://login.microsoftonline.com/{Tenant_ID}/adminconsent?client_id={Application_ID}* , el administrador reemplaza {Tenant_ID} por el identificador de inquilino de Fabrikam y reemplaza {Application_ID} por el identificador de aplicación de Contoso.
1. El administrador inicia sesión y concede permisos en nombre de la organización.

La entidad de servicio de la aplicación de Contoso en el inquilino de Fabrikam se crea si se concede consentimiento. El administrador de Fabrikam puede revisar el consentimiento en Azure AD haciendo lo siguiente:

1. Inicie sesión en Azure Portal como administrador.
1. Vaya a Azure Active Directory.
1. En el panel **Aplicaciones empresariales**, establezca el filtro **Tipo de aplicación** en **Todas las aplicaciones**.
1. En el campo para filtrar las aplicaciones, escriba el nombre de la aplicación Contoso.
1. Seleccione **Aplicar**.
1. Seleccione la entidad de servicio con el nombre necesario. 
1. Vaya al panel **Permisos**.

Puede ver que el estado del permiso para VoIP de Azure Communication Services es *Concedido para {Nombre_del_directorio}* .

## <a name="developer-actions"></a>Acciones para el desarrollador

El desarrollador de Contoso debe configurar la *aplicación cliente* para la autenticación de usuarios. A continuación, el desarrollador debe crear un punto de conexión en el *servidor* back-end para procesar el token de usuario de Azure AD después del redireccionamiento. Cuando se recibe un token de usuario de Azure AD, se intercambia por un token de acceso de Teams y se devuelve a la *aplicación cliente*. 

Las acciones necesarias del desarrollador se muestran en el diagrama siguiente:

![Diagrama de acciones del desarrollador para habilitar la experiencia de punto de conexión de Teams personalizado.](./media/teams-identities/teams-identity-developer-overview.png)

1. El desarrollador de Contoso configura la biblioteca MSAL para autenticar al usuario para la aplicación creada anteriormente por el administrador para el permiso para VoIP de Communication Services.
1. El desarrollador de Contoso inicializa el SDK de Communication Services Identity e intercambia el token de usuario de Azure AD entrante para el token de acceso de Teams a través del SDK. A continuación, se devuelve el token de acceso de Teams a la *aplicación cliente*.

Al usar la Biblioteca de autenticación de Microsoft (MSAL), los desarrolladores pueden adquirir tokens de usuario de Azure AD desde el punto de conexión de la Plataforma de identidad de Microsoft para autenticar a los usuarios y acceder a las API web protegidas. Se puede usar para proporcionar acceso seguro a Azure Communication Services. MSAL admite muchas arquitecturas y plataformas de aplicación distintas, como .NET, JavaScript, Java, Python, Android e iOS.

Para obtener más información sobre la configuración de entornos en la documentación pública, consulte [Introducción a la biblioteca de autenticación de Microsoft](../../active-directory/develop/msal-overview.md).

> [!NOTE]
> En las secciones siguientes se describe cómo intercambiar el token de Azure AD de acceso del token de Teams para la aplicación de consola en .NET.

### <a name="create-a-new-application"></a>Creación de una aplicación

En una ventana de la consola, como cmd, PowerShell o Bash, use el comando `dotnet new` para crear una aplicación de consola con el nombre `TeamsAccessTokensQuickstart`. Este comando crea un sencillo proyecto de C#, "Hola mundo", con un solo archivo de origen: *program.cs*.

```console
dotnet new console -o TeamsAccessTokensQuickstart
```

Cambie el directorio a la carpeta de la aplicación recién creada y use el comando `dotnet build` para compilar la aplicación.

```console
cd TeamsAccessTokensQuickstart
dotnet build
```
#### <a name="install-the-package"></a>Instalar el paquete
En el directorio de aplicaciones, instale el paquete de la biblioteca de identidades de Azure Communication Services para .NET usando el comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity
dotnet add package Microsoft.Identity.Client
```

> [!NOTE]
> Los paquetes para la versión preliminar privada no están disponibles en repositorios de paquetes oficiales como NPM o NuGet.org. Puede encontrar los SDK en los siguientes repositorios de paquetes [.NET](https://dev.azure.com/azure-sdk/public/_packaging?_a=package&feed=azure-sdk-for-net&package=Azure.Communication.Identity&protocolType=NuGet&version=1.1.0-alpha.20210531.2) y [JavaScript](https://www.npmjs.com/package/@azure/communication-identity/v/1.1.0-alpha.20210531.1).

#### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

En el directorio del proyecto, haga lo siguiente:

1. Abra el archivo *Program.cs* en un editor de texto.
1. Agregue una directiva `using` para incluir los siguientes espacios de nombres: 
    - Azure.Communication
    - Azure.Communication.Identity
    - Microsoft.Identity.Client
1. Actualice la declaración del método `Main` para admitir el código `async`.

Para empezar, use el código siguiente:

```csharp
using System;
using System.Text;
using Azure.Communication;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace TeamsAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services – Teams access tokens quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Paso 1: Recibir el token de usuario de Azure AD mediante la biblioteca MSAL

Use la biblioteca MSAL para autenticar a los usuarios en Azure AD para la aplicación Contoso con el permiso para VoIP de Communication Services. Configure el cliente para la aplicación Contoso (*parámetro applicationId*) en la nube pública (*parámetro authority*). El token de usuario de Azure AD se devolverá al identificador URI de redirección (*parámetro redirectUri*). Las credenciales se tomarán de la ventana emergente interactiva, que se abre en el explorador predeterminado.

> [!NOTE] 
> El identificador URI de redirección debe coincidir con el valor definido en la aplicación. Consulte el primer paso de la guía del administrador para ver cómo configurar el identificador URI de redirección.

```csharp
const string applicationId = "Contoso's_Application_ID";
const string authority = "https://login.microsoftonline.com/common";
const string redirectUri = "http://localhost";

var client = PublicClientApplicationBuilder
                .Create(applicationId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

const string scope = "https://auth.msft.communication.azure.com/VoIP";

var aadUserToken = await client.AcquireTokenInteractive(new[] { scope }).ExecuteAsync();

Console.WriteLine("\nAuthenticated user: " + aadUserToken.Account.Username);
Console.WriteLine("AAD user token expires on: " + aadUserToken.ExpiresOn);
```

La variable *aadUserToken* ahora incluye un token de usuario de Azure AD válido, que se usará para el intercambio.

### <a name="step-2-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Paso 2: Intercambiar el token de usuario de Azure AD para el token de acceso de Teams

El token de usuario de Azure AD autentica a los usuarios en Azure AD para la aplicación de terceros con permiso para VoIP de Communication Services. El SDK de Communication Services Identity usa el código siguiente para facilitar el intercambio del token de usuario de Azure AD para el token de acceso de Teams.

> [!NOTE]
> En el código siguiente, reemplace el valor "\<Connection-String>" por una cadena de conexión válida o use RBAC de Azure para la autenticación. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](./access-tokens.md).

```csharp
var identityClient = new CommunicationIdentityClient("<Connection-String>");
var teamsAccessToken = identityClient.ExchangeTeamsToken(aadUserToken.AccessToken);

Console.WriteLine("\nTeams access token expires on: " + teamsAccessToken.Value.ExpiresOn);
```

Si se cumplen todas las condiciones necesarias, se obtiene un token de acceso de Teams válido durante 24 horas.

#### <a name="run-the-code"></a>Ejecución del código
Ejecute la aplicación desde el directorio de la aplicación con el comando `dotnet run`.

```console
dotnet run
```

La salida de la aplicación describe cada acción que se completa:

```console
Azure Communication Services - Teams access tokens quickstart

Authenticated user: john.smith@contoso.com
Azure AD user token expires on: 6/10/2021 10:13:17 AM +00:00

Teams access token expires on: 6/11/2021 9:13:18 AM +00:00
```

## <a name="user-actions"></a>Acciones del usuario

El usuario representa a los usuarios de Fabrikam de la aplicación de Contoso. La experiencia del usuario se muestra en el diagrama siguiente.

![Diagrama de acciones del usuario para habilitar la experiencia de punto de conexión de Teams personalizado.](./media/teams-identities/teams-identity-user-overview.png)

1. El usuario de Fabrikam usa la *aplicación cliente* de Contoso y se le pide que se autentique.
1. La *aplicación cliente* de Contoso usa la biblioteca MSAL para autenticar al usuario en el inquilino de Azure AD de Fabrikam para la aplicación de Contoso con el permiso para VoIP de Azure Communication Services. 
1. La autenticación se redirige al *servidor*, tal como se define en la propiedad *Identificador URI de redirección* en MSAL y la aplicación de Contoso.
1. El *servidor* de Contoso intercambia el token de usuario de Azure AD por el token de acceso de Teams con el SDK de Communication Services Identity y devuelve el token de acceso de Teams a la *aplicación cliente*.

Con el token de acceso de Teams válido en la *aplicación cliente*, los desarrolladores pueden integrar el SDK de llamadas de Communication Services y crear un punto de conexión de Teams personalizado.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a hacer lo siguiente:

> [!div class="checklist"]
> * Cree y configure una aplicación de Azure AD.
> * Utilice la biblioteca MSAL para emitir un token de usuario de Azure AD.
> * Utilice el SDK de Communication Services Identity para intercambiar tokens de usuario de Azure AD por tokens de acceso de Teams.

Más información sobre los siguientes conceptos

- [Punto de conexión de Teams personalizado](../concepts/teams-endpoint.md)
- [Interoperabilidad de Teams](../concepts/teams-interop.md)

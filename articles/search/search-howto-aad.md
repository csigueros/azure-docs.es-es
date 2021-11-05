---
title: Autorización de solicitudes de búsqueda mediante Azure AD
titleSuffix: Azure Cognitive Search
description: Adquisición de un token de Azure AD para autorizar solicitudes de búsqueda
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: 0dcc729ea622c42592d1f118f58a831d3a637aea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056193"
---
# <a name="authorize-search-requests-using-azure-ad-preview"></a>Autorización de solicitudes de búsqueda mediante Azure AD (versión preliminar)

> [!IMPORTANT]
> El control de acceso basado en roles para las operaciones del plano de datos, como la creación de un índice o la consulta de un índice, se encuentra actualmente en versión preliminar pública y está disponible en los [términos complementarios de uso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Esta funcionalidad solo está disponible en nubes públicas y puede afectar a la latencia de las operaciones mientras la funcionalidad se encentra en versión preliminar. 

Con Azure Active Directory (Azure AD), puede usar el control de acceso basado en roles (RBAC) para conceder acceso a los servicios de Azure Cognitive Search. Una ventaja clave del uso de Azure AD es que ya no necesita almacenar las credenciales en el código. Azure AD autentica la entidad de seguridad (un usuario, grupo o entidad de servicio) ejecutando la aplicación. Si la autenticación se realiza correctamente, Azure AD devuelve el token de acceso a la aplicación y esta puede usar el token de acceso para autorizar las solicitudes a Azure Cognitive Search. Para más información sobre las ventajas de usar Azure AD en la aplicación, vea [Integración con Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration).

En este artículo se muestra cómo configurar la aplicación para la autenticación con la Plataforma de identidad de Microsoft. Para más información sobre la Plataforma de identidad de Microsoft, consulte la [Introducción a la Plataforma de identidad de Microsoft](../active-directory/develop/v2-overview.md). Para información general sobre el flujo de concesión de código OAuth 2.0 que Azure AD usa, consulte [Autorización del acceso a aplicaciones web de Azure Active Directory mediante el flujo de concesión de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="prepare-your-search-service"></a>Preparación del servicio de búsqueda

Como primer paso, [cree un servicio de búsqueda](search-create-service-portal.md) y configúrelo para usar el control de acceso basado en roles (RBAC).

### <a name="sign-up-for-the-preview"></a>Suscríbase a la vista previa

Las partes de las funcionalidades del control de acceso basado en roles de Azure Cognitive Search necesarias para usar Azure AD para la consulta del servicio de búsqueda se encuentran aún en una versión preliminar. Para usar estas funcionalidades, deberá agregar la característica en vista previa a la suscripción de Azure.

Para agregar la suscripción a la versión preliminar, haga lo siguiente:

1. Vaya a la página **Suscripciones** de [Azure Portal](https://portal.azure.com/).
1. Seleccione la suscripción que quiere usar.
1. En el lado izquierdo de la página de suscripción, seleccione **Características en vista previa**.
1. Use la barra de búsqueda o los filtros para buscar y seleccionar el **Control de acceso basado en roles (versión preliminar)**
1. Seleccione **Registrar** para agregar la característica a la suscripción.

![registro para el rbac en afec](media/search-howto-aad/rbac-signup-afec.png)

Para más información sobre cómo agregar características en vista previa, consulte [Configuración de las características en vista previa en la suscripción de Azure](../azure-resource-manager/management/preview-features.md?tabs=azure-portal).


### <a name="enable-rbac-for-data-plane-operations"></a>Habilitación de RBAC para las operaciones del plano de datos

Una vez que la suscripción se incorpore a la versión preliminar, deberá habilitar RBAC para las operaciones del plano de datos de modo que pueda usar la autenticación de Azure AD. De forma predeterminada, Azure Cognitive Search usa la autenticación basada en claves para las operaciones del plano de datos, pero puede cambiar la configuración para permitir el control de acceso basado en roles. 

Para habilitar el control de acceso basado en roles:

1. Vaya a Azure Portal con este vínculo de versión preliminar: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). 
1. En el panel de navegación izquierdo, seleccione **Claves**.
1. Determine si desea permitir tanto el control de acceso basado en claves como el control de acceso basado en roles, o solo el control de acceso basado en roles.

![opciones de autenticación para Azure Cognitive Search en el portal](media/search-howto-aad/portal-api-access-control.png)

También puede cambiar esta configuración mediante programación como se describe en la [documentación de RBAC de Azure Cognitive Search RBAC](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-2-preview-configuration).

## <a name="register-an-application-with-azure-ad"></a>Registro de una aplicación con Azure AD

El siguiente paso para usar Azure AD para la autenticación es registrar una aplicación con la [Plataforma de identidad de Microsoft](../active-directory/develop/quickstart-register-app.md). Si tiene problemas para crear la aplicación, asegúrese de que tiene los [permisos necesarios para registrar una aplicación](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Para registrar una aplicación con Azure AD:

1. Inicie sesión en su cuenta de Azure en [Azure Portal](https://portal.azure.com).
1. Seleccione **Azure Active Directory**.
1. Seleccione **Registros de aplicaciones**.
1. Seleccione **Nuevo registro**.
1. Asigne un nombre a la aplicación y seleccione un tipo de cuenta compatible, que determina quién puede usar la aplicación. Después, seleccione **Registrar**.

![Asistente para registrar una aplicación](media/search-howto-aad/register-app.png)

En este punto, ha creado una aplicación de Azure AD y una entidad de servicio. Anote el identificador de inquilino (o directorio) y el identificador de cliente (o aplicación) en la página de información general del registro de la aplicación. Necesitará esos valores en un paso posterior.

## <a name="create-a-client-secret"></a>Creación de un secreto de cliente

La aplicación también necesitará un secreto de cliente para demostrar su identidad al solicitar un token. En este documento, mostraremos cómo usar un secreto de cliente.

1. Vaya al registro de aplicaciones que acaba de crear.
1. Seleccione **Certificates and secrets** (Certificados y secretos).
1. En **Secretos de cliente**, haga clic en **Nuevo secreto de cliente**.
1. Proporcione una descripción para el secreto y elija el intervalo de expiración deseado.

![asistente para crear un secreto de cliente](media/search-howto-aad/create-secret.png)

Asegúrese de guardar el valor del secreto en una ubicación segura, ya que no podrá volver a acceder a dicho. 

## <a name="grant-your-application-permissions-to-azure-cognitive-search"></a>Concesión de permisos para Azure Cognitive Search a la aplicación

A continuación, debe conceder a la aplicación de Azure AD acceso al servicio de búsqueda. Azure Cognitive Search tiene varios [roles integrados](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#built-in-roles-used-in-search) que se pueden usar en función del acceso requerido por la aplicación.

En general, es mejor proporcionar a la aplicación solo el acceso necesario. Por ejemplo, si la aplicación solo necesita poder consultar el índice de búsqueda, podría concederle el rol [Lector de datos de índice de búsqueda (versión preliminar)](../role-based-access-control/built-in-roles.md#search-index-data-reader). Como alternativa, si necesita poder leer y escribir en un índice de búsqueda, podría usar el rol [Colaborador de datos de índice de búsqueda (versión preliminar)](../role-based-access-control/built-in-roles.md#search-index-data-contributor).

Para asignar un rol al registro de aplicaciones:

1. Abra Azure Portal y vuelva al servicio de búsqueda.
1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo.
1. En el lado derecho, en **Grant access to this resource** (Conceder acceso a este recurso), seleccione **Agregar asignación de roles**.
1. Seleccione el rol que desea usar y, a continuación, haga clic en **Siguiente**.
1. En la página siguiente, haga clic en **Seleccionar miembros** y busque la aplicación que creó anteriormente. 
1. Finalmente, haga clic en **Review + assign** (Revisar y asignar).

![Incorporación de asignación de roles en Azure Portal](media/search-howto-aad/role-assignment.png)

También puede [asignar roles mediante PowerShell](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-3-assign-roles).

### <a name="create-a-custom-role"></a>Crear un rol personalizado

Además de usar [roles integrados,](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#built-in-roles-used-in-search)también puede crear un [rol personalizado](../role-based-access-control/custom-roles.md) para definir exactamente lo que le gustaría que la aplicación pueda hacer.

Por ejemplo, si desea un rol que tenga la capacidad de administrar completamente los índices, incluida la capacidad de crear índices y leer datos de ellos, podría definir el rol que se muestra a continuación:

```json
{
  "Name": "Search Index Manager",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can manage search indexes and read or write to them",
  "Actions": [
    "Microsoft.Search/searchServices/indexes/*",
    
  ],
  "NotActions": [],
  "DataActions": [
      "Microsoft.Search/searchServices/indexes/documents/*"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}"
  ]
}
```

Puede crear roles personalizados con [Azure Portal](../role-based-access-control/custom-roles-portal.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md), la [CLI de Azure](../role-based-access-control/custom-roles-cli.md) o la [API REST](../role-based-access-control/custom-roles-rest.md). El código JSON anterior muestra la sintaxis para crear un rol personalizado con PowerShell.

Para obtener la lista completa de las operaciones disponibles, vea [Operaciones del proveedor de recursos Microsoft.Search](../role-based-access-control/resource-provider-operations.md#microsoftsearch).


### <a name="grant-access-to-only-a-single-index"></a>Concesión de acceso a un solo índice

En algunos escenarios, es posible que quiera tener en cuenta el acceso de una aplicación a un único recurso, como un índice. 

Actualmente, el portal no admite la concesión de acceso a un solo índice, pero se puede realizar con [PowerShell](../role-based-access-control/role-assignments-powershell.md) o la [CLI de Azure](../role-based-access-control/role-assignments-cli.md).

En PowerShell, usaría [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), proporcionando el nombre de usuario o grupo de Azure y el ámbito de la asignación.

Antes de empezar, asegúrese de cargar los módulos Azure y AzureAD y conectarse a su cuenta de Azure:

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

Para agregar una asignación de roles con ámbito a un índice individual, ejecutaría el siguiente comando:

```powershell
New-AzRoleAssignment -ObjectId <objectId> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

## <a name="set-up-azure-ad-authentication-in-your-client"></a>Configuración de la autenticación de Azure AD en el cliente

Una vez que haya creado una aplicación de Azure AD y le haya concedido permisos para acceder al servicio de búsqueda, estará listo para agregar código a la aplicación para autenticar una entidad de seguridad y adquirir un token de OAuth 2.0.

### <a name="azure-ad-authentication-with-the-net-sdk"></a>Autenticación de Azure AD con el SDK de .NET

Los SDK de Azure facilitan la integración con Azure AD. La versión [11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) y las versiones más recientes del SDK de .NET admiten la autenticación de Azure AD. La autenticación de Azure AD también se admite en los SDK de versión preliminar para [Java](https://search.maven.org/artifact/com.azure/azure-search-documents/11.5.0-beta.3/jar), [Python](https://pypi.org/project/azure-search-documents/11.3.0b3/) y [JavaScript](https://www.npmjs.com/package/@azure/search-documents/v/11.3.0-beta.3).

Como punto de partida, clone el [código fuente](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) del [inicio rápido de C#](search-get-started-dotnet.md).  El inicio rápido actualmente usa la autenticación basada en claves para crear `SearchClient` y `SearchIndexClient`, pero puede realizar un pequeño cambio para cambiar a la autenticación basada en roles. En lugar de usar `AzureKeyCredential` al principio de `Main()` en [Program.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v11/AzureSearchQuickstart-v11/Program.cs): 

```dotnet
AzureKeyCredential credential = new AzureKeyCredential(apiKey);

// Create a SearchIndexClient to send create/delete index commands
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);
// Create a SearchClient to load and query documents
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
```

Puede usar `ClientSecretCredential` para autenticarse con el servicio de búsqueda.

Con esto, necesitará:
+ El identificador de inquilino (o directorio). Esto se puede recuperar de la página de información general de su registro de aplicaciones.
+ El identificador de cliente (o aplicación). Esto se puede recuperar de la página de información general de su registro de aplicaciones.
+ Valor del secreto de cliente que copió en un paso de versión preliminar.

```dotnet
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, tokenCredential);
```

Tendrá que importar la biblioteca [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/) para usar `ClientSecretCredential`.

La documentación de Azure.Identity también incluye detalles adicionales sobre el uso de la [autenticación de Azure AD con el SDK de Azure para .NET](/dotnet/api/overview/azure/identity-readme).

### <a name="azure-ad-authentication-with-the-rest-api"></a>Autenticación de Azure AD con la API REST

El uso de un SDK de Azure simplifica el flujo de OAuth 2.0, pero también puede programar directamente con el protocolo de la aplicación. En [Plataforma de identidad de Microsoft y flujo de credenciales de cliente de OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) puede encontrar detalles completos.

#### <a name="get-a-token"></a>Obtención de un token

Comience [obteniendo un token](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token) de la Plataforma de identidad de Microsoft:

```
POST /[tenant id]/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=[client id]
&scope=https%3A%2F%2Fsearch.azure.com%2F.default
&client_secret=[client secret]
&grant_type=client_credentials
```

El ámbito necesario es "https://search.azure.com/.default". 

#### <a name="use-a-token"></a>Uso de un token

Ahora que tiene un token, está listo para emitir una solicitud al servicio de búsqueda. 

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
Content-Type: application/json   
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="see-also"></a>Consulte también

+ [Uso de la autorización basada en roles en Azure Cognitive Search](search-security-rbac.md)
+ [Autorización del acceso a aplicaciones web de Azure Active Directory mediante el flujo de concesión de código OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md)
+ [Integración con Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration)
+ [Roles personalizados en los recursos de Azure](../role-based-access-control/custom-roles.md)
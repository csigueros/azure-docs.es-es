---
title: Uso de la biblioteca cliente de Azure Identity para obtener un token de acceso para la autorización
titleSuffix: Azure Storage
description: Aprenda a usar la biblioteca cliente de Azure Identity para obtener un token de acceso que las aplicaciones pueden usar para autorizar el acceso a los datos en Azure Storage. Con la biblioteca de Azure Identity, puede usar el mismo código para obtener el token de acceso en el entorno de desarrollo o en Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 62033ff6c76c2c16a6a3b0d442fec843ae2e86af
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724024"
---
# <a name="use-the-azure-identity-library-to-get-an-access-token-for-authorization"></a>Uso de la biblioteca cliente de Azure Identity para obtener un token de acceso para la autorización

La biblioteca cliente de Azure Identity simplifica el proceso de obtención de un token de acceso de OAuth 2.0 para la autorización con Azure Active Directory (Azure AD) mediante [Azure SDK](https://github.com/Azure/azure-sdk). Las versiones más recientes de las bibliotecas cliente de Azure Storage para .NET, Java, Python, JavaScript y Go se integran con las bibliotecas de Azure Identity para dichos lenguajes para proporcionar un medio sencillo y seguro de obtener un token de acceso para la autorización de las solicitudes de Azure Storage.

Una ventaja de la biblioteca cliente de Azure Identity es que permite usar el mismo código para adquirir el token de acceso tanto si la aplicación se ejecuta en el entorno de desarrollo como en Azure. La biblioteca cliente de Azure Identity devuelve un token de acceso para una entidad de seguridad. Cuando el código se ejecuta en Azure, la entidad de seguridad puede ser una identidad administrada para recursos de Azure, una entidad de servicio, un usuario o un grupo. En el entorno de desarrollo, la biblioteca cliente proporciona un token de acceso para un usuario o una entidad de servicio con fines de prueba.

El token de acceso devuelto por la biblioteca cliente de Azure Identity se encapsula en una credencial de token. A continuación, puede usar la credencial de token para obtener un objeto de cliente de servicio que se usará para realizar operaciones autorizadas en Azure Storage. Una manera sencilla de obtener el token de acceso y la credencial de token es usar la clase **DefaultAzureCredential** proporcionada por la biblioteca cliente de Azure Identity. Una instancia de esta clase intenta obtener la credencial de token de varias maneras comunes y funciona tanto en el entorno de desarrollo como en Azure.

Para más información sobre la biblioteca cliente de Azure Identity para su lenguaje, consulte uno de los siguientes artículos:

- [Biblioteca de cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme)
- [Biblioteca cliente de Azure Identity para Java](/java/api/overview/azure/identity-readme)
- [Biblioteca cliente de Azure Identity para Python](/python/api/overview/azure/identity-readme)
- [Biblioteca cliente de Azure Identity para JavaScript](/javascript/api/overview/azure/identity-readme)
- [Biblioteca de cliente de Azure Identity para Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/azidentity)

## <a name="assign-azure-roles-for-access-to-data"></a>Asignación de roles de Azure para el acceso a los datos

Cuando una entidad de seguridad de Azure AD intenta acceder a datos de una cuenta de Azure Storage, dicha entidad de seguridad debe tener permisos en el recurso de datos. Si la entidad de seguridad es una identidad administrada en Azure o una cuenta de usuario de Azure AD que ejecuta código en el entorno de desarrollo, se le debe asignar a la entidad de seguridad un rol de Azure que conceda acceso a los datos de Azure Storage. Para obtener información sobre cómo asignar permisos para el acceso a datos mediante RBAC de Azure, consulte uno de los siguientes artículos:

- [Asignación de un rol de Azure el acceso a datos de blob](../blobs/assign-azure-role-data-access.md).
- [Asignación de un rol de Azure para acceder a datos de cola](../queues/assign-azure-role-data-access.md)
- [Asignación de un rol de Azure para acceder a datos de tablas (versión preliminar)](../tables/assign-azure-role-data-access.md)

> [!NOTE]
> Al crear una cuenta de Azure Storage, no se le asignan automáticamente permisos para tener acceso a datos a través de Azure AD. Tiene que asignarse a sí mismo de forma explícita un rol de Azure para Azure Storage. Puede asignarlo en el nivel de suscripción, grupo de recursos, cuenta de almacenamiento, contenedor, cola o tabla.

## <a name="authenticate-the-user-in-the-development-environment"></a>Autenticación del usuario en el entorno de desarrollo

Cuando el código se ejecuta en el entorno de desarrollo, la autenticación se puede administrar automáticamente o puede requerir el inicio de sesión en un explorador, según las herramientas que use. Por ejemplo, Microsoft Visual Studio y Microsoft Visual Studio Code admiten el inicio de sesión único (SSO), de forma que la cuenta de usuario de Azure AD activa se utiliza automáticamente para la autenticación. Para más información sobre el inicio de sesión único, consulte [Inicio de sesión único en las aplicaciones](../../active-directory/manage-apps/what-is-single-sign-on.md).

También puede crear una entidad de servicio y establecer variables de entorno que el entorno de desarrollo puede leer en tiempo de ejecución.

## <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticación de una entidad de servicio en el entorno de desarrollo

Si el entorno de desarrollo no admite el inicio de sesión único a través de un explorador web, puede usar una entidad de servicio para autenticarse desde el entorno de desarrollo. Después de crear la entidad de servicio, agregue los valores devueltos para la entidad de servicio a las variables de entorno.

### <a name="create-the-service-principal"></a>Creación de la entidad de servicio

Para crear una entidad de servicio con la CLI de Azure y asignar el rol de Azure, llame al comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac). Proporcione un rol de acceso a datos Azure Storage para asignar a la nueva entidad de servicio. Además, proporcione el ámbito de la asignación de roles. Para más información sobre los roles integrados que se proporcionan para Azure Storage, consulte [Roles integrados de Azure](../../role-based-access-control/built-in-roles.md).

Si no tiene permisos suficientes para asignar un rol a la entidad de servicio, puede que tenga que pedir al propietario o administrador de la cuenta que realice la asignación de roles.

En el ejemplo siguiente, se usa la CLI de Azure para crear una nueva entidad de servicio y asignarle el rol **Colaborador de datos de Storage Blob** con ámbito en la cuenta de almacenamiento:

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

El comando `az ad sp create-for-rbac` devuelve una lista de propiedades de entidad de servicio en formato JSON. Copie estos valores para poder usarlos con el fin de crear las variables de entorno necesarias en el paso siguiente.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

Para más información acerca de cómo crear una entidad de seguridad de servicio, consulte uno de los siguientes artículos:

- [Creación de una aplicación y una entidad de servicio de Azure AD en el portal](../../active-directory/develop/howto-create-service-principal-portal.md)
- [Creación de una entidad de servicio de Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Creación de una entidad de servicio de Azure con CLI de Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

> [!IMPORTANT]
> Las asignaciones de roles de Azure pueden tardar varios minutos en propagarse.

### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

La biblioteca cliente de Azure Identity lee valores de tres variables de entorno en tiempo de ejecución para autenticar la entidad de servicio. En la tabla siguiente se describe el valor que se debe establecer para cada variable de entorno.

|Variable de entorno|Value
|-|-
|`AZURE_CLIENT_ID`|Id. de la aplicación de la entidad de servicio
|`AZURE_TENANT_ID`|Id. de inquilino de Azure AD de la entidad de servicio
|`AZURE_CLIENT_SECRET`|Contraseña generada para la entidad de servicio

> [!IMPORTANT]
> Después de establecer las variables de entorno, cierre y vuelva a abrir la ventana de la consola. Si usa Visual Studio u otro entorno de desarrollo, es posible que tenga que reiniciar el entorno de desarrollo para que registre las nuevas variables de entorno.

Para más información, consulte [Creación de una identidad para aplicaciones de Azure en el portal](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="get-an-access-token-for-authorization"></a>Obtención de un token de acceso para la autorización

La biblioteca cliente de Azure Identity proporciona clases que puede usar para obtener un token de acceso para autorizar solicitudes con Azure AD. Las bibliotecas cliente de Azure Storage incluyen constructores para objetos de cliente de servicio que toman una credencial de token como parámetro. Puede usar los dos juntos para autorizar fácilmente las solicitudes de Azure Storage con credenciales de Azure AD.

Se recomienda usar la clase **DefaultAzureCredential** en la mayoría de los escenarios sencillos en los que la aplicación necesita obtener un token de acceso tanto en el entorno de desarrollo como en Azure. También hay disponibles varios tipos de credenciales de token para otros escenarios.

En el ejemplo siguiente, se muestra cómo usar DefaultAzureCredential para obtener un token en .NET. A continuación, la aplicación usa el token para crear un nuevo cliente de servicio, que luego se usa para crear un contenedor de blobs. Aunque en este ejemplo se usan .NET y el servicio Blob Storage, la clase **DefaultAzureCredential** se comporta de forma similar con otros lenguajes y con otros servicios de Azure.

```csharp
static void CreateBlobContainer(string accountName, string containerName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a token credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                  new DefaultAzureCredential());

    // Create the container if it does not exist.
    containerClient.CreateIfNotExists();
}
```  

Para más información sobre el uso de la clase DefaultAzureCredential para autorizar a una identidad administrada el acceso a Azure Storage, consulte [Biblioteca cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="see-also"></a>Consulte también

- [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Bibliotecas de autenticación de la Plataforma de identidad de Microsoft](../../active-directory/develop/reference-v2-libraries.md)
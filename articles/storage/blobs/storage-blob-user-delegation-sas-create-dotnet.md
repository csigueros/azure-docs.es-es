---
title: Uso de .NET para crear una SAS de delegación de usuarios para un contenedor, un directorio o un blob
titleSuffix: Azure Storage
description: Aprenda a crear una SAS de delegación de usuarios con credenciales de Azure Active Directory en Azure Storage mediante la biblioteca cliente de .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 80c02416f5e1fb232d84b1311d261122dc7b9a35
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733671"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>Creación de una SAS de delegación de usuarios para un contenedor, un directorio o un blob con .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

En este artículo se muestra cómo usar credenciales de Azure Active Directory (Azure AD) para crear una SAS de delegación de usuarios para un contenedor, un directorio o un blob con la biblioteca cliente de Azure Storage para .NET versión 12.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Asignación de roles de Azure para el acceso a los datos

Cuando una entidad de seguridad de Azure AD intenta acceder a los datos de blobs, dicha entidad debe tener permisos para el recurso. Si la entidad de seguridad es una identidad administrada en Azure o una cuenta de usuario de Azure AD que ejecuta código en el entorno de desarrollo, se le debe asignar un rol de Azure que conceda acceso a los datos de blobs en Azure Storage. Para obtener información sobre la asignación de permisos mediante Azure RBAC, consulte [Asignación de un rol de Azure para acceder a datos de blobs](assign-azure-role-data-access.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para más información sobre cómo autenticarse con la biblioteca de cliente de Azure Identity desde Azure Storage, consulte la sección titulada **Autenticación con la biblioteca de Azure Identity** en [Autorización del acceso a blobs y colas con Azure Active Directory e identidades administradas para los recursos de Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Obtención de una credencial de token autenticada

Para obtener una credencial de token que el código pueda usar para autorizar solicitudes para Azure Storage, cree una instancia de la clase [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

En el fragmento de código siguiente se muestra cómo obtener la credencial de token autenticada y usarla para crear un cliente de servicio para Blob Storage:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obtención de la clave de delegación de usuario

Cada SAS se firma con una clave. Para crear una SAS de delegación de usuarios, primero debe solicitar una clave de delegación de usuario, que se usará para firmar la SAS. La clave de delegación de usuarios es análoga a la clave de cuenta usada para firmar una SAS de servicio o una SAS de cuenta, excepto en que se basa en las credenciales de Azure AD. Cuando un cliente solicita una clave de delegación de usuario mediante un token de OAuth 2.0, Azure Storage devuelve la clave de delegación de usuario en nombre del usuario.

Una vez que tenga la clave de delegación de usuario, puede usar esa clave para crear cualquier número de firmas de acceso compartido de delegación de usuarios durante la vigencia de la clave. La clave de delegación de usuario es independiente del token de OAuth 2.0 que se usa para adquirirla, por lo que no es necesario renovar el token siempre y cuando la clave siga siendo válida. Puede especificar que la clave sea válida para un período de hasta 7 días.

Use uno de los métodos siguientes para solicitar la clave de delegación de usuario:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

El siguiente fragmento de código obtiene la clave de delegación de usuario y escribe sus propiedades:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Obtención de una SAS de delegación de usuarios para un blob

En el siguiente código de ejemplo se muestra el código completo para autenticar la entidad de seguridad y crear la SAS de delegación de usuarios para un blob:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

En el ejemplo siguiente se prueba la SAS de delegación de usuarios creada en el ejemplo anterior desde una aplicación cliente simulada. Si la SAS es válida, la aplicación cliente puede leer el contenido del blob. Si la SAS no es válida, por ejemplo, si ha expirado, Azure Storage devuelve el código de error 403 (prohibido).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Obtención de una SAS de delegación de usuarios para un contenedor

En el ejemplo de código siguiente se muestra cómo generar una SAS de delegación de usuarios para un contenedor:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

En el ejemplo siguiente se prueba la SAS de delegación de usuarios creada en el ejemplo anterior desde una aplicación cliente simulada. Si la SAS es válida, la aplicación cliente puede leer el contenido del blob. Si la SAS no es válida, por ejemplo, si ha expirado, Azure Storage devuelve el código de error 403 (prohibido).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Obtención de una SAS de delegación de usuarios para un directorio

En el ejemplo de código siguiente se muestra cómo generar una SAS de delegación de usuarios para un directorio cuando hay un espacio de nombres jerárquico habilitado para la cuenta de almacenamiento:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

En el ejemplo siguiente se prueba la SAS de delegación de usuarios creada en el ejemplo anterior desde una aplicación cliente simulada. Si la SAS es válida, la aplicación cliente puede mostrar las rutas de acceso de archivo de este directorio. Si la SAS no es válida, por ejemplo, si ha expirado, Azure Storage devuelve el código de error 403 (prohibido).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Consulte también

- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md) (Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido [SAS])
- [Obtener la operación de clave de delegación de usuario](/rest/api/storageservices/get-user-delegation-key)
- [Creación de una SAS de delegación de usuario (API de REST)](/rest/api/storageservices/create-user-delegation-sas)

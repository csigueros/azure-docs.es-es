---
title: Autorización del acceso a datos de blobs con una identidad administrada
titleSuffix: Azure Storage
description: Utilice identidades administradas para recursos de Azure para autorizar el acceso a datos de blobs desde aplicaciones que se ejecutan en máquinas virtuales de Azure, aplicaciones de funciones y otras.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 774bdecb65b885bcf7e930729ba2152b0bbbca39
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859605"
---
# <a name="authorize-access-to-blob-data-with-managed-identities-for-azure-resources"></a>Autorización del acceso a datos de blobs con identidades administradas para recursos de Azure

Azure Blob Storage admite la autenticación de Azure Active Directory (Azure AD) con [identidades administradas para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md). Las identidades administradas para recursos de Azure pueden autorizar el acceso a datos de blobs con credenciales de Azure AD desde aplicaciones que se ejecutan en máquinas virtuales (VM) de Azure, aplicaciones de funciones, conjuntos de escalado de máquinas virtuales y otros servicios. Si usa identidades administradas para recursos de Azure junto con autenticación de Azure AD, puede evitar el almacenamiento de credenciales con las aplicaciones que se ejecutan en la nube.

En este artículo, se muestra cómo autorizar el acceso a datos de blobs desde una máquina virtual de Azure mediante identidades administradas para recursos de Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitación de identidades administradas en una máquina virtual

Para poder usar identidades administradas para recursos de Azure a fin de autorizar el acceso a blobs desde la máquina virtual, primero debe habilitar las identidades administradas para recursos de Azure en la máquina virtual. Para aprender a habilitar las identidades administradas para los recursos de Azure, consulte uno de estos artículos:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI de Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Plantilla de Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas cliente de Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Para más información sobre las identidades administradas, consulte [Identidades administradas para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>Asignación de un rol RBAC a una identidad administrada

Cuando una entidad de seguridad de Azure AD intenta acceder a datos de una cuenta de Azure Storage, dicha entidad de seguridad debe tener permisos en el recurso de datos. Si la entidad de seguridad es una identidad administrada en Azure o una cuenta de usuario de Azure AD que ejecuta código en el entorno de desarrollo, se le debe asignar a la entidad de seguridad un rol de Azure que conceda acceso a los datos de Azure Storage. Para obtener información sobre la asignación de permisos para el acceso a datos mediante RBAC de Azure, consulte [Asignación de un rol de Azure para acceder a datos de blobs](assign-azure-role-data-access.md).

> [!NOTE]
> Al crear una cuenta de Azure Storage, no se le asignan automáticamente permisos para tener acceso a datos a través de Azure AD. Tiene que asignarse a sí mismo de forma explícita un rol de Azure para Azure Storage. Puede asignarlo al nivel de su suscripción, grupo de recursos, cuenta de almacenamiento o un contenedor.

## <a name="use-a-managed-identity-to-create-a-block-blob-in-net"></a>Uso de una identidad administrada para crear un blob en bloques en .NET

La biblioteca cliente de Azure Identity simplifica el proceso de obtención de un token de acceso de OAuth 2.0 para la autorización con Azure Active Directory (Azure AD) mediante [Azure SDK](https://github.com/Azure/azure-sdk). Al usar la biblioteca cliente de Azure Identity para obtener un token de acceso, puede usar el mismo código para adquirir el token tanto si la aplicación se ejecuta en el entorno de desarrollo como en Azure. Para más información, consulte [Uso de la biblioteca de Azure Identity para obtener un token de acceso para la autorización](../common/identity-library-acquire-token.md).

Para obtener un token que el código pueda usar para autorizar solicitudes para Azure Storage, cree una instancia de la clase [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). A continuación, puede usar el token para crear un objeto de cliente de servicio que esté autorizado para realizar operaciones de datos en Azure Storage. Para más información sobre el uso de la clase **DefaultAzureCredential** para autorizar a una identidad administrada a acceder a Azure Storage, consulte [Biblioteca cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme).

En el ejemplo de código siguiente, se muestra cómo obtener un token de acceso y usarlo para crear un objeto de cliente de servicio y, luego, usar el cliente de servicio para cargar un nuevo blob:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Para autorizar solicitudes de datos de blobs con Azure AD, debe usar HTTPS para esas solicitudes.

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de un rol de Azure el acceso a datos de blob](assign-azure-role-data-access.md).
- [Autorización del acceso a datos de blobs o colas desde una aplicación nativa o web](../common/storage-auth-aad-app.md)
- [Tutorial: Almacenamiento del acceso de App Service mediante identidades administradas](../../app-service/scenario-secure-app-access-storage.md)

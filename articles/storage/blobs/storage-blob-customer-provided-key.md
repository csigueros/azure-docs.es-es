---
title: 'Especificar una clave proporcionada por el cliente en una solicitud a Blob Storage con .NET: Azure Storage'
description: Obtenga información sobre cómo especificar una clave proporcionada por el cliente en una solicitud a Blob Storage mediante .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b6f7001b9eabd34774930378d0656b4ad7ef3c25
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857804"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Especificar una clave proporcionada por el cliente en una solicitud a Blob Storage con .NET

Los clientes que realizan solicitudes en Azure Blob Storage tienen la opción de proporcionar una clave de cifrado AES-256 en una solicitud individual. La inclusión de la clave de cifrado en la solicitud proporciona un control detallado sobre la configuración de cifrado para las operaciones de almacenamiento de blobs. Las claves proporcionadas por el cliente se pueden almacenar en Azure Key Vault o en otro almacén de claves.

En este artículo se muestra cómo especificar una clave proporcionada por el cliente en una solicitud con .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para obtener más información sobre cómo autenticarse con la biblioteca de cliente de identidad de Azure, consulte [Biblioteca cliente de identidades de Azure para .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Uso de una clave proporcionada por el cliente para escribir en un blob

En el ejemplo siguiente se proporciona una clave AES-256 al cargar un blob con la biblioteca cliente v12 para Blob Storage. En el ejemplo se usa el objeto [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) para autorizar la solicitud de escritura con Azure AD, pero también se puede autorizar la solicitud con credenciales de clave compartida. Para más información sobre el uso de la clase DefaultAzureCredential para autorizar a una identidad administrada el acceso a Azure Storage, consulte [Biblioteca cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme).

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Especificación de una clave de cifrado en una solicitud a Blob Storage](encryption-customer-provided-keys.md)
- [Cifrado de Azure Storage para datos en reposo](../common/storage-service-encryption.md)

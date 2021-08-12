---
title: Creación de una SAS de servicio para un contenedor o blob
titleSuffix: Azure Storage
description: Aprenda a crear una firma de acceso compartido (SAS) de servicio para un contenedor o un blob mediante la biblioteca cliente de Azure Blob Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 49b01e8322647a35ba24ccc9a7708b2a8754553f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110477747"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Creación de una SAS de servicio para un contenedor o blob

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

En este artículo se muestra cómo usar la clave de cuenta de almacenamiento para crear una SAS de servicio para un contenedor o un blob con la biblioteca cliente de Azure Storage para Blob Storage.

## <a name="create-a-service-sas-for-a-blob-container"></a>Creación de una SAS de servicio para un contenedor de blob

En el ejemplo de código siguiente se crea una SAS para un contenedor. Si se proporciona el nombre de una directiva de acceso almacenada existente, esa directiva se asocia con la SAS. Si no se proporciona ninguna directiva de acceso almacenada, el código crea una SAS ad hoc en el contenedor.

### <a name="net-v12-sdk"></a>[SDK de .NET, versión 12](#tab/dotnet)

Una SAS de servicio se firma con la clave de acceso de la cuenta. Use la clase [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) para crear la credencial que se usa para firmar la SAS. A continuación, cree un nuevo objeto [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) y llame al elemento [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obtener la cadena de token de SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11-sdk"></a>[SDK de .NET, versión 11](#tab/dotnetv11)

Para crear una SAS de servicio para un contenedor, llame al método [CloudBlobContainer.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature).

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12-sdk"></a>[SDK de JavaScript v12](#tab/javascript)

Una SAS de servicio se firma con la clave de acceso de la cuenta. Use la clase [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) para crear la credencial que se usa para firmar la SAS. A continuación, llame a la función [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) y proporcione los parámetros necesarios para obtener la cadena de tokens de SAS.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_ContainerSAS":::

---

## <a name="create-a-service-sas-for-a-blob"></a>Creación de una SAS de servicio para un blob

El ejemplo de código siguiente crea una SAS en un blob. Si se proporciona el nombre de una directiva de acceso almacenada existente, esa directiva se asocia con la SAS. Si no se proporciona ninguna directiva de acceso almacenada, el código crea una SAS ad hoc en el blob.

# <a name="net-v12-sdk"></a>[SDK de .NET, versión 12](#tab/dotnet)

Una SAS de servicio se firma con la clave de acceso de la cuenta. Use la clase [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) para crear la credencial que se usa para firmar la SAS. A continuación, cree un nuevo objeto [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) y llame al elemento [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obtener la cadena de token de SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11-sdk"></a>[SDK de .NET, versión 11](#tab/dotnetv11)

Para crear una SAS de servicio para un blob, llame al método [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature).

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12-sdk"></a>[SDK de JavaScript v12](#tab/javascript)

Para crear una SAS de servicio para un blob, llame al método [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature).

Para crear una SAS de servicio para un blob, llame a la función [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) y proporcione los parámetros necesarios.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_BlobSAS":::

---

## <a name="create-a-service-sas-for-a-directory"></a>Creación de una SAS de servicio para un directorio

En una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado, puede crear una SAS de servicio para un directorio. Para crear la SAS de servicio, asegúrese de que ha instalado la versión 12.5.0 o posterior del paquete [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/).

En el ejemplo siguiente se muestra cómo crear una SAS de servicio para un directorio con la biblioteca cliente v12 para .NET:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido (SAS)](../common/storage-sas-overview.md)
- [Create a service SAS](/rest/api/storageservices/create-service-sas) (Creación de una SAS de servicio)

---
title: Protección del acceso a los datos de la aplicación
titleSuffix: Azure Storage
description: Use tokens de SAS, cifrado y HTTPS para proteger los datos de la aplicación en la nube.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 607f7921b62540d26b1354c21f3352f5b5b943f4
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376655"
---
# <a name="secure-access-to-application-data"></a>Protección del acceso a los datos de la aplicación

Este tutorial es la tercera parte de una serie. Aprenda a proteger el acceso a la cuenta de almacenamiento. 

En la tercera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Usar tokens de SAS para acceder a imágenes en miniatura
> * Activar el cifrado de servidor
> * Habilitar el transporte solo HTTPS

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) proporciona un servicio sólido para almacenar archivos de aplicaciones. Este tutorial amplía [el tema anterior][previous-tutorial] para mostrar cómo proteger el acceso a la cuenta de almacenamiento desde una aplicación web. Al terminar, las imágenes están cifradas y la aplicación web usa tokens de SAS seguros para acceder a las imágenes en miniatura.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe haber completado el tutorial anterior sobre el almacenamiento: [Automatización del cambio de tamaño de imágenes cargadas mediante Event Grid][previous-tutorial].

## <a name="set-container-public-access"></a>Establecimiento del acceso público a contenedores

En esta parte de la serie de tutoriales, se usan tokens de SAS para acceder a las vistas en miniatura. En este paso se establece el acceso público del contenedor *thumbnails* a `off`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=(Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $blobStorageAccount).Key1

Set-AzStorageAccount -ResourceGroupName "MyResourceGroup" -AccountName $blobStorageAccount -KeyName $blobStorageAccountKey -AllowBlobPublicAccess $false
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

---

## <a name="configure-sas-tokens-for-thumbnails"></a>Configuración de los tokens de SAS para las vistas en miniatura

En la primera parte de esta serie de tutoriales, la aplicación web mostraba imágenes desde un contenedor público. En esta parte de la serie, se usan tokens de firmas de acceso compartido (SAS) para recuperar las imágenes en miniatura. Los tokens de SAS permiten proporcionar acceso restringido a un contenedor o blob en función del IP, el protocolo, el intervalo de tiempo o los derechos permitidos. Para obtener más información sobre las firmas de acceso compartido, consulte [Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido (SAS)](../common/storage-sas-overview.md).

En este ejemplo, el repositorio de código fuente usa la rama `sasTokens`, que tiene un ejemplo de código actualizado. Elimine la implementación de GitHub existente con [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Luego configure la implementación de GitHub en la aplicación web con el comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

En el siguiente comando, `<web-app>` es el nombre de la aplicación web.

```azurecli
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

La rama `sasTokens` del repositorio actualiza el archivo `StorageHelper.cs`. Reemplaza la tarea `GetThumbNailUrls` por el ejemplo de código siguiente. La tarea actualizada recupera las direcciones URL de las miniaturas mediante el uso de [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) para especificar la hora de inicio, la hora de expiración y los permisos del token de SAS. Una vez implementada, la aplicación web recupera las vistas en miniatura con una dirección URL mediante un token de SAS. La tarea actualizada se muestra en el ejemplo siguiente:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

En la tarea anterior se usan las clases, las propiedades y los métodos siguientes:

| Clase | Propiedades | Métodos |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Nombre](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Consultar](/dotnet/api/system.uribuilder.query) |  |
|[Lista](/dotnet/api/system.collections.generic.list-1) | | [Add (Agregar)](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Cifrado de Azure Storage

El [cifrado de Azure Storage](../common/storage-service-encryption.md) ayuda a proteger los datos mediante el cifrado de los datos en reposo y la administración del cifrado y el descifrado. A todos los datos se les aplica el [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uno de los cifrados de bloques más seguros disponibles.

Puede elegir que Microsoft administre las claves de cifrado o traer sus propias claves con las claves administradas por el cliente almacenadas en Azure Key Vault o Azure Key Vault Managed Hardware Security Model (HSM) (versión preliminar). Para más información, consulte [Claves administradas por el cliente para el cifrado de Azure Storage](../common/customer-managed-keys-overview.md).

El cifrado de Azure Storage cifra automáticamente los datos de todos los niveles de rendimiento (Estándar y Premium), todos los modelos de implementación (Azure Resource Manager y clásico) y todos los servicios de Azure Storage (Blob, Queue, Table y File).

## <a name="enable-https-only"></a>Habilitación de solo HTTPS

Para asegurarse de que las solicitudes de datos a y desde una cuenta de almacenamiento están protegidas, puede limitar las solicitudes a solo HTTPS. Actualice el protocolo necesario de la cuenta de almacenamiento mediante el comando [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Pruebe la conexión con `curl` mediante el protocolo `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Ahora que se necesita una transferencia segura, recibe el mensaje siguiente:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Pasos siguientes

En la tercera parte de la serie, ha aprendido a proteger el acceso a la cuenta de almacenamiento, por ejemplo a:

> [!div class="checklist"]
> * Usar tokens de SAS para acceder a imágenes en miniatura
> * Activar el cifrado de servidor
> * Habilitar el transporte solo HTTPS

Vaya a la cuarta parte de la serie para aprender a supervisar una aplicación de almacenamiento en la nube y a solucionar los problemas que plantee.

> [!div class="nextstepaction"]
> [Supervisar aplicaciones de almacenamiento en la nube y solucionar problemas](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json

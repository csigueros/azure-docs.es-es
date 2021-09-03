---
title: Archivo de inclusión
description: archivo de inclusión
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 4a25423cbdd5b0efbd037138a3cdf072cb90ebed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802212"
---
### <a name="default"></a>Valor predeterminado

Puede enlazar con los tipos siguientes para la escritura de blobs:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> requiere un enlace "in" `direction` en *function.json* o `FileAccess.Read` en una biblioteca de clases de C#. Sin embargo, puede usar el objeto de contenedor que proporciona el tiempo de ejecución para escribir operaciones, como cargar blobs en el contenedor.

<sup>2</sup> requiere un enlace "inout" `direction` en *function.json* o `FileAccess.ReadWrite` en una biblioteca de clases de C#.

Si intenta enlazar a uno de los tipos de SDK de Storage y recibe un mensaje de error, asegúrese de que hace referencia a [la versión de SDK de Storage correcta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

El enlace a `string` o `Byte[]` solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`. Para más información, consulte [Uso de simultaneidad y memoria](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) que apareció anteriormente en este artículo.

### <a name="additional-types"></a>Tipos adicionales

Las aplicaciones que usan la [versión 5.0.0 o posterior de la extensión de Azure Storage](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) también pueden usar tipos de [Azure SDK para .NET](/dotnet/api/overview/azure/storage.blobs-readme). En esta versión se elimina la compatibilidad con los tipos `CloudBlobContainer`, `CloudBlobDirectory`, `ICloudBlob`, `CloudBlockBlob`, `CloudPageBlob` y `CloudAppendBlob` heredados en favor de los siguientes tipos:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> requiere un enlace "in" `direction` en *function.json* o `FileAccess.Read` en una biblioteca de clases de C#. Sin embargo, puede usar el objeto de contenedor que proporciona el tiempo de ejecución para escribir operaciones, como cargar blobs en el contenedor.

<sup>2</sup> requiere un enlace "inout" `direction` en *function.json* o `FileAccess.ReadWrite` en una biblioteca de clases de C#.

Para obtener ejemplos de uso de estos tipos, consulte el [repositorio de GitHub de la extensión](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples). Obtenga más información sobre estos nuevos tipos y cómo migrarlos desde la [Guía de migración de Azure.Storage.Blobs](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md).

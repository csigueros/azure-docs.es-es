---
title: Uso de .NET para administrar datos en Azure Data Lake Storage Gen2
description: Use la biblioteca cliente de Azure Storage de . NET con el fin de administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado el espacio de nombres jerárquico.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e13e8127377e99faea8da89912ee6f3d9fb1f20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590335"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Uso de .NET para administrar directorios y archivos en Azure Data Lake Storage Gen2

En este artículo se explica cómo usar .NET para crear y administrar directorios y archivos en cuentas de almacenamiento que tengan habilitado un espacio de nombres jerárquico.

Para obtener información sobre cómo obtener, establecer y actualizar las listas de control de acceso (ACL) de directorios y archivos, consulte [Uso de .NET para administrar listas de control de acceso en Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Paquete (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Ejemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [Referencia de API](/dotnet/api/azure.storage.files.datalake) | [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Requisitos previos

- Suscripción a Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Una cuenta de almacenamiento que tenga habilitado un espacio de nombres jerárquico. Siga [estas](create-data-lake-storage-account.md) instrucciones para crear uno.

## <a name="set-up-your-project"></a>Configurar su proyecto

Para empezar, instale el paquete NuGet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/).

Para más información sobre cómo instalar paquetes NuGet, consulte [Instalación y administración de paquetes en Visual Studio con el Administrador de paquetes NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

A continuación, agregue estas instrucciones USING al inicio del archivo de código.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Conexión con la cuenta

Para usar los fragmentos de código de este artículo, tiene que crear una instancia de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente la cuenta de almacenamiento.

### <a name="connect-by-using-an-account-key"></a>Conexión con una clave de cuenta

Es la manera más sencilla de conectarse a una cuenta.

En este ejemplo se crea una instancia de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) con una clave de cuenta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conexión con Azure Active Directory (Azure AD)

Puede usar la [biblioteca cliente de identidad de Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar la aplicación con Azure AD.

En este ejemplo se crea una instancia de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) con un identificador de cliente, un secreto de cliente y un identificador de inquilino.  Para obtener estos valores, consulte [Adquisición de un token de Azure AD para la autorización de solicitudes desde una aplicación cliente](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para ver más ejemplos, consulte la documentación de la [biblioteca cliente de identidad de Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="create-a-container"></a>Crear un contenedor

Un contenedor actúa como sistema de archivos para sus archivos. Para crear uno puede llamar al método [DataLakeServiceClient.CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync).

En este ejemplo se crea un contenedor denominado `my-file-system`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Creación de un directorio

Para crear una referencia de directorio, llame al método [DataLakeFileSystemClient.CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync).

En este ejemplo se agrega un directorio denominado `my-directory` a un contenedor y luego se agrega un subdirectorio denominado `my-subdirectory`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Cambio de nombre o traslado de un directorio

Cambie el nombre de un directorio o muévalo con el método [DataLakeDirectoryClient.RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync). Pase la ruta de acceso del directorio que busca a un parámetro.

En este ejemplo se cambia el nombre de un subdirectorio a `my-subdirectory-renamed`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

En este ejemplo se mueve un directorio denominado `my-subdirectory-renamed` a un subdirectorio de un directorio denominado `my-directory-2`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Eliminación de un directorio

Elimine un directorio o muévalo mediante una llamada al método [DataLakeDirectoryClient.Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete).

En este ejemplo se elimina un directorio denominado `my-directory`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Carga de un archivo en un directorio

En primer lugar, cree una referencia de archivo en el directorio de destino creando una instancia de la clase [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient). Cargue un archivo llamando al método [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Asegúrese de completar la carga llamando al método [DataLakeFileClient.FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync).

En este ejemplo se carga un archivo de texto en un directorio denominado `my-directory`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Si el tamaño del archivo es grande, el código tendrá que realizar varias llamadas al método [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Considere la posibilidad de usar en su lugar el método [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_). De este modo, puede cargar todo el archivo en una sola llamada.
>
> Vea la sección siguiente para obtener un ejemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Carga de un archivo de gran tamaño en un directorio

Use el método [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) para cargar archivos grandes sin tener que realizar varias llamadas al método [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Descarga de un directorio

En primer lugar, cree una instancia de [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) que represente al archivo que quiere descargar. Use el método [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) y analice el valor devuelto para obtener un objeto [Stream](/dotnet/api/system.io.stream). Use cualquier archivo .NET que procese API para guardar bytes de la transmisión en un archivo.

En este ejemplo se usa [BinaryReader](/dotnet/api/system.io.binaryreader) y [FileStream](/dotnet/api/system.io.filestream) para guardar bytes en un archivo.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Lista del contenido del directorio

Enumere el contenido de un directorio con una llamada al método [FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) y, después, con la enumeración de los resultados.

En este ejemplo, se imprimen los nombres de cada uno de los archivos que se ubican en un directorio denominado `my-directory`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Consulte también

- [Documentación de referencia de API](/dotnet/api/azure.storage.files.datalake)
- [Paquete (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Muestras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Asignación de Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problemas conocidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envíenos sus comentarios](https://github.com/Azure/azure-sdk-for-net/issues)

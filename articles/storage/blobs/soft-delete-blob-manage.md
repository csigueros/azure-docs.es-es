---
title: Administración y restauración de blobs eliminados temporalmente
titleSuffix: Azure Storage
description: Administre y restaure las instantáneas y los blobs eliminados temporalmente con Azure Portal o con las bibliotecas cliente de Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c66b970a99ad6dd69b9336d1638b2c156868324
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627945"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Administración y restauración de blobs eliminados temporalmente

La eliminación temporal de blobs protege a cada uno de los blobs y sus versiones, instantáneas y metadatos de errores accidentales al borrar o sobrescribir los datos, ya que conserva en el sistema los datos eliminados durante el período de tiempo que se especifique. Durante este período de retención, los blobs pueden restaurarse a su estado original. Una vez vencido el período de retención especificado, el blob se elimina permanentemente. Para más información sobre la eliminación temporal, consulte este artículo sobre la [eliminación temporal de blobs](soft-delete-blob-overview.md).

La eliminación temporal de blobs forma parte de una exhaustiva estrategia para proteger los datos de los blobs. Si necesita más información sobre las recomendaciones de Microsoft para proteger los datos, consulte [Información general sobre la protección de datos](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs"></a>Administración de blobs eliminados temporalmente

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Administración de blobs eliminados temporalmente con Azure Portal

Puede usar Azure Portal para ver y restaurar las instantáneas y los blobs eliminados temporalmente.

#### <a name="view-deleted-blobs"></a>Visualización de los blobs eliminados

Cuando los blobs se eliminan temporalmente, son invisibles en Azure Portal de manera predeterminada. Para ver los blobs eliminados temporalmente, vaya a la página **Información general** del contenedor y active la opción **Mostrar blobs eliminados**. Los blobs eliminados temporalmente se muestran con el estado **Eliminado**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Captura de pantalla que muestra cómo enumerar los blobs eliminados temporalmente en Azure Portal":::

A continuación, seleccione el blob eliminado de la lista de blobs para mostrar sus propiedades. En la pestaña **Información general**, observe que el estado del blob es **Eliminado**. El portal también muestra el número de días hasta que el blob se elimina permanentemente.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Captura de pantalla que muestra las propiedades del blob eliminado temporalmente en Azure Portal":::

#### <a name="view-deleted-snapshots"></a>Visualización de las instantáneas eliminadas

Al eliminar un blob también se eliminan todas las instantáneas que tiene asociadas. Si un blob eliminado temporalmente tiene instantáneas, las instantáneas eliminadas también se pueden mostrar en el portal. Muestre las propiedades del blob eliminado temporalmente, vaya a la pestaña **Instantáneas** y active la opción **Mostrar instantáneas eliminadas**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Captura de pantalla que muestra ":::

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restauración de objetos eliminados temporalmente cuando el control de versiones está deshabilitado

Para restaurar un blob eliminado temporalmente en Azure Portal cuando el control de versiones de blob no esté habilitado, primero muestre las propiedades del blob y, a continuación, seleccione el botón **Recuperar** en la pestaña **Información general**. La restauración de un blob también restaura las instantáneas que se eliminaron durante el período de retención de eliminación temporal.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Captura de pantalla que muestra cómo restaurar un contenedor eliminado temporalmente en Azure Portal":::

Para promover una instantánea eliminada temporalmente en el blob base, primero asegúrese de que se han restaurado las instantáneas de eliminación temporal del blob. Seleccione el botón **Recuperar** para restaurar las instantáneas eliminadas temporalmente del blob, incluso si el blob base no se ha eliminado temporalmente. A continuación, seleccione la instantánea que desea promover y use el botón **Promover instantánea** para sobrescribir el blob base con el contenido de la instantánea.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Captura de pantalla que muestra cómo promover una instantánea al blob base":::

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restauración de blobs eliminados temporalmente cuando el control de versiones está habilitado

Para restaurar un blob eliminado temporalmente en Azure Portal cuando el control de versiones está habilitado, seleccione el blob eliminado temporalmente para mostrar sus propiedades y, a continuación, seleccione la pestaña **Versiones**. Seleccione la versión que desea promover como versión actual y, después, seleccione **Crear versión actual**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Captura de pantalla que muestra cómo promover una versión para restaurar un blob en Azure Portal":::

Para restaurar las versiones o instantáneas eliminadas cuando el control de versiones está habilitado, muestre las propiedades del blob y, a continuación, seleccione el botón **Recuperar** en la pestaña **Información general**.

> [!NOTE]
> Cuando el control de versiones está habilitado, al seleccionar el botón **Recuperar** en un blob eliminado, se restauran las instantáneas o versiones eliminadas temporalmente, pero no se restaura el blob base. Para restaurar el blob base, debe promover una versión anterior.

### <a name="manage-soft-deleted-blobs-with-code"></a>Administración de blobs eliminados temporalmente con código

Puede usar las bibliotecas cliente de Azure Storage para restaurar un blob o una instantánea eliminados temporalmente. En el siguiente ejemplo, se muestra cómo usar la biblioteca cliente de .NET.

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restauración de objetos eliminados temporalmente cuando el control de versiones está deshabilitado

##### <a name="net-v12-sdk"></a>[SDK de .NET, versión 12](#tab/dotnet)

Para restaurar los blobs eliminados cuando el control de versiones no está habilitado, llame a la operación [Undelete Blob](/rest/api/storageservices/undelete-blob) en esos blobs. La operación **Undelete Blob** restaura los blobs eliminados temporalmente y las instantáneas eliminadas que tienen asociadas.

La llamada a **Undelete Blob** en un blob que no se ha eliminado no tiene ningún efecto. En el ejemplo siguiente se llama a **Undelete Blob** en todos los blobs de un contenedor y se restauran los blobs eliminados temporalmente y sus instantáneas:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Para restaurar una instantánea específica que se eliminó de manera temporal, primero llame a la operación **Undelete Blob** en el blob base y, a continuación, copie la instantánea deseada en el blob base. En el ejemplo siguiente se restaura un blob en bloques a la última instantánea generada:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

##### <a name="net-v11-sdk"></a>[SDK de .NET, versión 11](#tab/dotnet11)

Para restaurar los blobs eliminados cuando el control de versiones no está habilitado, llame a la operación [Undelete Blob](/rest/api/storageservices/undelete-blob) en esos blobs. La operación **Undelete Blob** restaura los blobs eliminados temporalmente y las instantáneas eliminadas que tienen asociadas.

La llamada a **Undelete Blob** en un blob que no se ha eliminado no tiene ningún efecto. En el ejemplo siguiente se llama a **Undelete Blob** en todos los blobs de un contenedor y se restauran los blobs eliminados temporalmente y sus instantáneas:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para restaurar una instantánea específica que se eliminó de manera temporal, primero llame a la operación **Undelete Blob** en el blob base y, a continuación, copie la instantánea deseada en el blob base. En el ejemplo siguiente se restaura un blob en bloques a su última instantánea generada:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restauración de blobs eliminados temporalmente cuando el control de versiones está habilitado

Para restaurar un blob eliminado temporalmente cuando el control de versiones está habilitado, copie una versión anterior en el blob base con una operación [Copy Blob](/rest/api/storageservices/copy-blob) o [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url).

##### <a name="net-v12-sdk"></a>[SDK de .NET, versión 12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

##### <a name="net-v11-sdk"></a>[SDK de .NET, versión 11](#tab/dotnet11)

No es aplicable. El control de versiones de blobs solo se admite en la versión 12.x de las bibliotecas cliente de Azure Storage y en versiones posteriores.

---

## <a name="manage-soft-deleted-blobs-and-directories-hierarchical-namespace"></a>Administración de blobs y directorios eliminados temporalmente (espacio de nombres jerárquico)

Puede restaurar blobs y directorios eliminados temporalmente en cuentas que tengan un espacio de nombres jerárquico.

> [!IMPORTANT]
> La eliminación temporal en las cuentas que tienen habilitada la característica de espacio de nombres jerárquico está actualmente en versión preliminar y está disponible globalmente en todas las regiones de Azure.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>
>
> Para inscribirse en la versión preliminar, visite [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Administración de blobs eliminados temporalmente con Azure Portal

Puede usar Azure Portal para ver y restaurar blobs y directorios eliminados temporalmente.

#### <a name="view-deleted-blobs-and-directories"></a>Visualización de blobs y directorios eliminados

Cuando los blobs o los directorios se eliminan temporalmente, son invisibles en Azure Portal de manera predeterminada. Para ver los blobs y directorios eliminados temporalmente, vaya a la página **Información general** del contenedor y active la opción **Mostrar blobs eliminados**. Los blobs y los directorios eliminados temporalmente se muestran con el estado **Eliminado**. En la imagen siguiente, se muestra un directorio eliminado temporalmente.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra cómo enumerar blobs eliminados temporalmente en Azure Portal (cuentas habilitadas para espacios de nombres jerárquicos).](media/soft-delete-blob-manage/soft-deleted-blobs-list-portal-hns.png)

> [!NOTE]
> Si cambia el nombre de un directorio que contiene elementos eliminados temporalmente (subdirectorios y blobs), esos elementos eliminados temporalmente se desconectan del directorio, por lo que no aparecerán en Azure Portal al alternar la configuración **Mostrar blobs eliminados**. Si desea verlos en Azure Portal, tendrá que revertir el nombre del directorio al nombre original o crear un directorio independiente que use el nombre del directorio original.

A continuación, seleccione el directorio o blob eliminado de la lista para mostrar sus propiedades. En la pestaña **Información general**, observe que el estado es **Eliminado**. El portal también muestra el número de días hasta que el blob se elimina permanentemente.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra las propiedades de un blob eliminado temporalmente en Azure Portal (cuentas habilitadas para espacios de nombres jerárquicos).](media/soft-delete-blob-manage/soft-deleted-blob-properties-portal-hns.png)

#### <a name="restore-soft-delete-blobs-and-directories"></a>Restauración de blobs y directorios eliminados temporalmente

Para restaurar un blob o un directorio eliminados temporalmente en Azure Portal, muestre primero las propiedades del blob o el directorio y, a continuación, seleccione el botón **Recuperar** en la pestaña **Información general**. En la imagen siguiente, se muestra el botón Recuperar en un directorio eliminado temporalmente.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla que muestra cómo restaurar un blob eliminado temporalmente en Azure Portal (cuentas habilitadas para espacios de nombres jerárquicos).](media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal-hns.png)

### <a name="restore-soft-deleted-blobs-and-directories-by-using-powershell"></a>Restauración de blobs y directorios eliminados temporalmente mediante PowerShell

> [!IMPORTANT]
> Esta sección solo se aplica a las cuentas que tienen un espacio de nombres jerárquico.

1. Asegúrese de tener instalado el módulo de la versión preliminar de **Az.Storage**. Para más información, consulte el artículo sobre la [habilitación de la eliminación temporal de blobs mediante PowerShell](soft-delete-blob-enable.md?tabs=azure-powershell#enable-blob-soft-delete-hierarchical-namespace).

2. Obtenga la autorización de la cuenta de almacenamiento mediante una clave de cuenta de almacenamiento, una cadena de conexión o Azure Active Directory (Azure AD). Para más información, consulte [Conexión con la cuenta](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account).

   En el ejemplo siguiente, se obtiene la autorización mediante una clave de cuenta de almacenamiento.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

3. Para restaurar un elemento eliminado temporalmente, utilice el comando `Restore-AzDataLakeGen2DeletedItem`.

   ```powershell
   $filesystemName = "my-file-system"
   $dirName="my-directory"
   $deletedItems = Get-AzDataLakeGen2DeletedItem -Context $ctx -FileSystem $filesystemName -Path $dirName
   $deletedItems | Restore-AzDataLakeGen2DeletedItem
   ```

   Si cambia el nombre del directorio que contiene los elementos eliminados temporalmente, esos elementos se desconectan del directorio. Si desea restaurar esos elementos, tendrá que revertir el nombre del directorio al nombre original o crear un directorio independiente que use el nombre del directorio original. De lo contrario, recibirá un error al intentar restaurar esos elementos eliminados temporalmente.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-azure-cli"></a>Restauración de blobs y directorios eliminados temporalmente mediante la CLI de Azure

> [!IMPORTANT]
> Esta sección solo se aplica a las cuentas que tienen un espacio de nombres jerárquico.

1. Asegúrese de tener instalada la extensión `storage-preview`. Para más información, consulte el artículo sobre la [habilitación de la eliminación temporal de blobs mediante PowerShell](soft-delete-blob-enable.md?tabs=azure-CLI#enable-blob-soft-delete-hierarchical-namespace).

2. Obtenga una lista de los elementos eliminados.

   ```azurecli
   $filesystemName = "my-file-system"
   az storage fs list-deleted-path -f $filesystemName --auth-mode login
   ```

3. Para restaurar un elemento, utilice el comando `az storage fs undelete-path`.

   ```azurecli
   $dirName="my-directory"
   az storage fs undelete-path -f $filesystemName --deleted-path-name $dirName -deletion-id "<deletionId>" --auth-mode login
   ```

   Si cambia el nombre del directorio que contiene los elementos eliminados temporalmente, esos elementos se desconectan del directorio. Si desea restaurar esos elementos, tendrá que revertir el nombre del directorio al nombre original o crear un directorio independiente que use el nombre del directorio original. De lo contrario, recibirá un error al intentar restaurar esos elementos eliminados temporalmente.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-net"></a>Restauración de blobs y directorios eliminados temporalmente mediante .NET

> [!IMPORTANT]
> Esta sección solo se aplica a las cuentas que tienen un espacio de nombres jerárquico.

1. Abra un símbolo del sistema y cambie el directorio (`cd`) a la carpeta del proyecto, por ejemplo:

   ```console
   cd myProject
   ```

2. Instale la versión `Azure.Storage.Files.DataLake -v 12.7.0` del paquete NuGet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) mediante el comando `dotnet add package`.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v -v 12.7.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. A continuación, agregue estas instrucciones USING al inicio del archivo de código.

    ```csharp
    using Azure;
    using Azure.Storage;
    using Azure.Storage.Files.DataLake;
    using Azure.Storage.Files.DataLake.Models;
    using NUnit.Framework;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

4. El código siguiente elimina un directorio y, a continuación, restaura un directorio eliminado temporalmente.

   Este método supone que ha creado una instancia de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient). Para obtener información sobre cómo crear una instancia de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient), consulte [Conexión a la cuenta](data-lake-storage-directory-file-acl-dotnet.md#connect-to-the-account).

   ```csharp
      public void RestoreDirectory(DataLakeServiceClient serviceClient)
      {
          DataLakeFileSystemClient fileSystemClient =
             serviceClient.GetFileSystemClient("my-container");

          DataLakeDirectoryClient directory =
              fileSystem.GetDirectoryClient("my-directory");

          // Delete the Directory
          await directory.DeleteAsync();

          // List Deleted Paths
          List<PathHierarchyDeletedItem> deletedItems = new List<PathHierarchyDeletedItem>();
          await foreach (PathHierarchyDeletedItem deletedItem in fileSystemClient.GetDeletedPathsAsync())
          {
            deletedItems.Add(deletedItem);
          }

          Assert.AreEqual(1, deletedItems.Count);
          Assert.AreEqual("my-directory", deletedItems[0].Path.Name);
          Assert.IsTrue(deletedItems[0].IsPath);

          // Restore deleted directory.
          Response<DataLakePathClient> restoreResponse = await fileSystemClient.RestorePathAsync(
          deletedItems[0].Path.Name,
          deletedItems[0].Path.DeletionId);

      }

   ```

   Si cambia el nombre del directorio que contiene los elementos eliminados temporalmente, esos elementos se desconectan del directorio. Si desea restaurar esos elementos, tendrá que revertir el nombre del directorio al nombre original o crear un directorio independiente que use el nombre del directorio original. De lo contrario, recibirá un error al intentar restaurar esos elementos eliminados temporalmente.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-java"></a>Restauración de blobs y directorios eliminados temporalmente mediante Java

> [!IMPORTANT]
> Esta sección solo se aplica a las cuentas que tienen un espacio de nombres jerárquico.

1. Para empezar, abra el archivo *pom.xml* en el editor de texto. Agregue el siguiente elemento de dependencia al grupo de dependencias.

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-storage-file-datalake</artifactId>
     <version>12.6.0</version>
   </dependency>
   ```

2. Luego, agregue estas instrucciones Imports al archivo de código.

   ```java
   Put imports here
   ```

3. El siguiente fragmento de código restaura un archivo eliminado temporalmente llamado `my-file`.

   Este método supone que ha creado una instancia de **DataLakeServiceClient**. Para obtener información sobre cómo crear una instancia de **DataLakeServiceClient**, consulte [Conexión a la cuenta](data-lake-storage-directory-file-acl-java.md#connect-to-the-account).

   ```java

   public void RestoreFile(DataLakeServiceClient serviceClient){

       DataLakeFileSystemClient fileSystemClient =
           serviceClient.getFileSystemClient("my-container");

       DataLakeFileClient fileClient =
           fileSystemClient.getFileClient("my-file");

       String deletionId = null;

       for (PathDeletedItem item : fileSystemClient.listDeletedPaths()) {

           if (item.getName().equals(fileClient.getFilePath())) {
              deletionId = item.getDeletionId();
           }
       }

       fileSystemClient.restorePath(fileClient.getFilePath(), deletionId);
    }

   ```

   Si cambia el nombre del directorio que contiene los elementos eliminados temporalmente, esos elementos se desconectan del directorio. Si desea restaurar esos elementos, tendrá que revertir el nombre del directorio al nombre original o crear un directorio independiente que use el nombre del directorio original. De lo contrario, recibirá un error al intentar restaurar esos elementos eliminados temporalmente.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-python"></a>Restauración de blobs y directorios eliminados temporalmente mediante Python

> [!IMPORTANT]
> Esta sección solo se aplica a las cuentas que tienen un espacio de nombres jerárquico.

1. Instale la versión `12.4.0` o superior de la biblioteca cliente de Azure Data Lake Storage para Python mediante [pip](https://pypi.org/project/pip/). Este comando instala la versión más reciente de la biblioteca cliente de Azure Data Lake Storage para Python.

   ```
   pip install azure-storage-file-datalake
   ```

2. Agregue estas instrucciones import al inicio del archivo de código.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.storage.filedatalake import FileSystemClient
   ```

3. El código siguiente elimina un directorio y, a continuación, restaura un directorio eliminado temporalmente.

   El código que se muestra a continuación contiene un objeto llamado `service_client` del tipo **DataLakeServiceClient**. Para ver ejemplos sobre cómo crear una instancia de **DataLakeServiceClient**, consulte [Conexión a la cuenta](data-lake-storage-directory-file-acl-python.md#connect-to-the-account).

    ```python
    def restoreDirectory():

        try:
            global file_system_client

            file_system_client = service_client.create_file_system(file_system="my-file-system")

            directory_path = 'my-directory'
            directory_client = file_system_client.create_directory(directory_path)
            resp = directory_client.delete_directory()

            restored_directory_client = file_system_client.undelete_path(directory_client, resp['deletion_id'])
            props = restored_directory_client.get_directory_properties()

            print(props)

        except Exception as e:
            print(e)

    ```

   Si cambia el nombre del directorio que contiene los elementos eliminados temporalmente, esos elementos se desconectan del directorio. Si desea restaurar esos elementos, tendrá que revertir el nombre del directorio al nombre original o crear un directorio independiente que use el nombre del directorio original. De lo contrario, recibirá un error al intentar restaurar esos elementos eliminados temporalmente.

## <a name="next-steps"></a>Pasos siguientes

- [Eliminación temporal para Blob Storage](soft-delete-blob-overview.md)
- [Habilitación de la eliminación temporal para blobs](soft-delete-blob-enable.md)
- [Control de versiones de blobs](versioning-overview.md)
